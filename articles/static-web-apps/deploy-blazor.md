---
title: 'Tutorial: Construção de uma aplicação web estática com Blazor em Azure Static Web Apps'
description: Aprenda a construir um website Azure Static Web Apps com Blazor.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 5b8a94409f082177192847f2c65d44a513ecdbe4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305043"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>Tutorial: Construção de uma aplicação web estática com Blazor em Azure Static Web Apps

A Azure Static Web Apps publica um site para um ambiente de produção construindo aplicações a partir de um repositório GitHub. Neste tutorial, implementa uma aplicação web para aplicações Web Azure Static utilizando o portal Azure.

Se não tiver uma subscrição do Azure, [crie uma conta de teste gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- Conta do [GitHub](https://github.com)
- [Conta Azure](https://portal.azure.com)

## <a name="application-overview"></a>Descrição geral da aplicação

As Aplicações Web Estáticas Azure permitem criar aplicações web estáticas suportadas por um backend sem servidor. O seguinte tutorial demonstra como implementar a aplicação web C# Blazor que devolve dados meteorológicos.

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="App Completa blazor":::

A aplicação apresentada neste tutorial é composta por três projetos diferentes do Estúdio Visual:

- **Api**: A aplicação C# Azure Functions que implementa o ponto final da API que fornece informações meteorológicas para a aplicação estática. O **WeatherForecastFunction** devolve uma série de `WeatherForecast` objetos.

- **Cliente**: O projeto de montagem web blazor frontal. É implementada [uma rota de retorno](#fallback-route) para garantir que todas as rotas são servidasindex.htmficheiro _l._

- **Compartilhado**: Detém classes comuns referenciadas pelos projetos Api e Cliente que permitem que os dados fluam do ponto final da API para a aplicação web frontal. A [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs) aula é partilhada entre ambas as aplicações.

Juntos, estes projetos compõem as peças necessárias para criar uma aplicação de montagem web Blazor em execução no navegador suportado por um backend API.

## <a name="fallback-route"></a>Rota do recuo

A aplicação expõe URLs como _/counter_ e _/fetchdata_ que mapeiam para rotas específicas da aplicação. Uma vez que esta aplicação é implementada como uma aplicação de uma única página, cada rota é servidaindex.htmficheiro _l._ Para garantir que o pedido de retorno de caminho _index.html_ é implementada uma rota [de retorno](./routes.md#fallback-routes) nastaticwebapp.config.js _em_ ficheiro encontrado na pasta _wwwroot_ do projeto Cliente.

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

A configuração acima garante que os pedidos para qualquer rota na aplicação devolve a _página deindex.html._

## <a name="create-a-repository"></a>Criar um repositório

Este artigo usa um repositório de modelos GitHub para facilitar o seu início. O modelo apresenta uma aplicação inicial implantada para Azure Static Web Apps.

1. Certifique-se de que está inscrito no GitHub e navegue para o seguinte local para criar um novo repositório:
   - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. Nomeie o seu repositório **my-first-static-blazor-app**

## <a name="create-a-static-web-app"></a>Criar uma aplicação Web estática

Agora que o repositório é criado, crie uma aplicação web estática a partir do portal Azure.

1. Navegue para o [portal do Azure](https://portal.azure.com)
1. Selecione **Criar um recurso**
1. Procure **Aplicações Web Estáticas**
1. Selecione **aplicativos web estáticos (pré-visualização)**
1. Selecione **Criar**

Na secção _Basics,_ comece por configurar a sua nova app e ligá-la a um repositório GitHub.

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="Separador básico":::

1. Selecione a sua _subscrição Azure_
1. Selecione ou crie um novo _Grupo de Recursos_
1. Nomeie a aplicação **my-first-static-blazor-app**
   - Os carateres válidos são `a-z` (não sensível a maiúsculas e minúsculas), `0-9` e `-`.
1. Selecione uma _região_ mais próxima
1. Selecione o _SKU_ **gratuito**
1. Selecione o **'Iniciar s-in' com** o botão GitHub e autentica-se com o GitHub

Depois de assinar com o GitHub, insira a informação do repositório.

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="Detalhes do repositório":::

1. Selecione a sua _Organização_ Preferida
1. Selecione **a minha primeira-estática-blazor-app** a partir do _repositório_ drop-down
1. Selecione **principal** a partir do _drop-down branch_

   Se não vir nenhum repositório, poderá ter de autorizar aplicações web estáticas Azure no GitHub. Navegue no seu repositório GitHub e vá a **Definições > Aplicações > Aplicações OAuth Autorizadas**, selecione **Azure Static Web Apps**, e, em seguida, selecione **Grant**. Para repositórios de organização, você deve ser um proprietário da organização para conceder as permissões.

1. Na secção Detalhes do _Construção,_ adicione detalhes de configuração específicos de Blazor.

   - Selecione **Blazor** a partir do dropdown _Build Presets_ e mantenha todos os valores predefinidos.

1. Selecione **Rever + criar**.

   :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="Rever criar botão":::

1. Selecione **Criar**.

   :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="Botão Criar":::

1. Selecione **Ir para recurso**.

   :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="Botão Ir para recurso":::

## <a name="view-the-website"></a>Ver o site

Há dois aspetos para a implementação de uma aplicação estática. O primeiro aprovisiona os recursos subjacentes do Azure que compõem a sua aplicação. O segundo é um fluxo de trabalho do GitHub Actions que compila e publica a sua aplicação.

Antes de navegar para o seu novo site estático, a construção de implantação deve terminar primeiro de funcionar.

A janela de visão geral das Aplicações Web Estáticas exibe uma série de links que o ajudam a interagir com a sua aplicação web.

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="Janela de visão geral":::

1. Clicando no banner que diz, _clique aqui para verificar o estado das suas ações gitHub_ leva-o às Ações GitHub em execução contra o seu repositório. Assim que verificar que o trabalho de implantação está concluído, poderá navegar para o seu website através do URL gerado.

2. Assim que o fluxo de trabalho das Ações GitHub estiver concluído, pode selecionar o link _URL_ para abrir o website em novo separador.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, pode eliminar a instância Azure Static Web Apps através dos seguintes passos:

1. Abra o [portal do Azure](https://portal.azure.com)
1. Procure **a minha primeira-estática-blazor-app** a partir da barra de pesquisa superior
1. Selecione no nome da aplicação
1. Selecione no botão **Eliminar**
1. Selecione **Sim** para confirmar a ação de eliminação

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Autenticação e autorização](./authentication-authorization.md)
