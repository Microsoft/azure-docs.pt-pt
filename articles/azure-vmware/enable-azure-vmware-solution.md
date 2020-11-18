---
title: Como ativar o seu recurso Azure VMware Solution
description: Saiba como submeter um pedido de suporte para ativar o seu recurso Azure VMware Solution. Também pode solicitar mais nós na sua nuvem privada Azure VMware Solution.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: c9c31d8ffbf62a511f4f2ab19f995b6bc0ee0ca4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695151"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Como ativar o recurso Azure VMware Solution
Saiba como submeter um pedido de suporte para ativar o seu recurso [Azure VMware Solution.](introduction.md) Também pode solicitar mais nós na sua nuvem privada Azure VMware Solution.

## <a name="eligibility-criteria"></a>Critérios de elegibilidade

Você precisará de uma conta Azure numa subscrição do Azure. A subscrição da Azure deve respeitar um dos seguintes critérios:

* Uma subscrição ao abrigo de um [Acordo de Empresa Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) com a Microsoft.
* Um Fornecedor de Solução Cloud (CSP) geriu a subscrição ao abrigo de um plano Azure.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Ativar a Solução VMware Azure para clientes EA
Antes de criar o seu recurso Azure VMware Solution, terá de submeter um bilhete de apoio para que os seus nós tenham os seus nós atribuídos. Uma vez que a equipa de apoio recebe o seu pedido, leva até cinco dias úteis para confirmar o seu pedido e alocar os seus nós. Se tiver uma nuvem privada Azure VMware Solution e quiser mais nós atribuídos, passará pelo mesmo processo.


1. No seu portal Azure, no âmbito **da Ajuda + Suporte,** crie um **[novo pedido de suporte](https://rc.portal.azure.com/#create/Microsoft.Support)** e forneça as seguintes informações para o bilhete:
   - **Tipo de emissão:** Técnico
   - **Assinatura:** Selecione a sua subscrição
   - **Serviço:** Todos os serviços > Solução VMware Azure
   - **Recurso:** Pergunta geral 
   - **Resumo:** Capacidade de necessidade
   - **Tipo de problema:** Questões de Gestão de Capacidades
   - **Subtipo de problema:** Pedido de cliente para quota/capacidade adicional do anfitrião

1. Na **Descrição** do bilhete de apoio, no separador **Detalhes,** forneça:

   - POC ou Produção 
   - Nome da Região
   - Número de nós
   - Quaisquer outros detalhes

   >[!NOTE]
   >A Azure VMware Solution recomenda um mínimo de três nós para girar a sua nuvem privada e para os nós N+1 de redundância. 

1. Selecione **Review + Criar** para submeter o pedido.

   Levará até cinco dias úteis para um representante de apoio confirmar o seu pedido.

   >[!IMPORTANT] 
   >Se já tem uma Solução Azure VMware existente, e está a solicitar nóns adicionais, por favor, note que precisamos de cinco dias úteis para alocar os nós. 

1. Antes de poder aprovisionar os seus nós, certifique-se de que regista o fornecedor de recursos **Microsoft.AVS** no portal Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Para obter formas adicionais de registar o fornecedor de recursos, consulte [os fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Ativar a Solução VMware Azure para clientes CSP 

Os CSPs devem utilizar [o Microsoft Partner Center](https://partner.microsoft.com) para ativar a Solução VMware Azure para os seus clientes. 

   >[!IMPORTANT] 
   >O serviço Azure VMware Solution não fornece um ambiente multitenant, pelo que os parceiros de hospedagem ainda não são suportados. 

1. No **Partner Center,** selecione **CSP** para aceder à área **clientes.**

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Área de clientes do Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Selecione o seu cliente e, em seguida, **selecione Adicionar produtos**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Centro de Parceiros Microsoft" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Selecione **O plano Azure** e, em seguida, selecione **Adicionar ao carrinho**. 

1. Reveja e termine a configuração geral da subscrição do plano Azure para o seu cliente. Para obter mais informações, consulte [a documentação do Microsoft Partner Center](https://docs.microsoft.com/partner-center/azure-plan-manage).

Depois de configurar o plano Azure e as permissões de RBAC necessárias como CSP, irá contratar a Microsoft para ativar a quota para uma subscrição do plano Azure. Aceder ao portal Azure do Partner Center utilizando o procedimento **Admin Em Nome do** (AOBO).

1. Inscreva-se no [Partner Center](https://partner.microsoft.com).

1. Selecione **CSP** para aceder à área **clientes.**

1. Expandir os detalhes do cliente e selecionar **o Microsoft Azure Management Portal**.

1. No portal Azure, no âmbito **da Ajuda + Suporte,** crie um **[novo pedido de suporte](https://rc.portal.azure.com/#create/Microsoft.Support)** e forneça as seguintes informações para o bilhete:
   - **Tipo de emissão:** Técnico
   - **Assinatura:** Selecione a sua subscrição
   - **Serviço:** Todos os serviços > Solução VMware Azure
   - **Recurso:** Pergunta geral 
   - **Resumo:** Capacidade de necessidade
   - **Tipo de problema:** Questões de Gestão de Capacidades
   - **Subtipo de problema:** Pedido de cliente para quota/capacidade adicional do anfitrião

1. Na **Descrição** do bilhete de apoio, no separador **Detalhes,** forneça:

   - POC ou Produção 
   - Nome da Região
   - Número de nós
   - Quaisquer outros detalhes
   - Pretende-se hospedar vários clientes?

   >[!NOTE]
   >A Azure VMware Solution recomenda um mínimo de três nós para girar a sua nuvem privada e para os nós N+1 de redundância. 

1. Selecione **Review + Criar** para submeter o pedido.

   Levará até cinco dias úteis para um representante de apoio confirmar o seu pedido.

   >[!IMPORTANT] 
   >Se já tem uma Solução Azure VMware existente, e está a solicitar nóns adicionais, por favor, note que precisamos de cinco dias úteis para alocar os nós. 

1. Uma vez adicionado ao plano Azure, e com quota ativada, o cliente ou o administrador parceiro pode implementar uma Nuvem Privada Azure VMware através do portal Azure. Antes de poder aprovisionar os seus nós, certifique-se de que regista o fornecedor de recursos **Microsoft.AVS** no portal Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Para obter formas adicionais de registar o fornecedor de recursos, consulte [os fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Passos seguintes

Depois de ativar o seu recurso Azure VMware Solution e tiver a rede adequada no lugar, pode [criar uma nuvem privada.](tutorial-create-private-cloud.md)