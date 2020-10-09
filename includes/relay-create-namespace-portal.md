---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76021295"
---
1. Inicie sessão no [Portal do Azure][Azure portal].
1. Selecione **Criar um recurso**. Em seguida, selecione **Integration**  >  **Integration Relay**. Se não vir **Reencaminhamento** na lista, selecione **Ver Tudo** no canto superior direito.
1. **Selecione Criar**e insira um nome de espaço no campo **Nome.** O portal Azure verifica se o nome está disponível.
1. Escolha uma subscrição Azure para criar o espaço de nome.
1. Para [o grupo de recursos,](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)escolha um grupo de recursos existente para colocar o espaço de nomes ou criar um novo.  
1. Selecione o país ou região em que o seu espaço de nome deve ser hospedado.

    ![Create namespace][create-namespace]

1. Selecione **Criar**. O portal Azure cria o seu espaço de nome e permite-o. Após alguns minutos, o sistema aprovisiona recursos para a sua conta.

### <a name="get-management-credentials"></a>Obter credenciais de gestão

1. Selecione **Todos os recursos**e, em seguida, escolha o nome de espaço de nome recém-criado.
1. Selecione **políticas de acesso compartilhado**.  
1. Em **Políticas de acesso partilhado**, selecione **RootManageSharedAccessKey**.
1. Sob **a política sas: RootManageSharedAccessKey**, selecione o botão **Copy** ao lado da Cadeia **de Ligação Primária**. Esta ação copia o fio de ligação à sua área de transferência para utilização posterior. Cole este valor no Bloco de Notas ou noutra localização temporária.
1. Repita o passo anterior para copiar e colar o valor da **Chave primária** para uma localização temporária para utilizar mais tarde.  

    ![cadeia-ligação][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
