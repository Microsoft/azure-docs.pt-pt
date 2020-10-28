---
title: Obtenha ajuda com a implementação da Solução VMware Azure ou falhas no fornecimento
description: Como obter as informações necessárias da sua nuvem privada Azure VMware Solution para apresentar um pedido de serviço para implementação da Solução VMware Azure ou falhas no fornecimento.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 1f46dde895db417fd2b488a6203d5482e73d3c5e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779497"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Obtenha ajuda com a implementação da Solução VMware Azure ou falhas no fornecimento

Este artigo ajuda-o com a implementação da Azure VMware Solution e falhas no fornecimento. Quando tiver falhas na sua nuvem privada, terá de abrir um pedido de [apoio](https://rc.portal.azure.com/#create/Microsoft.Support) (SR) no portal Azure. 

Primeiro, porém, terá de recolher algumas informações chave no portal Azure:

- ID de Correlação
- ID do circuito ExpressRoute

## <a name="collect-the-correlation-id"></a>Recolher o ID da correlação
 
Um ID de correlação é gerado quando cria uma nuvem privada ou qualquer recurso em Azure. Cada implementação do Gestor de Recursos Azure também gera um ID de correlação. Este ID permite uma criação e resolução mais rápidas de SR. 
 
Aqui está um exemplo da saída de uma implementação de nuvem privada falhada, com o ID de correlação realçado.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Falha na implantação de nuvem privada com identificação de correlação.":::

Copie e guarde este ID de correlação para incluir no pedido de serviço. Para mais detalhes, consulte [Criar o seu pedido](#create-your-support-request) de apoio no final deste artigo.

Se a falha ocorrer nas fases de pré-validação, não se gera qualquer ID de correlação. Neste caso, pode fornecer as informações utilizadas ao criar a nuvem privada Azure VMware Solution, incluindo:

- Localização
- Grupo de recursos
- Nome do recurso
 
### <a name="collect-a-summary-of-errors"></a>Recolher um resumo de erros

Os detalhes de quaisquer erros também podem ajudar a resolver o seu problema. A partir do ecrã anterior, selecione a mensagem de aviso para ver um resumo dos erros.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Falha na implantação de nuvem privada com identificação de correlação.":::

Mais uma vez, copie e guarde este resumo para incluir no SR.
 
### <a name="retrieve-past-deployments"></a>Recuperar implantações passadas

Pode recuperar implementações passadas, incluindo as falhadas, pesquisando no registo de atividade de implementação acedido selecionando o ícone de notificações.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Falha na implantação de nuvem privada com identificação de correlação.":::

Em Notificações, selecione **Mais eventos no registo de atividades** .

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Falha na implantação de nuvem privada com identificação de correlação.":::

Em seguida, procure o nome do recurso ou outra informação usada para criar o recurso para encontrar a implementação falhada e o seu ID de correlação. O exemplo a seguir mostra os resultados da pesquisa num recurso de nuvem privada (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Falha na implantação de nuvem privada com identificação de correlação.":::
 
Selecionar o nome de funcionamento da implantação falhada abre uma janela com detalhes. Selecione o separador JSON e procure correlaid. Copiar e incluir no SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>Recolha o ID ExpressRoute (URI)
 
Talvez estejas a tentar escalar ou espreitar uma nuvem privada existente com o circuito expressRoute em nuvem privada, e falha. Nesse caso, vai precisar da identificação do ExpressRoute. 

No portal Azure, selecione **Conectividade > ExpressRoute** e copie o **ID ExpressRoute** para a sua área de transferência.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Falha na implantação de nuvem privada com identificação de correlação."::: 
 
> [!NOTE]
> Por vezes, os controlos de pré-validação podem falhar antes da implementação, e a única informação disponível serão as mensagens de erro e falha. Estes podem ser úteis em muitas falhas, por exemplo, questões relacionadas com quotas, e é importante incluir estas mensagens no pedido de apoio. Para os recolher, consulte a secção anterior, [colete um resumo dos erros](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Crie o seu pedido de apoio

Para obter orientações gerais na criação do seu pedido de apoio, consulte [Como criar um pedido de suporte Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Aqui está uma orientação específica na criação de um SR para a implementação da Solução VMware Azure ou falhas no fornecimento.

1. Selecione o ícone **Ajuda** e, em seguida, **+ Novo pedido de suporte** .

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Falha na implantação de nuvem privada com identificação de correlação.":::

2. Preencha todos os campos necessários e no separador **Básicos:**

    - Para **o tipo de problema** , selecione **Problemas de Configuração e Configuração** .

    - Para **o subtipo de problemas** , selecione **Provision a private cloud** .

3. No separador **Detalhes:**

    - Preencha todos os campos necessários.

    - Cole o seu ID de correlação ou iD ExpressRoute nos campos específicos fornecidos. Se não vir um campo específico, pode cole-los na caixa de texto em **fornecer detalhes sobre o assunto.**

    - Cole todos os detalhes de erro, incluindo o resumo dos erros que copiou, na caixa de texto **sob Fornecer detalhes sobre o problema.**

4. Reveja e **selecione Criar** para criar o seu SR.
