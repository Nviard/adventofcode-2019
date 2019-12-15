# Advent of Code 2019

Lien : [Advent of Code 2019](https://adventofcode.com/2019).

Je mettrai à jour le code au fur et à mesure (la description le matin et le code le lendemain).

Tous les exemples de codes sont executés en leur passant les données par l'entrée standard :

```
script.py < input.txt
```

## Jour 1 : The Tyranny of the Rocket Equation

Première partie : calculer la consommation en carburant nécessaire pour faire décoler des fusées en prenant en compte leur poids.

Deuxième partie : le poids du carburant supplémentaire doit également être pris en compte.

```python
def fuel(weight):
    return weight // 3 - 2


def fuel2(weight):
    ret = 0
    current = fuel(weight)
    while current > 0:
        ret += current
        current = fuel(current)
    return ret


data = []

try:
    while True:
        data.append(int(input()))
except EOFError:
    pass

print(sum(map(fuel, data)))
print(sum(map(fuel2, data)))
```

## Jour 2 : 1202 Program Alarm

Première partie : donner le résultat de l'exécution d'un programme exprimé dans un langage simple.

Deuxième partie : trouver deux paramètres permettant à ce programme de produire une valeur spécifique.

```python
import itertools


def execute(code, verb, noun):
    current = 0
    code[1] = verb
    code[2] = noun

    while code[current] != 99:
        if code[current] == 1:
            code[code[current + 3]] = code[code[current + 1]] + code[code[current + 2]]
        elif code[current] == 2:
            code[code[current + 3]] = code[code[current + 1]] * code[code[current + 2]]
        current += 4

    return code


data = input().split(",")


print(execute(list(map(int, data)), 12, 2)[0])

for rep in itertools.product(list(range(len(data))), repeat=2):
    values = execute(list(map(int, data)), rep[0], rep[1])
    if (values[0]) == 19690720:
        break

print(100 * values[1] + values[2])
```

## Jour 3 : Crossed Wires

Première partie : à partir de deux chemins partant d'un même point, trouver la plus proche intersection (distance de Manhattan).

Deuxième partie : à partir de deux chemins partant d'un même point, trouver le plus court chemin empruntant ces deux chemins (somme des distances).

```python
DIRECTIONS = {
    "R": (1, 0,),
    "L": (-1, 0,),
    "U": (0, 1,),
    "D": (0, -1,),
}


def positions(line):
    current = (
        0,
        0,
    )
    pos = dict()
    dist = 0
    for dep in line:
        val = int(dep[1:])
        d = DIRECTIONS[dep[0]]
        while val > 0:
            dist += 1
            current = (
                current[0] + d[0],
                current[1] + d[1],
            )
            val -= 1
            if current in pos:
                if pos[current] > dist:
                    dist = pos[current]
            else:
                pos[current] = dist

    return pos


line = input().split(",")
line2 = input().split(",")

pos = positions(line)
pos2 = positions(line2)

intersections = set(pos.keys()).intersection(set(pos2.keys()))

print(min((sum(map(abs, k)) for k in intersections)))
print(min((pos[k] + pos2[k] for k in intersections)))
```

## Jour 4 : Secure Container

Pour ce jour, les données d'entrée ne proviennent pas d'un fichier.

Première partie : compter des nombres composés de chiffres croissants et contenant au moins un chiffre en double.

Deuxième partie : compter des nombres composés de chiffres croissants et contenant et contenant au moins un chiffre en double (mais pas en triple).

```python
def valid(number):
    double = False
    previous = number % 10
    number //= 10

    for i in range(5):
        current = number % 10
        number //= 10
        if current > previous:
            return False
        if current == previous:
            double = True
        previous = current

    return double


def valid2(number):
    doubles = set()
    bigger = set()

    previous = number % 10
    number //= 10

    for i in range(5):
        current = number % 10
        number //= 10
        if current > previous:
            return False
        if current == previous:
            if current in doubles:
                bigger.add(current)
            else:
                doubles.add(current)
        previous = current

    return len(doubles - bigger) > 0


start = 367479
end = 893698

print(sum(map(valid, range(start, end))))
print(sum(map(valid2, range(start, end))))
```

## Jour 5 : Sunny with a Chance of Asteroids

Première partie : similaire au jour 2 avec des paramètres donnés par adresse ou par valeur.

Deuxième partie : similaire au jour 2 avec des instructions conditionnelles.

```python
def parse_args(code, pointer, instr, n, ret=True):
    args = []
    for i in range(n):
        if instr % 10 == 1:
            args.append(code[pointer + i])
        else:
            args.append(code[code[pointer + i]])
        instr //= 10
    if ret:
        args.append(code[pointer + n])

    return args


def execute(code, data):
    current = 0
    opcode = 0
    while opcode != 99:
        instr = code[current]

        opcode = instr % 100

        instr //= 100
        if opcode == 1:
            args = parse_args(code, current + 1, instr, 2)
            code[args[2]] = args[0] + args[1]
            current += 4
        elif opcode == 2:
            args = parse_args(code, current + 1, instr, 2)
            code[args[2]] = args[0] * args[1]
            current += 4
        elif opcode == 3:
            args = parse_args(code, current + 1, instr, 0)
            code[args[0]] = data
            current += 2
        elif opcode == 4:
            args = parse_args(code, current + 1, instr, 1, False)
            print(args[0])
            current += 2
        elif opcode == 5:
            args = parse_args(code, current + 1, instr, 2, False)
            if args[0] != 0:
                current = args[1]
            else:
                current += 3
        elif opcode == 6:
            args = parse_args(code, current + 1, instr, 2, False)
            if args[0] == 0:
                current = args[1]
            else:
                current += 3
        elif opcode == 7:
            args = parse_args(code, current + 1, instr, 2)
            code[args[2]] = 1 if args[0] < args[1] else 0
            current += 4
        elif opcode == 8:
            args = parse_args(code, current + 1, instr, 2)
            code[args[2]] = 1 if args[0] == args[1] else 0
            current += 4

    return code


data = input().split(",")

execute(list(map(int, data)), 1)
execute(list(map(int, data)), 5)
```

## Jour 6 : Universal Orbit Map

Première partie : compter le nombre de branches dans un arbre.

Deuxième partie : calculer une distance dans un arbre.

```python
from collections import deque

data = {}

try:
    while True:
        orbit = input().split(")")
        if orbit[0] not in data:
            data[orbit[0]] = []
        data[orbit[0]].append(orbit[1])
except EOFError:
    pass

orbits = {"COM": set()}
next_objects = deque(["COM"])

while next_objects:
    current_object = next_objects.pop()
    if current_object in data:
        next_orbits = orbits[current_object] | set([current_object])
        for n in data[current_object]:
            orbits[n] = next_orbits
            next_objects.append(n)


print(sum(map(len, orbits.values())))
print(len(orbits["SAN"] ^ orbits["YOU"]))
```

## Jour 7 : Amplification Circuit

Partie 1 : lancer plusieurs interpréteurs d'un programme similaire à ceux du jour 5.

Partie 2 : lancer plusieurs fois le programme sur chaque interpréteur.

```python
import itertools
from collections import deque


class Machine:
    def __init__(self, phase, code):
        self._inputs = deque([phase])
        self._code = code
        self._pointer = 0

    def parse_args(self, instr, n, ret=True):
        pointer = self._pointer + 1
        args = []
        for i in range(n):
            if instr % 10 == 1:
                args.append(self._code[pointer + i])
            else:
                args.append(self._code[self._code[pointer + i]])
            instr //= 10
        if ret:
            args.append(self._code[pointer + n])

        return args

    def execute(self, data):
        self._inputs.append(data)
        instr = self._code[self._pointer]
        opcode = instr % 100
        while opcode != 99:
            instr //= 100
            if opcode == 1:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = args[0] + args[1]
                self._pointer += 4
            elif opcode == 2:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = args[0] * args[1]
                self._pointer += 4
            elif opcode == 3:
                args = self.parse_args(instr, 0)
                self._code[args[0]] = self._inputs.popleft()
                self._pointer += 2
            elif opcode == 4:
                args = self.parse_args(instr, 1, False)
                self._pointer += 2
                return args[0]
            elif opcode == 5:
                args = self.parse_args(instr, 2, False)
                if args[0] != 0:
                    self._pointer = args[1]
                else:
                    self._pointer += 3
            elif opcode == 6:
                args = self.parse_args(instr, 2, False)
                if args[0] == 0:
                    self._pointer = args[1]
                else:
                    self._pointer += 3
            elif opcode == 7:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = 1 if args[0] < args[1] else 0
                self._pointer += 4
            elif opcode == 8:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = 1 if args[0] == args[1] else 0
                self._pointer += 4
            else:
                print("error")
                return None
            instr = self._code[self._pointer]
            opcode = instr % 100
        return None


data = input().split(",")

results = set()

for seq in itertools.permutations(list(range(5)), r=5):
    init = 0
    for phase in seq:
        init = Machine(phase, list(map(int, data))).execute(init)
    results.add(init)

print(max(results))

results = set()

for seq in itertools.permutations(list(range(5, 10)), r=5):
    init = 0
    machines = {phase: Machine(phase, list(map(int, data))) for phase in seq}
    while True:
        for phase in seq:
            init = machines[phase].execute(init)
        if init is None:
            results.add(lastval)
            break
        else:
            lastval = init

print(max(results))
```

## Jour 8 : Space Image Format

Partie 1 : dans une image en noir / blanc / transparent à plusieurs couches, appliquer une opération sur la couche la moins noire.

Partie 2 : afficher l'image.

```python
image = list(map(int, input()))
WIDTH = 25
HEIGHT = 6
layers = [image[i : i + WIDTH * HEIGHT] for i in range(0, len(image), WIDTH * HEIGHT)]
layer = min(layers, key=lambda x: x.count(0))
print(layer.count(1) * layer.count(2))
image = [[None for x in range(WIDTH)] for y in range(HEIGHT)]
for layer in layers:
    for i, val in enumerate(layer):
        if val != 2 and image[i // WIDTH][i % WIDTH] is None:
            image[i // WIDTH][i % WIDTH] = "O" if val else " "
for y in range(HEIGHT):
    print("".join(image[y]))
```

## Jour 9 : Sensor Boost

Partie 1 : ajout d'adresses relatives à un pointeur et d'une instruction pour le déplacer aux interpréteurs du jour 7.

Partie 2 : pas de code supplémentaire.

```python
import itertools
from collections import deque


class Code(list):
    def __getitem__(self, key):
        while key >= len(self):
            self.append(0)
        return super().__getitem__(key)

    def __setitem__(self, key, item):
        while key >= len(self):
            self.append(0)
        super().__setitem__(key, item)


class Machine:
    def __init__(self, code):
        self._inputs = deque([])
        self._code = Code(code)
        self._pointer = 0
        self._base = 0

    def parse_args(self, instr, n, ret=True):
        pointer = self._pointer + 1
        args = []
        for i in range(n):
            if instr % 10 == 1:
                args.append(self._code[pointer + i])
            elif instr % 10 == 2:
                args.append(self._code[self._base + self._code[pointer + i]])
            else:
                args.append(self._code[self._code[pointer + i]])
            instr //= 10
        if ret:
            if instr % 10 == 2:
                args.append(self._base + self._code[pointer + n])
            else:
                args.append(self._code[pointer + n])

        return args

    def execute(self, data):
        self._inputs.append(data)
        instr = self._code[self._pointer]
        opcode = instr % 100
        while opcode != 99:
            instr //= 100
            if opcode == 1:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = args[0] + args[1]
                self._pointer += 4
            elif opcode == 2:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = args[0] * args[1]
                self._pointer += 4
            elif opcode == 3:
                args = self.parse_args(instr, 0)
                self._code[args[0]] = self._inputs.popleft()
                self._pointer += 2
            elif opcode == 4:
                args = self.parse_args(instr, 1, False)
                self._pointer += 2
                print(args[0])
            elif opcode == 5:
                args = self.parse_args(instr, 2, False)
                if args[0] != 0:
                    self._pointer = args[1]
                else:
                    self._pointer += 3
            elif opcode == 6:
                args = self.parse_args(instr, 2, False)
                if args[0] == 0:
                    self._pointer = args[1]
                else:
                    self._pointer += 3
            elif opcode == 7:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = 1 if args[0] < args[1] else 0
                self._pointer += 4
            elif opcode == 8:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = 1 if args[0] == args[1] else 0
                self._pointer += 4
            elif opcode == 9:
                args = self.parse_args(instr, 1, False)
                self._base += args[0]
                self._pointer += 2
            else:
                print("error")
                return None
            instr = self._code[self._pointer]
            opcode = instr % 100
        return None


data = input().split(",")
Machine(list(map(int, data))).execute(1)
Machine(list(map(int, data))).execute(2)
```

## Jour 10 : Monitoring Station

Partie 1 : dans un repère, déterminer quel objet peut voir le plus d'autres objets.

Partie 2 : dans un repère, détruire successivement les objets les plus proches d'une référence en tournant dans le sens des aiguilles d'une montre.

```python
import itertools
import math

coords = set()

try:
    iline = 0
    while True:
        line = input()
        for icol, col in enumerate(line):
            if col == "#":
                coords.add((icol, iline,))
        iline += 1
except EOFError:
    pass

targets = {c: dict() for c in coords}
for couple in itertools.product(coords, repeat=2):
    dx = couple[1][0] - couple[0][0]
    dy = couple[0][1] - couple[1][1]
    angle = math.atan2(dy, dx)
    dist = math.hypot(dx, dy)
    if angle not in targets[couple[0]]:
        targets[couple[0]][angle] = {}
    targets[couple[0]][angle][dist] = couple[1]

monitoring = max(targets.values(), key=len)
print(len(monitoring))

angle = math.pi / 2
destroyed = 0
last_destroyed = None
while destroyed < 200:
    angle = min(monitoring, key=lambda x, ref=angle: (angle - x) % (2 * math.pi))
    dist = min(monitoring[angle])
    last_destroyed = monitoring[angle].pop(dist)
    if not monitoring[angle]:
        monitoring.pop(angle)
    destroyed += 1
    angle -= 0.0001

print(last_destroyed[0] * 100 + last_destroyed[1])
```

## Jour 11 : Space Police

Partie 1 : utiliser un interpréteur du jour 9 pour déplacer un robot et dessiner dans un plan.

Partie 2 : afficher le dessin effectué par le robot.

```python
import itertools
from collections import deque

DIRECTIONS = [(0, -1,), (-1, 0,), (0, 1,), (1, 0,)]


class Code(list):
    def __getitem__(self, key):
        while key >= len(self):
            self.append(0)
        return super().__getitem__(key)

    def __setitem__(self, key, item):
        while key >= len(self):
            self.append(0)
        super().__setitem__(key, item)


class Machine:
    def __init__(self, code):
        self._inputs = deque([])
        self._code = Code(code)
        self._pointer = 0
        self._base = 0

    def parse_args(self, instr, n, ret=True):
        pointer = self._pointer + 1
        args = []
        for i in range(n):
            if instr % 10 == 1:
                args.append(self._code[pointer + i])
            elif instr % 10 == 2:
                args.append(self._code[self._base + self._code[pointer + i]])
            else:
                args.append(self._code[self._code[pointer + i]])
            instr //= 10
        if ret:
            if instr % 10 == 2:
                args.append(self._base + self._code[pointer + n])
            else:
                args.append(self._code[pointer + n])

        return args

    def execute(self, data=None):
        if data is not None:
            self._inputs.append(data)
        instr = self._code[self._pointer]
        opcode = instr % 100
        while opcode != 99:
            instr //= 100
            if opcode == 1:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = args[0] + args[1]
                self._pointer += 4
            elif opcode == 2:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = args[0] * args[1]
                self._pointer += 4
            elif opcode == 3:
                args = self.parse_args(instr, 0)
                self._code[args[0]] = self._inputs.popleft()
                self._pointer += 2
            elif opcode == 4:
                args = self.parse_args(instr, 1, False)
                self._pointer += 2
                return args[0]
            elif opcode == 5:
                args = self.parse_args(instr, 2, False)
                if args[0] != 0:
                    self._pointer = args[1]
                else:
                    self._pointer += 3
            elif opcode == 6:
                args = self.parse_args(instr, 2, False)
                if args[0] == 0:
                    self._pointer = args[1]
                else:
                    self._pointer += 3
            elif opcode == 7:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = 1 if args[0] < args[1] else 0
                self._pointer += 4
            elif opcode == 8:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = 1 if args[0] == args[1] else 0
                self._pointer += 4
            elif opcode == 9:
                args = self.parse_args(instr, 1, False)
                self._base += args[0]
                self._pointer += 2
            else:
                print("error")
                return None
            instr = self._code[self._pointer]
            opcode = instr % 100
        return None


class Robot:
    def __init__(self, code):
        self._map = {}
        self._direction = 0
        self._position = (
            0,
            0,
        )
        self._brain = Machine(code)

    def turn(self, direction):
        if direction == 1:
            self._direction -= 1
        else:
            self._direction += 1
        self._direction %= len(DIRECTIONS)

    def paint(self, color):
        self._map[self._position] = color

    def step(self):
        self._position = (
            self._position[0] + DIRECTIONS[self._direction][0],
            self._position[1] + DIRECTIONS[self._direction][1],
        )

    def execute(self):
        self.paint(1)
        while True:
            color = self._brain.execute(self._map.get(self._position, 0))
            if color is None:
                return self._map
            self.paint(color)
            direction = self._brain.execute()
            if direction is None:
                return self._map
            self.turn(direction)
            self.step()


data = input().split(",")
print(len(Robot(list(map(int, data))).execute()))

coords = Robot(list(map(int, data))).execute()
maxx = max(coords, key=lambda x: x[0])[0]
maxy = max(coords, key=lambda x: x[1])[1]
minx = min(coords, key=lambda x: x[0])[0]
miny = min(coords, key=lambda x: x[1])[1]
height = maxy - miny + 1
width = maxx - minx + 1

print(height)
print(width)
image = [[" " for c in range(width)] for r in range(height)]
for k in coords:
    if coords[k] == 1:
        try:
            image[k[1]][k[0]] = "O"
        except:
            print(k[1])
            print(k[0])

for y in range(height):
    print("".join(image[y]))
```

## Jour 12 : The N-Body Problem

Partie 1 : calculer les déplacements d'objets dans un espace 3D en appliquant des règlees de gravitation.

Partie 2 : déterminer la période des positions de ces objets.

```python
import re
import itertools
import functools
import math

system = []

regex = re.compile("[a-z= ><]*")
try:
    while True:
        system.append([list(map(int, regex.sub("", input()).split(","))), [0, 0, 0]])
except EOFError:
    pass

periods = {}
pos = [
    tuple(itertools.chain(map(lambda x: (x[0][i], x[1][i]), system))) for i in range(3)
]
current = 0

while len(periods) < 3 or current < 1000:
    current += 1
    for moon, moon2 in itertools.combinations(system, r=2):
        for coord in range(3):
            if moon[0][coord] > moon2[0][coord]:
                moon[1][coord] -= 1
                moon2[1][coord] += 1
            elif moon[0][coord] < moon2[0][coord]:
                moon[1][coord] += 1
                moon2[1][coord] -= 1
    for moon in system:
        for i, v in enumerate(moon[1]):
            moon[0][i] += v
    for i in range(3):
        if i not in periods:
            if (
                tuple(itertools.chain(map(lambda x: (x[0][i], x[1][i]), system)))
                == pos[i]
            ):
                periods[i] = current
    if current == 1000:
        print(
            sum(
                map(
                    lambda moon: sum(map(abs, moon[0])) * sum(map(abs, moon[1])), system
                )
            )
        )

print(functools.reduce(lambda a, b: a * b // math.gcd(a, b), periods.values()))
```

## Jour 13 : Care Package

Partie 1 : utiliser un interpréteur du jour 9 pour dessiner l'écran d'un jeu d'arcade.

Partie 2 : déterminer les entrées nécessaires pour gagner la partie.

```
import itertools
from collections import deque


class Code(list):
    def __getitem__(self, key):
        while key >= len(self):
            self.append(0)
        return super().__getitem__(key)

    def __setitem__(self, key, item):
        while key >= len(self):
            self.append(0)
        super().__setitem__(key, item)


class Machine:
    def __init__(self, code):
        self._inputs = deque([])
        self._code = Code(code)
        self._pointer = 0
        self._base = 0

    def parse_args(self, instr, n, ret=True):
        pointer = self._pointer + 1
        args = []
        for i in range(n):
            if instr % 10 == 1:
                args.append(self._code[pointer + i])
            elif instr % 10 == 2:
                args.append(self._code[self._base + self._code[pointer + i]])
            else:
                args.append(self._code[self._code[pointer + i]])
            instr //= 10
        if ret:
            if instr % 10 == 2:
                args.append(self._base + self._code[pointer + n])
            else:
                args.append(self._code[pointer + n])

        return args

    def execute(self, data=None):
        if data is not None:
            self._inputs.append(data)
        instr = self._code[self._pointer]
        opcode = instr % 100
        while opcode != 99:
            instr //= 100
            if opcode == 1:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = args[0] + args[1]
                self._pointer += 4
            elif opcode == 2:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = args[0] * args[1]
                self._pointer += 4
            elif opcode == 3:
                args = self.parse_args(instr, 0)
                self._code[args[0]] = self._inputs.popleft()
                self._pointer += 2
            elif opcode == 4:
                args = self.parse_args(instr, 1, False)
                self._pointer += 2
                return args[0]
            elif opcode == 5:
                args = self.parse_args(instr, 2, False)
                if args[0] != 0:
                    self._pointer = args[1]
                else:
                    self._pointer += 3
            elif opcode == 6:
                args = self.parse_args(instr, 2, False)
                if args[0] == 0:
                    self._pointer = args[1]
                else:
                    self._pointer += 3
            elif opcode == 7:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = 1 if args[0] < args[1] else 0
                self._pointer += 4
            elif opcode == 8:
                args = self.parse_args(instr, 2)
                self._code[args[2]] = 1 if args[0] == args[1] else 0
                self._pointer += 4
            elif opcode == 9:
                args = self.parse_args(instr, 1, False)
                self._base += args[0]
                self._pointer += 2
            else:
                print("error")
                return None
            instr = self._code[self._pointer]
            opcode = instr % 100
        return None


data = input().split(",")
data[0] = 2
arcade = Machine(list(map(int, data)))

tiles = {}
begin = True

outputs = deque()
while True:
    try:
        output = arcade.execute()
    except IndexError:
        if begin:
            begin = False
            print(len(list(filter(lambda x: x == 2, tiles.values()))))
        paddle, _ = list(tiles.keys())[list(tiles.values()).index(3)]
        ball, _ = list(tiles.keys())[list(tiles.values()).index(4)]
        if paddle < ball:
            direction = 1
        elif paddle > ball:
            direction = -1
        else:
            direction = 0
        output = arcade.execute(direction)
    if output is None:
        break
    else:
        if len(outputs) == 2:
            tiles[tuple(outputs)] = output
            outputs.clear()
        else:
            outputs.append(output)

print(tiles[(-1, 0)])
```

## Jour 14 : Space Stoichiometry

Partie 1 : à partir d'une liste de réaction chimiques, calculer le nombre de réactifs de base nécessaires pour obtenir un produit final.

Partie 2 : calculer quelle est la quantité maximale de produit final pouvant être obtenue à partir d'une quantité donnée de réactif de base.

```
import re
import math

reactions = {}

regex = re.compile("[0-9]+ [A-Z]+")
try:
    while True:
        *input_chemicals, output_chemical = regex.findall(input())
        output_quantity, output_name = output_chemical.split()
        components = []
        for chem in input_chemicals:
            chem = chem.split()
            components.append((int(chem[0]), chem[1]))
        reactions[output_name] = (int(output_quantity), components)

except EOFError:
    pass


def make_fuel(n):
    need = {"ORE": 0, "FUEL": n}
    made = {}

    while len(need) > 1:
        new_need = {"ORE": need.pop("ORE")}
        for key, val in need.items():
            if key not in made:
                made[key] = 0
            made[key] -= val
            val = -made[key]
            if val > 0:
                quantity, components = reactions[key]
                factor = math.ceil(val / quantity)
                made[key] += quantity * factor
                for input_quantity, input_name in components:
                    if input_name not in new_need:
                        new_need[input_name] = 0
                    new_need[input_name] += input_quantity * factor
        need = new_need
    return need["ORE"]


print(make_fuel(1))

res = 0
fuel = 2
while res <= 1000000000000:
    fuel *= 2
    res = make_fuel(fuel)
fuel //= 2
base = fuel // 2
while base > 0:
    if make_fuel(fuel + base) <= 1000000000000:
        fuel += base
    base //= 2

print(fuel)
```

## Jour 15 : Oxygen System

Partie 1 : déplacer un robot sur une carte avec un interpréteur du jour 9 pour déterminer le plus court chemin permettant d'atteindre un point.

Partie 2 : déplacer un robot sur une carte pour trouver la plus longue distance depuis un point donné.

## Jour 16

## Jour 17

## Jour 18

## Jour 19

## Jour 20

## Jour 21

## Jour 22

## Jour 23

## Jour 24
