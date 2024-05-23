Problema da Mochila Binária (0/1 Knapsack)
======

Objetivos
---------

Ao final desta aula, você deveria ser capaz de:

1. Entender o conceito do problema da mochila binária.
2. Aplicar métodos de programação dinâmica para resolver o problema.
3. Analisar a complexidade de tempo e espaço das soluções.

Vamos ao que interessa: o que é o problema da mochila binária?
---------

Imagine que você vai fazer uma viagem com a sua família. Para isso, você precisa fazer sua mala, mas para caber no carro, seus pais te pedem para colocar somente o essencial dentro da mala. Assim, sabendo que a capacidade máxima que sua mala aguenta é de 15kg, você seleciona o mais importante para a viagem (considerando que cada item tem seu valor de acordo com sua importância - por exemplo um laptop tem muito mais valor do que uma caneta) e pé na estrada!

Isso que acontece com o `problema problema da mochila binária`: ele surge com a necessidade de empacotar um conjunto de itens com valores e tamanhos específicos (como pesos ou volumes) em um contêiner com capacidade máxima. Com isso, o objetivo dele é escolher um subconjunto dos itens de valor total máximo que cabem no contêiner. Difícil? Observe na imagem abaixo uma representação desse problema:

![](knapsack.png)

???
Para ver se você está entendendo, considere uma mochila com **capacidade de 6 kg**, e a seguinte lista de itens, cada um com seu respectivo valor (esse é só pra ver se você está acordado):

|Item    | Peso     | Valor     |
|--------|----------|-----------|
| 1      | 2      | 10       |
| 2      | 1      | 5        |
| 3      | 4      | 8        |
| 4      | 6      | 15       |
| 5      | 3      | 7        |


Quais são os itens selecionados para compor a mochila para atingir o valor máximo considerando que o peso não pode passar de 6 kg?

::: Resposta:

* Os itens selecionados são: `1`, `2` e `5`.
* A soma dos valores dessa seleção é: `22`.
* O peso total dessa seleção é: ` 6 kg`.

:::
???

Moleza, não? Se você é curioso e tentou resolver o problema da imagem lá em cima, deve ter percebido que o objeto de maior valor é leve, então é incluido na solução. Por outro lado, no exercício, o objeto de maior valor é pesado e não é incluido na resolução, já que existe uma combinação melhor de itens. Então, nem sempre o objeto de maior valor vão ser incluidos: temos que olhar para peso e valor conjuntamente. Pena que agora complica um pouco... Mas, vamos entender um algoritmo que vai fazer isso por nós.

Um jeito simples, mas demorado (Força Bruta)
---------

Como o próprio nome diz, vamos usar mais força e menos razão. Como fariamos para isso? Simplesmente teriamos que verificar todas as combinações possíveis de itens, verificando quais caberiam na mochila e teriam a maior soma de valores.

Esse algoritmo ficaria mais ou menos assim:

```
função mochila_binaria_forca_bruta(items, capacidade):
    melhor_valor = 0
    melhor_combinação = null
    para cada combinação de itens:
        peso_total = soma dos pesos dos itens na combinação
        valor_total = soma dos valores dos itens na combinação
        se peso_total <= capacidade e valor_total > melhor_valor:
            melhor_valor = valor_total
            melhor_combinação = combinação
    retorna melhor_valor, melhor_combinação
```

Pense nisso com um conjunto de dados grande. Você já deve estar imaginando que esse algoritmo exige muito poder computacional, e é inviável nessa situação.

??? Para estimular o cérebro:
Você saberia dizer qual a complexidade desse código?

Dica: Temos n itens e cada item só tem 2 opções: pode ser pego ou não.

::: Resposta:
O(2ˆn)
:::

???

Um jeito melhor (nosso foco) - Programação Dinâmica
---------

**Programação dinâmica, se lembra dela?**

Vocês devem se lembrar que utilizamos um algoritmo de programação dinâmica para resolver um problema de sequenciamento de DNA na APS 3. Pense um pouquinho em como funcionava...

Pensou? Bom, básicamente o problema da APS podia ser subdividido em problemas menores, mais simples e mais fáceis de se resolver. Assim, os próximos problemas, mais complexos, dependiam das soluções anteriores, que eram armazenadas em uma tabela e, sempre que necessário, eram acessadas, sem precisar ficar recalculando soluções. Dito isso, vamos pensar no nosso problema da Mochila Binária e em como esse algoritmo pode se aplicar.

**Subproblemas menores... não sei se entendi isso muito bem**

Calma, colega! Estou aqui para te ajudar. Vamos passo a passo.

Para resolver esse problema, vamos decompor em subproblemas menores. Você pode pensar que selecionaremos subconjuntos em mochilas menores e que vamos armazenar esses resultados. É evidente que, se diminuirmos a capacidade da mochila e o número de itens, será mais fácil escolher qual ou quais itens entrarão na solução. A ideia aqui é que a melhor solução para esse problema completo depende das melhores soluções para os subproblemas, que são mais fáceis de resolver.

