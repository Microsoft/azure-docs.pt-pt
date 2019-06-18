---
title: Alterar o modo do dispositivo StorSimple | Documentos da Microsoft
description: Descreve os modos de dispositivo do StorSimple e explica como utilizar o Windows PowerShell para StorSimple para alterar o modo de dispositivo.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: e55964beff48df6ce24d99c01975d39b662f1612
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60576096"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Alterar o modo de dispositivo no dispositivo StorSimple

Este artigo fornece uma breve descrição os diversos modos nos quais o dispositivo StorSimple pode operar. O dispositivo StorSimple, pode funcionar em três modos: normal, a manutenção e a recuperação.

Depois de ler este artigo, ficará a saber:

* São de que os modos de dispositivo do StorSimple
* Como descobrir que modo o dispositivo StorSimple está em
* Como alterar do normal para o modo de manutenção e *vice-versa*

As tarefas de gestão acima só podem ser efetuadas através da interface do Windows PowerShell do dispositivo StorSimple.

## <a name="about-storsimple-device-modes"></a>Sobre os modos de dispositivo do StorSimple

O dispositivo StorSimple pode operar no modo normal, manutenção ou recuperação. Cada um desses modos resumidamente é descrita abaixo.

### <a name="normal-mode"></a>Modo normal

Isso é definido como o modo operacional normal para um dispositivo StorSimple totalmente configurado. Por predefinição, o dispositivo deve estar no modo normal.

### <a name="maintenance-mode"></a>Modo de manutenção

Por vezes, o dispositivo StorSimple poderá ter de ser colocado no modo de manutenção. Este modo permite-lhe realizar a manutenção no dispositivo e instalar atualizações disruptivas, como os relacionados com o firmware do disco.

Pode colocar o sistema no modo de manutenção apenas através do Windows PowerShell para StorSimple. Todos os pedidos de e/s são colocadas em pausa nesse modo. Serviços, tais como a memória de acesso de aleatório não volátil (NVRAM) ou o serviço de clustering também são paradas. Ambos os controladores são reiniciados quando entrem ou saia neste modo. Quando sair do modo de manutenção, todos os serviços serão retomada e devem ser bom estado de funcionamento. Esta operação poderá demorar alguns minutos.

> [!NOTE]
> **Modo de manutenção só é suportado num dispositivo está a funcionar corretamente. Não é suportado num dispositivo em que um ou ambos os controladores não estão a funcionar.**


### <a name="recovery-mode"></a>Modo de recuperação

Modo de recuperação pode ser descrito como "Modo seguro para o Windows com suporte de rede". Modo de recuperação seja aplicada a equipe de Support da Microsoft e permite que execute o diagnóstico do sistema. O objetivo principal do modo de recuperação é recuperar os registos do sistema.

Se o seu sistema entra no modo de recuperação, deve contactar o Support da Microsoft para passos seguintes. Para obter mais informações, aceda a [contacte o suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Não é possível colocar o dispositivo no modo de recuperação. Se o dispositivo está num Estado incorreto, modo de recuperação tenta obter o dispositivo num Estado em que o pessoal de Support da Microsoft pode examiná-lo.**

## <a name="determine-storsimple-device-mode"></a>Determinar o modo do dispositivo StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Para determinar o modo atual do dispositivo

1. Inicie sessão da consola de série do dispositivo ao seguir os passos em [utilizar o PuTTY para ligar a consola de série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Ver a mensagem de faixa no menu da consola de série do dispositivo. Esta mensagem indica explicitamente se o dispositivo está no modo de manutenção ou de recuperação. Se a mensagem não contém quaisquer informações específicas relacionadas para o modo de sistema, o dispositivo estiver no modo normal.

## <a name="change-the-storsimple-device-mode"></a>Alterar o modo de dispositivo do StorSimple

Pode colocar o dispositivo StorSimple no modo de manutenção (a partir do modo normal) para executar a manutenção ou instalar atualizações de modo de manutenção. Efetue os seguintes procedimentos para entrar ou sair do modo de manutenção.

> [!IMPORTANT]
> Antes de entrar no modo de manutenção, certifique-se de que os dois controladores de dispositivo estão em bom Estados ao aceder a **definições do dispositivo > Estado de funcionamento do Hardware** para o seu dispositivo no portal do Azure. Se um ou ambos os controladores não estão em bom Estados, contacte o Support da Microsoft para os passos seguintes. Para obter mais informações, aceda a [contacte o suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Para entrar no modo de manutenção

1. Inicie sessão da consola de série do dispositivo ao seguir os passos em [utilizar o PuTTY para ligar a consola de série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Quando solicitado, forneça o **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é: `Password1`.
3. Na linha de comandos, escreva 
   
    `Enter-HcsMaintenanceMode`
4. Verá uma mensagem de aviso informando que o modo de manutenção irá interromper todos os pedidos de e/s e a ligação ao portal do Azure do servidor, e será solicitado para confirmação. Tipo **Y** para entrar no modo de manutenção.
5. Os dois controladores serão reiniciado. Quando o reinício estiver concluído, a faixa de consola de série indica que o dispositivo estiver no modo de manutenção. É apresentada abaixo uma saída de exemplo.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>Para sair do modo de manutenção

1. Inicie sessão na consola de série do dispositivo. Certifique-se da mensagem de faixa que o dispositivo está no modo de manutenção.
2. Na linha de comandos, escreva:
   
    `Exit-HcsMaintenanceMode`
3. Serão apresentada uma mensagem de aviso e uma mensagem de confirmação. Tipo **Y** para sair do modo de manutenção.
4. Os dois controladores serão reiniciado. Quando o reinício estiver concluído, a faixa de consola de série indica que o dispositivo estiver no modo normal. É apresentada abaixo uma saída de exemplo.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Passos Seguintes

Saiba como [aplicar atualizações de modo normal e a manutenção](storsimple-update-device.md) no dispositivo StorSimple.

