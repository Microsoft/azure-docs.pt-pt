---
title: Suporte para implementação ou falha de provisionamento da Solução VMware Azure
description: Obtenha informações da sua nuvem privada Azure VMware Solution para apresentar um pedido de serviço para uma implementação ou falha de fornecimento de Azure VMware Solution.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 27b645f4ca225fdd74bca6499b6581b3803e41a4
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542410"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Abrir um pedido de suporte para uma implementação ou falha de provisionamento da Azure VMware Solution

Este artigo mostra-lhe como abrir um pedido de [suporte](https://rc.portal.azure.com/#create/Microsoft.Support) e fornecer informações fundamentais para uma implementação ou falha de provisionamento da Azure VMware Solution. 

Quando tiver uma falha na sua nuvem privada, tem de abrir um pedido de apoio no portal Azure. Para abrir um pedido de apoio, primeiro obtenha algumas informações chave no portal Azure:

- ID de Correlação
- ID do circuito Azure ExpressRoute
- Mensagens de erro

## <a name="get-the-correlation-id"></a>Obtenha o ID da correlação
 
Quando cria uma nuvem privada ou qualquer recurso em Azure, um ID de correlação para o recurso é gerado automaticamente para o recurso. Inclua o ID de correlação de nuvem privada no seu pedido de apoio para abrir e resolver o pedido mais rapidamente.

No portal Azure, você pode obter o ID de correlação para um recurso de duas maneiras:

* **Painel geral**
* Registos de implantação
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>Obtenha o ID de correlação a partir da visão geral do recurso

Aqui está um exemplo dos detalhes da operação de uma implementação de nuvem privada falhada, com o ID de correlação selecionado:

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Screenshot que mostra uma implementação de nuvem privada falhada com o ID de correlação selecionado.":::

Para aceder à implementação resulta num painel **de visão geral de** nuvem privada:

1. No portal Azure, selecione a sua nuvem privada.

1. No menu esquerdo, selecione **Overview**.

Após o início de uma implantação, os resultados da implantação são apresentados no painel **de visão geral** da nuvem privada.

Copie e guarde o ID de correlação de implementação de nuvem privada para incluir no pedido de serviço.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>Obtenha o ID de correlação do registo de implementação

Pode obter o ID de correlação para uma implementação falhada, pesquisando o registo de atividade de implementação no portal Azure.

Para aceder ao registo de implementação:

1. No portal Azure, selecione a sua nuvem privada e, em seguida, selecione o ícone de notificações.

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Screenshot que mostra o ícone de notificações no portal Azure.":::

1. No painel **de notificações,** selecione **Mais eventos no registo de atividades:**

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Screenshot que mostra mais eventos no link de registo de atividade selecionado no painel de Notificações.":::

1. Para encontrar a implementação falhada e o seu ID de correlação, procure o nome do recurso ou outra informação que usou para criar o recurso. 

    O exemplo a seguir mostra os resultados da pesquisa de um recurso de nuvem privada chamado pc03.
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Screenshot que mostra resultados de pesquisa para um recurso de nuvem privada exemplo e o painel Criar ou atualizar um painel PrivateCloud.":::
 
1. Nos resultados de pesquisa no painel **de registo de atividade,** selecione o nome de funcionamento da implementação falhada.

1. No painel Criar ou atualizar um painel **PrivateCloud,** selecione o separador **JSON** e, em seguida, procure `correlationId` no registo que é mostrado. Copie o `correlationId` valor para incluí-lo no seu pedido de apoio. 
 
## <a name="copy-error-messages"></a>Copiar mensagens de erro

Para ajudar a resolver o seu problema de implementação, inclua quaisquer mensagens de erro que sejam mostradas no portal Azure. Selecione uma mensagem de aviso para ver um resumo dos erros:
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Screenshot que mostra detalhes de erro no separador Resumo do painel Errors, com o ícone de cópia selecionado.":::

Para copiar a mensagem de erro, selecione o ícone de cópia. Guarde a mensagem copiada para incluir no seu pedido de apoio.
 
## <a name="get-the-expressroute-id-uri"></a>Obtenha o ExpressRoute ID (URI)
 
Talvez estejas a tentar escalar ou espreitar uma nuvem privada existente com o circuito expressRoute em nuvem privada, e falha. Nesse cenário, precisa do ID ExpressRoute para incluir no seu pedido de apoio.

Para copiar o ID ExpressRoute:

1. No portal Azure, selecione a sua nuvem privada.
1. No menu esquerdo, em **Manage** , selecione **Conectividade**. 
1. No painel direito, selecione o **separador ExpressRoute.**
1. Selecione o ícone de cópia para **ExpressRoute ID** e guarde o valor a utilizar no seu pedido de suporte.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Copie o ID ExpressRoute para a área de transferência."::: 
 
## <a name="pre-validation-failures"></a>Falhas de pré-validação

Se a sua verificação de pré-validação de nuvem privada falhar (antes da implementação), não terá sido gerado um ID de correlação. Neste cenário, pode fornecer as seguintes informações no seu pedido de apoio:

- Mensagens de erro e falha. Estas mensagens podem ser úteis em muitas falhas, por exemplo, para questões relacionadas com quotas. É importante copiar estas mensagens e incluí-las no pedido de apoio, como descrito neste artigo.
- Informação que usou para criar a nuvem privada Azure VMware Solution, incluindo:
  - Localização
  - Grupo de recursos
  - Nome do recurso

## <a name="create-your-support-request"></a>Crie o seu pedido de apoio

Para obter informações gerais sobre a criação de um pedido de apoio, consulte [Como criar um pedido de apoio ao Azure.](../azure-portal/supportability/how-to-create-azure-support-request.md) 

Para criar um pedido de suporte para uma implementação ou falha de provisionamento da Azure VMware Solution:

1. No portal Azure, selecione o ícone **Ajuda** e, em seguida, selecione **Novo pedido de suporte**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Screenshot do novo painel de pedido de suporte no portal Azure.":::

1. Insira ou selecione as informações necessárias:

   1. No separador **Básico** :

      1. Para **o tipo de problema** , selecione **Problemas de Configuração e Configuração**.

      1. Para **o subtipo de problemas** , selecione **Provision a private cloud**.

   1. No separador **Detalhes:**

      1. Introduza ou selecione as informações necessárias.

      1. Cole o seu ID de correlação ou iD ExpressRoute onde esta informação é solicitada. Se não vir uma caixa de texto específica para estes valores, cole-os na Caixa de Texto **de Emissão.**

    1. Cole quaisquer detalhes de erro, incluindo as mensagens de erro ou falha que copiou, na Caixa de Texto **de Emissão.**

1. Reveja as suas entradas e, em seguida, **selecione Criar** para criar o seu pedido de suporte.
