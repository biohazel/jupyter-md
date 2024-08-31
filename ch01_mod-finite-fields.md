# Fundamentos Matemáticos para a Criptografia do Bitcoin

Para entendermos as decisões de design da tecnologia Bitcoin e efetivamente nos tornarmos programadores fluentes nessa tecnologia, é importante olharmos para as bases matemáticas simples e elegantes que compõem seus algoritmos criptográficos.

Vamos começar com o entendimento da matemática por trás da criptografia de curvas elípticas—ECC (Elliptic Curve Cryptography).

A *aritmética modular* nos ajuda a lidar com situações cíclicas, como os dias da semana, onde os números reiniciam após um certo ponto.

Imagine que hoje é segunda-feira e queremos saber que dia da semana será daqui a 40 dias. Em vez de contar manualmente cada dia, podemos usar aritmética modular para simplificar o problema.

Os dias da semana se repetem a cada 7 dias (segunda, terça, quarta, ..., domingo). Portanto, o nosso módulo é 7. Queremos calcular:

$$
40 \bmod 7 = 5
$$


Esse cálculo nos dá o **resto da divisão de 40 por 7**, que é 5.

Dividindo 40 por 7, temos:

$$
40 \div 7 = 5 \quad \text{(quociente)}
$$

O resto da divisão é:

$$
40 - (7 \times 5) = 40 - 35 = 5 \quad \text{(resto)}
$$

Portanto:

$$
40 \equiv 5 \pmod{7}
$$



Lê-se que 40 é congruente a 5 módulo 7. Isso significa que 40 dias a partir de hoje será o mesmo que 5 dias após hoje. Se hoje é segunda-feira (dia 1 da semana), 5 dias depois será sexta-feira:

Perceba que se fôssemos contar manualmente, consideraríamos o seguinte.  
Se 1 é segunda. Temos que contar 40 dias. Nossa contagem termina no número 5, que equivale a sexta-feira.

$$
1, 2, 3, 4, 5, 6, 0, 1, 2, 3, 4, 5, 6, 0, 1, 2, 3, 4, 5, 6, 0, 1, 2, 3, 4, 5, 6, 0, 1, 2, 3, 4, 5, 6, 0, 1, 2, 3, 4, 5
$$

- $1$—Segunda
- $2$—Terça
- $3$—Quarta
- $4$—Quinta
- $5$—Sexta
- $6$—Sábado
- $0$—Domingo

Assim, 40 dias a partir de uma segunda-feira será sexta-feira.

Veja esta proposição matemática:

$a \equiv b \pmod{n} \iff a \bmod n = b \bmod n$

O mod à esquerda é uma *relação*, e o mod à direita é uma *operação binária*. 

Aqui, vemos que após o número 6, a contagem reinicia em 0. Esta estrutura é um exemplo de aritmética modular, onde o resultado de operações como soma e subtração são moldados dentro de um limite (neste caso, 7). Este conceito é análogo ao que ocorre em campos finitos, como os utilizados nos algoritmos de criptografia de Bitcoin, onde operações são realizadas dentro de um intervalo definido por um número primo, garantindo a ciclicidade e propriedades matemáticas desejadas para segurança criptográfica.

O campo finito $\mathbb{F}_p$ é composto pelos números inteiros de $0$ a $p-1$, onde $p$ é um número primo. 

De maneira análoga, no contexto dos dias da semana, o conjunto das possibilidades das operações modulares vai de $0$ a $6$ (se considerarmos que o módulo é $7$).

Ou seja, o conjunto de valores possíveis para as operações modulares, no caso dos dias da semana, é $(0, 1, 2, 3, 4, 5, 6)$, que é análogo ao conjunto de elementos de um campo finito $\mathbb{F}_p$ onde $p = 7$.

No caso da criptografia do bitcoin, esse p é um número primo muito grande. Veremos especificamente que número é esse mais adiante. Por ora, vamos entender um pouco mais sobre operações modulares. 

Na aritmética modular, em vez de fazermos aritmética sobre inteiros ou racionais, o novo conjunto em que vamos trabalhar é denotado por $\mathbb{Z}_n$, em que $n$ é um número inteiro positivo. Define-se o conjunto $\mathbb{Z}_n$ como:

