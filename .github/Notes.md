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
