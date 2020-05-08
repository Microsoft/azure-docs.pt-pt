---
title: Windows Virtual Desktop tipo de tarefa pessoal desktop - Azure
description: Como configurar o tipo de atribuição para um pool de anfitriões pessoal do Windows Virtual Desktop.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8451dc14a7ed42aa92f9adbd5ad050936949e302
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612423"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configure o tipo pessoal de atribuição de piscina de anfitrião do ambiente de trabalho

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Pode configurar o tipo de atribuição do seu conjunto pessoal de anfitriões para ajustar o ambiente de ambiente de trabalho virtual do Windows para melhor atender às suas necessidades. Neste tópico, vamos mostrar-lhe como configurar a atribuição automática ou direta para os seus utilizadores.

>[!NOTE]
> As instruções deste artigo aplicam-se apenas a piscinas pessoais de anfitriões de ambiente de trabalho, não piscinas de anfitriões reunidas, uma vez que os utilizadores em piscinas de anfitriões não são atribuídos a anfitriões específicos da sessão.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já descarregou e instalou o módulo PowerShell do Windows Virtual Desktop. Se não o fez, siga as instruções no [módulo PowerShell](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Configurar a atribuição automática

A atribuição automática é o tipo de atribuição padrão para novos conjuntos pessoais de anfitriões de ambiente de trabalho criados no seu ambiente de ambiente de trabalho virtual Windows. Atribuir automaticamente os utilizadores não requer um anfitrião de sessão específico.

Para atribuir automaticamente os utilizadores, atribua-os primeiro ao conjunto de anfitriões pessoal para que possam ver o ambiente de trabalho no seu feed. Quando um utilizador designado lançar o ambiente de trabalho no seu feed, solicitará um anfitrião de sessão disponível se ainda não estiver ligado à piscina anfitriã, que completa o processo de atribuição.

Para configurar um conjunto de anfitriões para atribuir automaticamente os utilizadores a VMs, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Para atribuir um utilizador à piscina de anfitriões pessoal do ambiente de trabalho, execute o seguinte cmdlet PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Configurar a atribuição direta

Ao contrário da atribuição automática, quando utiliza a atribuição direta, deve atribuir o utilizador ao conjunto de anfitriões pessoal do ambiente de trabalho e a um anfitrião de sessão específico antes de se ligar ao seu ambiente de trabalho pessoal. Se o utilizador for atribuído apenas a uma piscina de anfitriões sem uma atribuição de anfitriões de sessão, não poderá aceder aos recursos.

Para configurar um pool de anfitriões para exigir a atribuição direta dos utilizadores aos anfitriões da sessão, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Para atribuir um utilizador à piscina de anfitriões pessoal do ambiente de trabalho, execute o seguinte cmdlet PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Para atribuir um utilizador a um anfitrião de sessão específico, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Passos seguintes

Agora que configurao o tipo de atribuição pessoal de desktop, pode iniciar sessão num cliente do Windows Virtual Desktop para o testar como parte de uma sessão de utilizador. Estes próximos dois How-tos dir-lhe-ão como se conectar a uma sessão usando o cliente da sua escolha:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](connect-windows-7-and-10.md)
- [Ligar com o cliente web](connect-web.md)
- [Ligar ao cliente Android](connect-android.md)
- [Ligar ao cliente de iOS](connect-ios.md)
- [Ligar ao cliente de macOS](connect-macos.md)