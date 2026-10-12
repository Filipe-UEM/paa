# Revisão para a prova — Quicksort (CLRS4BR, Capítulo 7)

Esta revisão reúne os conceitos centrais do Quicksort, seu funcionamento, sua análise de desempenho, a versão aleatorizada e a comparação com outros algoritmos de ordenação. O foco está em compreender os mecanismos, reconhecer os casos de análise e saber justificar as complexidades em uma prova, sem entrar na demonstração detalhada do tempo esperado do Quicksort aleatorizado.

## 1. Introdução

O Quicksort é um algoritmo de ordenação baseado na estratégia de divisão e conquista (divide-and-conquer). Ele organiza os elementos de um vetor por meio de partições sucessivas em torno de um pivô.

A estratégia possui três etapas:

1. Divisão: particionar o vetor em torno de um pivô.
2. Conquista: ordenar recursivamente as partições resultantes.
3. Combinação: não é necessária uma etapa explícita de combinação, pois as partições ficam organizadas no próprio vetor.

Diferentemente do Merge Sort, que combina subvetores ordenados ao final, o Quicksort realiza a maior parte do trabalho durante a partição.

## 2. Descrição do Quicksort

O algoritmo utiliza dois procedimentos principais: `QUICKSORT` e `PARTITION`.

### 2.1. Pseudocódigo do Quicksort

```
QUICKSORT(A, p, r)
    if p < r
        q = PARTITION(A, p, r)
        QUICKSORT(A, p, q - 1)
        QUICKSORT(A, q + 1, r)
```

Em que:

- `A`: vetor a ser ordenado.
- `p`: índice inicial do subvetor.
- `r`: índice final do subvetor.
- `q`: posição definitiva do pivô após a partição.

A condição `p < r` é o caso-base implícito: se o subvetor tem zero ou um elemento, ele já está ordenado.

### 2.2. Procedimento PARTITION

Na versão apresentada pelo CLRS, o pivô é o último elemento do subvetor, \\(A[r]\\).

```
PARTITION(A, p, r)
    x = A[r]
    i = p - 1
    for j = p to r - 1
        if A[j] <= x
            i = i + 1
            exchange A[i] with A[j]
    exchange A[i + 1] with A[r]
    return i + 1
```

O procedimento percorre os elementos anteriores ao pivô e separa aqueles menores ou iguais a ele dos elementos maiores.

Ao final, o vetor fica dividido em três regiões:

- À esquerda do pivô: elementos menores ou iguais ao pivô.
- Na posição definitiva do pivô: o próprio pivô.
- À direita do pivô: elementos maiores que o pivô.

O pivô não participa das chamadas recursivas seguintes, pois já está em sua posição definitiva.

### 2.3. Exemplo de execução

Considere o vetor:

\\[ A=[4,2,7,3,6] \\]

O pivô inicial é \\(6\\). Após a partição, os elementos menores ou iguais a \\(6\\) ficam à esquerda e os maiores ficam à direita. Uma possível disposição após a partição é:

\\[ [4,2,3,\boxed{6},7] \\]

O pivô ficou na posição correta. O algoritmo agora ordena recursivamente os subvetores \\([4,2,3]\\) e \\([7]\\). O segundo já está ordenado por conter apenas um elemento.

O resultado final é:

\\[ [2,3,4,6,7] \\]

A ordem dos elementos dentro de cada partição pode variar conforme a implementação; o importante é respeitar a relação com o pivô.

## 3. Desempenho do Quicksort

O desempenho depende principalmente de como o pivô divide o vetor.

### 3.1. Melhor caso

O melhor caso ocorre quando as partições ficam aproximadamente equilibradas, dividindo o problema em subproblemas de tamanhos semelhantes.

Para simplificar, suponha que cada chamada divide o vetor em duas metades:

\\[ T(n)=2T(n/2)+\Theta(n) \\]

O termo \\(\Theta(n)\\) representa o custo da partição. Pelo Teorema Mestre:

\\[ \boxed{T(n)=\Theta(n\lg n)} \\]

### 3.2. Pior caso

O pior caso ocorre quando uma partição contém \\(n-1\\) elementos e a outra contém zero elementos. Isso pode acontecer, por exemplo, quando o pivô escolhido é sempre o menor ou o maior elemento.

A recorrência é:

\\[ T(n)=T(n-1)+T(0)+\Theta(n) \\]

Como \\(T(0)=\Theta(1)\\), podemos simplificar para:

\\[ T(n)=T(n-1)+\Theta(n) \\]

Expandindo a recorrência, obtemos uma soma proporcional a:

\\[ 1+2+3+\cdots+n=\Theta(n^2) \\]

