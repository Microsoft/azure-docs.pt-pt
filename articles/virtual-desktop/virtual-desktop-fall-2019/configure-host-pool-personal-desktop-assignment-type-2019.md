---
title: Windows Virtual Desktop (clássico) tipo de atribuição de ambiente de trabalho pessoal - Azure
description: Como configurar o tipo de atribuição para um conjunto de anfitriões de ambiente de trabalho pessoal virtual do Windows Virtual Desktop (clássico).
author: Heidilohr
ms.topic: how-to
ms.date: 05/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 669010d2de90498c98fc685fe931b084a11cd104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008513"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type-for-windows-virtual-desktop-classic"></a>Configure o tipo de atribuição de piscina de anfitriões de ambiente de trabalho pessoal para o Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../configure-host-pool-personal-desktop-assignment-type.md).

Pode configurar o tipo de atribuição do seu pool de anfitriões de ambiente de trabalho pessoal para ajustar o ambiente de trabalho virtual do Windows de forma melhor adequada às suas necessidades. Neste tópico, vamos mostrar-lhe como configurar a atribuição automática ou direta para os seus utilizadores.

>[!NOTE]
> As instruções deste artigo aplicam-se apenas a piscinas pessoais de anfitriões de desktop, não piscinas de anfitriões, uma vez que os utilizadores em piscinas de anfitriões não são atribuídos a anfitriões de sessão específicos.

## <a name="configure-automatic-assignment"></a>Configure a atribuição automática

A atribuição automática é o tipo de atribuição padrão para novas piscinas de anfitriões de ambiente de trabalho pessoais criadas no seu ambiente de ambiente de trabalho virtual do Windows. Atribuir automaticamente os utilizadores não requer um anfitrião de sessão específico.

Para atribuir automaticamente os utilizadores, atribua-os primeiro ao pool pessoal do anfitrião do ambiente de trabalho para que possam ver o ambiente de trabalho no seu feed. Quando um utilizador designado lançar o ambiente de trabalho no seu feed, irá solicitar um anfitrião de sessão disponível se ainda não estiver ligado ao pool anfitrião, que completa o processo de atribuição.

Antes de começar, [descarregue e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) se ainda não o fez.

> [!NOTE]
> Certifique-se de que instalou o módulo 1.0.1534.2 do Windows Virtual desktop powerShell ou mais tarde antes de seguir estas instruções.

Depois disso, execute o seguinte cmdlet para iniciar scontabilidade na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Para configurar um pool anfitrião para atribuir automaticamente os utilizadores a VMs, executar o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Para atribuir um utilizador ao pool pessoal do anfitrião do ambiente de trabalho, execute o seguinte cmdlet PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Configure a atribuição direta

Ao contrário da atribuição automática, quando utiliza a atribuição direta, deve atribuir o utilizador tanto ao pool pessoal do anfitrião do ambiente de trabalho como a um anfitrião de sessão específico antes de poder ligar-se ao seu ambiente de trabalho pessoal. Se o utilizador for apenas designado para uma piscina de anfitriões sem uma atribuição de anfitrião de sessão, eles não serão capazes de aceder a recursos.

Para configurar uma piscina de anfitriões para exigir a atribuição direta dos utilizadores aos anfitriões de sessão, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Para atribuir um utilizador ao pool pessoal do anfitrião do ambiente de trabalho, execute o seguinte cmdlet PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Para atribuir um utilizador a um anfitrião de sessão específico, execute o seguinte cmdlet PowerShell:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="remove-a-user-assignment"></a>Remover uma atribuição de utilizador

Pode querer remover uma atribuição de utilizador porque o utilizador já não precisa do ambiente de trabalho pessoal, o utilizador deixou a empresa ou pretende reutilizar o ambiente de trabalho para outra pessoa.

Atualmente, a única forma de remover a atribuição do utilizador para um ambiente de trabalho pessoal é remover totalmente o anfitrião da sessão. Para remover o anfitrião da sessão, execute este cmdlet:

```powershell
Remove-RdsSessionHost
```

Se precisar de adicionar o anfitrião da sessão de volta à piscina pessoal do anfitrião do ambiente de trabalho, desinstale o Ambiente de Trabalho Virtual do Windows nessa máquina e, em seguida, siga os passos na [Criar uma piscina de anfitriões com o PowerShell](create-host-pools-powershell-2019.md) para voltar a registar o anfitrião da sessão.

## <a name="next-steps"></a>Passos seguintes

Agora que configuraste o tipo de atribuição de ambientes de trabalho pessoais, podes iniciar sessão num cliente virtual do Windows para o testar como parte de uma sessão de utilizador. Estes próximos dois How-tos dir-lhe-ão como se conectar a uma sessão utilizando o cliente à sua escolha:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](connect-windows-7-10-2019.md)
- [Ligar com o cliente web](connect-web-2019.md)
