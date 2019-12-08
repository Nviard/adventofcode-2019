# Advent of Code 2019

Lien : [Advent of Code 2019](https://adventofcode.com/2019).

Je mettrai à jour le code au fur et à mesure (la description le matin et le code après 18h).

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

## Jour 9

## Jour 10

## Jour 11

## Jour 12

## Jour 13

## Jour 14

## Jour 15

## Jour 16

## Jour 17

## Jour 18

## Jour 19

## Jour 20

## Jour 21

## Jour 22

## Jour 23

## Jour 24
