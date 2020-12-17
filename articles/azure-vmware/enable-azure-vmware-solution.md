---
title: Como ativar o seu recurso Azure VMware Solution
description: Saiba como submeter um pedido de suporte para ativar o seu recurso Azure VMware Solution. Também pode solicitar mais anfitriões na sua nuvem privada Azure VMware Solution.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 6d614dffc4ab3127e1e6740b1a8773e5fd7c23ff
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630892"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Como ativar o recurso Azure VMware Solution
Saiba como submeter um pedido de suporte para ativar o seu recurso [Azure VMware Solution.](introduction.md) Também pode solicitar mais anfitriões na sua nuvem privada Azure VMware Solution.

## <a name="eligibility-criteria"></a>Critérios de elegibilidade

Você precisará de uma conta Azure numa subscrição do Azure. A subscrição da Azure deve respeitar um dos seguintes critérios:

* Uma subscrição ao abrigo de um [Acordo de Empresa Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) com a Microsoft.
* Um Fornecedor de Solução Cloud (CSP) gerido ao abrigo de um contrato CSP Azure existente oferece contrato ou um plano Azure.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Ativar a Solução VMware Azure para clientes EA
Antes de criar o seu recurso Azure VMware Solution, terá de submeter um bilhete de apoio para que os seus anfitriões tenham sido atribuídos. Uma vez que a equipa de apoio recebe o seu pedido, leva até cinco dias úteis para confirmar o seu pedido e alocar os seus anfitriões. Se tiver uma nuvem privada Azure VMware Solution e quiser mais anfitriões, passará pelo mesmo processo.


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
   - Número de anfitriões
   - Quaisquer outros detalhes

   >[!NOTE]
   >A Azure VMware Solution recomenda um mínimo de três anfitriões para girar a sua nuvem privada e para anfitriões N+1 de redundância. 

1. Selecione **Review + Criar** para submeter o pedido.

   Levará até cinco dias úteis para um representante de apoio confirmar o seu pedido.

   >[!IMPORTANT] 
   >Se já tem uma Solução Azure VMware existente, e está a solicitar anfitriões adicionais, por favor, note que precisamos de cinco dias úteis para alocar os anfitriões. 

1. Antes de poder aprovisionar os seus anfitriões, certifique-se de que regista o fornecedor de recursos **Microsoft.AVS** no portal Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Para obter formas adicionais de registar o fornecedor de recursos, consulte [os fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Ativar a Solução VMware Azure para clientes CSP 

Os CSPs devem utilizar [o Microsoft Partner Center](https://partner.microsoft.com) para ativar a Solução VMware Azure para os seus clientes. Este artigo utiliza o [plano CSP Azure](/partner-center/azure-plan-lp) como exemplo para ilustrar o procedimento de compra dos parceiros.

   >[!IMPORTANT] 
   >O serviço Azure VMware Solution não fornece um arrendamento multi-arrendamento necessário. Os parceiros de acolhimento que o exigem não são apoiados. 

1. No **Partner Center,** selecione **CSP** para aceder à área **clientes.**

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Área de clientes do Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Selecione o seu cliente e, em seguida, **selecione Adicionar produtos**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Centro de Parceiros Microsoft" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Selecione **O plano Azure** e, em seguida, selecione **Adicionar ao carrinho**. 

1. Reveja e termine a configuração geral da subscrição do plano Azure para o seu cliente. Para obter mais informações, consulte [a documentação do Microsoft Partner Center](/partner-center/azure-plan-manage).

Depois de configurar o plano Azure e as [permissões Azure RBAC necessárias](/partner-center/azure-plan-manage) para a subscrição, irá envolver a Microsoft para ativar a quota para uma subscrição do plano Azure. Aceder ao portal Azure do [Microsoft Partner Center](https://partner.microsoft.com) utilizando o procedimento **Admin On Behalf Of** (AOBO).

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
   - Número de anfitriões
   - Quaisquer outros detalhes
   - Pretende-se hospedar vários clientes?

   >[!NOTE]
   >A Azure VMware Solution recomenda um mínimo de três anfitriões para girar a sua nuvem privada e para anfitriões N+1 de redundância. 

1. Selecione **Review + Criar** para submeter o pedido.

   Levará até cinco dias úteis para um representante de apoio confirmar o seu pedido.

   >[!IMPORTANT] 
   >Se já tem uma Solução Azure VMware existente, e está a solicitar anfitriões adicionais, por favor, note que precisamos de cinco dias úteis para alocar os anfitriões. 

1. Se a subscrição for gerida pelo prestador de serviços, a sua equipa de administração terá de aceder ao portal Azure utilizando novamente o procedimento **Admin On Behalf Of** (AOBO) do Partner Center. Um no portal Azure lança uma instância [Cloud Shell](../cloud-shell/overview.md) e regista o fornecedor de recursos **Microsoft.AVS** e procede à implementação da nuvem privada Azure VMware Solution.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Para obter formas adicionais de registar o fornecedor de recursos, consulte [os fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md).

1. Se a subscrição for gerida diretamente pelo cliente, o registo do fornecedor de recursos **Microsoft.AVS** deve ser feito por um utilizador com permissões suficientes na subscrição, consulte [os fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md) para obter mais detalhes e formas de registar o fornecedor de recursos. 


## <a name="next-steps"></a>Passos seguintes

Depois de ativar o seu recurso Azure VMware Solution e tiver a rede adequada no lugar, pode [criar uma nuvem privada.](tutorial-create-private-cloud.md)
