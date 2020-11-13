---
title: Como ativar o seu recurso Azure VMware Solution
description: Saiba como submeter um pedido de suporte para ativar o seu recurso Azure VMware Solution. Também pode solicitar mais nós na sua nuvem privada Azure VMware Solution.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 7c805e9e622f55593ff1fbb72a355d233b7e3618
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576403"
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

1. No **Partner Center,** selecione **CSP** para aceder à área **clientes.**

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Área de clientes do Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Selecione o seu cliente e, em seguida, **selecione Adicionar produtos**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Centro de Parceiros Microsoft" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Selecione **O plano Azure** e, em seguida, selecione **Adicionar ao carrinho**. 

1. Reveja e termine a configuração geral da subscrição do plano Azure para o seu cliente. Para obter mais informações, consulte [a documentação do Microsoft Partner Center](https://docs.microsoft.com/partner-center/azure-plan-manage).

Depois de configurar o plano Azure e as permissões de RBAC necessárias como CSP, irá envolver a Microsoft usando um procedimento semelhante para permitir a quota para uma subscrição do plano Azure. Uma vez adicionado ao plano Azure, o cliente ou o administrador parceiro podem implementar uma Nuvem Privada Azure VMware através do portal Azure. 

## <a name="next-steps"></a>Passos seguintes

Depois de ativar o seu recurso Azure VMware Solution e tiver a rede adequada no lugar, pode [criar uma nuvem privada.](tutorial-create-private-cloud.md)