---
title: Gerir grupos de aplicativos para Windows Virtual Desktop PowerShell - Azure
description: Como gerir os grupos de aplicações do Windows Virtual Desktop com o PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 531c7a819bf83edff2756fe1e62859bcb8fef459
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615269"
---
# <a name="manage-app-groups-using-powershell"></a>Gerir grupos de aplicações usando powerShell

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/manage-app-groups-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

O grupo de aplicações predefinido criado para um novo pool de anfitriões do Windows Virtual Desktop também publica todo o ambiente de trabalho. Além disso, pode criar um ou mais grupos de aplicações RemoteApp para o pool anfitrião. Siga este tutorial para criar um grupo de aplicações RemoteApp e publique aplicações individuais de menu **Start.**

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um grupo RemoteApp.
> * Conceder acesso a programas RemoteApp.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que seguiu as instruções no [Conjunto do módulo PowerShell](powershell-module.md) para configurar o módulo PowerShell e iniciar sessão na sua conta Azure.

## <a name="create-a-remoteapp-group"></a>Criar um grupo RemoteApp

Para criar um grupo RemoteApp com powerShell:

1. Execute o seguinte cmdlet PowerShell para criar um novo grupo de aplicações RemoteApp vazio.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. (Opcional) Para verificar se o grupo de aplicações foi criado, pode executar o seguinte cmdlet para ver uma lista de todos os grupos de aplicações para o pool anfitrião.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Execute o seguinte cmdlet para obter uma lista de aplicações de menu **Iniciar** na imagem virtual da máquina do pool anfitrião. Anote os valores para **FilePath,** **IconPath,** **IconIndex,** e outras informações importantes para a aplicação que pretende publicar.

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
   
4. Executar o seguinte cmdlet para `AppAlias`instalar a aplicação com base em . `AppAlias`torna-se visível quando executa a saída a partir do passo 3.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require> 
   ```

5. (Opcional) Execute o seguinte cmdlet para publicar um novo programa RemoteApp para o grupo de aplicações criado no passo 1.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require> 
   ```

6. Para verificar se a aplicação foi publicada, execute o seguinte cmdlet.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Repita os passos 1-5 para cada aplicação que pretende publicar para este grupo de aplicações.
8. Executar o seguinte cmdlet para dar aos utilizadores acesso aos programas RemoteApp no grupo de aplicações.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>Passos seguintes

Se você veio a este guia como fazer dos nossos tutoriais, confira [Create a host pool para validar atualizações](create-validation-host-pool.md)de serviço . Você pode usar um pool anfitrião de validação para monitorizar atualizações de serviço antes de as lançar para o seu ambiente de produção.