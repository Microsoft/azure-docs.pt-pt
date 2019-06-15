---
title: Atualizar o dispositivo StorSimple | Documentos da Microsoft
description: Explica como utilizar a funcionalidade de atualização do StorSimple para instalar atualizações de modo regular e a manutenção e correções.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: v-sharos
ms.openlocfilehash: d973a16c121a1e8ebee10826d135bcbb33ef748c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61409992"
---
# <a name="update-your-storsimple-8000-series-device"></a>Atualizar o seu dispositivo StorSimple série 8000
> [!NOTE]
> O portal clássico do StorSimple foi preterido. Os Gestores de Dispositivos do StorSimple vão ser migrados automaticamente para o portal do Azure novo, de acordo com a agenda de preterição. Receberá uma mensagem de e-mail e uma notificação no portal relativamente a esta migração. Este documento também será descontinuado em breve. Para quaisquer perguntas sobre a migração, consulte [FAQ: Mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Descrição geral
As funcionalidades de atualizações do StorSimple permitem-lhe facilmente manter atualizado o dispositivo StorSimple. Dependendo do tipo de atualização, pode aplicar atualizações para o dispositivo através do portal clássico do Azure ou a interface do Windows PowerShell. Este tutorial descreve os tipos de atualização e como instalar cada um deles.

Pode aplicar dois tipos de atualizações do dispositivo: 

* Regular (ou modo Normal) atualizações
* Atualizações do modo de manutenção

Pode instalar atualizações regulares através do portal clássico do Azure ou o Windows PowerShell; No entanto, tem de utilizar o Windows PowerShell para instalar atualizações de modo de manutenção. 

Cada tipo de atualização é descrito separadamente, abaixo.

### <a name="regular-updates"></a>Atualizações regulares
Atualizações regulares são atualizações não disruptivas que podem ser instaladas quando o dispositivo estiver no modo Normal. Estas atualizações são aplicadas através do site do Microsoft Update para cada controlador de dispositivo. 

> [!IMPORTANT]
> Uma ativação pós-falha de controlador pode ocorrer durante o processo de atualização. No entanto, isso não irá afetar a disponibilidade de sistema ou a operação.
> 
> 

* Para obter detalhes sobre como instalar atualizações regulares através do portal clássico do Azure, consulte [instalar atualizações regulares através do portal clássico do Azure](#install-regular-updates-via-the-azure-classic-portal).
* Também pode instalar atualizações regulares através do Windows PowerShell para StorSimple. Para obter detalhes, consulte [instalar atualizações regulares através do Windows PowerShell para StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Atualizações do modo de manutenção
Atualizações do modo de manutenção são atualizações disruptivas, tais como atualizações de firmware do disco. Estas atualizações exigem que o dispositivo para ser colocado no modo de manutenção. Para obter detalhes, consulte [passo 2: Introduza o modo de manutenção](#step2). Não é possível utilizar o portal clássico do Azure para instalar atualizações de modo de manutenção. Em vez disso, tem de utilizar o Windows PowerShell para StorSimple. 

Para obter detalhes sobre como instalar atualizações de modo de manutenção, consulte [atualizações de modo de manutenção instalar através do Windows PowerShell para StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Atualizações do modo de manutenção devem ser aplicadas em separado para cada controlador. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instalar atualizações regulares através do portal clássico do Azure
Pode utilizar o portal clássico do Azure para aplicar atualizações ao dispositivo StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalar atualizações regulares através do Windows PowerShell para StorSimple
Em alternativa, pode utilizar o Windows PowerShell para StorSimple para aplicar atualizações regulares (modo Normal).

> [!IMPORTANT]
> Embora possa instalar atualizações regulares com o Windows PowerShell para StorSimple, recomendamos vivamente que instale atualizações regulares através do portal clássico do Azure. A partir do Update 1, pré-verificações de serão executadas antes de instalar atualizações a partir do portal. Estas pré-verificações de serão prevenir falhas e certifique-se de uma experiência mais suave. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instale as atualizações de modo de manutenção através do Windows PowerShell para StorSimple
Utilizar o Windows PowerShell para StorSimple para aplicar atualizações de modo de manutenção para o seu dispositivo StorSimple. Todos os pedidos de e/s são colocadas em pausa nesse modo. Serviços, tais como a memória de acesso de aleatório não volátil (NVRAM) ou o serviço de clustering também são paradas. Ambos os controladores são reiniciados quando entrem ou saia neste modo. Quando sair deste modo, todos os serviços serão retomada e devem ser bom estado de funcionamento. (Pode demorar alguns minutos.)

Se precisar de aplicar atualizações de modo de manutenção, receberá um alerta através do portal clássico do Azure que tem as atualizações que devem ser instaladas. Este alerta irá incluir instruções para utilizar o Windows PowerShell para StorSimple para instalar as atualizações. Depois de atualizar o seu dispositivo, utilize o mesmo procedimento para alterar o dispositivo para o modo normal. Para obter instruções passo a passo, consulte [passo 4: Sair do modo de manutenção](#step4).

> [!IMPORTANT]
> * Antes de entrar no modo de manutenção, certifique-se de que os dois controladores de dispositivo estão em bom Estados ao verificar a **estado do Hardware** sobre o **manutenção** página no portal clássico do Azure. Se o controlador não está em bom estado, contacte o Support da Microsoft para os passos seguintes. Para obter mais informações, vá para o contactar suporte da Microsoft. 
> * Quando estiver no modo de manutenção, tem de aplicar a atualização pela primeira vez num controlador e, em seguida, no outro controlador.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Passo 1: Ligar à consola de série <a name="step1">
Em primeiro lugar, utilize uma aplicação como o PuTTY para aceder à consola de série. O procedimento seguinte explica como utilizar o PuTTY para ligar à consola de série.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Passo 2: Introduza o modo de manutenção <a name="step2">
Depois de ligar para a consola, determine se existem atualizações para instalar e, em modo de manutenção para instalá-los.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Passo 3: Instalar as atualizações <a name="step3">
Em seguida, instale as atualizações.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Passo 4: Sair do modo de manutenção <a name="step4">
Por fim, saia do modo de manutenção.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalar correções através do Windows PowerShell para StorSimple
Ao contrário das atualizações para o Microsoft Azure StorSimple, correções são instaladas a partir de uma pasta compartilhada. Tal como acontece com as atualizações, existem dois tipos de correções: 

* Correções regulares 
* Correções do modo de manutenção  

Os procedimentos seguintes explicam como usar o Windows PowerShell para StorSimple para instalar regular e correções do modo de manutenção.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>O que acontece às atualizações se efetuar uma reposição de fábrica do dispositivo?
Se um dispositivo é reposto para as definições de fábrica, todas as atualizações serão perdidas. Depois do dispositivo de reposição de fábrica é registado e configurado, terá de instalar manualmente as atualizações através do portal clássico do Azure e/ou o Windows PowerShell para StorSimple. Para obter mais informações sobre a reposição de fábrica, consulte [repor o dispositivo para as predefinições de fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [usando o Windows PowerShell para StorSimple para administrar o seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).
* Saiba mais sobre [utilizar o serviço StorSimple Manager para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).