Portanto:

\\[ \boxed{T(n)=\Theta(n^2)} \\]

Nesse caso, a árvore de recursão fica semelhante a uma sequência de nós, um abaixo do outro. Sua altura é \\(\Theta(n)\\).

### 3.3. Caso médio

No caso médio, as partições não precisam ser exatamente iguais. Desde que o comportamento médio produza divisões suficientemente equilibradas, o tempo de execução permanece:

\\[ \boxed{T(n)=\Theta(n\lg n)} \\]

É importante distinguir os conceitos:

| Característica        | Partições equilibradas | Pior caso                                                                        |
| --------------------- | ---------------------- | -------------------------------------------------------------------------------- |
| Altura da árvore      | \\(\Theta(\lg n)\\)    | \\(\Theta(n)\\)                                                                  |
| Custo total por nível | \\(\Theta(n)\\)        | Não é \\(\Theta(n)\\) em todos os níveis; diminui conforme o subproblema encolhe |
| Tempo total           | \\(\Theta(n\lg n)\\)   | \\(\Theta(n^2)\\)                                                                |

No pior caso, portanto, não se deve multiplicar simplesmente \\(n\\) níveis por um custo de \\(\Theta(n)\\) em todos eles. O correto é somar os custos das partições, que diminuem de \\(n\\) para \\(n-1\\), depois \\(n-2\\), e assim por diante.

## 4. Versão aleatorizada do Quicksort

O Quicksort tradicional pode escolher sempre o último elemento como pivô. Essa escolha pode ser desfavorável para determinados vetores.

O Quicksort aleatorizado (Randomized Quicksort) escolhe o pivô aleatoriamente entre os elementos do subvetor. No CLRS, isso é feito com o procedimento `RANDOMIZED-PARTITION`.

A ideia é reduzir a dependência entre a ordem inicial dos elementos e a qualidade das partições.

O procedimento funciona assim:

1. Seleciona aleatoriamente um índice entre \\(p\\) e \\(r\\).
2. Troca o elemento selecionado com o último elemento do subvetor.
3. Executa o procedimento `PARTITION` convencional.
4. Continua recursivamente com as partições à esquerda e à direita do pivô.

A aleatorização não garante que todas as partições sejam equilibradas. Ela torna menos provável que a execução apresente repetidamente divisões muito desfavoráveis.

As complexidades principais são:

- Tempo esperado: \\(\Theta(n\lg n)\\).
- Pior caso: \\(\Theta(n^2)\\).

O pior caso continua possível. A vantagem é que o tempo esperado é \\(\Theta(n\lg n)\\), independentemente da ordem inicial dos elementos, considerando as escolhas aleatórias do algoritmo.

## 5. Análise do Quicksort

Para analisar o Quicksort, é necessário observar três elementos: o custo da partição, os tamanhos dos subproblemas e a altura da árvore de recursão.

### 5.1. Custo da partição

O procedimento `PARTITION` percorre o subvetor uma vez, realizando um número de operações proporcional ao número de elementos.

Assim, para um subvetor de tamanho \\(n\\):

\\[ \boxed{\text{PARTITION}=\Theta(n)} \\]

Esse custo é linear, independentemente de a divisão resultante ser equilibrada ou desequilibrada.

### 5.2. Recorrência geral

Se o pivô deixa \\(q\\) elementos à esquerda, então ficam \\(n-q-1\\) elementos à direita. A recorrência geral é:

\\[ T(n)=T(q)+T(n-q-1)+\Theta(n) \\]

Essa expressão permite analisar os diferentes casos de execução.

- Se \\(q\\) for aproximadamente \\(n/2\\), temos divisões equilibradas e tempo \\(\Theta(n\lg n)\\).
- Se \\(q=0\\) ou \\(q=n-1\\) repetidamente, temos divisões extremamente desequilibradas e tempo \\(\Theta(n^2)\\).

### 5.3. Propriedades importantes

O Quicksort é um algoritmo in-place. Ele reorganiza os elementos no próprio vetor e utiliza apenas uma quantidade limitada de memória auxiliar por chamada, além da pilha de recursão.

Entretanto, o espaço ocupado pela pilha depende da altura da recursão: costuma ser \\(O(\lg n)\\) em execuções equilibradas e pode chegar a \\(O(n)\\) no pior caso.

O Quicksort não é estável em sua implementação convencional. Um algoritmo estável preserva a ordem relativa dos elementos com chaves iguais; o Quicksort convencional não oferece essa garantia.

## 6. Visão geral dos algoritmos de ordenação por comparação

