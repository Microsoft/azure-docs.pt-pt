---
title: Utilize o controlo de acesso baseado em funções Azure para | StorSimple Microsoft Docs
description: Descreve como utilizar o controlo de acesso baseado em funções Azure (Azure RBAC) no contexto do StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: 49c38e23ddbbfe983ff82ad25363c744292d4d69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518981"
---
# <a name="azure-role-based-access-control-for-storsimple"></a>Controlo de acesso baseado em funções Azure para StorSimple

Este artigo fornece uma breve descrição de como o controlo de acesso baseado em funções Azure (Azure RBAC) pode ser usado para o seu dispositivo StorSimple. O Azure RBAC oferece uma gestão de acessos finos para o Azure. Utilize o RBAC Azure para conceder a quantidade certa de acesso aos utilizadores StorSimple para fazer o seu trabalho em vez de dar a todos acesso ilimitado. Para obter mais informações sobre os fundamentos da gestão de acessos em Azure, consulte [o que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md).

Este artigo aplica-se aos dispositivos da série StorSimple 8000 que executam a Atualização 3.0 ou mais tarde no portal Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-roles-for-storsimple"></a>Funções de Azure para StorSimple

O Azure RBAC pode ser atribuído com base nas funções. As funções garantem certos níveis de permissão baseados nos recursos disponíveis no ambiente. Existem dois tipos de funções que os utilizadores StorSimple podem escolher: incorporados ou personalizados.

* **Funções incorporadas** - As funções incorporadas podem ser proprietárias, contribuintes, leitor ou administrador de acesso ao utilizador. Para obter mais informações, consulte [as funções incorporadas para o Controlo de Acesso baseado em funções Azure.](../role-based-access-control/built-in-roles.md)

* **Funções personalizadas** - Se as funções incorporadas não se adequarem às suas necessidades, pode criar funções personalizadas Azure para storSimple. Para criar um papel personalizado Azure, comece com um papel incorporado, edite-o e, em seguida, importe-o de volta no ambiente. O download e o upload da função são geridos usando a Azure PowerShell ou o Azure CLI. Para obter mais informações, consulte [Criar funções personalizadas para controlo de acesso baseado em funções.](../role-based-access-control/custom-roles.md)

Para visualizar as diferentes funções disponíveis para um utilizador de dispositivo StorSimple no portal Azure, vá ao seu serviço StorSimple Device Manager e, em seguida, vá para **o Controle de Acesso (IAM) > Roles**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Criar um papel personalizado para administrador de infraestrutura StorSimple

No exemplo seguinte, começamos com a função incorporada **Reader** que permite aos utilizadores visualizar todos os âmbitos de recursos, mas não editá-los ou criar novos. Em seguida, alargamos este papel para criar um novo papel personalizado StorSimple Infrastructure administrador. Esta função é atribuída a utilizadores que possam gerir a infraestrutura para os dispositivos StorSimple.

1. Executar o Windows PowerShell como administrador.

2. Iniciar sessão no Azure.

    `Connect-AzAccount`

3. Exporte o papel de Leitor como modelo JSON no seu computador.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Abra o ficheiro JSON no Estúdio Visual. Você vê que um papel típico de Azure consiste em três secções principais, **Ações**, **NotActions** e **AssignableScopes**.

    Na secção **Ação,** todas as operações permitidas para esta função estão listadas. Cada ação é atribuída a um fornecedor de recursos. Para um administrador de infraestrutura StorSimple, utilize o `Microsoft.StorSimple` fornecedor de recursos.

    Utilize o PowerShell para ver todos os fornecedores de recursos disponíveis e registados na sua subscrição.

    `Get-AzResourceProvider`

    Também pode verificar se todos os cmdlets powershell disponíveis para gerir os fornecedores de recursos.

    Nas secções **NotActions,** todas as ações restritas para um papel particular do Azure estão listadas. Neste exemplo, não são restringidas as ações.
    
    Nos **'Atribui-se',** os IDs de subscrição estão listados. Certifique-se de que o papel Azure contém o ID de subscrição explícito onde é utilizado. Se o ID de subscrição correto não for especificado, não está autorizado a importar o papel na sua subscrição.

    Edite o ficheiro tendo em mente as considerações anteriores.

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

6. Importe o papel personalizado Azure de volta para o ambiente.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Esta função deve agora figurar na lista de funções na lâmina **de controlo Access.**

![Ver papéis de Azure](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Para mais informações, aceda a [funções personalizadas.](../role-based-access-control/custom-roles.md)

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Saída de amostra para criação de função personalizada através do PowerShell

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

## <a name="add-users-to-the-custom-role"></a>Adicione os utilizadores ao papel personalizado

Pode conceder acesso de dentro do recurso, grupo de recursos ou subscrição, que é o âmbito da atribuição de função. Ao fornecer acesso, tenha em mente que o acesso concedido no nó principal é herdado pela criança. Para obter mais informações, aceda ao [controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md).

1. Ir ao **controlo de acesso (IAM)**. Clique **+ Adicione** na lâmina de controlo de acesso.

    ![Adicionar acesso ao papel de Azure](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Selecione o papel que pretende atribuir, neste caso é o **Administrador de Infraestrutura StorSimple.**

3. Selecione o utilizador, grupo ou aplicação no diretório para o qual pretende dar acesso. Pode procurar o diretório com os nomes a apresentar, endereços de correio eletrónico e identificadores de objetos.

4. **Selecione Guardar** para criar a atribuição.

    ![Adicionar permissões ao papel de Azure](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Uma notificação **de utilizador adicionando** rastreia o progresso. Após a adição do utilizador com sucesso, a lista de utilizadores no controlo access é atualizada.

## <a name="view-permissions-for-the-custom-role"></a>Ver permissões para o papel personalizado

Uma vez criada esta função, pode ver as permissões associadas a este papel no portal Azure.

1. Para visualizar as permissões associadas a esta função, vá ao **Access control (IAM) > Roles > StorSimple Infrastructure Admin**. A lista de utilizadores nesta função é apresentada.

2. Selecione um utilizador StorSimple Infrastructure Admin e clique em **Permissões**.

    ![Ver permissões para papel de Infra Administrador StorSimple](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. As permissões associadas a esta função são apresentadas.

    ![Ver utilizadores na função de Infra Administrador StorSimple](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Passos seguintes

Saiba como [atribuir funções personalizadas para utilizadores internos e externos.](../role-based-access-control/role-assignments-external-users.md)
