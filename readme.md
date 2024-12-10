# Cours cryptographie

# NOTION

A est inversible modulo n si et seulement si a et n sont premiers entre eux
Pour calculer l'inverse de a modulo n, on utilise l'algorithme d'Euclide étendu



## Exercice 1
- Test si un nombre est premier

```python
def is_prime(int n) -> bool:
    if n < 2:
        return False
    for i in range(2, int(math.sqrt(n)) +1):
        if n % i == 0:
            return False
    return True
```
Output:
```
is_prime(11)
True
```

- Renvoyer la liste des nombres premiers inférieurs à n

```python
def get_primes_below(int n) -> list[int]:
    primes = []
    for i in range(2 ,n):
        if is_prime(i):
            primes.append(i)
    return primes
```
Output:
```
get_primes_below(11)
[2, 3, 5, 7]
```

- Renvoyer le ration de nombres premiers jusqu'à n

```python
def get_prime_ration(int n) -> float:
    primes_list = get_primes_below(n)
    ration = len(primes_list) / n
    theoretical_ratio = 1 / math.log(n)

    print(f"Nombres jusqu'à {n}:")
    print(f"Proportion de premiers: {ratio:.4f}")
    print(f"1/ln(n): {theoretical:.4f}")
    print(f"Différence: {abs(ratio - theoretical):.4f}")
    return ratio, theoretical
```

Output:
```
get_prime_ration(11)
(0.4545, 0.4545)
```

## Exercice 2
### Algorithme d'Euclide
- Calculer le pgcd de deux nombres (etendu)

```python
def extended_euclid(a ,b) -> tuple[int , int , int]:
    if b == 0:
        return a, 1 , 0
    
    u1 , v1 = 1, 0
    u2 , v2 = 0, 1

    while b != 0:
        q = a // b
        r = a % b
        a, b = b, r
        u1, u2 = u2, u1 - q * u2
        v1, v2 = v2, v1 - q * v2
    return a, u1, v1
```

- Calculer l'inverse modulaire

```python
def mod_inverse(int a, int n) -> int:
    return pow(a, -1, n)
```

## Exercice 3
- Démontrer que $$7^{n} + 1$$ est divisible par 8 si n est impair
### Preuve

1. **Base de l'induction**: Pour \(n = 1\), nous avons \(7^1 + 1 = 8\), qui est clairement divisible par 8.

2. **Observation**: Pour \(n\) impair, nous pouvons écrire \(n = 2k + 1\) où \(k\) est un entier.

3. **Calcul de \(7^n \mod 8\)**:
   - \(7 \equiv -1 \pmod{8}\) car \(7 + 1 = 8\).
   - Donc, \(7^n \equiv (-1)^n \pmod{8}\).

4. **Cas où \(n\) est impair**:
   - Si \(n\) est impair, alors \((-1)^n = -1\).
   - Ainsi, \(7^n \equiv -1 \pmod{8}\).

5. **Conclusion**:
   - \(7^n + 1 \equiv -1 + 1 \equiv 0 \pmod{8}\).

Cela montre que \(7^n + 1\) est divisible par 8 lorsque \(n\) est impair.

## Exercice 3.2

Trouver le reste de la division par 13 du nombre \(100^{1000}\).

### Solution

1. **Première étape**: Simplifions \(100 \bmod 13\)
   \[
   100 = 7 \times 13 + 9
   \]
   \[
   100 \equiv 9 \pmod{13}
   \]

2. **Deuxième étape**: Donc \(100^{1000} \equiv 9^{1000} \pmod{13}\)

3. **Troisième étape**: Calculons les puissances de 9 modulo 13
   \[
   9^1 \equiv 9 \pmod{13}
   \]
   \[
   9^2 \equiv 81 \equiv 3 \pmod{13}
   \]
   \[
   9^3 \equiv 3 \times 9 \equiv 27 \equiv 1 \pmod{13}
   \]
   \[
   9^4 \equiv 1 \times 9 \equiv 9 \pmod{13}
   \]

4. **Quatrième étape**: On observe un cycle de longueur 3
   - \(9^1 \equiv 9 \pmod{13}\)
   - \(9^2 \equiv 3 \pmod{13}\)
   - \(9^3 \equiv 1 \pmod{13}\)

5. **Conclusion**: Pour trouver \(9^{1000} \pmod{13}\)
   - Division euclidienne de 1000 par 3:
   \[
   1000 = 333 \times 3 + 1
   \]
   - Donc \(9^{1000} \equiv 9^1 \equiv 9 \pmod{13}\)

Le reste de la division de \(100^{1000}\) par 13 est donc 9.


## Exercice 4

### Chiffrement affine

