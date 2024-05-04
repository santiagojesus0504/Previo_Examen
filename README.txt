using System;
using System.Collections.Generic;

class Program
{
    /// 
    /// Video:
    /// https://youtu.be/oNoW6Ghd7qg
    /// 
    static void Main(string[] args)
    {
        // Matriz
        int[,] matriz = new int[,]
        {
            {0,0,0,0,0,0,0,0,0,0},
            {0,0,0,0,0,0,0,0,0,0},
            {0,0,0,1,0,0,0,1,0,0},
            {0,0,0,1,0,0,0,1,0,0},
            {1,1,1,0,0,0,0,1,0,0},
            {0,0,0,0,1,1,1,1,0,0},
            {0,0,0,0,1,0,0,0,0,0},
            {0,0,0,0,1,0,0,0,0,0},
            {0,0,0,0,1,0,0,0,0,0},
            {0,0,0,0,1,0,0,0,0,0}
        };
        // Coordenadas del inicio y del fin
        List<(int, int)> ruta = Camino(matriz, (7, 0), (3, 9));

        ImprimirSolucion(matriz, ruta);
       
        Console.ReadKey();
    }

    static List<(int, int)> Camino(int[,] matriz, (int, int) inicio, (int, int) fin)
    {
        int filas = matriz.GetLength(0);
        int columnas = matriz.GetLength(1);

        // Definir direcciones: arriba, abajo, izquierda, derecha
        int[] dx = {-1,1,0,0};
        int[] dy = {0,0,-1,1};

        // Función para la distancia Manhattan entre dos puntos
        int DistanciaManhattan((int, int) a, (int, int) b)
        {
            return Math.Abs(a.Item1 - b.Item1) + Math.Abs(a.Item2 - b.Item2);
        }

        // Función para validar su movimiento
        bool MovimientoValido(int x, int y)
        {
            return x >= 0 && x < filas && y >= 0 && y < columnas && matriz[x, y] == 0;
        }

        // Guarda los nodos que ya se recorrieron
        HashSet<(int, int)> recorrido = new HashSet<(int, int)>();
        // Guarda los nodos iniciales del recorrido
        Dictionary<(int, int), (int, int)> nodoini = new Dictionary<(int, int), (int, int)>();
        //Registra los nodos sin recorrer
        SortedSet<(int, (int, int))> sinrecorrer = new SortedSet<(int, (int, int))>();

        sinrecorrer.Add((0, inicio));
        nodoini[inicio] = inicio;

        while (sinrecorrer.Count > 0)
        {
            var current = sinrecorrer.Min;
            sinrecorrer.Remove(current);

            if (current.Item2 == fin)
            {
                // Reconstruir el camino encontrado
                List<(int, int)> ruta = new List<(int, int)>();
                var node = current.Item2;
                while (node != inicio)
                {
                    ruta.Add(node);
                    node = nodoini[node];
                }
                ruta.Add(inicio);
                ruta.Reverse();
                return ruta;
            }

            recorrido.Add(current.Item2);

            for (int i = 0; i < 4; i++)
            {
                int X0 = current.Item2.Item1 + dx[i];
                int Y0 = current.Item2.Item2 + dy[i];

                if (MovimientoValido(X0, Y0) && !recorrido.Contains((X0, Y0)))
                {
                    var neighbor = (X0, Y0);
                    int gScore = DistanciaManhattan(inicio, current.Item2) + DistanciaManhattan(current.Item2, neighbor);
                    int fScore = gScore + DistanciaManhattan(neighbor, fin);

                    if (!sinrecorrer.Contains((fScore, neighbor)))
                    {
                        sinrecorrer.Add((fScore, neighbor));
                        nodoini[neighbor] = current.Item2;
                    }
                }
            }
        }

        return null;
    }

    static void ImprimirSolucion(int[,] matriz, List<(int, int)> ruta)
    {
        int filas = matriz.GetLength(0);
        int columnas = matriz.GetLength(1);

        for (int i = 0; i < filas; i++)
        {
            for (int j = 0; j < columnas; j++)
            {
                if (ruta.Contains((i, j)))
                {
                    Console.Write("S ");
                }
                else
                {
                    Console.Write(matriz[i, j] + " ");
                }
            }
            Console.WriteLine();
        }
    }
}
