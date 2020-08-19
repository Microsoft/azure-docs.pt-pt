---
title: 'Quickstart: Construir a sua primeira aplicação web estática com aplicações web estáticas Azure'
description: Aprenda a construir uma instância Azure Static Web Apps com o seu quadro frontal preferido.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: bbc06b657525880f22bd5fb38e902f906d438c9c
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565915"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Quickstart: Construir a sua primeira aplicação web estática

As Aplicações Web Estáticas do Azure publicam sites num ambiente de produção através da compilação de aplicações a partir de um repositório do GitHub. Neste arranque rápido, você constrói uma aplicação web usando a sua estrutura frontal preferida a partir de um repositório GitHub.

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

# <a name="react"></a>[React](#tab/react)

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

- Navegue até ao [portal Azure](https://portal.azure.com)
- Clique em **Criar um Recurso**.
- Procure **Aplicações Web Estáticas**
- Clique em **Aplicações Web Estáticas (Pré-visualização)**
- Clique em **Criar**

### <a name="basics"></a>Informações básicas

Comece por configurar a sua nova aplicação e ligá-la a um repositório GitHub.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="Separador básico":::

- Selecione a sua _subscrição Azure_
- Selecione ou crie um novo _Grupo de Recursos_
- Nomeie a aplicação **my-first-static-web-app**.
  - Os carateres válidos são `a-z` (não sensível a maiúsculas e minúsculas), `0-9` e `-`.
- Selecione uma _região_ mais próxima
- Selecione o _SKU_ **gratuito**
- Clique no botão **Iniciar sessão com o GitHub** e autentique-se no GitHub

Assim que assinar com o GitHub, insira as informações do repositório.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="Detalhes do repositório":::

- Selecione a sua _Organização_ Preferida
- Selecione **a minha primeira web-web-static-app** a partir do _repositório_ drop-down
- Selecione **mestre** do _ramo_ drop-down
- Clique no botão **Seguinte: Compilar>** para editar a configuração de compilação

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="Próximo botão de construção":::

> [!NOTE]
>  Se não vir nenhum repositório, poderá ter de autorizar aplicações web estáticas Azure no GitHub. Navegue na página inicial do [GitHub](https://github.com) e clique na imagem da sua conta para abrir o menu suspenso. Clique em **Definições,** em seguida, **aplicações > aplicações OAuth autorizadas > Azure Static Web Apps**, e finalmente selecione **Grant**. Para repositórios de organização, você deve ser um proprietário da organização para conceder as permissões.

### <a name="build"></a>Compilar

Em seguida, adicione detalhes de configuração específicos da sua arquitetura de front-end preferida.

# <a name="angular"></a>[Angular](#tab/angular)

- Insira **/** na caixa _de localização_ da App
- Limpe o valor padrão da caixa de _localização Api_
- Introduza **dist/angular-básico** na caixa de _localização_ do artefacto da App

# <a name="react"></a>[React](#tab/react)

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

### <a name="review--create"></a>Rever + criar

Após a validação do pedido, pode continuar a criar a aplicação.

Clique no botão **Criar**.

:::image type="content" source="media/getting-started/create-button.png" alt-text="Botão Criar":::

Uma vez criado o recurso, clique no botão **Go para o** botão de recursos

:::image type="content" source="media/getting-started/resource-button.png" alt-text="Botão Ir para recurso":::

## <a name="view-the-website"></a>Ver o site

Há dois aspetos para a implementação de uma aplicação estática. O primeiro aprovisiona os recursos subjacentes do Azure que compõem a sua aplicação. O segundo é um fluxo de trabalho do GitHub Actions que compila e publica a sua aplicação.

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