$\mathbb{Z}_n = \{0, 1, 2, \ldots, n-1\}$

Ou seja, $\mathbb{Z}_n$ contém todos os números naturais de $0$ a $n-1$ inclusive. Chamamos este sistema de *inteiros mod n*.

Agora que aprendemos o conceito e o espaço das operações modulares, vamos aprender sobre as operações que dão o nome á Aritmética Modular. 

# Aritmética Modular

Na aritmética modular, trabalhamos com os restos das divisões por um número $m$ (mod $m$). Quando $m$ é um número primo $p$, o conjunto dos inteiros ${0, 1, \ldots, p-1}$ com as operações de adição e multiplicação módulo $p$ forma um campo finito, conhecido como o campo dos restos módulo $p$ ou campo finito $\mathbb{F}_p$ (também notado como $\mathbb{Z}/p\mathbb{Z}$).

A propriedade de unicidade da fatoração em números primos garante que os cálculos modulares sejam consistentes e bem definidos, o que é um requisito crucial para a segurança criptográfica. Por exemplo, em um campo finito com módulo primo, cada elemento não nulo possui um inverso multiplicativo único. Esta característica é fundamental para operações seguras em criptografia, como as que ocorrem em curvas elípticas.

**Campo Finito**: Quando $m$ é um número primo $p$, o conjunto ${0, 1, \ldots, p-1}$ com as operações de adição e multiplicação módulo $p$ forma um campo finito porque satisfaz todas as propriedades de um campo (fechamento, associatividade, comutatividade, existência de identidade, existência de inversos para adição e multiplicação, e distributividade da multiplicação sobre a adição).

**Notação $\mathbb{Z}/p\mathbb{Z}$ e $\mathbb{F}_p$**: A notação $\mathbb{Z}/p\mathbb{Z}$ é frequentemente usada para descrever o anel dos números inteiros módulo $p$. Quando $p$ é primo, este anel é um campo, e é frequentemente denotado por $\mathbb{F}_p$, que é a notação mais comum em teoria de campos para um campo finito com $p$ elementos.

Vamos entender passo a passo como funcionam as operações modulares e compreender o que significa inverso multiplicativo único para a aritmética modular, essa propriedade tão importante para a segurança criptográfica. 

Para distinguir as operações modulares, considere as seguintes representações:

$$
\oplus, \ominus, \otimes, \oslash
$$

Vamos nos referir a essas operações como **adição mod $n$**, **subtração mod $n$**, **multiplicação mod $n$** e **divisão mod $n$**.

### Adição e Multiplicação Modulares


Sejam $n$ um inteiro positivo e $a, b \in \mathbb{Z}_n$. Definimos:

$$
a \oplus b = (a + b) \mod n \quad \text{e} \quad a \otimes b = (ab) \mod n
$$

As operações à esquerda são operações definidas para $\mathbb{Z}_n$. As operações à direita são operações ordinárias com inteiros.

Seja $n = 10$. Temos o seguinte:

$$
5 \oplus 5 = 0 \quad 9 \oplus 8 = 7
$$

$$
5 \otimes 5 = 5 \quad 9 \otimes 8 = 2
$$

```rust
fn modular_add(a: i32, b: i32, m: i32) -> i32 {
    (a + b) % m
}

fn modular_multiplication(a: i32, b: i32, m: i32) -> i32 {
    (a * b) % m
}

// Example Usage
let result_add = modular_add(40, 5, 7);
println!("Result of modular addition: {}", result_add); // Output: Result of modular addition: 5

let result_mul = modular_multiplication(4, 6, 7);
println!("Result of modular multiplication: {}", result_mul); // Output: Result of modular multiplication: 3
```

Note que os símbolos $\oplus$ e $\otimes$ dependem do contexto. Se estamos trabalhando em $\mathbb{Z}_{10}$, então $5 \oplus 5 = 0$, mas, se estamos trabalhando em $\mathbb{Z}_9$, então $5 \oplus 5 = 1$. Seria melhor criarmos um símbolo mais barroco, como $\oplus_n$, para denotar a adição mod $n$, mas, em quase todas as situações, o valor de $n$ não varia. Simplesmente devemos permanecer atentos ao contexto corrente.

