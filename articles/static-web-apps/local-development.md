---
title: Configurar o desenvolvimento local para aplicações web estáticas Azure
description: Aprenda a definir-lhe o seu ambiente de desenvolvimento local para aplicações web estáticas Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: e19d39a32d48ec55473bb957595d47ec5148e74b
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588790"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Configurar o desenvolvimento local para pré-visualização de aplicações web estáticas Azure

Quando publicado na nuvem, um site de Aplicações Web Estáticas Azure tem muitos serviços que funcionam em conjunto como se fossem a mesma aplicação. Estes serviços incluem:

- A aplicação web estática
- AAPI de Funções Azure
- Serviços de autenticação e autorização
- Serviços de encaminhamento e configuração

Estes serviços devem comunicar entre si, e a Azure Static Web Apps lida com esta integração para si na nuvem.

Funcionando localmente, no entanto, estes serviços não estão automaticamente ligados.

Para proporcionar uma experiência semelhante ao que obtém no Azure, o [Azure Static Web Apps CLI](https://github.com/Azure/static-web-apps-cli) fornece os seguintes serviços:

- Um servidor local estático
- Um representante para o servidor de desenvolvimento de enquadramento de frente
- Um proxy para os seus pontos finais da API - disponível através de Ferramentas Principais de Funções Azure
- Um servidor de autenticação e autorização de simulação
- Aplicação de rotas e configurações locais

## <a name="how-it-works"></a>Como funciona

O gráfico que se segue mostra como os pedidos são tratados localmente.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Pedido e fluxo de resposta CLI da App Web Estática Azure":::

> [!IMPORTANT]
> Navegue `http://localhost:4280` para aceder à aplicação servida pelo CLI.

- **Os pedidos** feitos à porta `4280` são reencaminhados para o servidor apropriado, dependendo do tipo de pedido.

- Os pedidos **de conteúdo estático,** tais como HTML ou CSS, são tratados pelo servidor interno de conteúdo estático CLI ou pelo servidor de estrutura frontal para depurar.

- Os pedidos **de autenticação e autorização** são tratados por um emulador, que fornece um perfil de identidade falso à sua aplicação.

- **Funções O tempo de execução das Ferramentas Principais** lida com os pedidos para a API do site.

- **As respostas** de todos os serviços são devolvidas ao navegador como se fossem uma única aplicação.

## <a name="prerequisites"></a>Pré-requisitos

- **Site de Aplicações Web Estáticas Azure existente**: Se não tiver um, comece com a aplicação de arranque [vanilla-api.](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate)
- **[Node.js](https://nodejs.org) com a npm**: Executar a [ versãoNode.js LTS,](https://nodejs.org) que inclui acesso à [npm](https://www.npmjs.com/).
- **[Código de Estúdio Visual](https://code.visualstudio.com/)**: Utilizado para depurar a aplicação API, mas não necessário para o CLI.

## <a name="get-started"></a>Introdução

Abra um terminal para a pasta raiz do seu site de Aplicações Web Estáticas Azure existente.

1. Instale o CLI.

    `npm install -g @azure/static-web-apps-cli`

1. Construa a sua aplicação se necessário pela sua aplicação.

    Corra, `npm run build` ou o comando equivalente para o seu projeto.

1. Mude para o diretório de saída para a sua aplicação. As pastas de saída são frequentemente denominadas _construção_ ou algo similar.

1. Inicie o CLI.

    `swa start`

1. Navegue http://localhost:4280 para ver a aplicação no navegador.

### <a name="other-ways-to-start-the-cli"></a>Outras formas de iniciar o CLI

| Description | Comando |
|--- | --- |
| Sirva uma pasta específica | `swa start ./output-folder` |
| Use um servidor de desenvolvimento de quadros em execução | `swa start http://localhost:3000` |
| Iniciar uma aplicação Funções numa pasta | `swa start ./output-folder --api ./api` |
| Utilize uma aplicação de funções de execução | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Autorização e emulação de autenticação

O Static Web Apps CLI imita o [fluxo de segurança](./authentication-authorization.md) implementado no Azure. Quando um utilizador inicia sessão, pode definir um perfil de identidade falso devolvido à aplicação.

Por exemplo, quando tenta navegar `/.auth/login/github` para, é devolvida uma página que lhe permite definir um perfil de identidade.

> [!NOTE]
> O emulador trabalha com qualquer fornecedor de segurança, não apenas com o GitHub.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Emulador de autenticação e autorização local":::

O emulador fornece uma página que lhe permite fornecer os seguintes valores principais do [cliente:](./user-information.md#client-principal-data)

| Valor | Descrição |
| --- | --- |
| **Nome de Utilizador** | O nome da conta associado ao fornecedor de segurança. Este valor aparece como o `userDetails` imóvel no principal cliente e é autogerido se não fornecer um valor. |
| **ID de Utilizador** | Valor autogerado pelo CLI.  |
| **Funções** | Uma lista de nomes de papéis, onde cada nome está numa nova linha.  |

Uma vez iniciado o login:

- Pode utilizar o `/.auth/me` ponto final ou um ponto final de função para recuperar o principal [cliente](./user-information.md)do utilizador .

- Navegando para `./auth/logout` limpar o principal cliente e regista o utilizador falso.

## <a name="debugging"></a>Depurar

Existem dois contextos de depurar numa aplicação web estática. O primeiro é para o site de conteúdo estático, e o segundo é para funções de API. A depuragem local é possível permitindo que o CLI de Aplicações Web Estáticas utilize servidores de desenvolvimento para um ou ambos os contextos.

Os passos seguintes mostram-lhe um cenário comum que utiliza servidores de desenvolvimento para ambos os contextos de depurações.

1. Inicie o servidor de desenvolvimento do site estático. Este comando é específico da estrutura frontal que está a usar, mas muitas vezes vem sob a forma de comandos como `npm run build` `npm start` , ou `npm run dev` .

1. Abra a pasta de aplicações API no Código do Estúdio Visual e inicie uma sessão de depuragem.

1. Passe os endereços para o servidor estático e o servidor API para o `swa start` comando, listando-os por ordem.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

As imagens que se seguem mostram os terminais para um cenário típico de depuragem:

O site de conteúdo estático está a ser gerido através `npm run dev` de .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Servidor de desenvolvimento de site estático":::

A aplicação Azure Functions API está a decorrer uma sessão de depuração no Código do Estúdio Visual.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Depuragem visual do Código do Estúdio API":::

O CLI estático de aplicações web é lançado usando ambos os servidores de desenvolvimento.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Terminal CLI de aplicações web estáticas Azure":::

Agora os pedidos que passam pela porta `4280` são encaminhados para o servidor de desenvolvimento de conteúdo estático, ou para a sessão de depuragem da API.

Para obter mais informações sobre diferentes cenários de depuração, com orientação sobre como personalizar portas e endereços de servidor, consulte o [repositório CLI das Aplicações Web Estáticas Azure](https://github.com/Azure/static-web-apps-cli).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar a aplicação](configuration.md)