Vamos voltar ao exemplo que você fez acima para entender como é construído o raciocínio. Lembrando que são **5 itens** e a capacidade da mochila é **6 kg** (itens abaixo):

|Item    | Peso     | Valor     |
|--------|----------|-----------|
| **1**  | 2      | 10       |
| **2**  | 1      | 5        |
| **3**  | 4      | 8        |
| **4**  | 6      | 15       |
| **5**  | 3      | 7        |


??? Checkpoint:
Considerando o exemplo, vamos pensar no primeiro subproblema.

a) Você consegue dizer qual é o subproblema mais simples? Como subproblema queremos dizer um pedaço do problema grande. Tendo em vista o exemplo anterior, um possível subproblema seria considerar qual o valor máximo que se consegue atingir utilizando somente os itens 1 e 2, sem passar da capacidade.

Dica: considere a maior redução possível na capacidade da mochila e no número de itens. O problema deve ficar extremamente simples!

b) Considerando esse subproblema, quantos e quais itens você levaria na mochila? (Se você não entendeu o item a, pode dar uma espiada em baixo, mas tente não ver a b)

::: Resposta:

a) O subproblema mais simples tem:
* Capacidade máxima da mochila = 0.
* Número de itens disponíveis = 0.

b) Aí o problema ficou simples demais... Se conseguiu fazer o item a, conseguiu o b também. Nesse caso a melhor solução não leva nenhum item. Guarde isso na memória!

:::

???

Concluindo a partir do exercício, quando a capacidade da mochila é 0, você não pode levar nenhum item. E quando o número de itens disponíveis é zero... bom, já sabe né? Vá comprar suas coisas para a viagem.

Vamos, agora, para um segundo subproblema, um pouco mais complexo. 


??? Checkpoint:
Considere somente o primerio item como disponível:

|Item    | Peso     | Valor     |
|--------|----------|-----------|
| **1**  | 2        | 10        |

Incrementando em 1 kg a capacidade da mochila de 0 à capacidade da máxima (6), a partir de qual capacidade seria possível inserir esse item?

::: Resposta:

O item pode ser inserido quando `a capacidade >= peso do item (2)`, ou seja, a partir da capacidade de 2kg. Guarde novamente na memória!

:::

???

Está com a memória boa? Lembra que falei que as próximas soluções vão depender das anteriores? Bom, vamos montar uma tabela que contenha as soluções anteriores. Essa eu vou dar de mão beijada, mas nas próximas você terá que fazer.

![](tabela1exemplo.png)


Na tabela a primeira linha se refere ao peso, podendo variar de 0 até a capacidade máxima, que no nosso caso seria 6, e na primeira coluna temos a quantidade de itens ordenados disponíveis.

Vamos avançar um pouco mais agora.


??? Checkpoint:
Considere tambem o segundo item como disponível:

|Item    | Peso     | Valor     |
|--------|----------|-----------|
| **1**  | 2        | 10        |
| **2**  | 1        | 5         |

Você consegue montar uma nova tabela considerando o 2º item? faça isso!

::: Resposta:

![](tabela2exemplo.png)

Veja que a partir do número 3 você pode colocar os dois itens diponíveis, já que a soma de seus pesos resulta exatamente em 3kg.

:::

???

Agora que ja entendemos o raciocínio por trás de cada subproblema, basta irmos adicionando um novo item até que todos sejam considerados no problema! fácil não?

??? Checkpoint:
Considere todos os items do problema disponíveis:

|Item    | Peso     | Valor     |
|--------|----------|-----------|
| **1**  | 2      | 10       |
| **2**  | 1      | 5        |
| **3**  | 4      | 8        |
| **4**  | 6      | 15       |
| **5**  | 3      | 7        |

Monte como ficaria a tabela dos maiores valores possíveis para cada situação.
  
::: Resposta:

:montagem

:::

???

Transformando em código
---------

Vamos transformar agora toda essa lógica que vimos em código. Lembremos de algumas coisas:
* A função `mochila_binaria()` deve receber como argumentos:
    * A capacidade máxima da mochila (`int capacidade`)
    * Uma lista com os pesos de cada item (`int pesos[]`)
    * Uma lista com os valores de cada item (`int valores[]`)
        * Lembre-se que o mesmo item terá o mesmo índice nas listas de pesos e valores
    * O número total de itens (`int n`)

* A função `mochila_binaria()` deve imprimir:
    * A soma dos valores dos itens incluidos
    * Uma lista dos índices dos itens selecionados


??? Checkpoint:
Considere a seguinte função a ser preenchida:

``` c
void mochila_binaria(int capacidade, int pesos[], int valores[], int n) {
    // Vamos preencher isso aqui!
}
```
Crie a matriz que será preenchida com a solução de cada subproblema

::: Resposta:

``` c
void mochila_binaria(int capacidade, int pesos[], int valores[], int n) {

    int K[n+1][capacidade+1];

    // Vamos preencher isso aqui!
}
```

Lembre-se que temos que incluir mais 1 para termos espaço de contablizar desde o 0.

:::

???

??? Checkpoint:
Considere a seguinte função a ser preenchida:

``` c
void mochila_binaria(int capacidade, int pesos[], int valores[], int n) {

    int K[n+1][capacidade+1];

    // Vamos preencher isso aqui!
}
```
Agora percorra a matriz para podermos resolver cada subproblema em seu devido lugar como vimos.

Dica: Preenchemos cada linha inteira antes de pular para a próxima.

::: Resposta:

``` c
void mochila_binaria(int capacidade, int pesos[], int valores[], int n) {
    int i, w;
    int K[n+1][capacidade+1];

    for (i = 0; i <= n; i++) {
        for (w = 0; w <= capacidade; w++) {
        }
    }
}
```

:::

???

??? Checkpoint:
Considere a seguinte função a ser preenchida:

``` c
void mochila_binaria(int capacidade, int pesos[], int valores[], int n) {
    int i, w;
    int K[n+1][capacidade+1];

    for (i = 0; i <= n; i++) {
        for (w = 0; w <= capacidade; w++) {
        }
    }
}
```
Vamos complicar um pouco mais agora, criando a lógica para encontrar o melhor valor para cada subproblema. Antes disso, vamos relembrar alguns pontos do raciocínio de resolução. O primeiro é que se não tivermos nenhum item ou se o peso for zero, o valor do subproblema será, obviamente, zero. O segundo é que se o peso do item for maior que o peso disponível, o valor do subproblema será igual ao valor do número superior da matriz. A terceira é que, como vimos, para resolver o subproblema, devemos encontrar o maior valor entre o número acima na matriz (que não considera o item em questão) ou o valor do item mais o valor máximo do que sobrou da capacidade de peso considerando o peso desse item.

Dica: Você pode criar uma função auxiliar para ajudar a escolher o máximo entre dois valores.

::: Resposta:

``` c
int max(int a, int b) {
    return (a > b) ? a : b;
}


void mochila_binaria(int capacidade, int pesos[], int valores[], int n) {
    int i, w;
    int K[n+1][capacidade+1];

    for (i = 0; i <= n; i++) {
        for (w = 0; w <= capacidade; w++) {
            if (i == 0 || w == 0)
                K[i][w] = 0;
            else if (pesos[i-1] <= w)
                K[i][w] = max(valores[i-1] + K[i-1][w-pesos[i-1]], K[i-1][w]);
            else
                K[i][w] = K[i-1][w];
        }
    }
}
```

:::

???

??? Checkpoint:
Considere a seguinte função a ser preenchida:

``` c
int max(int a, int b) {
    return (a > b) ? a : b;
}


void mochila_binaria(int capacidade, int pesos[], int valores[], int n) {
    int i, w;
    int K[n+1][capacidade+1];

    for (i = 0; i <= n; i++) {
        for (w = 0; w <= capacidade; w++) {
            if (i == 0 || w == 0)
                K[i][w] = 0;
            else if (pesos[i-1] <= w)
                K[i][w] = max(valores[i-1] + K[i-1][w-pesos[i-1]], K[i-1][w]);
            else
                K[i][w] = K[i-1][w];
        }
    }
}
```

Já temos a matriz preenchida e, consequentemente, também já temos o valor máximo que podemos atingir. Mas como conseguimos os índices dos itens selecionados? É simples! Basta checarmos, a partir do valor máximo, se ele é diferente do valor acima na matriz. Se for igual, isso significa que o item da linha não foi utilizado. Tendo isso em vista, implemente agora a lógica para sabermos quais itens foram escolhidos. Aproveite para também imprimir já a soma dos valores dos itens escolhidos.

::: Resposta:

``` c
int max(int a, int b) {
    return (a > b) ? a : b;
}


void mochila_binaria(int capacidade, int pesos[], int valores[], int n) {
    int i, w;
    int K[n+1][capacidade+1];

    for (i = 0; i <= n; i++) {
        for (w = 0; w <= capacidade; w++) {
            if (i == 0 || w == 0)
                K[i][w] = 0;
            else if (pesos[i-1] <= w)
                K[i][w] = max(valores[i-1] + K[i-1][w-pesos[i-1]], K[i-1][w]);
            else
                K[i][w] = K[i-1][w];
        }
    }

    int result = K[n][capacidade];
    printf("Soma dos valores dos itens incluídos: %d\n", result);

    int a = capacidade;
    printf("Índices dos itens selecionados: ");
    for (i = n; i > 0 && result > 0; i--) {
        if (result == K[i-1][a])
            continue;
        else {
            printf("%d ", i-1);
            result -= valores[i-1];
            a -= pesos[i-1];
        }
    }
}
```

:::

???


Código feito! Esse algoritmo é muito mais eficiente do que o da força bruta. Já usamos a programação dinâmica em uma APS!

??? Vamos lá, agora um pouco mais complexo.
Tente entender qual a complexidade do pseudocódigo acima.

Dica: O que vamos preencher para armazenar os valores?

::: Resposta:
Se você pensou em O(nXw), sendo n o número de itens e w a capacidade, você está correto. É um algotirmo bem melhor.
:::

???

FIM