Dado que $a, b \in \mathbb{Z}_n$, os resultados de $a \oplus b$ e $a \otimes b$ estão também em $\mathbb{Z}_n$.

#### Proposição
Sejam $a, b \in \mathbb{Z}_n$. Então, $a \oplus b \in \mathbb{Z}_n$ e $a \otimes b \in \mathbb{Z}_n$. (Fechamento).


#### Proposição
Seja $n$ inteiro com $n \geq 2$.

- Para todos $a, b \in \mathbb{Z}_n$, $a \oplus b = b \oplus a$ e $a \otimes b = b \otimes a$. (Comutativa).
- Para todos $a, b, c \in \mathbb{Z}_n$, $(a \oplus b) \oplus c = a \oplus (b \oplus c)$ e $(a \otimes b) \otimes c = a \otimes (b \otimes c)$. (Associativa).
- Para todos $a \in \mathbb{Z}_n$, $a \oplus 0 = 0 \oplus a = a$ e $a \otimes 0 = 0 \otimes a = 0$. (Elementos identidade).
- Para todos $a, b, c \in \mathbb{Z}_n$, $a \otimes (b \oplus c) = (a \otimes b) \oplus (a \otimes c)$. (Distributiva).

### Subtração Modular

**(Subtração modular)** Seja $n$ um inteiro positivo e sejam $a, b \in \mathbb{Z}_n$. Definimos $a \ominus b$ como o único valor $x \in \mathbb{Z}_n$ de modo que:

$$
a \ominus b = (a - b) \bmod n.
$$

### Divisão Modular

A aritmética modular é fácil. Chegamos agora à parte difícil: a divisão modular é significativamente diferente de quaisquer outras operações modulares. Por exemplo, na aritmética comum dos inteiros, temos as leis do corte (ou do cancelamento). Se $a, b, c$ são inteiros com $a \neq 0$, então

$$
ab = ac \implies b = c
$$

Porém, em $\mathbb{Z}_{10}$,

$$
5 \otimes 2 = 5 \otimes 4 \text{ mas } 2 \neq 4.
$$

A despeito do fato de ser $5 \neq 0$, não podemos cancelá-lo ou dividir ambos os membros por 5. Motivados pela definição de $\ominus$, seria interessante podermos definir $a \oslash b$ como o valor único $x \in \mathbb{Z}_n$ de modo que $a = b \otimes x$. Isso é problemático. Consideremos $6 \oslash 2$ em $\mathbb{Z}_{10}$. Este deveria ser o único $x \in \mathbb{Z}_{10}$ de modo que $2 \otimes x = 6$. Será $x = 3$? Isso seria realmente interessante. E somos encorajados pelo fato de que $2 \otimes 3 = 6$. Entretanto, notemos que $2 \otimes 8 = 6$. Seria o caso de termos $6 \oslash 2 = 8$? O problema é que poderia não haver uma solução única para $6 = 2 \otimes x$.

Tentemos outra abordagem. Em $\mathbb{Q}$, podemos definir $a \div b$ como $a \cdot b^{-1}$ (isto é, a divisão por $b$ se define como a multiplicação pelo inverso dos $b$). Isso explica por que a divisão por $0$ não é definida; zero não tem um inverso. Procuremos ser precisos sobre o que queremos dizer por inverso. O **inverso** de um número racional $x$ é um número racional $y$ de modo que $xy = 1$.

Podemos utilizar esse fato como base para nossa definição de divisão em $\mathbb{Z}_n$. Começamos definindo inversos.

## Inverso modular

Sejam $n$ um inteiro positivo e $a \in \mathbb{Z}_n$. O inverso de $a$ é um elemento $b \in \mathbb{Z}_n$ de modo que $a \otimes b = 1$. Um elemento de $\mathbb{Z}_n$ que tenha um inverso é chamado **inversível**. E se $a$ tem um inverso, esse inverso é único. 

