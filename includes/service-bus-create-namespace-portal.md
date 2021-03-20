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
ms.openlocfilehash: d2c309340155bc626d4da94d74aee9be51bde510
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90606287"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Criar um espaço de nomes no portal do Azure
Para começar a utilizar as entidades de mensagens do Service Bus no Azure, tem de, primeiro, criar um espaço de nomes que seja exclusivo em todo o Azure. Um espaço de nomes fornece um contentor de âmbito para abordar os recursos do Service Bus na sua aplicação.

Para criar um espaço de nomes:

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
2. No painel de navegação à esquerda do portal, selecione **+ Crie um recurso,** selecione **Integração** e, em seguida, selecione **Service Bus**.

    ![Criar um ônibus de serviço de integração > -> recursos](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. No diálogo do espaço de **nome Create,** faça os seguintes passos: 
    1. Insira um **nome para o espaço de nomes**. O sistema verifica imediatamente a disponibilidade do nome. Para obter uma lista de regras para nomear espaços de nomeação, consulte [Create Namespace REST API](/rest/api/servicebus/create-namespace).
    2. Selecione o nível de preços (Básico, Standard ou Premium) para o espaço de nome. Se quiser utilizar [tópicos e subscrições,](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)escolha Standard ou Premium. Os tópicos/subscrições não são suportadas no escalão de preço Básico.
    3. Se selecionou o nível de preços **Premium,** siga estes passos: 
        1. Especificar o número de **unidades de mensagens.** O nível premium proporciona isolamento de recursos ao nível da CPU e da memória para que cada carga de trabalho seja isolada. Este contentor de recursos é designado por unidade de mensagens. Um espaço de nome premium tem pelo menos uma unidade de mensagens. Pode selecionar 1, 2 ou 4 unidades de mensagens para cada espaço de nome Service Bus Premium. Para mais informações, consulte [service bus Premium Messaging.](../articles/service-bus-messaging/service-bus-premium-messaging.md)
        2. Especifique se pretende tornar a zona de espaço de nome **redundante**. A redundância da zona proporciona uma maior disponibilidade, espalhando réplicas através de zonas de disponibilidade dentro de uma região sem custos adicionais. Para mais informações, consulte [as zonas de disponibilidade em Azure.](../articles/availability-zones/az-overview.md)
    4. Para **Subscrição**, escolha uma subscrição Azure para criar o espaço de nome.
    5. Para **o grupo de recursos,** escolha um grupo de recursos existente no qual o espaço de nomes viverá ou crie um novo.      
    6. Para **localização,** escolha a região em que o seu espaço de nome deve ser hospedado.
    7. Selecione **Criar**. O sistema cria agora o seu espaço de nomes e ativa o mesmo. Poderá ter de aguardar alguns minutos enquanto o sistema aprovisiona recursos para a sua conta.
   
        ![Create namespace](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Confirme se o espaço de nome do autocarro de serviço é implantado com sucesso. Para ver as notificações, selecione o ícone da **campainha (Alertas)** na barra de ferramentas. Selecione o **nome do grupo de recursos** na notificação como mostrado na imagem. Você vê o grupo de recursos que contém o espaço de nome do ônibus de serviço.

    ![Alerta de implantação](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Na página do **grupo Recursos** para o seu grupo de recursos, selecione o seu espaço de nome **de ônibus de serviço**. 

    ![Página de grupo de recursos - selecione o seu espaço de nome de ônibus de serviço](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. Você vê a página inicial para o seu espaço de nome de ônibus de serviço. 

    ![Página inicial para o seu espaço de nome de ônibus de serviço](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>Obter a cadeia de ligação 
A criação de um espaço de nomes gera automaticamente uma regra inicial de Assinatura de Acesso Partilhado (SAS) com um par de chaves primárias e secundárias associado que cada uma concede controlo total sobre todos os aspetos do espaço de nomes. Consulte [a autenticação do Service Bus e a autorização](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) para obter informações sobre como criar regras com direitos mais restritos para remetentes e recetores regulares. Para copiar as chaves primárias e secundárias para o seu espaço de nome, siga estes passos: 

1. Clique em **Todos os recursos** e clique no nome do espaço de nomes criado recentemente.
2. Na janela de espaço de nomes, clique em **Políticas de acesso partilhado**.
3. No ecrã **Políticas de acesso partilhado**, clique em **RootManageSharedAccessKey**.
   
    ![O Screenshot mostra a janela das políticas de acesso partilhado com uma política realçada.](./media/service-bus-create-namespace-portal/connection-info.png)
4. Na janela **Política: RootManageSharedAccessKey**, clique no botão de copiar junto a **Cadeia de Ligação Primária** para copiar a cadeia de ligação para a sua área de transferência para utilizar mais tarde. Cole este valor no Bloco de Notas ou noutra localização temporária.
   
    ![O Screenshot mostra uma política S A S chamada RootManageSharedAccessKey, que inclui chaves e cordas de conexão.](./media/service-bus-create-namespace-portal/connection-string.png)
5. Repita o passo anterior, copie e cole o valor da **Chave primária** para uma localização temporária para utilizar mais tarde.

<!--Image references-->

