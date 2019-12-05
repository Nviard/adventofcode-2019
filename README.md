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

## Jour 6

## Jour 7

## Jour 8

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