No contexto de $\mathbb{Z}_n$, $a^{-1}$ é o inverso de $a$. Não é ($e$ nunca devemos escrever) $\frac{1}{a}$. Por exemplo, no contexto de $\mathbb{Z}_{10}$, temos $3^{-1} = 7$.

Seja $n$ um inteiro positivo e seja $a \in \mathbb{Z}_n$. Suponhamos que $a$ seja inversível. Se $b = a^{-1}$, então $b$ é inversível e $a = b^{-1}$. Em outras palavras, $(a^{-1})^{-1} = a$.

## Divisão modular

Seja $n$ um inteiro positivo e seja $b$ um elemento inversível de $\mathbb{Z}_n$. Seja $a \in \mathbb{Z}_n$ arbitrário. Então, $a \oslash b$ se define como $a \otimes b^{-1}$.

Notemos que $a \oslash b$ só é definido quando $b$ é inversível; isso é análogo ao fato de que, para números racionais, $a \div b$ só é definido quando $b$ é inversível (isto é, diferente de zero).

Por exemplo: Em $\mathbb{Z}_{10}$, calcule $2 \oslash 7$. Note que $7^{-1} = 3$; assim, $2 \oslash 7 = 2 \otimes 3 = 6$.

### Teorema 
#### Elementos inversíveis de $\mathbb{Z}_n$

Seja $n$ um inteiro positivo e seja $a \in \mathbb{Z}_n$. Então, $a$ é inversível se, e somente se, $a$ e $n$ são relativamente primos.

Um corolário afirma que $a$ e $b$ são relativamente primos se e somente se existe uma solução inteira para $ax + by = 1$. 

Portanto, temos que:

$$
a \otimes b = ab \pmod{n} = 1.
$$

Assim, $b$ é o inverso de $a$ e, por conseguinte, $a$ é um elemento inversível de $\mathbb{Z}_n$.

Sabemos agora que os elementos inversíveis de $\mathbb{Z}_n$ são exatamente aqueles que são relativamente primos com $n$. 

Além disso, a prova do Teorema dos elementos inversíveis nos fornece um método para calcular inversos.

## Algoritmo de Euclides

O **Algoritmo de Euclides** é um método clássico para otimizar o cálculo do máximo divisor comum (mdc) de dois números inteiros. Este algoritmo é fundamental em teoria dos números e na criptografia, especialmente no contexto de curvas elípticas utilizadas no Bitcoin. Vamos explorar como o Algoritmo de Euclides funciona, como ele otimiza cálculos e sua importância nas curvas elípticas.

Trata-se de uma técnica iterativa para encontrar o máximo divisor comum (mdc) de dois números inteiros $a$ e $b$. O mdc de dois números é o maior número que divide ambos sem deixar resto. O algoritmo é baseado na seguinte observação:

$$
\text{mdc}(a, b) = \text{mdc}(b, a \mod b)
$$

onde $a \mod b$ é o resto da divisão de $a$ por $b$. Este processo continua recursivamente até que o segundo número se torne zero. O máximo divisor comum (mdc) é o último número não nulo antes de se alcançar o zero no algoritmo.

#### Otimização de cálculos

O Algoritmo de Euclides é extremamente eficiente porque reduz o problema de encontrar o mdc de dois números para um problema de tamanho menor em cada iteração. Em vez de procurar divisores potenciais até o menor dos dois números, o algoritmo usa uma sequência de divisões, o que reduz drasticamente o número de cálculos necessários.

Vamos considerar um exemplo para entender como o algoritmo funciona na prática:

#### Exemplo de Cálculo com o Algoritmo de Euclides

Vamos calcular o mdc de 252 e 105 usando o Algoritmo de Euclides:

1. Calcule $252 \mod 105$:

$$
252 \div 105 = 2 \quad (\text{quociente}), \quad 252 - 2 \times 105 = 42 \quad (\text{resto}).
$$

Então, temos:

$$
\text{mdc}(252, 105) = \text{mdc}(105, 42).
$$

2. Calcule $105 \mod 42$:

$$
105 \div 42 = 2 \quad (\text{quociente}), \quad 105 - 2 \times 42 = 21 \quad (\text{resto}).
$$

Portanto:

$$
\text{mdc}(105, 42) = \text{mdc}(42, 21).
$$

