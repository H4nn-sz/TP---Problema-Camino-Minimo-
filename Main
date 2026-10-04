import random
import networkx as nx
import matplotlib.pyplot as plt


# Ingreso manual de aristas
def crear_grafo_manual(nodos):
    grafo = {nodo: [] for nodo in nodos}

    print(f"\nLos nodos disponibles son: {nodos}")
    print("Ingrese las aristas con formato: origen,destino,peso (ejemplo: A,B,5)")
    print("Escriba 'fin' para terminar\n")

    while True:
        entrada = input("Arista: ").strip().upper()
        if entrada == "FIN":
            break
        try:
            partes = entrada.split(",")
            if len(partes) != 3:
                print("Formato incorrecto, use: origen,destino,peso")
                continue

            origen = partes[0].strip()
            destino = partes[1].strip()
            peso = int(partes[2].strip())

            if origen not in nodos or destino not in nodos:
                print("Error: uno de los vertices no existe")
                continue
            if origen == destino:
                print("Error: no se permiten bucles")
                continue
            if peso < 0:
                print("Error: no se admiten pesos negativos")
                continue

            grafo[origen].append((destino, peso))
            print(f"Arista agregada: {origen} -> {destino} con peso {peso}")

        except ValueError:
            print("Formato incorrecto, ingrese un peso numerico entero")

    _validar_es_dag(grafo)
    return grafo


# Generacion aleatoria del grafo aciclico
def crear_grafo_aleatorio(nodos):
    grafo = {nodo: [] for nodo in nodos}
    n = len(nodos)

    print("\nGenerando grafo aleatorio...")

    for i in range(n - 1):
        posibles_destinos = nodos[i + 1:]
        cantidad = random.randint(1, min(3, len(posibles_destinos)))
        destinos = random.sample(posibles_destinos, cantidad)

        for destino in destinos:
            peso = random.randint(1, 20)
            grafo[nodos[i]].append((destino, peso))

    return grafo


# Verificacion de ciclos
def _validar_es_dag(grafo):
    g_temp = nx.DiGraph()
    for origen, adyacentes in grafo.items():
        g_temp.add_node(origen)
        for destino, peso in adyacentes:
            g_temp.add_edge(origen, destino, weight=peso)

    if not nx.is_directed_acyclic_graph(g_temp):
        print("\n[ADVERTENCIA] El grafo contiene al menos un ciclo cerrado\n")


# Algoritmo de Dijkstra con registro de rutas alternas
def dijkstra(grafo, nodos, origen):
    distancia = {nodo: float("inf") for nodo in nodos}
    distancia[origen] = 0
    predecesores = {nodo: [] for nodo in nodos}
    visitados = set()
    iteracion = 0

    print("\n--- INICIO DEL ALGORITMO DE DIJKSTRA ---")
    print(f"Iteracion {iteracion}: origen '{origen}' con distancia [0, -]")

    while len(visitados) < len(nodos):
        actual = None
        menor_distancia = float("inf")

        for nodo in nodos:
            if nodo not in visitados and distancia[nodo] < menor_distancia:
                menor_distancia = distancia[nodo]
                actual = nodo

        if actual is None:
            break

        visitados.add(actual)
        iteracion += 1
        print(f"\nIteracion {iteracion}: vertice '{actual}' con acumulado {distancia[actual]}")

        for (vecino, peso) in grafo[actual]:
            if vecino in visitados:
                continue

            nueva_distancia = distancia[actual] + peso

            if nueva_distancia < distancia[vecino]:
                distancia[vecino] = nueva_distancia
                predecesores[vecino] = [actual]
                print(f"   -> '{vecino}' actualizado a [{nueva_distancia}, {actual}]")

            elif nueva_distancia == distancia[vecino]:
                predecesores[vecino].append(actual)
                print(f"   -> Camino alterno hacia '{vecino}' pasando por '{actual}'")

    print("\n--- FIN DEL ALGORITMO ---")
    return distancia, predecesores


# Reconstruccion de rutas optimas por backtracking
def reconstruir_caminos(predecesores, origen, destino):
    caminos = []

    def buscar(nodo_actual, camino_parcial):
        if nodo_actual == origen:
            caminos.append([origen] + camino_parcial)
            return
        for anterior in predecesores[nodo_actual]:
            buscar(anterior, [nodo_actual] + camino_parcial)

    buscar(destino, [])
    return caminos


# Renderizado del grafo y resaltado de ruta
def visualizar_grafo(grafo, camino_resaltado=None, titulo="Grafo del problema"):
    g = nx.DiGraph()
    for nodo, adyacentes in grafo.items():
        g.add_node(nodo)
        for vecino, peso in adyacentes:
            g.add_edge(nodo, vecino, weight=peso)

    plt.figure(figsize=(10, 6))

    try:
        capas = list(nx.topological_generations(g))
        for i, capa in enumerate(capas):
            for nodo in capa:
                g.nodes[nodo]["layer"] = i
        posiciones = nx.multipartite_layout(g, subset_key="layer")
    except Exception:
        posiciones = nx.spring_layout(g, seed=42)

    nx.draw_networkx_nodes(g, posiciones, node_color="lightblue", node_size=800)
    nx.draw_networkx_edges(g, posiciones, edge_color="gray", arrows=True, arrowsize=15)
    nx.draw_networkx_labels(g, posiciones, font_weight="bold")

    pesos = nx.get_edge_attributes(g, "weight")
    nx.draw_networkx_edge_labels(g, posiciones, edge_labels=pesos)

    if camino_resaltado and len(camino_resaltado) > 1:
        aristas_camino = list(zip(camino_resaltado, camino_resaltado[1:]))
        nx.draw_networkx_nodes(g, posiciones, nodelist=camino_resaltado, node_color="salmon", node_size=800)
        nx.draw_networkx_edges(g, posiciones, edgelist=aristas_camino, edge_color="red", width=3, arrows=True, arrowsize=15)

    plt.title(titulo)
    plt.axis("off")
    plt.tight_layout()
    plt.savefig("grafo_resultado.png", dpi=150)
    print("\n(Se guardo la imagen del grafo como 'grafo_resultado.png')")
    plt.show()


