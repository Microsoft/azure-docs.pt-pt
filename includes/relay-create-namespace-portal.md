---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: bbcf38ab54632144920b729f433c222f426d96dc
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825318"
---
1. Inicie sessão no [Portal do Azure][Azure portal].
2. No menu do lado esquerdo, selecione **+ Criar um recurso**. Em seguida, selecione **Integração** > **Reencaminhamento**. Se não vir **Reencaminhamento** na lista, selecione **Ver Tudo** no canto superior direito. 
3. Em **Criar espaço de nomes**, introduza um nome de espaço de nomes. O sistema verifica imediatamente a disponibilidade do nome.
4. Na caixa **Subscrição**, selecione a subscrição do Azure em que pretende criar o espaço de nomes.
5. Na caixa [Grupo de recursos](../articles/azure-resource-manager/manage-resource-groups-portal.md), selecione um grupo de recursos existente em que o espaço de nomes será colocado ou crie um novo.  
6. Em **Localização**, selecione o país ou a região em que o espaço de nomes deverá ser alojado.
   
    ![Create namespace][create-namespace]
7. Selecione **Criar**. O sistema cria o seu espaço de nomes e ativa o mesmo. Após alguns minutos, o sistema aprovisiona recursos para a sua conta.

### <a name="get-management-credentials"></a>Obter credenciais de gestão

1. Selecione **Todos os recursos** e, em seguida, selecione o nome do espaço de nomes criado recentemente.
2. No espaço de nomes Reencaminhamento, clique em **Políticas de acesso partilhado**.  
3. Em **Políticas de acesso partilhado**, selecione **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. Em **política: RootManageSharedAccessKey**, selecione o **cópia** junto a **ligação cadeia de caracteres – chave primária**. Esta ação copia a cadeia de ligação para a sua área de transferência, para que possa utilizá-la posteriormente. Cole este valor no Bloco de Notas ou noutra localização temporária.
   
    ![connection-string][connection-string]

5. Repita o passo anterior para copiar e colar o valor da **Chave primária** para uma localização temporária para utilizar mais tarde.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
