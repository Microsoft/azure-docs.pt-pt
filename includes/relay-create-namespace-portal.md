---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 3e3617f68d58e51f24affaae7d69812195b72fd0
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71210310"
---
1. Inicie sessão no [portal do Azure][Azure portal].
1. Selecione **criar um recurso**. Em seguida, selecione **Integração** > **Reencaminhamento**. Se não vir **Reencaminhamento** na lista, selecione **Ver Tudo** no canto superior direito.
1. Selecione **criar**e insira um nome de namespace no campo **nome** . Portal do Azure verifica se o nome está disponível.
1. Escolha uma assinatura do Azure na qual criar o namespace.
1. Para [grupo de recursos](../articles/azure-resource-manager/manage-resource-groups-portal.md), escolha um grupo de recursos existente no qual o namespace será colocado ou crie um novo.  
1. Selecione o país ou região no qual o namespace deve ser hospedado.

    ![Criar espaço de nomes][create-namespace]

1. Selecione **Criar**. O portal do Azure cria o namespace e o habilita. Após alguns minutos, o sistema aprovisiona recursos para a sua conta.

### <a name="get-management-credentials"></a>Obter credenciais de gestão

1. Selecione **todos os recursos**e, em seguida, escolha o nome do namespace recém-criado.
1. Selecione **políticas de acesso compartilhado**.  
1. Em **Políticas de acesso partilhado**, selecione **RootManageSharedAccessKey**.
1. Em **política SAS: RootManageSharedAccessKey**, selecione o botão **copiar** ao lado da **cadeia de conexão primária**. Essa ação copia a cadeia de conexão para a área de transferência para uso posterior. Cole este valor no Bloco de Notas ou noutra localização temporária.
1. Repita o passo anterior para copiar e colar o valor da **Chave primária** para uma localização temporária para utilizar mais tarde.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
