---
title: 'Quickstart: Construir a sua primeira aplicação web estática com aplicações web estáticas Azure'
description: Aprenda a construir uma instância Azure Static Web Apps com o seu quadro frontal preferido.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 72a76fb513dc6eb008fcf1d1e19ffc33e713cfdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259257"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Quickstart: Construir a sua primeira aplicação web estática

A Azure Static Web Apps publica websites para um ambiente de produção construindo aplicações a partir de um repositório GitHub. Neste arranque rápido, você constrói uma aplicação web usando a sua estrutura frontal preferida a partir de um repositório GitHub.

Se não tiver uma subscrição do Azure, [crie uma conta de teste gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- [Conta GitHub](https://github.com)
- [Conta Azure](https://portal.azure.com)

## <a name="create-a-repository"></a>Criar um repositório

Este artigo usa repositórios de modelos GitHub para facilitar a criação de um novo repositório. Os modelos apresentam aplicações de arranque construídas com diferentes estruturas frontais.

# <a name="angular"></a>[Angular](#tab/angular)

- Certifique-se de que está conectado ao GitHub e, navegue para o local seguinte para criar um novo repositório
  - https://github.com/staticwebdev/angular-basic/generate
- Nomeie o seu repositório **my-first-static-web-app**

# <a name="react"></a>[Reagir](#tab/react)

- Certifique-se de que está conectado ao GitHub e, navegue para o local seguinte para criar um novo repositório
  - https://github.com/staticwebdev/react-basic/generate
- Nomeie o seu repositório **my-first-static-web-app**

# <a name="vue"></a>[Vue](#tab/vue)

- Certifique-se de que está conectado ao GitHub e, navegue para o local seguinte para criar um novo repositório
  - https://github.com/staticwebdev/vue-basic/generate
- Nomeie o seu repositório **my-first-static-web-app**

# <a name="no-framework"></a>[Sem Enquadramento](#tab/vanilla-javascript)

- Certifique-se de que está conectado ao GitHub e, navegue para o local seguinte para criar um novo repositório
  - https://github.com/staticwebdev/vanilla-basic/generate
- Nomeie o seu repositório **my-first-static-web-app**

> [!NOTE]
> A azure Static Web Apps requer pelo menos um ficheiro HTML para criar uma aplicação web. O repositório que cria neste passo inclui um único ficheiro _index.html._

---

Clique no **repositório Criar a partir do** botão do modelo.

:::image type="content" source="media/getting-started/create-template.png" alt-text="Criar repositório a partir do modelo":::

## <a name="create-a-static-web-app"></a>Crie uma aplicação web estática

Agora que o repositório foi criado, pode criar uma aplicação web estática a partir do portal Azure.

- Navegue para o [portal do Azure](https://portal.azure.com)
- Clique **em Criar um recurso**
- Pesquisa de **aplicativos web estáticos**
- Clique **em Aplicativos Web Estáticos (pré-visualização)**
- Clique **em Criar**

### <a name="basics"></a>Noções básicas

Comece por configurar a sua nova aplicação e ligá-la a um repositório GitHub.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="Separador básico":::

- Selecione a sua _subscrição Azure_
- Selecione ou crie um novo _Grupo de Recursos_
- Nomeie a aplicação **my-first-static-web-app**.
  - Caracteres válidos são `a-z` (caso insensível), `0-9` e `-` .
- Selecione uma _região_ mais próxima
- Selecione o _SKU_ **gratuito**
- Clique no **'Iniciar s-in' com** o botão GitHub e autense com o GitHub

Assim que assinar com o GitHub, insira as informações do repositório.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="Detalhes do repositório":::

- Selecione a sua _Organização_ Preferida
- Selecione **a minha primeira web-web-static-app** a partir do _repositório_ drop-down
- Selecione **mestre** do _ramo_ drop-down
- Clique no **Seguinte: Construa >** botão para editar a configuração de construção

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="Próximo botão de construção":::

### <a name="build"></a>Compilação

Em seguida, adicione detalhes de configuração específicos à sua estrutura frontal preferida.

# <a name="angular"></a>[Angular](#tab/angular)

- Insira **/** na caixa _de localização_ da App
- Limpe o valor padrão da caixa de _localização Api_
- Introduza **dist/angular-básico** na caixa de _localização_ do artefacto da App

# <a name="react"></a>[Reagir](#tab/react)

- Insira **/** na caixa _de localização_ da App
- Limpe o valor padrão da caixa de _localização Api_
- Introduza **a construção** na caixa _de localização_ do artefacto da App

# <a name="vue"></a>[Vue](#tab/vue)

- Insira **/** na caixa _de localização_ da App
- Limpe o valor padrão da caixa de _localização Api_
- Introduza **dist** na caixa _de localização_ do artefacto da App

# <a name="no-framework"></a>[Sem Enquadramento](#tab/vanilla-javascript)

- Insira **/** na caixa _de localização_ da App
- Limpe o valor padrão da caixa de _localização Api_
- Limpe o valor padrão da caixa de _localização_ do artefacto da App

---

Clique no botão **Rever + criar**.

:::image type="content" source="media/getting-started/review-create.png" alt-text="Rever criar botão":::

Para alterar estes valores após a criação da aplicação, pode editar o [ficheiro workflow](github-actions-workflow.md).

### <a name="review--create"></a>Revisão + criar

Após a validação do pedido, pode continuar a criar a aplicação.

Clique no botão **Criar**

:::image type="content" source="media/getting-started/create-button.png" alt-text="Criar botão":::

Uma vez criado o recurso, clique no botão **Go para o** botão de recursos

:::image type="content" source="media/getting-started/resource-button.png" alt-text="Ir para o botão de recursos":::

## <a name="view-the-website"></a>Ver o site

Há dois aspetos para a implementação de uma aplicação estática. As primeiras disposições são os recursos Azure subjacentes que compõem a sua aplicação. O segundo é um fluxo de trabalho gitHub Actions que constrói e publica a sua aplicação.

Antes de navegar para o seu novo site estático, a construção de implantação deve terminar primeiro de funcionar.

A janela de visão geral das Aplicações Web Estáticas exibe uma série de links que o ajudam a interagir com a sua aplicação web.

:::image type="content" source="media/getting-started/overview-window.png" alt-text="Janela de visão geral":::

1. Clicar no banner que diz: "Clique aqui para verificar o estado das suas ações do GitHub" leva-o às Ações GitHub que correm contra o seu repositório. Assim que verificar que o trabalho de implantação está concluído, poderá navegar para o seu website através do URL gerado.

2. Assim que o fluxo de trabalho das Ações GitHub estiver concluído, pode clicar no link _URL_ para abrir o website em novo separador.

## <a name="clean-up-resources"></a>Limpar recursos

Se não continuar a utilizar esta aplicação, pode eliminar a instância Azure Static Web Apps através dos seguintes passos:

1. Abra o [portal do Azure](https://portal.azure.com)
1. Procure **a minha primeira web-web-app** a partir da barra de pesquisa de topo
1. Clique no nome da aplicação
1. Clique no botão **Eliminar**
1. Clique **em Sim** para confirmar a ação de eliminação

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)
