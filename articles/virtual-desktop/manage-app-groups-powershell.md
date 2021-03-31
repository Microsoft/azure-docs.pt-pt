---
title: Gerir grupos de aplicações para Windows Virtual Desktop PowerShell - Azure
description: Como gerir grupos de aplicações virtual do Windows Desktop com o PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a6f24dea00a174aa0276a9b30add0854c3694056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008649"
---
# <a name="manage-app-groups-using-powershell"></a>Gerir grupos de aplicações usando PowerShell

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

O grupo de aplicações padrão criado para um novo conjunto de anfitriões do Windows Virtual Desktop também publica o ambiente de trabalho completo. Além disso, pode criar um ou mais grupos de aplicações RemoteApp para o pool anfitrião. Siga este tutorial para criar um grupo de aplicações RemoteApp e publicar aplicações individuais do menu **Start.**

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um grupo RemoteApp.
> * Conceder acesso a programas RemoteApp.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que seguiu as instruções no [Conjunto do módulo PowerShell](powershell-module.md) para configurar o módulo PowerShell e iniciar súm na sua conta Azure.

## <a name="create-a-remoteapp-group"></a>Criar um grupo RemoteApp

Para criar um grupo RemoteApp com PowerShell:

1. Executar o cmdlet PowerShell seguinte para criar um novo grupo de aplicações RemoteApp vazio.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. (Opcional) Para verificar se o grupo de aplicações foi criado, pode executar o seguinte cmdlet para ver uma lista de todos os grupos de aplicações para a piscina anfitriã.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Execute o cmdlet seguinte para obter uma lista de aplicações de menu **Iniciar** na imagem da máquina virtual da piscina anfitriã. Anote os valores para **FilePath,** **IconPath,** **IconIndex** e outras informações importantes para a aplicação que pretende publicar.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   A saída deve mostrar todos os itens do menu Iniciar num formato como este:

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. Executar o cmdlet seguinte para instalar a aplicação com base em `AppAlias` . `AppAlias` torna-se visível quando corre a saída do passo 3.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. (Opcional) Execute o cmdlet seguinte para publicar um novo programa RemoteApp para o grupo de aplicações criado no passo 1.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. Para verificar se a aplicação foi publicada, execute o seguinte cmdlet.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Repita os passos 1-5 para cada aplicação que pretende publicar para este grupo de aplicações.
8. Execute o cmdlet seguinte para garantir aos utilizadores o acesso aos programas RemoteApp no grupo de aplicações.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>Passos seguintes

Se você veio a este guia como guiar a partir dos nossos tutoriais, confira [Criar uma piscina de anfitriões para validar atualizações de serviços.](create-validation-host-pool.md) Pode utilizar um pool de anfitriões de validação para monitorizar as atualizações de serviço antes de as lançar para o seu ambiente de produção.