```python
#4.1 Création des dictionnaires
def create_dictionaries():
    #Création du dictionnaire
    char_to_val = {}
    alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
    #Ajout des lettres avec leur valeur associée
    for i , char in enumerate(alphabet):
        char_to_val[char] = i
    
    char_to_val[':'] = 0
    char_to_val['.'] = 27
    char_to_val['?'] = 28
    char_to_val['!'] = 29
    char_to_val[' '] = 30

    #Création du dictionnaire inverse
    val_to_char = {v: k for k, v in char_to_val.items()}

    return char_to_val , val_to_char

#4.2 Chiffrement affine
def chiffrement_affine(str message , int a , int b) -> str: 
    char_to_val , val_to_char = create_dictionaries()
    #Vérifier que a est premier avec 31
    if math.gcd(a, 31) != 1:
        raise ValueError("a doit être premier avec 31")
    
    encrypted_message = ""
    for char in message:
        if char in char_to_val:
            val = char_to_val[char]
            encrypted_val = (a * val + b) % 31
            encrypted_message += val_to_char[encrypted_val]

    return encrypted_message

#4.3 Déchiffrement affine
def dechiffrement_affine(str message , int a , int b)-> str:
    char_to_val , val_to_char = create_dictionaries()
    #Vérifier que a est premier avec 31
    if math.gcd(a, 31) != 1:
        raise ValueError("a doit être premier avec 31")
    
    decrypted_message = ""
    for char in message:
        if char in char_to_val:
            val = char_to_val[char]
            decrypted_val = (pow(a, -1, 31) * (val - b)) % 31
            decrypted_message += val_to_char[decrypted_val]

    return decrypted_message
```

## Exercice 6

### Chiffrement RSA

```python
# Fonction pour savoir si un nombre est premier
def is_prime(n) -> bool:
    if n < 2:
        return False
    for i in range(2, int(math.sqrt(n)) + 1):
        if n % i == 0:
            return False
    return True

# Fonction pour obtenir le n-ième nombre premier
def get_nth_prime(n) -> int:
    count = 0
    num = 2
    while count < n:
        if is_prime(num):
            count += 1
        num += 1
    return num - 1

# Fonction pour créer une paire de clés RSA
# Return (n, e, d) (n = modulus, e = public key, d = private key)
def key_creation() -> tuple[int , int , int]:
    p = get_nth_prime(random.randint(26, 1000))
    q = get_nth_prime(random.randint(26, 1000))
    while p == q:
        q = get_nth_prime(random.randint(26, 1000))
    
    n = p * q
    phi = (p -1) *(q-1)

    e = 65537
    
    while math.gcd(e,phi) != 1:
        e = random.randrange(3, phi, 2)
    
    d = pow(e, -1, phi)

    return (n, e, d)
```

Fonction de chiffrement RSA

```python
def encryption_int(n , e , m) -> int:
    return pow(m, e, n)
```

Fonction de déchiffrement RSA

```python
def decryption_int(n , d , c) -> int:
    return pow(c, d, n)
```


### Exercice 7 

#### 7.1 Code de Hamming

**Message original** : 0110110010

**Bits de parité** : positions en puissance de 2
- 2⁰ = 1 (p1)
- 2¹ = 2 (p2)
- 2² = 4 (p3)
- 2³ = 8 (p4)

**Message avec emplacements des bits de parité** : 
```
_ / _ / 0 / _ / 1 / 1 / 0 / _ / 1 / 1 / 0 / 0 / 1 / 0
p1  p2  d1  p3  d2  d3  d4  p4  d5  d6  d7  d8  d9  d10
```

#### Calcul des bits de parité

Pour les bits contrôlés (b5, b6, b9, b10, b13) :

| Position | 2⁰ (p1) | 2¹ (p2) | 2² (p3) | 2³ (p4) | Valeur |
|----------|---------|---------|---------|---------|---------|
| b5       | 1       | 0       | 1       | 0       | 1       |
| b6       | 0       | 1       | 1       | 0       | 1       |
| b9       | 1       | 0       | 0       | 1       | 1       |
| b10      | 0       | 1       | 0       | 1       | 1       |
| b13      | 1       | 0       | 1       | 1       | 1       |
| Résultat | 1       | 0       | 1       | 1       | -       |

**Message final codé** : 10011101110010

#### Vérification
- Les positions des bits de parité (1, 2, 4, 8) contiennent les valeurs calculées
- Les autres positions contiennent les bits du message original
- Le code permet de détecter et corriger une erreur sur un bit

#### 7.2 Exploitation code de Hamming 

**Code reçu** : 1011001000

**Bits de parité** : positions en puissance de 2
- 2⁰ = 1 (p1)
- 2¹ = 2 (p2)
- 2² = 4 (p3)
- 2³ = 8 (p4)


| Position | 2⁰ (p1) | 2¹ (p2) | 2² (p3) | 2³ (p4) | Valeur |
|----------|---------|---------|---------|---------|---------|
| b3       | 1       | 1       | 0       | 0       | 1       |
| b7       | 1       | 1       | 1       | 0       | 1       |
| Résultat | 0       | 0       | 1       | 0       | -       |

Erreur sur le bit 1 

```python 

def dechiff_affine(mess, a, b):
    aInv = pow(a, -1, 31)
    
    t = ""

    for c in mess:
        t += dict_doc[((dict_cod[c]-b)*aInv)%31]

    return t
```

```python
def getRandomGenerator(p):
    while True:
        num = rd.randint(2, p-1)
        
        for i in range(1,p-2): # On doit s'arrêter avant p-1 car il sera forcément égal à 1 congru à p
            if pow(num, i, p) == 1:
                break
        else: 
            return num

```

```python
def log_discret(y, g, p):
    for i in range(1, p):
        if pow(g, i, p) == y%p:
            return i
    
    return None
```




# python_read
