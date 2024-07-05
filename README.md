


class Graph:
    def __init__(self):
        self.vertices = {}

    def add_vertex(self, key):
        self.vertices[key] = Vertex(key)

    def get_vertex(self, key):
        return self.vertices[key]

    def add_edge(self, src_key, dest_key, weight=1):
        self.vertices[src_key].add_neighbour(self.vertices[dest_key], weight)
        self.vertices[dest_key].add_neighbour(self.vertices[src_key], weight)

    def dijkstra(self, source):
        unvisited = set(self.vertices.values())
        distance = {v: sys.maxsize for v in self.vertices.values()}
        distance[self.vertices[source]] = 0

        while unvisited:
            closest = min(unvisited, key=lambda v: distance[v])
            unvisited.remove(closest)

            for neighbour in closest.get_neighbours():
                new_distance = distance[closest] + closest.get_weight(neighbour)
                if distance[neighbour] > new_distance:
                    distance[neighbour] = new_distance

        return distance

class Vertex:
    def __init__(self, key):
        self.key = key
        self.points_to = {}

    def add_neighbour(self, dest, weight):
        self.points_to[dest] = weight

    def get_neighbours(self):
        return self.points_to.keys()

    def get_weight(self, dest):
        return self.points_to[dest]

def main():
    g = Graph()

    while True:
        print('Menu')
        print('add vertex <key>')
        print('add edge <src> <dest> <weight>')
        print('shortest <source vertex key>')
        print('display')
        print('quit')

        do = input('What would you like to do? ').split()

        if do[0] == 'add':
            if do[1] == 'vertex':
                key = int(do[2])
                g.add_vertex(key)
            elif do[1] == 'edge':
                src = int(do[2])
                dest = int(do[3])
                weight = int(do[4])
                g.add_edge(src, dest, weight)
        elif do[0] == 'shortest':
            key = int(do[1])
            distance = g.dijkstra(key)
            print('Distances from {}:'.format(key))
            for v in distance:
                print('Distance to {}: {}'.format(v.key, distance[v]))
        elif do[0] == 'display':
            print('Vertices:', ' '.join(str(v.key) for v in g.vertices.values()))
            print('Edges:')
            for v in g.vertices.values():
                for dest in v.get_neighbours():
                    w = v.get_weight(dest)
                    print('(src={}, dest={}, weight={})'.format(v.key, dest.key, w))
        elif do[0] == 'quit':
            break
