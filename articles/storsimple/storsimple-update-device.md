---
title: Atualize o seu dispositivo StorSimple / Microsoft Docs
description: Explica como utilizar a funcionalidade de atualização StorSimple para instalar atualizações regulares e de modo de manutenção e correções de calor.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933424"
---
# <a name="update-your-storsimple-8000-series-device"></a>Atualize o seu dispositivo Série StorSimple 8000
> [!NOTE]
> O portal clássico do StorSimple foi preterido. Os Gestores de Dispositivos do StorSimple vão ser migrados automaticamente para o portal do Azure novo, de acordo com a agenda de preterição. Receberá uma mensagem de e-mail e uma notificação no portal relativamente a esta migração. Este documento também será descontinuado em breve. Relativamente a perguntas sobre a migração, veja [FAQ: Move to Azure portal](storsimple-8000-move-azure-portal-faq.md) (FAQ: migrar para o portal do Azure).

## <a name="overview"></a>Descrição geral
As funcionalidades de atualizações StorSimple permitem manter facilmente o seu dispositivo StorSimple atualizado. Dependendo do tipo de atualização, pode aplicar atualizações no dispositivo através do portal clássico Azure ou através da interface Windows PowerShell. Este tutorial descreve os tipos de atualização e como instalar cada um deles.

Pode aplicar dois tipos de atualizações do dispositivo: 

* Atualizações regulares (ou modo normal)
* Atualizações do modo de manutenção

Pode instalar atualizações regulares através do portal clássico Do Azure ou do Windows PowerShell; no entanto, deve utilizar o Windows PowerShell para instalar atualizações do modo de manutenção. 

Cada tipo de atualização é descrito separadamente, abaixo.

### <a name="regular-updates"></a>Atualizações regulares
Atualizações regulares são atualizações não disruptivas que podem ser instaladas quando o dispositivo está em modo Normal. Estas atualizações são aplicadas através do website da Microsoft Update para cada controlador de dispositivos. 

> [!IMPORTANT]
> Pode ocorrer uma falha no controlador durante o processo de atualização. No entanto, isto não afetará a disponibilidade ou operação do sistema.
> 
> 

* Para mais detalhes sobre como instalar atualizações regulares através do portal clássico Azure, consulte [Instalar atualizações regulares através do portal clássico Azure](#install-regular-updates-via-the-azure-classic-portal).
* Também pode instalar atualizações regulares via Windows PowerShell para storSimple. Para mais detalhes, consulte [Instalar atualizações regulares via Windows PowerShell para StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Atualizações do modo de manutenção
As atualizações do Modo manutenção são atualizações disruptivas, tais como atualizações de firmware de disco. Estas atualizações requerem que o dispositivo seja colocado no modo de manutenção. Para mais detalhes, consulte [o Passo 2: Introduza](#step2)o modo de manutenção . Não é possível utilizar o portal clássico Azure para instalar atualizações do modo de manutenção. Em vez disso, tem de utilizar o Windows PowerShell para o StorSimple. 

Para mais informações sobre como instalar atualizações do modo de manutenção, consulte as atualizações do modo de manutenção através do [Windows PowerShell para o StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> As atualizações do modo de manutenção devem ser aplicadas separadamente a cada controlador. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instale atualizações regulares através do portal clássico Azure
Pode utilizar o portal clássico Azure para aplicar atualizações no seu dispositivo StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instale atualizações regulares via Windows PowerShell para storSimple
Em alternativa, pode utilizar o Windows PowerShell para o StorSimple para aplicar atualizações regulares (modo normal).

> [!IMPORTANT]
> Apesar de poder instalar atualizações regulares utilizando o Windows PowerShell para o StorSimple, recomendamos vivamente que instale atualizações regulares através do portal clássico Azure. A partir do Update 1, serão realizadas pré-verificações antes da instalação de atualizações a partir do portal. Estes pré-controlos preemtisem falhas e garantam uma experiência mais suave. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instale atualizações do modo de manutenção via Windows PowerShell para StorSimple
Utiliza o Windows PowerShell para o StorSimple para aplicar atualizações do modo de manutenção no seu dispositivo StorSimple. Todos os pedidos de I/O são interrompidos neste modo. Serviços como a memória de acesso aleatório não volátil (NVRAM) ou o serviço de agrupamento também estão parados. Ambos os controladores são reiniciados quando entra ou sai deste modo. Quando sair deste modo, todos os serviços serão retomados e devem ser saudáveis. (Isto pode levar alguns minutos.)

Se precisar de aplicar atualizações do modo de manutenção, receberá um alerta através do portal clássico Azure que tem atualizações que devem ser instaladas. Este alerta incluirá instruções para utilizar o Windows PowerShell para o StorSimple instalar as atualizações. Depois de atualizar o dispositivo, utilize o mesmo procedimento para alterar o dispositivo para o modo Regular. Para instruções passo a passo, consulte [o passo 4: Modo de manutenção de saída](#step4).

> [!IMPORTANT]
> * Antes de entrar no modo de manutenção, verifique se ambos os controladores do dispositivo estão saudáveis, verificando o Estado do **Hardware** na página **de Manutenção** do portal clássico Azure. Se o controlador não estiver saudável, contacte o Microsoft Support para os próximos passos. Para mais informações, contacte o Microsoft Support. 
> * Quando estiver no modo de manutenção, tem de aplicar a atualização primeiro num controlador e depois no outro controlador.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Passo 1: Ligar à consola em série<a name="step1">
Em primeiro lugar, utilize uma aplicação como a PuTTY para aceder à consola em série. O procedimento seguinte explica como usar o PuTTY para se ligar à consola em série.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Passo 2: Entrar no modo de manutenção<a name="step2">
Depois de ligar à consola, determine se existem atualizações para instalar e introduza o modo de manutenção para as instalar.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Passo 3: Instale as suas atualizações<a name="step3">
Em seguida, instale as suas atualizações.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Passo 4: Modo de manutenção de saída<a name="step4">
Finalmente, saia do modo de manutenção.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instale hotfixes via Windows PowerShell para StorSimple
Ao contrário das atualizações para o Microsoft Azure StorSimple, os hotfixes são instalados a partir de uma pasta partilhada. Tal como acontece com as atualizações, existem dois tipos de hotfixos: 

* Hotfixos regulares 
* Hotfixos do modo de manutenção  

Os seguintes procedimentos explicam como utilizar o Windows PowerShell para o StorSimple instalar artigos de forma regular e de manutenção.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>O que acontece às atualizações se realizar um reset de fábrica do dispositivo?
Se um dispositivo for reposto para as definições de fábrica, então todas as atualizações se perdem. Depois de o dispositivo de reset de fábrica estar registado e configurado, terá de instalar manualmente atualizações através do portal clássico Azure e/ou do Windows PowerShell para o StorSimple. Para obter mais informações sobre o reset da fábrica, consulte [Redefinir o dispositivo para as definições predefinidas da fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a utilização do Windows PowerShell para o StorSimple para administrar o seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).
* Saiba mais sobre [a utilização do serviço StorSimple Manager para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).

