---
title: Windows Virtual Desktop tipo de atribuição de ambiente de trabalho pessoal - Azure
description: Como configurar uma atribuição automática ou direta para um conjunto de anfitriões de ambiente de trabalho pessoal do Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 446dae3c064f5f23d35cb12b2b24bdfea9e27012
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88007816"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configure o tipo de atribuição de piscina de anfitriões de ambiente de trabalho pessoal

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).

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

Para atribuir diretamente um utilizador a um anfitrião de sessão no portal Azure:

1. Inicie sessão no portal do Azure em <https://portal.azure.com>.
2. Introduza **o Ambiente de Trabalho Virtual do Windows** na barra de pesquisa.
3. Em **Serviços**, selecione **Windows Virtual Desktop**.
4. Na página de Ambiente de Trabalho Virtual do Windows, vá ao menu do lado esquerdo da janela e selecione **as piscinas host**.
5. Selecione o nome da piscina de anfitrião que pretende atualizar.
6. Em seguida, vá ao menu do lado esquerdo da janela e selecione **grupos de aplicação**.
7. Selecione o nome do grupo de aplicações de ambiente de trabalho que pretende editar e, em seguida, selecione **Atribuições** no menu do lado esquerdo da janela.
8. Selecione **+ Adicionar,** em seguida, selecione os utilizadores ou grupos de utilizadores para onde pretende publicar este grupo de aplicações de ambiente de trabalho.
9. Selecione **Atribuir VM** na barra de informação para atribuir um anfitrião de sessão a um utilizador.
10. Selecione o anfitrião da sessão que pretende atribuir ao utilizador e, em seguida, selecione **Atribuir**.
11. Selecione o utilizador que pretende atribuir o anfitrião da sessão à lista de utilizadores disponíveis.
12. Quando terminar, **selecione** Select .

## <a name="next-steps"></a>Passos seguintes

Agora que configuraste o tipo de atribuição de ambientes de trabalho pessoais, podes iniciar sessão num cliente virtual do Windows para o testar como parte de uma sessão de utilizador. Estes próximos dois How-tos dir-lhe-ão como se conectar a uma sessão utilizando o cliente à sua escolha:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](connect-windows-7-10.md)
- [Ligar com o cliente web](connect-web.md)
- [Ligar ao cliente Android](connect-android.md)
- [Ligar ao cliente de iOS](connect-ios.md)
- [Ligar ao cliente de macOS](connect-macos.md)