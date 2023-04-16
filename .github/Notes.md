### Ignite-Shop

Criando o HTML em projeto Next.

- O nome do arquivo deve ser `_document.tsx`
- O proprio next tem suas tags que precisam ser importadas
- { Html, Head, Main, NextScript }
- "crossorigin" ao importar deve se atentar, necessário alterar para camelCase => `crossOrigin`

```ts
import { Html, Head, Main, NextScript } from 'next/document'

export default function Document() {
  return (
    <Html>
      <Head>
        <link rel="preconnect" href="https://fonts.googleapis.com" />
        <link
          rel="preconnect"
          href="https://fonts.gstatic.com"
          crossOrigin="anonymous"
        />
        <link
          href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap"
          rel="stylesheet"
        />
      </Head>
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```

### Renderizar o CSS no servidor

A função `getCssText` do Stitches carrega todo o CSS necessário para que a página possua a estilização durante o SSR

```js
import { getCssText } from '@/styles'

export default function Document() {
  return (
    <Html>
      <Head>
        ...
        <style
          id="stitches"
          dangerouslySetInnerHTML={{ __html: getCssText() }}
        />
      </Head>
    </Html>
  )
}
```

### Style Global

No arquivo `_app.tsx` chamamos a função `globalStyles()`. Ele deve ficar por fora da função do App, para evitar re-render.

```ts
import { globalStyles } from '@/styles/global'
import type { AppProps } from 'next/app'

globalStyles()

export default function App({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />
}
```

```ts
import { globalCss } from '.'

export const globalStyles = globalCss({
  '*': {
    margin: 0,
    padding: 0
  },

  body: {
    '-webkit-font-smoothing': 'antialiased',
    backgroundColor: '$gray900',
    color: '$gray100'
  },

  'body, input, textarea, button': {
    fontFamily: 'Roboto',
    fontWeight: 400
  }
})
```

### Imagens no Nextjs

```js
//Usando o Nativo do Nextjs
      <Image src={logoImg} width={400} alt="" />
  ...
//Usando como se fosse HTML
      <img src={logoImg.src}>
```

### Renderização elementos para todo o site

No arquivo `_app.tsx` é onde colocamos os elementos que desejamos que seja mostrado em todas as pages. (É uma das formas)

```js
export default function App({ Component, pageProps }: AppProps) {
  return (
    <Container>
      <Header>
        <Image src={logoImg} width={400} alt="" />
      </Header>

      <Component {...pageProps} />
    </Container>
  )
}
```

### Chamada da API com getServerSideProps

O resultado da API é renderizado no server

- Expand: propriedade que nos permite obter dados que possuem relacionamentos na API do Stripe. No caso utilizado para o id do price.

```ts
export const getServerSideProps: GetServerSideProps = async () => {
  const response = await stripe.products.list({
    expand: ['data.default_price']
  })

  const products = response.data.map(product => {
    //O price pode está como String ou um objeto de string.
    const price = product.default_price as Stripe.Price

    return {
      id: product.id,
      name: product.name,
      imageUrl: product.images[0],
      price: new Intl.NumberFormat('pt-BR', {
        style: 'currency',
        currency: 'BRL'
      }).format((price.unit_amount as number) / 100)
    }
  })

  return {
    props: {
      products
    }
  }
}
```

#### Renderizando imagens da API.

É necessário passar o dominio no `next.config` para renderizar as imagens da API.

```ts
const nextConfig = {
  reactStrictMode: true,

  images: {
    domains: ['files.stripe.com']
  }
}
```

## 🚀 Diferença de SSG e SSR 🚀

O SSR é executado quando acessamos a página, ou seja, imediatamente ao acessar a página ele é executado e depois dele executar, ele retorna o HTML para o cliente.

Mas agora, requisições que tem autenticação você sempre vai usar SSR ou o lado do cliente para autenticar o usuário, e nunca o SSG.

Isso pois o SSG gera páginas estáticas a partir da primeira pessoa que acessar a página, ou seja, se você gerar uma página estática que depende de autenticação você poderia gerar uma página com dados de outro usuário para vários usuários ou até por exemplo caso ele esteja deslogado, gerar uma página que não tem as informações do usuário, e isso é bem problemático.

# PRODUTO E & CHECKOUT

### Navegação no Next

Para navegação entre as páginas usamos a tag `Link`

### Fallback

O fallback: true, o que o NextJS faz é renderizar uma página com dados vazios, que vai servir de fallback.
Com isso, você pode por exemplo programar um estado de loading ou algo do tipo do lado do cliente. Enquanto essa página é mostrada de fallback, o next-js vai usar o servidor para gerar a página estáticamente, e assim que ela estiver pronta, mostrar para o cliente.

Então no final, vai ser SSG, mas você apenas não está gerando a página estática durante o build, e sim quando um usuário requisitar.

O fallback: 'blocking', mostra uma tela vazia durante o carregamento da página.

```ts
const { isFallback } = useRouter()

if (isFallback) {
  return <p>Loading...</p>
}
```

### Prefetch de links

Intersection Observer - quando a page tem algum link, o app faz um precarregamento daquela page, o prefetch. Por padrão o Nextjs tem o `prefetch:true`;

### Trabalhando Com SEO
```js
//O Head dar nome nas navegações no google

<Head>
  <title>Compra efetuada | Ignite Shop</title>

  <meta name="robots" content="noindex" />
  //Faz com que a página não seja indexada pelo google
</Head>
```
