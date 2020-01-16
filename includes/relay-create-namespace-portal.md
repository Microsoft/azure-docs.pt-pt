---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021295"
---
1. Inicie sessão no [Portal do Azure][Azure portal].
1. Selecione **criar um recurso**. Em seguida, selecione **Integração** > **Reencaminhamento**. Se não vir **Reencaminhamento** na lista, selecione **Ver Tudo** no canto superior direito.
1. Selecione **criar**e insira um nome de namespace no campo **nome** . Portal do Azure verifica se o nome está disponível.
1. Escolha uma assinatura do Azure na qual criar o namespace.
1. Para [grupo de recursos](../articles/azure-resource-manager/management/manage-resource-groups-portal.md), escolha um grupo de recursos existente no qual o namespace será colocado ou crie um novo.  
1. Selecione o país ou região no qual o namespace deve ser hospedado.

    ![Create namespace][create-namespace]

1. Selecione **Criar**. O portal do Azure cria o namespace e o habilita. Após alguns minutos, o sistema aprovisiona recursos para a sua conta.

### <a name="get-management-credentials"></a>Obter credenciais de gestão

1. Selecione **todos os recursos**e, em seguida, escolha o nome do namespace recém-criado.
1. Selecione **políticas de acesso compartilhado**.  
1. Em **Políticas de acesso partilhado**, selecione **RootManageSharedAccessKey**.
1. Em **política SAS: RootManageSharedAccessKey**, selecione o botão **copiar** ao lado da **cadeia de conexão primária**. Essa ação copia a cadeia de conexão para a área de transferência para uso posterior. Cole este valor no Bloco de Notas ou noutra localização temporária.
1. Repita o passo anterior para copiar e colar o valor da **Chave primária** para uma localização temporária para utilizar mais tarde.  

    ![cadeia-ligação][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
