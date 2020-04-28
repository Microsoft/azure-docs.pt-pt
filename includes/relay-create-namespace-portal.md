---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76021295"
---
1. Inicie sessão no [portal do Azure][Azure portal].
1. Selecione **Criar um recurso**. Em seguida, selecione**Retransmissão**de **Integração** > . Se não vir **Reencaminhamento** na lista, selecione **Ver Tudo** no canto superior direito.
1. Selecione **Criar**e introduza um nome de espaço de nome no campo **Nome.** O portal Azure verifica se o nome está disponível.
1. Escolha uma subscrição Azure para criar o espaço de nome.
1. Para [o grupo Recursos,](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)escolha um grupo de recursos existente para colocar o espaço de nome, ou criar um novo.  
1. Selecione o país ou região em que o seu espaço de nome deve ser hospedado.

    ![Create namespace][create-namespace]

1. Selecione **Criar**. O portal Azure cria o seu espaço de nome e permite-o. Após alguns minutos, o sistema aprovisiona recursos para a sua conta.

### <a name="get-management-credentials"></a>Obter credenciais de gestão

1. **Selecione Todos os recursos**e, em seguida, escolha o nome de espaço de nome recém-criado.
1. Selecione políticas de **acesso partilhado**.  
1. Em **Políticas de acesso partilhado**, selecione **RootManageSharedAccessKey**.
1. Sob **a política SAS: RootManageSharedAccessKey,** selecione o botão **Copiar** ao lado da cadeia **de ligação primária**. Esta ação copia a cadeia de ligação à sua prancheta para posterior utilização. Cole este valor no Bloco de Notas ou noutra localização temporária.
1. Repita o passo anterior para copiar e colar o valor da **Chave primária** para uma localização temporária para utilizar mais tarde.  

    ![cadeia-ligação][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
