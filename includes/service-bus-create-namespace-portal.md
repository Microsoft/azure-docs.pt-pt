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
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75928040"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Criar um espaço de nomes no portal do Azure
Para começar a utilizar as entidades de mensagens do Service Bus no Azure, tem de, primeiro, criar um espaço de nomes que seja exclusivo em todo o Azure. Um espaço de nomes fornece um contentor de âmbito para abordar os recursos do Service Bus na sua aplicação.

Para criar um espaço de nomes:

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
2. No painel de navegação à esquerda do portal, selecione **+ criar um recurso**, selecione **integração**e, em seguida, selecione **barramento de serviço**.

    ![Criar um recurso-integração de >-> Service Bus](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. Na caixa de diálogo **criar namespace** , execute as seguintes etapas: 
    1. Insira um **nome para o namespace**. O sistema verifica imediatamente a disponibilidade do nome. Para obter uma lista de regras para nomes de namespaces, consulte [criar API REST do namespace](/rest/api/servicebus/create-namespace).
    2. Selecione o tipo de preço (básico, Standard ou Premium) para o namespace. Se você quiser usar [Tópicos e assinaturas](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), escolha Standard ou Premium. Os tópicos/subscrições não são suportadas no escalão de preço Básico.
    3. Se você tiver selecionado o tipo de preço **Premium** , siga estas etapas: 
        1. Especifique o número de **unidades de mensagens**. A camada Premium fornece isolamento de recursos no nível de CPU e memória para que cada carga de trabalho seja executada isoladamente. Esse contêiner de recursos é chamado de unidade de mensagens. Um namespace Premium tem pelo menos uma unidade de mensagens. Você pode selecionar 1, 2 ou 4 unidades de mensagens para cada namespace Premium do barramento de serviço. Para obter mais informações, consulte [mensagens do barramento de serviço Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md).
        2. Especifique se você deseja tornar a zona de namespace **redundante**. A redundância de zona fornece disponibilidade avançada, distribuindo réplicas entre zonas de disponibilidade em uma região sem custo adicional. Para obter mais informações, consulte [zonas de disponibilidade no Azure](../articles/availability-zones/az-overview.md).
    4. Para **assinatura**, escolha uma assinatura do Azure na qual criar o namespace.
    5. Para **grupo de recursos**, escolha um grupo de recursos existente no qual o namespace residirá ou crie um novo.      
    6. Para **local**, escolha a região na qual o namespace deve ser hospedado.
    7. Selecione **Criar**. O sistema cria agora o seu espaço de nomes e ativa o mesmo. Poderá ter de aguardar alguns minutos enquanto o sistema aprovisiona recursos para a sua conta.
   
        ![Create namespace](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Confirme se o namespace do barramento de serviço foi implantado com êxito. Para ver as notificações, selecione o **ícone de sino (alertas)** na barra de ferramentas. Selecione o **nome do grupo de recursos** na notificação, conforme mostrado na imagem. Você vê o grupo de recursos que contém o namespace do barramento de serviço.

    ![Alerta de implantação](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Na página **grupo de recursos** do grupo de recursos, selecione o **namespace do barramento de serviço**. 

    ![Página grupo de recursos – selecione o namespace do barramento de serviço](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. Você verá o home page para o namespace do barramento de serviço. 

    ![Home Page do namespace do barramento de serviço](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>Obter a cadeia de ligação 
A criação de um espaço de nomes gera automaticamente uma regra inicial de Assinatura de Acesso Partilhado (SAS) com um par de chaves primárias e secundárias associado que cada uma concede controlo total sobre todos os aspetos do espaço de nomes. Consulte [autenticação e autorização do barramento de serviço](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) para obter informações sobre como criar regras com direitos mais restritos para remetentes e destinatários regulares. Para copiar as chaves primária e secundária para seu namespace, siga estas etapas: 

1. Clique em **Todos os recursos** e clique no nome do espaço de nomes criado recentemente.
2. Na janela de espaço de nomes, clique em **Políticas de acesso partilhado**.
3. No ecrã **Políticas de acesso partilhado**, clique em **RootManageSharedAccessKey**.
   
    ![connection-info](./media/service-bus-create-namespace-portal/connection-info.png)
4. Na janela **Política: RootManageSharedAccessKey**, clique no botão de copiar junto a **Cadeia de Ligação Primária** para copiar a cadeia de ligação para a sua área de transferência para utilizar mais tarde. Cole este valor no Bloco de Notas ou noutra localização temporária.
   
    ![cadeia-ligação](./media/service-bus-create-namespace-portal/connection-string.png)
5. Repita o passo anterior, copie e cole o valor da **Chave primária** para uma localização temporária para utilizar mais tarde.

<!--Image references-->

