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
ms.openlocfilehash: a95f5ee5105c45ba9e5b1705e83d60bf24b1dc12
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57457981"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Criar um espaço de nomes no portal do Azure
Para começar a utilizar as entidades de mensagens do Service Bus no Azure, tem de, primeiro, criar um espaço de nomes que seja exclusivo em todo o Azure. Um espaço de nomes fornece um contentor de âmbito para abordar os recursos do Service Bus na sua aplicação.

Para criar um espaço de nomes:

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
2. No painel de navegação esquerdo do portal, selecione **+ criar um recurso**, selecione **integração**e, em seguida, selecione **do Service Bus**.

    ![Criar um recurso -> integração -> do Service Bus](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. Na **criar espaço de nomes** caixa de diálogo, siga os passos abaixo: 
    1. Introduza um **nome para o espaço de nomes**. O sistema verifica imediatamente a disponibilidade do nome. Para obter uma lista de regras de nomenclatura de espaços de nomes, consulte [criar API REST do espaço de nomes](/rest/api/servicebus/create-namespace).
    2. Selecione o escalão de preço (básico, Standard ou Premium) para o espaço de nomes. Se quiser usar [tópicos e subscrições](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), escolha o Standard ou Premium. Os tópicos/subscrições não são suportadas no escalão de preço Básico.
    3. Se tiver selecionado o **Premium** escalão de preço siga estes passos: 
        1. Especifique o número de **unidades de mensagens**. O escalão premium fornece isolamento de recursos ao nível da CPU e memória para que cada carga de trabalho seja executado de forma isolada. Este contentor de recurso é chamado de uma unidade de mensagens. Um espaço de nomes premium tem, pelo menos, uma unidade de mensagens. Pode selecionar 1, 2 ou 4 unidades de mensagens para cada espaço de nomes Premium do Service Bus. Para obter mais informações, consulte [mensagens do Service Bus Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md).
        2. Especifique se pretende tornar o espaço de nomes **redundância de zona**. A redundância de zona fornece disponibilidade melhorada através da distribuição réplicas por várias zonas de disponibilidade numa região sem custos adicionais. Para obter mais informações, consulte [zonas de disponibilidade no Azure](../articles/availability-zones/az-overview.md).
    4. Para **subscrição**, escolha uma subscrição do Azure na qual criar o espaço de nomes.
    5. Para **grupo de recursos**, escolha um grupo de recursos existente em que o espaço de nomes em direto ou, crie um novo.      
    6. Para **localização**, selecione a região em que o seu espaço de nomes deverá ser alojado.
    7. Selecione **Criar**. O sistema cria o espaço de nomes e ativa-o. Poderá ter de aguardar alguns minutos enquanto o sistema aprovisiona recursos para a sua conta.
   
        ![Create namespace](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Confirme que o espaço de nomes do barramento de serviço é implementado com êxito. Para ver as notificações, selecione o **ícone de sino (alertas)** na barra de ferramentas. Selecione o **nome do grupo de recursos** na notificação, conforme mostrado na imagem. Ver o grupo de recursos que contém o espaço de nomes do service bus.

    ![Alerta de implementação](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Sobre o **grupo de recursos** página para o seu grupo de recursos, selecione seu **espaço de nomes do service bus**. 

    ![Grupo de recursos página – selecione o seu espaço de nomes do service bus](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. Ver a home page para o espaço de nomes do service bus. 

    ![Home page de seu espaço de nomes do service bus](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>Obter a cadeia de ligação 
A criação de um espaço de nomes gera automaticamente uma regra inicial de Assinatura de Acesso Partilhado (SAS) com um par de chaves primárias e secundárias associado que cada uma concede controlo total sobre todos os aspetos do espaço de nomes. Ver [do Service Bus autenticação e autorização](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) para obter informações sobre como criar regras com mais restrita direitos para remetentes e recetores regulares. Para copiar as chaves primárias e secundárias para o espaço de nomes, siga estes passos: 

1. Clique em **Todos os recursos** e clique no nome do espaço de nomes criado recentemente.
2. Na janela de espaço de nomes, clique em **Políticas de acesso partilhado**.
3. No ecrã **Políticas de acesso partilhado**, clique em **RootManageSharedAccessKey**.
   
    ![connection-info](./media/service-bus-create-namespace-portal/connection-info.png)
4. Na **política: RootManageSharedAccessKey** janela, clique no botão de cópia junto a **cadeia de ligação primária**para copiar a cadeia de ligação para a área de transferência para utilização posterior. Cole este valor no Bloco de Notas ou noutra localização temporária.
   
    ![connection-string](./media/service-bus-create-namespace-portal/connection-string.png)
5. Repita o passo anterior, copie e cole o valor da **Chave primária** para uma localização temporária para utilizar mais tarde.

<!--Image references-->

