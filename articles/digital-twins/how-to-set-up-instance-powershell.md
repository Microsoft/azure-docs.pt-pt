---
title: Configurar um caso e autenticação (PowerShell)
titleSuffix: Azure Digital Twins
description: Veja como configurar um caso do serviço Azure Digital Twins usando a Azure PowerShell
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65495da13bc6c9ed91c1ecbd587c16c949136d73
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98040693"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Configurar uma instância e autenticação Azure Digital Twins (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo abrange as etapas para **a criação de um novo exemplo de Azure Digital Twins**, incluindo a criação do caso e a criação de autenticação. Depois de completar este artigo, terá uma instância Azure Digital Twins pronta para começar a programar contra.

Esta versão deste artigo passa por estes passos manualmente, um a um, utilizando a Azure PowerShell.

* Para passar por estes passos manualmente utilizando o portal Azure, consulte a versão porta deste artigo: [*Como fazer: Configurar uma instância e autenticação (portal)*](how-to-set-up-instance-portal.md).
* Para passar por uma configuração automatizada utilizando uma amostra de script de implementação, consulte a versão escrita deste artigo: [*Como-a-fazer: Configurar uma instância e autenticação (scripted)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>Preparar o ambiente

1. Se optar por utilizar a Azure PowerShell localmente:
   1. [Instale o módulo Az PowerShell](/powershell/azure/install-az-ps).
   1. Ligue-se à sua conta Azure utilizando o [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)
1. Se optar por utilizar a Azure Cloud Shell:
   1. Consulte [a visão geral da Azure Cloud Shell](../cloud-shell/overview.md) para obter mais informações.
   1. Na barra de ícones Cloud Shell, certifique-se de que a cloud shell está definida para executar a versão PowerShell.

      :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Janela Cloud Shell mostrando a seleção da versão PowerShell":::

1. Se tiver várias subscrições do Azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione uma subscrição específica utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Se esta for a sua primeira utilização da Azure Digital Twins com esta subscrição, tem de registar o fornecedor de recursos **Microsoft.DigitalTwins.**

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

> [!IMPORTANT]
> Enquanto o módulo **PowerShell Az.DigitalTwins** estiver em pré-visualização, deve instalá-lo separadamente utilizando o `Install-Module` cmdlet. Quando este módulo do PowerShell entrar em disponibilidade geral, fará parte das versões futuras do módulo Az PowerShell e estará disponível por predefinição a partir do Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.DigitalTwins
```

## <a name="create-the-azure-digital-twins-instance"></a>Criar a instância Azure Digital Twins

Nesta secção, irá **criar uma nova instância de Azure Digital Twins** utilizando a Azure PowerShell.
Terá de providenciar:

* Um [grupo de recursos Azure.](../azure-resource-manager/management/overview.md) Se ainda não tiver um grupo de recursos existente, pode criar um utilizando o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* Uma região para o destacamento. Para ver que regiões suportam a Azure Digital Twins, visite [*os produtos Azure disponíveis por região.*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)
* Um nome para o seu exemplo. O nome do novo caso deve ser único na região para a sua subscrição. Se a sua subscrição tiver outro exemplo de Azure Digital Twins na região que já está a usar o nome especificado, será solicitado que escolha um nome diferente.

Use os seus valores no seguinte exemplo para criar o caso:

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Verifique o sucesso e colete valores importantes

Se a instância foi criada com sucesso, o resultado parece semelhante à seguinte saída contendo informações sobre o recurso que criou:

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

> [!TIP]
> Você pode ver estas propriedades, juntamente com todas as propriedades do seu caso, a qualquer momento, correndo `Get-AzDigitalTwinsInstance` e canalizando para `Select-Object -Property *` .

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

Note o **nome de anfitrião,** **nome** e Grupo **de Recursos** da Azure Digital Twins. Estes são valores importantes que poderá necessitar à medida que continua a trabalhar com a sua instância Azure Digital Twins, para configurar a autenticação e os recursos Azure relacionados. Se outros utilizadores estiverem a programar contra a ocorrência, deverá partilhar estes valores com eles.

Agora tens uma instância do Azure Digital Twins pronta para ir. Em seguida, você dará as permissões apropriadas do utilizador Azure para geri-lo.

## <a name="set-up-user-access-permissions"></a>Configurar permissões de acesso ao utilizador

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Determine o **ObjectId** para a conta Azure AD do utilizador que deve ser atribuída a função utilizando o cmdlet [Get-AzAdUser.](/powershell/module/az.resources/get-azaduser)

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Utilize o seguinte comando para atribuir a função. Deve ser gerido por um utilizador com [permissões suficientes](#prerequisites-permission-requirements) na subscrição do Azure. O comando requer que passe no **ObjectId** para a conta Azure AD do utilizador que deve ser atribuída a função. Também requer que use o mesmo ID de subscrição, nome de grupo de recursos e nome de instância Azure Digital Twins que escolheu anteriormente.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

O resultado deste comando é informação sobre a atribuição de funções que foi criada.

### <a name="verify-success"></a>Verificar o sucesso

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Tem agora uma instância Azure Digital Twins pronta a ser executada, e atribuiu permissões para geri-lo.

## <a name="next-steps"></a>Passos seguintes

Veja como ligar uma aplicação do cliente ao seu caso com o código de autenticação:
* [*Como fazer: Escrever código de autenticação de aplicativos*](how-to-authenticate-client.md)
