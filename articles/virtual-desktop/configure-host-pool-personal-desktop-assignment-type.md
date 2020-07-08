---
title: Windows Virtual Desktop tipo de atribuição de ambiente de trabalho pessoal - Azure
description: Como configurar o tipo de atribuição para um conjunto de anfitriões de ambiente de trabalho pessoal virtual do Windows Desktop.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158ac92a930b53e02ee81570c62711ca27dc4ae8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200397"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configure o tipo de atribuição de piscina de anfitriões de ambiente de trabalho pessoal

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização primavera 2020 com objetos de desktop virtual do Windows Manager do Azure. Se estiver a utilizar o desbloqueio virtual do Windows Desktop Fall 2019 sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).
>
> A atualização Virtual Desktop Spring 2020 do Windows está atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode configurar o tipo de atribuição do seu pool de anfitriões de ambiente de trabalho pessoal para ajustar o ambiente de trabalho virtual do Windows de forma melhor adequada às suas necessidades. Neste tópico, vamos mostrar-lhe como configurar a atribuição automática ou direta para os seus utilizadores.

>[!NOTE]
> As instruções deste artigo aplicam-se apenas a piscinas pessoais de anfitriões de desktop, não piscinas de anfitriões, uma vez que os utilizadores em piscinas de anfitriões não são atribuídos a anfitriões de sessão específicos.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já descarregou e instalou o módulo Windows Virtual Desktop PowerShell. Caso não o tenha, siga as instruções do [módulo PowerShell](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Configure a atribuição automática

A atribuição automática é o tipo de atribuição padrão para novas piscinas de anfitriões de ambiente de trabalho pessoais criadas no seu ambiente de ambiente de trabalho virtual do Windows. Atribuir automaticamente os utilizadores não requer um anfitrião de sessão específico.

Para atribuir automaticamente os utilizadores, atribua-os primeiro ao pool pessoal do anfitrião do ambiente de trabalho para que possam ver o ambiente de trabalho no seu feed. Quando um utilizador designado lançar o ambiente de trabalho no seu feed, irá solicitar um anfitrião de sessão disponível se ainda não estiver ligado ao pool anfitrião, que completa o processo de atribuição.

Para configurar um pool anfitrião para atribuir automaticamente os utilizadores a VMs, executar o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Para atribuir um utilizador ao pool pessoal do anfitrião do ambiente de trabalho, execute o seguinte cmdlet PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Configure a atribuição direta

Ao contrário da atribuição automática, quando utiliza a atribuição direta, deve atribuir o utilizador tanto ao pool pessoal do anfitrião do ambiente de trabalho como a um anfitrião de sessão específico antes de poder ligar-se ao seu ambiente de trabalho pessoal. Se o utilizador for apenas designado para uma piscina de anfitriões sem uma atribuição de anfitrião de sessão, eles não serão capazes de aceder a recursos.

Para configurar uma piscina de anfitriões para exigir a atribuição direta dos utilizadores aos anfitriões de sessão, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Para atribuir um utilizador ao pool pessoal do anfitrião do ambiente de trabalho, execute o seguinte cmdlet PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Para atribuir um utilizador a um anfitrião de sessão específico, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Próximos passos

Agora que configuraste o tipo de atribuição de ambientes de trabalho pessoais, podes iniciar sessão num cliente virtual do Windows para o testar como parte de uma sessão de utilizador. Estes próximos dois How-tos dir-lhe-ão como se conectar a uma sessão utilizando o cliente à sua escolha:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](connect-windows-7-and-10.md)
- [Ligar com o cliente web](connect-web.md)
- [Ligar ao cliente Android](connect-android.md)
- [Ligar ao cliente de iOS](connect-ios.md)
- [Ligar ao cliente de macOS](connect-macos.md)