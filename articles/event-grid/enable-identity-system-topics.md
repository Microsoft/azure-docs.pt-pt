---
title: Ativar a identidade gerida no tópico do sistema Azure Event Grid
description: Este artigo descreve como permitir a identidade de serviço gerida para um tópico do sistema Azure Event Grid.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: d8219cf9cba4ce0a4fb5ddbcf695f1ec72de36fe
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280518"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Atribua uma identidade gerida pelo sistema a um tópico do sistema de grelha de eventos
Neste artigo, aprende-se a permitir a identidade gerida pelo sistema para um tópico de sistema de Grade de Eventos existente. Para conhecer as identidades geridas, veja [o que são identidades geridas para os recursos da Azure.](../active-directory/managed-identities-azure-resources/overview.md)  

> [!IMPORTANT]
> Atualmente, não é possível ativar uma identidade gerida pelo sistema ao criar um novo tópico do sistema, ou seja, ao criar uma subscrição de eventos num recurso Azure que suporte tópicos do sistema. 


## <a name="use-azure-portal"></a>Utilizar o portal do Azure
O procedimento que se segue mostra como ativar a identidade gerida pelo sistema para um tópico do sistema. 

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Procure **tópicos do sistema de grelha de eventos** na barra de pesquisa no topo.
3. Selecione o tópico do **sistema** para o qual deseja ativar a identidade gerida. 
4. Selecione **Identidade** no menu esquerdo. Não se vê esta opção para um tópico de sistema que está na localização global. 
5. Ligue  o interruptor para ativar a identidade. 
1. **Selecione Guarde** na barra de ferramentas para guardar a definição. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="Página de identidade para um tópico do sistema"::: 
1. Selecione **Sim** na mensagem de confirmação. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Atribuir identidade a um tópico do sistema - confirmação"::: 
1. Confirme que vê o ID do objeto da identidade gerida atribuída pelo sistema e veja um link para atribuir funções. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Atribuir identidade a um tópico do sistema - concluído"::: 

## <a name="global-azure-sources"></a>Fontes globais do Azure
Você pode ativar a identidade gerida pelo sistema apenas para os recursos regionais Azure. Não é possível capacitá-lo para tópicos do sistema associados a recursos globais do Azure, como subscrições Azure, grupos de recursos ou Azure Maps. Os tópicos do sistema para estas fontes globais também não estão associados a uma região específica. Não se vê a página **identidade** para o tópico do sistema cuja localização está definida para a **Global**. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="Tópico do sistema com localização definida para a Global"::: 



## <a name="next-steps"></a>Passos seguintes
Adicione a identidade a um papel apropriado (por exemplo, Service Bus Data Sender) no destino (por exemplo, uma fila de autocarros de serviço). Para etapas detalhadas, consulte [grant geriu a identidade o acesso ao destino Event Grid](add-identity-roles.md). 