Os algoritmos de ordenação por comparação determinam a ordem dos elementos comparando pares de chaves. Quicksort, Merge Sort, Heapsort e Insertion Sort são exemplos.

| Algoritmo      | Melhor caso          | Caso médio                      | Pior caso            | Espaço auxiliar                                                   |
| -------------- | -------------------- | ------------------------------- | -------------------- | ----------------------------------------------------------------- |
| Insertion Sort | \\(\Theta(n)\\)      | \\(\Theta(n^2)\\)               | \\(\Theta(n^2)\\)    | \\(O(1)\\)                                                        |
| Merge Sort     | \\(\Theta(n\lg n)\\) | \\(\Theta(n\lg n)\\)            | \\(\Theta(n\lg n)\\) | \\(O(n)\\)                                                        |
| Heapsort       | \\(\Theta(n\lg n)\\) | \\(\Theta(n\lg n)\\)            | \\(\Theta(n\lg n)\\) | \\(O(1)\\)                                                        |
| Quicksort      | \\(\Theta(n\lg n)\\) | \\(\Theta(n\lg n)\\)            | \\(\Theta(n^2)\\)    | \\(O(\lg n)\\) em execuções equilibradas; \\(O(n)\\) no pior caso |
| Intro Sort     | —                    | Geralmente \\(\Theta(n\lg n)\\) | \\(O(n\lg n)\\)      | Depende da implementação                                          |

O Intro Sort combina Quicksort, Heapsort e, normalmente, Insertion Sort para pequenas partições. Sua principal vantagem é preservar o bom desempenho prático do Quicksort e garantir complexidade \\(O(n\lg n)\\) no pior caso.

### Limite inferior da ordenação por comparação

O modelo de árvore de decisão demonstra que qualquer algoritmo de ordenação baseado exclusivamente em comparações precisa realizar, no pior caso, pelo menos:

\\[ \boxed{\Omega(n\lg n)} \\]

comparações.

Isso significa que Merge Sort e Heapsort atingem assintoticamente esse limite inferior no pior caso. O Quicksort também atinge essa ordem no caso médio, embora seu pior caso seja quadrático.

Esse limite não se aplica da mesma forma a algoritmos que exploram características específicas das chaves, como Counting Sort e Radix Sort.

## 7. Outros itens correlatos

### 7.1. Quicksort com Insertion Sort para pequenas partições

Uma otimização prática consiste em interromper a recursão do Quicksort quando o subvetor fica menor que um limite \\(k\\), ordenando-o com Insertion Sort.

O custo pode ser expresso como:

\\[ O(nk+n\lg(n/k)) \\]

O termo \\(O(nk)\\) corresponde ao custo de ordenar as pequenas partições por Insertion Sort, considerando o pior caso de cada uma. O termo \\(O(n\lg(n/k))\\) representa o custo das partições do Quicksort até atingir subvetores de tamanho menor que \\(k\\), sob a hipótese de divisões equilibradas.

Na teoria, escolher \\(k\\) como uma constante permite manter a complexidade \\(O(n\lg n)\\). Na prática, o melhor valor depende da implementação e do ambiente de execução.

### 7.2. Particionamento pela mediana de três elementos

Outra variação seleciona três elementos aleatoriamente e utiliza a mediana deles como pivô.

Essa estratégia tende a evitar pivôs extremamente pequenos ou grandes, favorecendo partições mais equilibradas. Entretanto, ela não elimina a possibilidade do pior caso.

### 7.3. Propriedades de somas e partições

Em exercícios de análise, pode ser necessário demonstrar que uma expressão como

\\[ q^2+(n-q-1)^2 \\]

atinge seu máximo quando \\(q=0\\) ou \\(q=n-1\\). A ideia é reescrever a expressão como:

\\[ (n-1)^2-2q(n-q-1) \\]

Como \\(0\le q\le n-1\\), o produto \\(q(n-q-1)\\) é não negativo. Logo, a expressão não ultrapassa \\((n-1)^2\\), atingindo esse valor nos extremos.

Esse tipo de resultado ajuda a entender por que partições muito desequilibradas produzem subproblemas grandes e podem levar ao pior caso quadrático.

## 8. O que você deve saber para a prova

Confira se consegue responder sem consultar o material:

Use as perguntas para revisar. Selecione uma para conferir a resposta.

1\. Qual é a estratégia usada pelo Quicksort?

2\. Qual é o custo de PARTITION?

3\. Quando ocorre o pior caso?

4\. Qual é a complexidade do pior caso?

5\. Qual é o tempo esperado do Quicksort aleatorizado?

6\. O Quicksort é estável?

7\. Qual é a principal vantagem do Intro Sort?

8\. Qual é o limite inferior para ordenação por comparação?
