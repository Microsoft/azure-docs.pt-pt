---
title: Solicite quota de anfitrião e permita a Solução VMware Azure
description: Saiba como solicitar quota/capacidade de anfitrião e capacite o fornecedor de recursos Azure VMware Solution. Também pode solicitar mais anfitriões numa nuvem privada Azure VMware Solution.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653176"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>Solicite quota de anfitrião e permita a Solução VMware Azure

Neste como fazer, aprenderá a solicitar quota/capacidade de anfitrião e ativará o fornecedor de recursos [Azure VMware Solution,](introduction.md) que permite o serviço. Antes de ativar a Azure VMware Solution, terá de submeter um bilhete de apoio para que os seus anfitriões tenham sido atribuídos. Se tiver uma nuvem privada Azure VMware Solution e quiser mais anfitriões, seguirá o mesmo processo.

>[!IMPORTANT]
>Pode levar alguns dias para alocar os anfitriões dependendo do número solicitado.  Por isso, solicite o que é necessário para o provisionamento para que não precise de pedir um aumento de quota com a frequência.


O processo global é simples e inclui dois passos:
- Solicitar quota/capacidade de anfitrião adicional para [clientes da EA](#request-host-quota-for-ea-customers) ou [clientes CSP](#request-host-quota-for-csp-customers) 
- Ativar o fornecedor de recursos Microsoft.AVS

## <a name="eligibility-criteria"></a>Critérios de elegibilidade

Você precisará de uma conta Azure numa subscrição do Azure. A subscrição do Azure deve seguir com um dos seguintes critérios:

- Uma subscrição ao abrigo de um [Acordo de Empresa Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) com a Microsoft.
- Um Fornecedor de Solução Cloud (CSP) gerido ao abrigo de um contrato CSP Azure existente oferece contrato ou um plano Azure.

## <a name="request-host-quota-for-ea-customers"></a>Solicitar quota de acolhimento para clientes da EA

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


## <a name="request-host-quota-for-csp-customers"></a>Solicitar quota de acolhimento para clientes CSP 

Os CSPs devem utilizar [o Microsoft Partner Center](https://partner.microsoft.com) para ativar a Solução VMware Azure para os seus clientes. Este artigo utiliza o [plano CSP Azure](/partner-center/azure-plan-lp) como exemplo para ilustrar o procedimento de compra dos parceiros.

Aceda ao portal Azure utilizando o procedimento **Admin On Behalf Of** (AOBO) do Partner Center.

>[!IMPORTANT] 
>O serviço Azure VMware Solution não fornece um arrendamento multi-arrendamento necessário. Os parceiros de acolhimento que o exigem não são apoiados. 

1. Configure o plano CSP Azure:

   1. No **Partner Center,** selecione **CSP** para aceder à área **clientes.**
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Área de clientes do Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. Selecione o seu cliente e, em seguida, **selecione Adicionar produtos**.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Centro de Parceiros Microsoft" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. Selecione **O plano Azure** e, em seguida, selecione **Adicionar ao carrinho**. 
   
   1. Reveja e termine a configuração geral da subscrição do plano Azure para o seu cliente. Para obter mais informações, consulte [a documentação do Microsoft Partner Center](/partner-center/azure-plan-manage).

1. Depois de configurar o plano Azure e tiver as [permissões Azure RBAC necessárias](/partner-center/azure-plan-manage) para a subscrição, irá solicitar a quota para a sua subscrição do plano Azure. 

   1. Aceder ao portal Azure do [Microsoft Partner Center](https://partner.microsoft.com) utilizando o procedimento **Admin On Behalf Of** (AOBO).
   
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

## <a name="register-the-microsoftavs-resource-provider"></a>Registe-se o fornecedor de recursos **Microsoft.AVS**

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>Passos seguintes

Depois de ativar o recurso e a rede adequada no lugar, pode [criar uma nuvem privada](tutorial-create-private-cloud.md).
