# Doom Fire Algorithm

Este projeto simula um efeito visual inspirado no algoritmo de fogo do Doom, utilizando **JavaScript**, **HTML** e **Canvas**.

A ideia principal é representar o fogo como uma matriz de pixels. Cada pixel possui um valor de intensidade, e essa intensidade é convertida em uma cor durante a renderização.

No projeto atual, a simulação é exibida em um canvas de `40x40` pixels, ampliado visualmente para `400x400` pixels com `image-rendering: crisp-edges`, mantendo o aspecto de pixel art.

## Como o projeto funciona

A lógica principal pode ser dividida em três partes:

1. Estrutura de dados
2. Renderização no Canvas
3. Algoritmo de propagação do fogo

## Estrutura de dados

A base do projeto é um array chamado `arr`, criado pela função `createDataStructure()`.

Mesmo que visualmente o fogo seja exibido como uma matriz, internamente os dados são armazenados em um array contínuo com `fireWidth * fireHeight` posições.

```js
const fireWidth = 40;
const fireHeight = 40;
const len = fireWidth * fireHeight;
```

Cada posição desse array representa um pixel da tela e armazena um valor de intensidade do fogo.

Quanto maior o valor armazenado em uma posição, mais intensa será a cor daquele pixel. Quanto menor o valor, mais fraco será o fogo naquele ponto.

No projeto, os valores de intensidade são usados como índice da paleta `original_palette`.

```js
const color = original_palette[arr[pixelIndex]];
```

## Paleta de cores

A paleta `original_palette` contém as cores usadas para desenhar cada nível de intensidade.

Cada item da paleta possui valores `r`, `g` e `b`, que são aplicados no canvas por meio de `fillStyle`.

```js
ctx.fillStyle = `rgb(${color.r}, ${color.g}, ${color.b})`;
```

O valor armazenado no array define qual cor será usada. Por exemplo, uma intensidade `0` usa a primeira cor da paleta, enquanto uma intensidade `36` usa a última cor, que representa o ponto de maior intensidade.

## Renderização com Canvas

A renderização é feita pela função `renderFire()` utilizando o elemento `<canvas>`.

```html
<canvas id="screen" width="40" height="40"></canvas>
```

A função percorre o array de pixels e converte cada índice para uma posição visual no canvas.

```js
const pixelIndex = j + i * fireWidth;
```

Depois disso, a cor correspondente à intensidade daquele pixel é buscada na paleta e desenhada com `fillRect()`.

```js
ctx.fillRect(j, i, 1, 1);
```

Dessa forma, o array funciona como o estado interno da simulação, enquanto o canvas é responsável apenas por exibir esse estado visualmente.

A função também usa `requestAnimationFrame(renderFire)` para continuar redesenhando a tela de forma contínua.

## Inicialização da base do fogo

Para que o fogo exista, é necessário criar uma fonte de calor na parte inferior da matriz.

Isso é feito pela função `createFireSource()`.

```js
function createFireSource() {
    for (let col = 0; col < fireWidth; col++) {
        const pixelIndex = len - fireWidth + col;
        arr[pixelIndex] = 36;
    }
}
```

Essa função percorre a última linha do array e define todos os seus pixels com intensidade máxima, `36`.

Esses pixels funcionam como a fonte do fogo. A partir dessa base, o algoritmo de propagação espalha a intensidade para os pixels acima.

## Propagação do fogo

A função mais importante do projeto é `calculateFirePropagation()`.

Ela é responsável por criar o aspecto visual de movimento do fogo.

A ideia principal é pegar o valor de intensidade de um pixel localizado abaixo e propagá-lo para uma posição acima, aplicando um decaimento aleatório.

```js
const decay = Math.floor(Math.random() * 3);
const nextPixelIndex = i + fireWidth;
const nextPixelFireIntensity = arr[nextPixelIndex];
const newFireIntensity = nextPixelFireIntensity - decay;
```

De forma simplificada:

```txt
novoValor = valorDoPixelAbaixo - decay
```

O valor `decay` é aleatório e pode variar entre `0` e `2`.

Esse decaimento reduz a intensidade do fogo conforme ele sobe pela matriz, criando o efeito de variação típico de uma chama.

Além disso, o projeto escreve o novo valor em `arr[i - decay]`.

```js
arr[i - decay] = newFireIntensity > 0 ? newFireIntensity : 0;
```

Esse pequeno deslocamento horizontal baseado no decaimento faz com que o fogo não suba de forma perfeitamente reta, deixando o movimento mais orgânico.

A propagação é executada continuamente com `setInterval()` a cada `50ms`.

```js
setInterval(calculateFirePropagation, 50);
```

## Como executar o projeto

Para executar o projeto, basta servir o arquivo `index.html`.

Uma forma simples de fazer isso é utilizando o Vite com `npx`.

No terminal, dentro da pasta do projeto, execute:

```bash
npx vite
```

Depois, acesse no navegador o endereço exibido pelo terminal, geralmente:

```txt
http://localhost:5173
```

O Vite irá servir o projeto localmente, permitindo visualizar o `index.html` e executar o JavaScript corretamente.

## Resumo da lógica

O projeto funciona da seguinte forma:

- Um array representa todos os pixels da tela.
- Cada posição do array armazena uma intensidade de fogo.
- A paleta `original_palette` converte intensidades em cores.
- O canvas renderiza o array visualmente como uma matriz de pixels.
- A última linha da matriz é inicializada com intensidade máxima.
- A função `calculateFirePropagation()` propaga o fogo para cima.
- Um decaimento aleatório é aplicado em cada propagação.
- O deslocamento causado pelo `decay` cria variação horizontal.
- Esse processo gera o efeito visual de fogo em pixel art.
