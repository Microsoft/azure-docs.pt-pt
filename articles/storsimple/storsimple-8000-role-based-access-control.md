---
title: Utilizar o Controlo de Acesso baseado em funções para o StorSimple [ StorSimple] Microsoft Docs
description: Descreve como utilizar o Controlo de Acesso baseado em Papel Azure (RBAC) no contexto do StorSimple.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "66159138"
---
# <a name="role-based-access-control-for-storsimple"></a>Controlo de Acesso baseado em funções para StorSimple

Este artigo fornece uma breve descrição de como o Controlo de Acesso baseado em Funções Azure (RBAC) pode ser usado para o seu dispositivo StorSimple. O RBAC oferece uma gestão de acesso de grãos finos para o Azure. Utilize o RBAC para conceder a quantidade certa de acesso aos utilizadores StorSimple para fazer em vez de dar a todos acesso sem restrições. Para obter mais informações sobre os fundamentos da gestão de acessos em Azure, consulte [Iniciar-se com o Controlo de Acesso baseado em Funções no portal Azure.](../role-based-access-control/overview.md)

Este artigo aplica-se aos dispositivos da série StorSimple 8000 que executam o Update 3.0 ou mais tarde no portal Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>Funções RBAC para StorSimple

O RBAC pode ser atribuído com base nas funções. As funções asseguram certos níveis de permissão com base nos recursos disponíveis no ambiente. Existem dois tipos de funções que os utilizadores do StorSimple podem escolher: incorporados ou personalizados.

* **Funções incorporadas** - As funções incorporadas podem ser donos, colaboradores, leitores ou administradores de acesso ao utilizador. Para mais informações, consulte [as funções incorporadas para](../role-based-access-control/built-in-roles.md)o Controlo de Acesso baseado em Papel Azure .

* **Funções personalizadas** - Se as funções incorporadas não se adequarem às suas necessidades, pode criar funções RBAC personalizadas para o StorSimple. Para criar um papel RBAC personalizado, comece com um papel incorporado, edite-o e, em seguida, importe-o de volta para o ambiente. O download e upload da função são geridos usando o Azure PowerShell ou o Azure CLI. Para mais informações, consulte [Criar funções personalizadas para controlo de acesso baseado em funções.](../role-based-access-control/custom-roles.md)

Para ver as diferentes funções disponíveis para um utilizador de dispositivoS StorSimple no portal Azure, aceda ao serviço StorSimple Device Manager e depois vá ao Controle de **Acesso (IAM) > Funções**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Criar uma função personalizada para administrador de infraestrutura StorSimple

No exemplo seguinte, começamos com o papel incorporado **Reader** que permite que os utilizadores vejam todos os âmbitos de recursos, mas não editem ou criem novos. Em seguida, estendemos este papel para criar um novo papel personalizado StorSimple Infrastructure administrador. Esta função é atribuída aos utilizadores que possam gerir a infraestrutura para os dispositivos StorSimple.

1. Executar o Windows PowerShell como administrador.

2. Iniciar sessão no Azure.

    `Connect-AzAccount`

3. Exporte o papel do Leitor como modelo JSON no seu computador.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Abra o ficheiro JSON no Estúdio Visual. Você vê que uma função RBAC típica consiste em três secções principais, **Ações,** **NotActions**e **Scopescopes Atribuindo.**

    Na secção **ação,** estão enumeradas todas as operações permitidas para este papel. Cada ação é atribuída a um fornecedor de recursos. Para um administrador de infraestrutura `Microsoft.StorSimple` StorSimple, utilize o fornecedor de recursos.

    Utilize o PowerShell para ver todos os fornecedores de recursos disponíveis e registados na sua subscrição.

    `Get-AzResourceProvider`

    Também pode verificar se todos os cmdlets PowerShell disponíveis para gerir os fornecedores de recursos.

    Nas secções **NotActions,** estão listadas todas as ações restritas para uma determinada função RBAC. Neste exemplo, nenhuma ação é restrita.
    
    Nos **Âmbitos dos Scopes Atribuídos,** os IDs de subscrição estão listados. Certifique-se de que a função RBAC contém o ID de subscrição explícito onde é utilizado. Se o ID de subscrição correto não for especificado, não está autorizado a importar o papel na sua subscrição.

    Editar o ficheiro tendo em conta as considerações anteriores.

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

6. Importar o papel rBAC personalizado de volta para o ambiente.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Esta função deverá figurar agora na lista de funções na lâmina de controlo de **acesso.**

![Ver papéis RBAC](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Para mais informações, vá a [papéis personalizados.](../role-based-access-control/custom-roles.md)

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Saída de amostra para criação de papel personalizado através da PowerShell

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

## <a name="add-users-to-the-custom-role"></a>Adicione os utilizadores à função personalizada

Pode conceder acesso de dentro do recurso, grupo de recursos ou subscrição, que é o âmbito da atribuição de função. Ao fornecer acesso, tenha em conta que o acesso concedido no nó principal é herdado pela criança. Para mais informações, aceda ao [controlo de acesso baseado em papéis.](../role-based-access-control/overview.md)

1. Ir para o controlo de **acesso (IAM)**. Clique **+ Adicione** na lâmina de controlo de acesso.

    ![Adicionar acesso à função RBAC](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Selecione o papel que pretende atribuir, neste caso é o Administrador de **Infraestruturas StorSimple**.

3. Selecione o utilizador, grupo ou aplicação no diretório para o qual pretende dar acesso. Pode procurar o diretório com os nomes a apresentar, endereços de correio eletrónico e identificadores de objetos.

4. Selecione **Guardar** para criar a atribuição.

    ![Adicionar permissões ao papel RBAC](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Uma notificação **de utilizador adicionando** acompanha o progresso. Depois de o utilizador ser adicionado com sucesso, a lista de utilizadores no controlo de Acesso é atualizada.

## <a name="view-permissions-for-the-custom-role"></a>Ver permissões para o papel personalizado

Uma vez criado este papel, pode ver as permissões associadas a este papel no portal Azure.

1. Para ver as permissões associadas a esta função, aceda ao Controlo de **Acesso (IAM) > Funções > StorSimple Infrastructure Admin**. A lista de utilizadores nesta função é apresentada.

2. Selecione um utilizador de administração de infraestrutura StorSimple e clique em **Permissões**.

    ![Ver permissões para o papel de StorSimple Infra Admin](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. As permissões associadas a este papel são apresentadas.

    ![Ver utilizadores na função StorSimple Infra Admin](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Passos seguintes

Saiba atribuir [funções personalizadas a utilizadores internos e externos.](../role-based-access-control/role-assignments-external-users.md)
