#if packages are missing, you may have to use `pip install pycryptodome==3.4.3`
from Crypto.Util.number import inverse
from collections import namedtuple
from random import randint
import hashlib
import os
import sys

# Note from challenge author: You honestly do not need to waste much time looking at the cryptohack functions.
#                             You might want or even need to use some of these functions for your solution, but there's
#                             nothing wrong with them, you don't need to understand the intricacies of how they work, and
#                             you most certainly do not have to reverse engineer/exploit/do anything complicated with them.

################################################################
### Some basic EC functions and definitions, from cryptohack.org
################################################################

# Create a simple Point class to represent the set of ec points.
Point = namedtuple("Point", "x y")

# The point at infinity (origin for the group law).
O = 'Origin'

# check that P is a point on the curve with parameters a and b mod p
# helper function
def check_point(P: tuple):
    if P == O:
        return True
    else:
        return (P.y**2 - (P.x**3 + a*P.x + b)) % p == 0 and 0 <= P.x < p and 0 <= P.y < p

# invert P on the curve mod p
# helper function
def point_inverse(P: tuple):
    if P == O:
        return P
    return Point(P.x, -P.y % p)

# add points P and Q on the curve with parameters a and b mod p
# helper function
# you might use this one
def point_addition(P: tuple, Q: tuple):
    if P == O:
        return Q
    elif Q == O:
        return P
    elif Q == point_inverse(P):
        return O
    else:
        if P == Q:
            lam = (3*P.x**2 + a)*inverse(2*P.y, p)
            lam %= p
        else:
            lam = (Q.y - P.y) * inverse((Q.x - P.x), p)
            lam %= p
    Rx = (lam**2 - P.x - Q.x) % p
    Ry = (lam*(P.x - Rx) - P.y) % p
    R = Point(Rx, Ry)
    assert check_point(R)
    return R

# add point P to itself n times on the curve with parameters a and b mod p (calculate nP)
# you will probably use this one
def double_and_add(P: tuple, n: int):
    # based of algo. in ICM
    Q = P
    R = O
    while n > 0:
        if n % 2 == 1:
            R = point_addition(R, Q)
        Q = point_addition(Q, Q)
        n = n // 2
    assert check_point(R)
    return R

################################################################
### end of cryptohack fn's
################################################################

# Define the curve
# C = {(x,y)| y^2 = x^3+3x+2 (mod p)}
p = 310717010502520989590157367261876774703
a = 2
b = 3

# Generator, so we can get all the points!
# for all points x in C, there exists a positive integer n such that x = double_and_add(G,n)
g_x = 179210853392303317793440285562762725654
g_y = 105268671499942631758568591033409611165
G = Point(g_x, g_y)

# useful for our prng
def point_x(P: tuple):
    return P.x

# the number generator
def p_rand_generator(P: tuple, Q: tuple, state: int, num_rounds: int):
    for i in range (num_rounds):
        intermediate_point = double_and_add(P, state)
        intermediate = point_x(intermediate_point)
        output_point = double_and_add(Q, intermediate)
        output = point_x(output_point)
        print(str(output)[2:])
        update_point = double_and_add(P, intermediate)
        state = point_x(update_point)

################################################################
### don't forget to delete (at least the body of) these 3 functions
### so they can't figure it out trivially
################################################################
        
def is_modular_square(a, p):
    return (pow(a,(p-1)//2,p)==1)

def modular_sqrt(a, p):
    assert is_modular_square(a,p)
    assert (p%4==3)
    x = pow(a,(p+1)//4,p)
    return min(x,p-x)

# def decode_try(P:tuple, Q:tuple, output: str):

################################################################

# my two favorite numbers!
p_prime_1 = 2993887
p_prime_2 = 7429

# points for the p_rng
P = double_and_add(G, p_prime_1)
Q = double_and_add(G, p_prime_2)

# change seed value to "??????????????????" so they can't get the correct flag just by running it
seed = "??????????????????" 
seed_bytes = str.encode(seed)
seed_num = int.from_bytes(seed_bytes, byteorder=sys.byteorder)
p_rand_generator(P,Q,seed_num,21)
# last number from output is flag, wrap number in flag{}
print("--------------------------------------")
