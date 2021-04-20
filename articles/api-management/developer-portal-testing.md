---
title: Teste o portal de desenvolvimento auto-hospedado
titleSuffix: Azure API Management
description: Saiba como configurar testes de unidade e testes de ponta a ponta para o seu portal de Gestão de API auto-hospedado.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ab6a7aa8fc4f11c34126415379294ef1e48fa286
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741886"
---
# <a name="test-the-self-hosted-developer-portal"></a>Teste o portal de desenvolvimento auto-hospedado

Este artigo explica como configurar testes de unidade e testes de ponta a ponta para o seu [portal auto-hospedado](developer-portal-self-host.md).

## <a name="unit-tests"></a>Testes de unidade

Um teste de unidade é uma abordagem para validar pequenas peças de funcionalidade. É feito isoladamente de outras partes da aplicação.

### <a name="example-scenario"></a>Cenário de exemplo

Neste cenário, está a testar um controlo de entrada de password. Só aceita palavras-passe que contenham, pelo menos:

- Uma letra

- Um número

- Um personagem especial
 
Então, o teste para validar estes requisitos é assim:

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>Estrutura do projeto

É comum manter um teste de unidade ao lado do componente que é suposto validar.

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>Pedidos DE SIMULAÇÃO HTTP

Existem casos em que se espera que um componente faça pedidos HTTP. O componente deve reagir adequadamente a diferentes tipos de respostas. Para simular respostas HTTP específicas, `MockHttpClient` utilize. Implementa a `HttpClient` interface utilizada por muitos outros componentes do projeto.

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>Testes de ponta a ponta

Um teste de ponta a ponta executa um cenário de utilizador específico, tomando medidas exatas que espera que o utilizador realize. Numa aplicação web como o portal de desenvolvimento da API Management te Azure, o utilizador percorre o conteúdo e seleciona opções para obter determinados resultados. 

Para replicar a navegação do utilizador, pode utilizar bibliotecas de ajuda de manipulação de navegador como [o Puppeteer.](https://github.com/puppeteer/puppeteer) Permite simular as ações do utilizador e automatizar cenários assumidos. O "Puppeteer" também tira automaticamente imagens de páginas ou componentes em qualquer fase do teste. Compare-os mais tarde com resultados anteriores para capturar desvios e potenciais regressões.

### <a name="example-scenario"></a>Cenário de exemplo

Neste cenário, é necessário validar o fluxo de entrada de um utilizador. Este cenário exigiria os seguintes passos:

1. Abra o navegador e navegue para a página de iniciar sê-in.

1. Insira o endereço de e-mail.

1. Insira a senha.

1. Selecione **Iniciar s-in**.

1. Verifique se o utilizador foi redirecionado para a página inicial.

1. Verifique se a página inclui o item do menu **do perfil.** É um dos possíveis indicadores em que assinou com sucesso.

Para executar o teste automaticamente, crie um script com exatamente os mesmos passos:

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> Cordas como "#email", "#password" e "#signin" são selecionadores semelhantes a CSS que identificam elementos HTML na página. Consulte a especificação [do Nível 3](https://www.w3.org/TR/selectors-3/) W3C dos Selectors para saber mais.

### <a name="ui-component-maps"></a>Mapas de componentes de UI

Os fluxos de utilizador geralmente passam pelas mesmas páginas ou componentes. Um bom exemplo é o menu principal do site que está presente em cada página. 

Crie um mapa de componentes de UI para evitar configurar e atualizar os mesmos seletores para cada teste. Por exemplo, pode substituir os passos 2 a 6 no exemplo anterior por apenas duas linhas:

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>Configuração do teste

Certos cenários podem requerer dados ou configurações pré-criados. Por exemplo, poderá ter de automatizar o sismo de utilização com contas nas redes sociais. É difícil criar esses dados de forma rápida ou fácil.

Para o efeito, pode adicionar um ficheiro de configuração especial ao seu cenário de teste. Os scripts de teste podem recolher os dados necessários do ficheiro. Dependendo do oleoduto de construção e teste, os testes podem extrair os segredos de uma loja segura chamada.

Aqui está um exemplo de um `validate.config.json` que seria armazenado na pasta do seu `src` projeto.

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>Testes sem cabeça vs normais

Navegadores modernos como o Chrome ou Microsoft Edge permitem executar automação tanto em modo sem cabeça como em modo normal. O navegador funciona sem uma interface gráfica do utilizador em modo sem cabeça. Ainda realiza manipulações da mesma página e do Modelo de Objetos de Documento (DOM). A UI do navegador normalmente não é necessária em oleodutos de entrega. Nesse caso, fazer testes em modo sem cabeça é uma ótima opção.

Quando desenvolve um script de teste, é útil ver o que está exatamente acontecendo no navegador. É uma boa altura para usar o modo normal.

Para alternar entre os modos, altere a opção de `headless` opção no `constants.ts` ficheiro. Está na pasta do `tests` seu projeto:

```typescript
export const LaunchOptions = {
    headless: false
};
```

Outra opção útil `slowMo` é. Interrompe a execução do teste entre cada ação:

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>Testes de execução

Existem duas formas incorporadas de executar testes neste projeto:

**comando npm**

```console
npm run test
```

**Explorador de Testes**

A extensão do Test Explorer para o Código VS (por exemplo, [Mocha Test Explorer)](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)tem uma UI conveniente e uma opção para executar testes automaticamente em cada alteração do código fonte:

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="Screenshot do Explorador de Teste de Código de Estúdio Visual":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o portal de desenvolvedores:

- [Descrição geral do portal do programador da Gestão de API do Azure](api-management-howto-developer-portal.md)

- [Auto-anfitrião do portal de desenvolvedores](developer-portal-self-host.md)
