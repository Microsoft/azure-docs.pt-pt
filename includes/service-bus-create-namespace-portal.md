---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dc80141d796b66dd7e610342166f7b88df58f530
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75928040"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Criar um espaço de nomes no portal do Azure
Para começar a utilizar as entidades de mensagens do Service Bus no Azure, tem de, primeiro, criar um espaço de nomes que seja exclusivo em todo o Azure. Um espaço de nomes fornece um contentor de âmbito para abordar os recursos do Service Bus na sua aplicação.

Para criar um espaço de nomes:

1. Inscreva-se no [portal Azure](https://portal.azure.com)
2. No painel de navegação à esquerda do portal, selecione **+ Crie um recurso,** selecione **Integração,** e, em seguida, selecione **Service Bus**.

    ![Criar um recurso -> Integration -> Service Bus](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. No diálogo Criar espaço de **nome,** faça os seguintes passos: 
    1. Introduza um **nome para o espaço de nome**. O sistema verifica imediatamente a disponibilidade do nome. Para obter uma lista de regras para nomear espaços de nome, consulte [Create Namespace REST API](/rest/api/servicebus/create-namespace).
    2. Selecione o nível de preços (Básico, Standard ou Premium) para o espaço de nome. Se quiser utilizar [tópicos e subscrições,](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)escolha standard ou Premium. Os tópicos/subscrições não são suportadas no escalão de preço Básico.
    3. Se selecionou o nível de preços **Premium,** siga estes passos: 
        1. Especifique o número de unidades de **mensagens**. O nível premium proporciona isolamento de recursos ao nível da CPU e da memória para que cada carga de trabalho seja isolada. Este contentor de recursos é designado por unidade de mensagens. Um espaço de nome premium tem pelo menos uma unidade de mensagens. Pode selecionar 1, 2 ou 4 unidades de mensagens para cada espaço de nome Service Bus Premium. Para mais informações, consulte [Service Bus Premium Messaging](../articles/service-bus-messaging/service-bus-premium-messaging.md).
        2. Especifique se pretende tornar a zona espaço de nome **redundante**. O despedimento da zona proporciona uma maior disponibilidade espalhando réplicas através de zonas de disponibilidade dentro de uma região sem custos adicionais. Para mais informações, consulte [zonas de disponibilidade em Azure](../articles/availability-zones/az-overview.md).
    4. Para **Subscrição,** escolha uma subscrição Azure para criar o espaço de nome.
    5. Para **o grupo Recursos,** escolha um grupo de recursos existente no qual o espaço de nome sairá ou crie um novo.      
    6. Para **Localização**, escolha a região em que o seu espaço de nome deve ser hospedado.
    7. Selecione **Criar**. O sistema cria agora o seu espaço de nomes e ativa o mesmo. Poderá ter de aguardar alguns minutos enquanto o sistema aprovisiona recursos para a sua conta.
   
        ![Create namespace](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Confirme que o espaço de nome do ônibus de serviço é implantado com sucesso. Para ver as notificações, selecione o ícone do **sino (Alertas)** na barra de ferramentas. Selecione o **nome do grupo de recursos** na notificação, como mostrado na imagem. Você vê o grupo de recursos que contém o espaço de nome do ônibus de serviço.

    ![Alerta de implantação](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Na página do **grupo Recursos** para o seu grupo de recursos, selecione o seu espaço de nome do autocarro **de serviço**. 

    ![Página do grupo de recursos - selecione o seu espaço de nome do autocarro de serviço](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. Você vê a página inicial para o seu espaço de nome de ônibus de serviço. 

    ![Página inicial para o seu espaço de nome de ônibus de serviço](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>Obter a cadeia de ligação 
A criação de um espaço de nomes gera automaticamente uma regra inicial de Assinatura de Acesso Partilhado (SAS) com um par de chaves primárias e secundárias associado que cada uma concede controlo total sobre todos os aspetos do espaço de nomes. Consulte a [autenticação do Ônibus de serviço e a autorização](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) para obter informações sobre como criar regras com direitos mais limitados para remetentes e recetores regulares. Para copiar as chaves primárias e secundárias para o seu espaço de nome, siga estes passos: 

1. Clique em **Todos os recursos** e clique no nome do espaço de nomes criado recentemente.
2. Na janela de espaço de nomes, clique em **Políticas de acesso partilhado**.
3. No ecrã **Políticas de acesso partilhado**, clique em **RootManageSharedAccessKey**.
   
    ![informações-ligação](./media/service-bus-create-namespace-portal/connection-info.png)
4. Na janela **Política: RootManageSharedAccessKey**, clique no botão de copiar junto a **Cadeia de Ligação Primária** para copiar a cadeia de ligação para a sua área de transferência para utilizar mais tarde. Cole este valor no Bloco de Notas ou noutra localização temporária.
   
    ![cadeia-ligação](./media/service-bus-create-namespace-portal/connection-string.png)
5. Repita o passo anterior, copie e cole o valor da **Chave primária** para uma localização temporária para utilizar mais tarde.

<!--Image references-->

