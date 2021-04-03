---
title: Atualize o seu dispositivo StorSimple | Microsoft Docs
description: Explica como utilizar a função de atualização StorSimple para instalar atualizações e ajustes de modo de manutenção regulares.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: faf06775b78c5a7c90cea000ac0a1eb768107ef4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956845"
---
# <a name="update-your-storsimple-8000-series-device"></a>Atualize o seu dispositivo StorSimple 8000 Series
> [!NOTE]
> O portal clássico do StorSimple foi preterido. Os Gestores de Dispositivos do StorSimple vão ser migrados automaticamente para o portal do Azure novo, de acordo com a agenda de preterição. Receberá uma mensagem de e-mail e uma notificação no portal relativamente a esta migração. Este documento também será descontinuado em breve. Relativamente a perguntas sobre a migração, veja [FAQ: Move to Azure portal](./index.yml) (FAQ: migrar para o portal do Azure).

## <a name="overview"></a>Descrição Geral
As atualizações StorSimple permitem-lhe manter facilmente o seu dispositivo StorSimple atualizado. Dependendo do tipo de atualização, pode aplicar atualizações ao dispositivo através do portal clássico Azure ou através da interface Windows PowerShell. Este tutorial descreve os tipos de atualização e como instalar cada um deles.

Pode aplicar dois tipos de atualizações de dispositivos: 

* Atualizações regulares (ou modo normal)
* Atualizações do modo de manutenção

Pode instalar atualizações regulares através do portal clássico Azure ou do Windows PowerShell; no entanto, tem de utilizar o Windows PowerShell para instalar atualizações do modo manutenção. 

Cada tipo de atualização é descrito separadamente, abaixo.

### <a name="regular-updates"></a>Atualizações regulares
Atualizações regulares são atualizações não disruptivas que podem ser instaladas quando o dispositivo está no modo Normal. Estas atualizações são aplicadas através do website Microsoft Update para cada controlador de dispositivos. 

> [!IMPORTANT]
> Pode ocorrer uma falha no controlador durante o processo de atualização. No entanto, tal não afetará a disponibilidade ou o funcionamento do sistema.
> 
> 

* Para mais detalhes sobre como instalar atualizações regulares através do portal clássico Azure, consulte [instalar atualizações regulares através do portal clássico Azure](#install-regular-updates-via-the-azure-classic-portal).
* Também pode instalar atualizações regulares através do Windows PowerShell para StorSimple. Para mais informações, consulte [instalar atualizações regulares através do Windows PowerShell para StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Atualizações do modo de manutenção
As atualizações do Modo de Manutenção são atualizações disruptivas, tais como atualizações de firmware de disco. Estas atualizações requerem que o dispositivo seja colocado no modo manutenção. Para mais informações, consulte [o passo 2: Introduza o modo manutenção](#step2). Não é possível utilizar o portal clássico Azure para instalar atualizações do modo manutenção. Em vez disso, tem de utilizar o Windows PowerShell para StorSimple. 

Para obter mais informações sobre como instalar atualizações do modo manutenção, consulte [as atualizações do modo de manutenção do Windows através do Windows PowerShell para StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> As atualizações do modo de manutenção devem ser aplicadas separadamente a cada controlador. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instale atualizações regulares através do portal clássico Azure
Pode utilizar o portal clássico Azure para aplicar atualizações no seu dispositivo StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instale atualizações regulares via Windows PowerShell para StorSimple
Em alternativa, pode utilizar o Windows PowerShell para storSimple para aplicar atualizações regulares (modo Normal).

> [!IMPORTANT]
> Apesar de poder instalar atualizações regulares utilizando o Windows PowerShell para StorSimple, recomendamos vivamente que instale atualizações regulares através do portal clássico Azure. A partir da Atualização 1, as pré-verificações serão realizadas antes da instalação de atualizações a partir do portal. Estes pré-controlos impedirão falhas e assegurarão uma experiência mais suave. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instale atualizações do modo manutenção através do Windows PowerShell para StorSimple
Utilize o Windows PowerShell para storSimple para aplicar atualizações do modo de manutenção no seu dispositivo StorSimple. Todos os pedidos de E/S são interrompidos neste modo. Serviços como a memória de acesso aleatório não volátil (NVRAM) ou o serviço de agrupamento também estão parados. Ambos os controladores são reiniciados quando entra ou sai deste modo. Ao sair deste modo, todos os serviços serão retomados e devem ser saudáveis. (Isto pode demorar alguns minutos.)

Se precisar de aplicar atualizações do modo manutenção, receberá um alerta através do portal clássico Azure de que tem atualizações que devem ser instaladas. Este alerta incluirá instruções para a utilização do Windows PowerShell para o StorSimple instalar as atualizações. Depois de atualizar o seu dispositivo, utilize o mesmo procedimento para alterar o dispositivo para o modo Regular. Para obter instruções passo a passo, consulte [o passo 4: Modo de manutenção de saída](#step4).

> [!IMPORTANT]
> * Antes de entrar no modo manutenção, verifique se ambos os controladores do dispositivo estão saudáveis verificando o Estado do **Hardware** na página **manutenção** no portal clássico Azure. Se o controlador não estiver saudável, contacte o Microsoft Support para os próximos passos. Para mais informações, contacte o Microsoft Support. 
> * Quando estiver em modo manutenção, tem de aplicar a atualização primeiro num controlador e depois no outro controlador.
> 
> 

### <a name="step-1-connect-to-the-serial-console"></a>Passo 1: Ligar-se à consola em série <a name="step1"></a>
Primeiro, utilize uma aplicação como a PuTTY para aceder à consola em série. O procedimento a seguir explica como utilizar o PuTTY para ligar à consola em série.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode"></a>Passo 2: Entrar no modo de manutenção <a name="step2"></a>
Depois de ligar à consola, determine se existem atualizações para instalar e introduza o modo Manutenção para as instalar.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates"></a>Passo 3: Instale as suas atualizações <a name="step3"></a>
Em seguida, instale as suas atualizações.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode"></a>Passo 4: Modo de manutenção de saída <a name="step4"></a>
Finalmente, saída Modo manutenção.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instale hotfixes via Windows PowerShell para StorSimple
Ao contrário das atualizações para o Microsoft Azure StorSimple, os hotfixes são instalados a partir de uma pasta partilhada. Tal como acontece com as atualizações, existem dois tipos de hotfixes: 

* Hotfixes regulares 
* Hotfixes do modo de manutenção  

Os seguintes procedimentos explicam como utilizar o Windows PowerShell para o StorSimple instalar fixações regulares e de modo de manutenção.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>O que acontece com as atualizações se efetuar um reset de fábrica do dispositivo?
Se um dispositivo for reiniciado para as definições de fábrica, todas as atualizações são perdidas. Depois de o dispositivo de reset de fábrica estar registado e configurado, terá de instalar manualmente atualizações através do portal clássico Azure e/ou Do Windows PowerShell para storSimple. Para obter mais informações sobre o reset da fábrica, consulte [redefinir o dispositivo para as definições predefinidos da fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a utilização do Windows PowerShell para storSimple para administrar o seu dispositivo StorSimple](./storsimple-8000-windows-powershell-administration.md).
* Saiba mais sobre [a utilização do serviço StorSimple Manager para administrar o seu dispositivo StorSimple](./storsimple-8000-manager-service-administration.md).