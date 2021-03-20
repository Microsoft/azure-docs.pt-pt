---
title: Excluir a piscina de anfitriões virtual do Windows Desktop - Azure
description: Como eliminar uma piscina de anfitriões no Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dfc9858bea468389d8ce90677f048e5d1fd3bb82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88007595"
---
# <a name="delete-a-host-pool"></a>Eliminar um conjunto de anfitriões

Todas as piscinas de anfitriões criadas no Windows Virtual Desktop estão anexadas a anfitriões de sessão e grupos de aplicações. Para eliminar uma piscina de anfitriões, é necessário eliminar os seus grupos de aplicações e anfitriões de sessão associados. Eliminar um grupo de aplicações é bastante simples, mas eliminar um anfitrião de sessão é mais complicado. Quando eliminar um anfitrião de sessão, tem de se certificar de que não tem sessões ativas de utilizador. Todas as sessões de utilizador no anfitrião da sessão devem ser iniciadas para evitar que os utilizadores percam dados.

## <a name="delete-a-host-pool-with-powershell"></a>Excluir uma piscina de anfitriões com PowerShell

Para eliminar um pool de anfitriões usando o PowerShell, primeiro tem de eliminar todos os grupos de aplicações na piscina anfitriã. Para eliminar todos os grupos de aplicações, executar o seguinte cmdlet PowerShell:

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

Em seguida, executar este cmdlet para apagar a piscina anfitriã:

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

Este cmdlet remove todas as sessões de utilizador existentes no anfitrião da sessão do anfitrião. Também não registra o anfitrião da sessão da piscina anfitriã. Quaisquer máquinas virtuais relacionadas (VMs) continuarão a existir dentro da sua subscrição.

## <a name="delete-a-host-pool-with-the-azure-portal"></a>Elimine uma piscina de anfitriões com o portal Azure

Para eliminar uma piscina de anfitriões no portal Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Procure e selecione **Windows Virtual Desktop**.

3. Selecione **as piscinas host** no menu no lado esquerdo da página e, em seguida, selecione o nome da piscina anfitriã que pretende eliminar.

4. No menu do lado esquerdo da página, selecione **Grupos de Aplicação**.

5. Selecione todos os grupos de aplicação na piscina de anfitrião que vai eliminar e, em seguida, selecione **Remover**.

6. Depois de remover os grupos de aplicações, vá ao menu do lado esquerdo da página e selecione **A Visão Geral**.

7. Selecione **Remover**.

8. Se houver anfitriões de sessão na piscina de anfitriões que você está apagando, você verá uma mensagem pedindo a sua permissão para continuar. Selecione **Yes** (Sim).

9. O portal Azure irá agora remover todos os anfitriões da sessão e eliminar a piscina anfitriã. Os VMs relacionados com o anfitrião da sessão não serão eliminados e permanecerão na sua subscrição.

## <a name="next-steps"></a>Passos seguintes

Para aprender a criar uma piscina de anfitriões, confira estes artigos:

- [Criar um conjunto de anfitriões com o portal do Azure](create-host-pools-azure-marketplace.md)
- [Criar um conjunto de anfitriões com o PowerShell](create-host-pools-powershell.md)

Para aprender a configurar as configurações da piscina do anfitrião, confira estes artigos:

- [Personalize propriedades do Protocolo de Ambiente de Trabalho Remoto para uma piscina de anfitriões](customize-rdp-properties.md)
- [Configurar o método de balanceamento de carga do Windows Virtual Desktop](configure-host-pool-load-balancing.md)
- [Configure o tipo de atribuição de piscina de anfitriões de ambiente de trabalho pessoal](configure-host-pool-personal-desktop-assignment-type.md)