3. Calcule $42 \mod 21$:

$$
42 \div 21 = 2 \quad (\text{quociente}), \quad 42 - 2 \times 21 = 0 \quad (\text{resto}).
$$

Como o resto é zero, concluímos que:

$$
\text{mdc}(42, 21) = 21.
$$

Logo, o mdc de 252 e 105 é 21.

#### Importância do Algoritmo de Euclides na Criptografia de Curvas Elípticas

Na criptografia de curvas elípticas, como a utilizada pelo Bitcoin, o Algoritmo de Euclides é uma ferramenta essencial para realizar várias operações matemáticas de forma eficiente. Aqui estão alguns motivos pelos quais o Algoritmo de Euclides é crucial:

**Cálculo de Inversos Modulares**: Um passo crítico nas operações de curva elíptica é o cálculo do inverso multiplicativo de um número módulo $n$. O Algoritmo de Euclides Estendido permite calcular esse inverso de forma eficiente, o que é fundamental para a criação e verificação de assinaturas digitais no Bitcoin.
  
**Otimização de Operações**: As operações de assinatura e validação de transações na rede Bitcoin requerem cálculos modulares rápidos e eficientes. O Algoritmo de Euclides minimiza o tempo necessário para essas operações, garantindo que o sistema funcione de maneira rápida e segura.
  
**Segurança Matemática**: O uso de mdc para encontrar inversos multiplicativos e para determinar se dois números são co-primos (relativamente primos) ajuda a garantir a robustez matemática das chaves privadas e públicas no esquema de criptografia de curva elíptica.

## Calculando inversos com o Algoritmo de Euclides Estendido
Seja $a \in \mathbb{Z}_n$ e suponhamos que $\text{mdc}(a, n) = 1$. Então, existem inteiros $x$ e $y$ tais que:

$$
ax + ny = 1.
$$

Para determinarmos os números $x$ e $y$, utilizamos a retrosubstituição no algoritmo de Euclides.

### Exemplo de Uso na Curva Elíptica do Bitcoin

Suponha que queiramos encontrar o inverso modular de $a = 3$ módulo $n = 7$, que é necessário para calcular uma chave privada na curva elíptica. Usamos o Algoritmo de Euclides Estendido:

1. Precisamos encontrar $x$ e $y$ tais que:

$$
3x + 7y = 1.
$$

2. Através da aplicação do Algoritmo de Euclides Estendido, encontramos inteiros que satisfazem a função:

$$
x = 5, \quad y = -2.
$$

Assim, o inverso de 3 módulo 7 é 5, pois:

$$
3 \times 5 \equiv 1 \pmod{7}.
$$

Esse inverso é usado em diversas operações de curvas elípticas, como a geração de chaves e a verificação de assinaturas, tornando o Algoritmo de Euclides indispensável para a segurança e a eficiência do Bitcoin.

```rust // Função para calcular o Máximo Divisor Comum (MDC) usando o Algoritmo de Euclides
fn gcd(mut a: i32, mut b: i32) -> i32 {
    // Enquanto b não for zero, continuamos o processo de divisão
    while b != 0 {
        let temp = b; // Guardamos o valor de b temporariamente
        b = a % b;    // Atualizamos b para o resto da divisão de a por b
        a = temp;     // Atualizamos a para o valor antigo de b
    }
    a // O valor final de a é o MDC
}

// Função para calcular o Algoritmo de Euclides Estendido
fn extended_gcd(a: i32, b: i32) -> (i32, i32, i32) {
    if b == 0 {
        // Caso base: se b for 0, retornamos (a, 1, 0)
        (a, 1, 0)
    } else {
        // Chamamos recursivamente o algoritmo com (b, a % b)
        let (g, x, y) = extended_gcd(b, a % b);
        // Retornamos o MDC, x e y ajustados
        (g, y, x - (a / b) * y)
    }
}

// Exemplo de uso do Algoritmo de Euclides Estendido
let (gcd, x, y) = extended_gcd(3, 7);
println!("MDC: {}, x: {}, y: {}", gcd, x, y); // Saída: MDC: 1, x: 5, y: -2
```


# Campos Finitos




  



















