---
title: Implementar & gerir instâncias de contentores do Azure
description: Automatizar tarefas e fluxos de trabalho que criam e gerem a implantação de contentores em instâncias de contentores Azure utilizando apps da Azure Logic
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76046295"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Implementar e gerir instâncias de contentores Azure utilizando aplicações lógicas Azure

Com apps Azure Logic Apps e o conector Azure Container Instance, pode configurar tarefas automatizadas e fluxos de trabalho que implementem e gerem [grupos de contentores.](../container-instances/container-instances-container-groups.md) O conector de instância do contentor suporta as seguintes ações:

* Criar ou eliminar um grupo de contentores
* Obtenha as propriedades de um grupo de contentores
* Obtenha uma lista de grupos de contentores
* Obtenha os registos de uma instância de contentor

Utilize estas ações nas suas aplicações lógicas para tarefas como executar uma carga de trabalho de contentor em resposta a um gatilho de Apps Lógicas. Também pode ter outras ações que utilizem a saída das ações de Container Instance. 

Este conector fornece apenas ações, por isso, para iniciar a sua aplicação lógica, utilize um gatilho separado, como um gatilho **de Recorrência** para executar uma carga de trabalho do contentor num horário regular. Ou, pode ter de desencadear uma implantação de grupo de contentores após um evento como a chegada de um e-mail do Outlook. 

Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar apps lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md) e [como criar e gerir casos de contentores](../container-instances/container-instances-quickstart.md)

* A aplicação lógica onde pretende aceder às suas instâncias de contentores. Para utilizar uma ação, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **de Recorrência.**

## <a name="add-a-container-instance-action"></a>Adicione uma ação de instância de contentor

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Escolha um caminho: 

   * Sob o último passo onde pretende adicionar uma ação, escolha **Novo passo**. 

     -ou-

   * Entre os passos onde pretende adicionar uma ação, mova o ponteiro sobre a seta entre os degraus. 
   Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Na caixa de pesquisa, introduza "instância do recipiente" como filtro. Na lista de ações, selecione a ação do conector Azure Container Instance que pretende.

1. Indique um nome para a ligação. 

1. Forneça os detalhes necessários para a sua ação selecionada e continue a construir o fluxo de trabalho da sua aplicação lógica.

  Por exemplo, selecione **Criar grupo de contentores** e introduzir as propriedades de um grupo de contentores e uma ou mais instâncias de contentores no grupo, como mostra a seguinte imagem (detalhe parcial):

  ![Criar grupo de contentores](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os gatilhos, ações e limites, descritos pela descrição do conector OpenAPI (anteriormente Swagger), reveja a página de [referência](/connectors/aci/) do conector ou [a referência YAML](../container-instances/container-instances-reference-yaml.md)do grupo de contentores.

## <a name="next-steps"></a>Passos seguintes

* Consulte uma [aplicação lógica de amostra](https://github.com/Azure-Samples/aci-logicapps-integration) que executa um contentor em Instâncias de Contentores Azure para analisar o sentimento de e-mail ou texto do Twitter

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)