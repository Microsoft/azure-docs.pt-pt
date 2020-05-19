---
title: 'Quickstart: Construindo a sua primeira aplicação estática com Aplicações Web Estáticas Azure'
description: Aprenda a construir uma instância de Web Apps Estáticas Azure com a sua estrutura frontal preferida.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 14fd237b6437c15ede2da7c0694004f6c22736cc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83600004"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Quickstart: Construindo a sua primeira aplicação web estática

As Aplicações Web Estáticas azure publicam websites para um ambiente de produção através da construção de aplicações a partir de um repositório GitHub. Neste arranque rápido, você constrói uma aplicação web usando a sua estrutura frontal preferida a partir de um repositório GitHub.

Se não tiver uma subscrição Azure, [crie uma conta de teste gratuita.](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Pré-requisitos

- Conta [GitHub](https://github.com)
- [Conta Azure](https://portal.azure.com)

## <a name="create-a-repository"></a>Criar um repositório

Este artigo usa repositórios de modelo GitHub para facilitar a criação de um novo repositório. Os modelos apresentam aplicações de arranque construídas com diferentes estruturas frontais.

# <a name="angular"></a>[Angular](#tab/angular)

- Navegue para o seguinte local para criar um novo repositório
  - https://github.com/staticwebdev/angular-basic/generate
- Nomeie o seu repositório **my-first-static-web-app**

# <a name="react"></a>[Reagir](#tab/react)

- Navegue para o seguinte local para criar um novo repositório
  - https://github.com/staticwebdev/react-basic/generate
- Nomeie o seu repositório **my-first-static-web-app**

# <a name="vue"></a>[Rio Vue](#tab/vue)

- Navegue para o seguinte local para criar um novo repositório
  - https://github.com/staticwebdev/vue-basic/generate
- Nomeie o seu repositório **my-first-static-web-app**

# <a name="no-framework"></a>[Sem Enquadramento](#tab/vanilla-javascript)

- Navegue para o seguinte local para criar um novo repositório
  - https://github.com/staticwebdev/vanilla-basic/generate
- Nomeie o seu repositório **my-first-static-web-app**

> [!NOTE]
> As Aplicações Web Estáticas Azure requerem pelo menos um ficheiro HTML para criar uma aplicação web. O repositório que cria neste passo inclui um único _ficheiro indexado.html._

---

Clique no **repositório Criar a partir do** botão do modelo.

:::image type="content" source="media/getting-started/create-template.png" alt-text="Criar repositório a partir do modelo":::

## <a name="create-a-static-web-app"></a>Criar uma aplicação web estática

Agora que o repositório é criado, você pode criar uma aplicação web estática a partir do portal Azure.

- Navegue para o [portal do Azure](https://portal.azure.com)
- Clique em **Criar um Recurso**
- Pesquisa de **aplicações web estáticas**
- Clique em **aplicações web estáticas (pré-visualização)**
- Clique em **Criar**

### <a name="basics"></a>Noções básicas

Comece por configurar a sua nova aplicação e ligá-la a um repositório GitHub.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="Separador básico":::

- Selecione a sua _subscrição Azure_
- Selecione ou crie um novo _Grupo de Recursos_
- Nomeie a aplicação **my-first-static-web-app**.
  - Os caracteres válidos são `a-z` (caso insensível), `0-9` e `_` .
- Selecione uma _Região_ mais próxima de si
- Selecione o _SKU_ **gratuito**
- Clique no **Sign-in com** o botão GitHub e autenticar com gitHub

Assim que iniciar sessão com o GitHub, insira as informações do repositório.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="Detalhes do repositório":::

- Selecione a sua _Organização_ Preferida
- Selecione **my-first-web-static-app** do _Repository_ drop-down
- Selecione **mestre** da queda do _Branch_
- Clique no **seguinte: Construa >** botão para editar a configuração de construção

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="Próximo botão construir":::

### <a name="build"></a>Compilação

Em seguida, adicione detalhes de configuração específicos da sua estrutura frontal preferida.

# <a name="angular"></a>[Angular](#tab/angular)

- Insira **/** na caixa de localização da _App_
- Limpe o valor padrão da caixa de _localização Api_
- Introduza **dist/angular-basic** na caixa de localização do artefacto da _App_

# <a name="react"></a>[Reagir](#tab/react)

- Insira **/** na caixa de localização da _App_
- Limpe o valor padrão da caixa de _localização Api_
- Insira a **construção** na caixa de localização do artefacto da _App_

# <a name="vue"></a>[Rio Vue](#tab/vue)

- Insira **/** na caixa de localização da _App_
- Limpe o valor padrão da caixa de _localização Api_
- Insira o **dist** na caixa de localização do artefacto da _App_

# <a name="no-framework"></a>[Sem Enquadramento](#tab/vanilla-javascript)

- Insira **/** na caixa de localização da _App_
- Limpe o valor padrão da caixa de _localização Api_
- Limpe o valor padrão da caixa de localização do artefacto da _App_

---

Clique no botão **Rever + criar**.

:::image type="content" source="media/getting-started/review-create.png" alt-text="Rever criar botão":::

### <a name="review--create"></a>Rever + criar

Após a validação do pedido, pode continuar a criar a aplicação.

Clique no botão **Criar**

:::image type="content" source="media/getting-started/create-button.png" alt-text="Criar botão":::

Assim que o recurso for criado, clique no botão **Ir para o recurso**

:::image type="content" source="media/getting-started/resource-button.png" alt-text="Vá para o botão de recursos":::

## <a name="view-the-website"></a>Ver o site

Há dois aspetos para implementar uma aplicação estática. As primeiras disposições dos recursos Azure subjacentes que compõem a sua app. O segundo é um fluxo de trabalho gitHub Actions que constrói e publica a sua aplicação.

Antes de poder navegar para o seu novo local estático, a construção de implantação deve terminar primeiro a correr.

A janela de visão geral das Aplicações Da Web Estática exibe uma série de links que o ajudam a interagir com a sua aplicação web.

:::image type="content" source="media/getting-started/overview-window.png" alt-text="Janela de visão geral":::

1. Clicar no banner que diz: "Clique aqui para verificar o estado das suas Ações GitHub" leva-o às Ações GitHub que correm contra o seu repositório. Assim que verificar que o trabalho de implementação está concluído, pode navegar para o seu website através do URL gerado.

2. Assim que o fluxo de trabalho gitHub Actions estiver completo, pode clicar no link _URL_ para abrir o website em novo separador.

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, pode eliminar a instância de Aplicações Web Estáticas Do Azure através dos seguintes passos:

1. Abra o [portal do Azure](https://portal.azure.com)
1. Procure a **minha primeira aplicação estática** da web a partir da barra de pesquisa superior
1. Clique no nome da aplicação
1. Clique no botão **Eliminar**
1. Clique **Sim** para confirmar a ação de exclusão

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione uma API](add-api.md)