# Flujo principal del programa
def main():
    while True:
        print("=" * 60)
        print(" SIMULADOR DEL ALGORITMO DE DIJKSTRA - CAMINO MINIMO")
        print("=" * 60)

        # Cantidad de nodos
        while True:
            entrada_n = input("\nIngrese la cantidad de nodos del grafo (7 a 16): ").strip()
            try:
                n = int(entrada_n)
                if entrada_n != str(n):
                    print("Error: Ingrese el numero entero sin ceros a la izquierda")
                    continue
                if 7 <= n <= 16:
                    break
                print("El numero debe encontrarse obligatoriamente en el rango de 7 a 16")
            except ValueError:
                print("Entrada no valida, por favor ingrese unicamente un numero entero")

        nodos = [chr(65 + i) for i in range(n)]

        # Metodo de creacion
        print("\nComo desea generar el grafo?")
        print("  1. Ingreso manual de aristas")
        print("  2. Generacion aleatoria")

        while True:
            modo = input("Seleccione una opcion (1/2): ").strip()
            if modo in ("1", "2"):
                break
            print("Opcion incorrecta, debe responder exclusivamente escribiendo 1 o 2")

        if modo == "1":
            grafo = crear_grafo_manual(nodos)
        else:
            grafo = crear_grafo_aleatorio(nodos)

        print("\nGrafo generado en memoria (lista de adyacencia):")
        for nodo in nodos:
            print(f"  {nodo}: {grafo[nodo]}")

        # Consultas de trayectorias
        while True:
            # Validacion de origen
            while True:
                origen = input(f"\nIngrese el vertice de ORIGEN {nodos}: ").strip().upper()
                if origen in nodos:
                    break
                print(f"Error: '{origen}' no es una opcion valida, debe ingresar una sola letra existente en la lista")

            # Validacion de destino
            while True:
                destino = input(f"Ingrese el vertice de DESTINO {nodos}: ").strip().upper()
                if destino not in nodos:
                    print(f"Error: '{destino}' no es una opcion valida, debe ingresar una sola letra existente en la lista")
                    continue
                if destino == origen:
                    print("Aviso: El vertice de origen y destino coinciden, seleccione un destino distinto")
                    continue
                break

            distancias, predecesores = dijkstra(grafo, nodos, origen)

            print("\n" + "=" * 60)
            print(" RESULTADOS")
            print("=" * 60)

            # Manejo de casos sin conexion
            if distancias[destino] == float("inf"):
                print(f"\nNo existe ningun camino posible desde el vertice '{origen}' hacia el vertice '{destino}'")
                print("Explicacion: Dado que es un grafo dirigido y aciclico (DAG), las conexiones no permiten retroceder hacia vertices previos ni enlazar ramas desconectadas")

                ver_grafo = input("\nDesea visualizar el grafo de todas maneras para analizar su estructura? (s/n): ").strip().lower()
                if ver_grafo == "s":
                    try:
                        visualizar_grafo(grafo, titulo=f"Grafo sin camino entre {origen} y {destino}")
                    except Exception as err:
                        print(f"No fue posible abrir la representacion grafica debido a: {err}")
            else:
                print(f"Distancia minima de '{origen}' a '{destino}': {distancias[destino]}")
                caminos = reconstruir_caminos(predecesores, origen, destino)
                print(f"Cantidad de caminos optimos encontrados: {len(caminos)}")
                for i, camino in enumerate(caminos, start=1):
                    print(f"  Camino {i}: {' -> '.join(camino)}")

                try:
                    visualizar_grafo(
                        grafo,
                        camino_resaltado=caminos[0],
                        titulo=f"Camino minimo de {origen} a {destino} (distancia = {distancias[destino]})"
                    )
                except Exception as err:
                    print(f"No fue posible abrir la representacion grafica debido a: {err}")

            # Navegacion final
            print("\n" + "-" * 50)
            print("Que accion desea realizar ahora?")
            print("  1. Probar otros vertices en este mismo grafo")
            print("  2. Crear un grafo completamente nuevo")
            print("  3. Salir del programa")

            while True:
                opcion = input("Seleccione una opcion (1/2/3): ").strip()
                if opcion in ("1", "2", "3"):
                    break
                print("Opcion no reconocida, por favor ingrese 1, 2 o 3")

            if opcion == "1":
                continue
            elif opcion == "2":
                break
            else:
                print("\nPrograma finalizado correctamente, que tenga un buen dia")
                return


if __name__ == "__main__":
    main()
