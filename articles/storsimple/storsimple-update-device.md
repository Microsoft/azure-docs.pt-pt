---
title: Atualizar seu dispositivo StorSimple | Microsoft Docs
description: Explica como usar o recurso de atualização do StorSimple para instalar atualizações e hotfixes regulares e do modo de manutenção.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: twooley
ms.openlocfilehash: c9451afaefdd220b5f87d4650c7844f06926b03a
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933424"
---
# <a name="update-your-storsimple-8000-series-device"></a>Atualizar seu dispositivo StorSimple da série 8000
> [!NOTE]
> O portal clássico do StorSimple foi preterido. Os Gestores de Dispositivos do StorSimple vão ser migrados automaticamente para o portal do Azure novo, de acordo com a agenda de preterição. Receberá uma mensagem de e-mail e uma notificação no portal relativamente a esta migração. Este documento também será descontinuado em breve. Relativamente a perguntas sobre a migração, veja [FAQ: Move to Azure portal](storsimple-8000-move-azure-portal-faq.md) (FAQ: migrar para o portal do Azure).

## <a name="overview"></a>Visão geral
Os recursos de atualizações do StorSimple permitem que você mantenha seu dispositivo StorSimple atualizado com facilidade. Dependendo do tipo de atualização, você pode aplicar atualizações ao dispositivo por meio do portal clássico do Azure ou por meio da interface do Windows PowerShell. Este tutorial descreve os tipos de atualização e como instalar cada um deles.

Você pode aplicar dois tipos de atualizações de dispositivo: 

* Atualizações regulares (ou no modo normal)
* Atualizações do modo de manutenção

Você pode instalar atualizações regulares por meio do portal clássico do Azure ou do Windows PowerShell; no entanto, você deve usar o Windows PowerShell para instalar atualizações do modo de manutenção. 

Cada tipo de atualização é descrito separadamente, abaixo.

### <a name="regular-updates"></a>Atualizações regulares
Atualizações regulares são atualizações sem interrupções que podem ser instaladas quando o dispositivo está no modo normal. Essas atualizações são aplicadas por meio do site Microsoft Update para cada controlador de dispositivo. 

> [!IMPORTANT]
> Um failover de controlador pode ocorrer durante o processo de atualização. No entanto, isso não afetará a disponibilidade ou a operação do sistema.
> 
> 

* Para obter detalhes sobre como instalar atualizações regulares por meio do portal clássico do Azure, consulte [instalar atualizações regulares por meio do portal clássico do Azure](#install-regular-updates-via-the-azure-classic-portal).
* Você também pode instalar atualizações regulares via Windows PowerShell para StorSimple. Para obter detalhes, consulte [instalar atualizações regulares via Windows PowerShell para StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Atualizações do modo de manutenção
As atualizações do modo de manutenção são atualizações de interrupção, como atualizações de firmware de disco. Essas atualizações exigem que o dispositivo seja colocado no modo de manutenção. Para obter detalhes, consulte [etapa 2: entrar no modo de manutenção](#step2). Você não pode usar o portal clássico do Azure para instalar atualizações do modo de manutenção. Em vez disso, você deve usar Windows PowerShell para StorSimple. 

Para obter detalhes sobre como instalar atualizações do modo de manutenção, consulte [instalar atualizações do modo de manutenção via Windows PowerShell para StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> As atualizações do modo de manutenção devem ser aplicadas separadamente a cada controlador. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instalar atualizações regulares por meio do portal clássico do Azure
Você pode usar o portal clássico do Azure para aplicar atualizações ao seu dispositivo StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalar atualizações regulares via Windows PowerShell para StorSimple
Como alternativa, você pode usar Windows PowerShell para StorSimple para aplicar atualizações regulares (modo normal).

> [!IMPORTANT]
> Embora seja possível instalar atualizações regulares usando Windows PowerShell para StorSimple, é altamente recomendável que você instale atualizações regulares por meio do portal clássico do Azure. A partir da atualização 1, as verificações prévias serão executadas antes da instalação das atualizações do Portal. Essas verificações antecipadas causarão falhas e garantirão uma experiência mais suave. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalar atualizações do modo de manutenção via Windows PowerShell para StorSimple
Você usa Windows PowerShell para StorSimple para aplicar atualizações do modo de manutenção ao seu dispositivo StorSimple. Todas as solicitações de e/s são pausadas nesse modo. Serviços como a memória de acesso aleatório não volátil (NVRAM) ou o serviço de cluster também são interrompidos. Ambos os controladores são reinicializados quando você entra ou sai desse modo. Quando você sair desse modo, todos os serviços serão retomados e deverão estar íntegros. (Isso pode levar alguns minutos).

Se você precisar aplicar atualizações do modo de manutenção, receberá um alerta por meio do portal clássico do Azure que você tem atualizações que devem ser instaladas. Esse alerta incluirá instruções sobre como usar Windows PowerShell para StorSimple para instalar as atualizações. Depois de atualizar o dispositivo, use o mesmo procedimento para alterar o dispositivo para o modo normal. Para obter instruções detalhadas, consulte [etapa 4: sair do modo de manutenção](#step4).

> [!IMPORTANT]
> * Antes de entrar no modo de manutenção, verifique se ambos os controladores de dispositivo estão íntegros verificando o **status do hardware** na página **manutenção** no portal clássico do Azure. Se o controlador não estiver íntegro, entre em contato com Suporte da Microsoft para as próximas etapas. Para obter mais informações, acesse Suporte da Microsoft de contato. 
> * Quando estiver no modo de manutenção, você precisará aplicar a atualização primeiro em um controlador e, em seguida, no outro controlador.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Etapa 1: conectar-se ao console serial <a name="step1">
Primeiro, use um aplicativo como de saída para acessar o console serial. O procedimento a seguir explica como usar a reutilização para se conectar ao console serial.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Etapa 2: entrar no modo de manutenção <a name="step2">
Depois de se conectar ao console do, determine se há atualizações a serem instaladas e entre no modo de manutenção para instalá-las.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Etapa 3: instalar as atualizações <a name="step3">
Em seguida, instale as atualizações.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Etapa 4: sair do modo de manutenção <a name="step4">
Por fim, saia do modo de manutenção.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalar hotfixes via Windows PowerShell para StorSimple
Ao contrário das atualizações para Microsoft Azure StorSimple, os hotfixes são instalados de uma pasta compartilhada. Assim como acontece com as atualizações, há dois tipos de hotfixes: 

* Hotfixes regulares 
* Hotfixes do modo de manutenção  

Os procedimentos a seguir explicam como usar Windows PowerShell para StorSimple para instalar hotfixes regulares e do modo de manutenção.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>O que acontece com as atualizações se você executar uma redefinição de fábrica do dispositivo?
Se um dispositivo for redefinido para as configurações de fábrica, todas as atualizações serão perdidas. Depois que o dispositivo de redefinição de fábrica for registrado e configurado, será necessário instalar manualmente as atualizações por meio do portal clássico do Azure e/ou Windows PowerShell para StorSimple. Para obter mais informações sobre a redefinição de fábrica, consulte [redefinir o dispositivo para as configurações padrão de fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como [usar Windows PowerShell para StorSimple para administrar seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).
* Saiba mais sobre como [usar o serviço de StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

