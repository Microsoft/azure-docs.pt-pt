---
title: Definir o modo de drenagem virtual do Ambiente de Trabalho do Windows - Azure
description: Como configurar e utilizar o modo de drenagem no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 19ef7d520800ac703ed77dc0520e5b860306c4bd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509128"
---
# <a name="set-drain-mode"></a>Definir o modo de drenagem

O modo de drenagem isola um anfitrião de sessão quando pretende aplicar patches e fazer manutenção sem perturbar as sessões do utilizador. Quando isolado, o anfitrião da sessão não aceita novas sessões de utilizador. Quaisquer novas ligações serão redirecionadas para o próximo anfitrião disponível. As ligações existentes no anfitrião da sessão continuarão a funcionar até que o utilizador assine ou o administrador termine a sessão. Quando o anfitrião da sessão está em modo de drenagem, os administradores também podem ligar-se remotamente ao servidor sem passar pelo serviço de ambiente de trabalho virtual do Windows. Pode aplicar esta definição em ambientes de trabalho agridos e pessoais.

## <a name="set-drain-mode-using-the-azure-portal"></a>Definir o modo de drenagem utilizando o portal Azure

Para ligar o modo de drenagem no portal Azure:

1. Abra o portal Azure e vá para a piscina anfitriã que deseja isolar.

2. No menu de navegação, selecione **Anfitriões de Sessão**.

3. Em seguida, selecione os anfitriões para os quais pretende ligar o modo de drenagem e, em seguida, selecione **Ligue o modo de drenagem .**

4. Para desligar o modo de drenagem, selecione as piscinas hospedeiras que tenham o modo de drenagem ligado e, em seguida, selecione **Desligue o modo de drenagem**.

## <a name="set-drain-mode-using-powershell"></a>Definir o modo de drenagem usando PowerShell

Pode definir o modo de drenagem em PowerShell com o parâmetro *AllowNewessions,* que faz parte do comando [Update-AzWvdSessionhost.](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true)

Executar este cmdlet para permitir o modo de drenagem:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

Executar este cmdlet para desativar o modo de drenagem:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>Terá de executar este comando para cada anfitrião de sessão a que está a aplicar a definição.

## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre o portal Azure para o Windows Virtual Desktop, consulte os [nossos tutoriais.](create-host-pools-azure-marketplace.md) Se já está familiarizado com o básico, confira algumas das outras funcionalidades que pode utilizar com o portal Azure, como o [attach da app MSIX](app-attach-azure-portal.md) e [o Azure Advisor](azure-advisor.md).

Se estiver a utilizar o método PowerShell e quiser ver o que mais o módulo pode fazer, confira [configurar o módulo PowerShell para o Windows Virtual Desktop](powershell-module.md) e a nossa referência [PowerShell](/powershell/module/az.desktopvirtualization/).