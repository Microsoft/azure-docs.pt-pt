---
title: Utilize o controlo de acesso baseado em funções para StorSimple | Documentos da Microsoft
description: Descreve como utilizar o controlo de acesso baseado em funções do Azure (RBAC) no contexto do StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: a79753a897a62e194a759c23a9c0acc45c5f36c1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785389"
---
# <a name="role-based-access-control-for-storsimple"></a>Controlo de acesso baseado em funções para StorSimple

Este artigo fornece uma breve descrição de como o controlo de acesso de controlo (RBAC) pode ser utilizado para o dispositivo StorSimple. RBAC oferece gestão de acessos detalhada para o Azure. Utilize o RBAC para conceder acesso sem restrições apenas a quantidade certa de acesso para os utilizadores do StorSimple para realizar seus trabalhos em vez de dar a todos os utilizadores. Para obter mais informações sobre as noções básicas de gestão de acesso no Azure, consulte [introdução ao controlo de acesso baseado em funções no portal do Azure](../role-based-access-control/overview.md).

Este artigo aplica-se a dispositivos de série 8000 do StorSimple com o Update 3.0 ou mais tarde no portal do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>Funções do RBAC para o StorSimple

RBAC pode ser atribuído com base nas funções. As funções de garantir que determinados níveis de permissão com base nos recursos disponíveis no ambiente. Existem dois tipos de funções de que os utilizadores do StorSimple podem escolher de entre: internas ou personalizadas.

* **Funções incorporadas** -funções incorporadas podem ser proprietário, Contribuidor, leitor ou administrador de acesso do utilizador. Para obter mais informações, consulte [funções incorporadas para controlo de acesso baseado em funções do Azure](../role-based-access-control/built-in-roles.md).

* **Funções personalizadas** -se as funções incorporadas não satisfazer as suas necessidades, pode criar funções personalizadas do RBAC para o StorSimple. Para criar uma função RBAC personalizada, começar com uma função incorporada, editá-lo e, em seguida, importá-lo novamente no ambiente. A transferência e o carregamento da função são geridas com o Azure PowerShell ou a CLI do Azure. Para obter mais informações, consulte [criar funções personalizadas para controlo de acesso baseado em funções](../role-based-access-control/custom-roles.md).

Para ver as diferentes funções disponíveis para um utilizador do dispositivo StorSimple no portal do Azure, aceda ao seu serviço StorSimple Device Manager e, em seguida, aceda a **controlo de acesso (IAM) > funções**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Criar uma função personalizada para o administrador de infraestrutura do StorSimple

No exemplo a seguir, vamos começar com a função incorporada **leitor** que permite aos utilizadores para ver todos os âmbitos de recursos, mas não editá-los ou criar novos. Vamos expandir, em seguida, esta função para criar uma nova função personalizada administrador de infraestrutura do StorSimple. Esta função é atribuída a utilizadores que podem gerir a infraestrutura para os dispositivos do StorSimple.

1. Execute o Windows PowerShell como administrador.

2. Iniciar sessão no Azure.

    `Connect-AzAccount`

3. Exporte a função de leitor como um modelo JSON no seu computador.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Abra o ficheiro JSON no Visual Studio. Verá que uma função RBAC típica consiste em três seções principais, **ações**, **NotActions**, e **AssignableScopes**.

    Na **ação** secção, todas as operações permitidas para esta função estão listadas. Cada ação é atribuída a partir de um fornecedor de recursos. Para um administrador de infraestrutura do StorSimple, utilize o `Microsoft.StorSimple` fornecedor de recursos.

    Utilize o PowerShell para ver todos os fornecedores de recursos disponíveis e registados na sua subscrição.

    `Get-AzResourceProvider`

    Também pode procurar os cmdlets do PowerShell disponíveis gerir os fornecedores de recursos.

    Na **NotActions** secções, todas as ações restritas por determinada função RBAC estão listadas. Neste exemplo, não existem ações são restritas.
    
    Sob o **AssignableScopes**, são listados os IDs de subscrição. Certifique-se de que a função RBAC contém o ID de subscrição explícita em que é utilizada. Se não for especificado o ID de subscrição corretas, não tem permissão para importar a função na sua subscrição.

    Edite o ficheiro tendo em mente as considerações de anteriores.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importe a função RBAC personalizada de volta para o ambiente.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Esta função deverá agora aparecer na lista de funções no **controlo de acesso** painel.

![Funções RBAC do Vista](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Para obter mais informações, aceda a [funções personalizadas](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Saída de exemplo para criação da função personalizada através do PowerShell

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Adicionar utilizadores à função personalizada

Pode conceder acesso de dentro do recurso, grupo de recursos ou subscrição, que é o âmbito da atribuição de função. Ao fornecer acesso, lembre-se de que o acesso concedido no nó principal é herdada pelo elemento subordinado. Para obter mais informações, aceda a [controlo de acesso baseado em funções](../role-based-access-control/overview.md).

1. Aceda a **controlo de acesso (IAM)**. Clique em **+ adicionar** no painel de controlo de acesso.

    ![Adicionar acesso à função RBAC](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Selecione a função que deseja atribuir, neste caso é o **administrador de infraestrutura do StorSimple**.

3. Selecione o utilizador, grupo ou aplicação no diretório para o qual pretende dar acesso. Pode procurar o diretório com os nomes a apresentar, endereços de correio eletrónico e identificadores de objetos.

4. Selecione **guardar** para criar a atribuição.

    ![Adicionar permissões à função RBAC](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Uma **adicionar utilizador** notificação controla o progresso. Depois do utilizador é adicionado com êxito, a lista de utilizadores no controlo de acesso é atualizada.

## <a name="view-permissions-for-the-custom-role"></a>Permissões de visualização para a função personalizada

Assim que esta função é criada, pode ver as permissões associadas esta função no portal do Azure.

1. Para ver as permissões associadas esta função, aceda ao **controlo de acesso (IAM) > funções > administrador de infraestrutura do StorSimple**. É apresentada a lista de utilizadores nesta função.

2. Selecione um utilizador de administrador de infraestrutura do StorSimple e clique em **permissões**.

    ![Permissões de visualização para a função de administrador infra-estrutura do StorSimple](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. São apresentadas as permissões associadas esta função.

    ![Ver utilizadores na função de administrador infra-estrutura do StorSimple](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Passos Seguintes

Saiba como [atribuir funções personalizadas para utilizadores internos e externos](../role-based-access-control/role-assignments-external-users.md).
