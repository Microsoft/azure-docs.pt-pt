---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 7c3887367b6365ea3577bbff6cc19bf34d178ee6
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081554"
---
### <a name="create-a-notification-hub"></a>Criar um hub de notificação 

Nesta secção, cria-se um centro de notificação e configura a autenticação com **a APNS**. Pode utilizar um certificado de pressão p12 ou uma autenticação baseada em fichas. Se quiser utilizar um centro de notificação que já criou, pode saltar para o passo 5.

1. Inscreva-se no [Azure.](https://portal.azure.com)

1. Clique **em Criar um recurso,** em seguida, procurar e escolher O Centro de **Notificação,** em seguida, clique em **Criar**.

1. Atualizar os seguintes campos e, em seguida, clicar **Em Criar**:

    **DETALHES BÁSICOS**  

    **Assinatura:** Escolha a **Subscrição-alvo** da lista de drop-down  
    **Grupo de Recursos:** Criar um novo **Grupo de Recursos** (ou escolher um existente)  

    **DETALHES DO ESPAÇO DE NOME**  

    **Espaço de nome do hub de notificação:** Introduza um nome globalmente único para o espaço de **nomes do Centro de Notificação**  

    > [!NOTE]
    > Certifique-se de que a nova opção **Criar** está selecionada para este campo.

    **DETALHES DO HUB DE NOTIFICAÇÃO**  

    **Centro de Notificação:** Insira um nome para o **Centro de Notificação**  
    **Localização:** Escolha um local adequado da lista de drop-down  
    **Nível de preços:** Mantenha a opção **padrão Grátis**  

    > [!NOTE]
    > A não ser que tenha atingido o número máximo de centros no nível livre.

1. Uma vez que o **Centro de Notificação** tenha sido aprovisionado, navegue para esse recurso.
1. Navegue para o seu novo **Centro de Notificação.**
1. Selecione Políticas de **Acesso** da lista (em **MANAGE).**
1. Tome nota dos valores **do Nome** da Política juntamente com os valores correspondentes da cadeia **de ligação.**
