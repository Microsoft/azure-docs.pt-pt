---
title: Alterar o modo de dispositivo StorSimple [ StorSimple] Microsoft Docs
description: Descreve os modos do dispositivo StorSimple e explica como utilizar o Windows PowerShell para o StorSimple para alterar o modo de dispositivo.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60576096"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Altere o modo do dispositivo no seu dispositivo StorSimple

Este artigo fornece uma breve descrição dos vários modos em que o seu dispositivo StorSimple pode funcionar. O seu dispositivo StorSimple pode funcionar em três modos: normal, manutenção e recuperação.

Depois de ler este artigo, saberá:

* Quais são os modos de dispositivo StorSimple
* Como descobrir em que modo o dispositivo StorSimple está
* Como mudar do modo normal para o modo de manutenção e *vice-versa*

As tarefas de gestão acima só podem ser executadas através da interface Windows PowerShell do seu dispositivo StorSimple.

## <a name="about-storsimple-device-modes"></a>Sobre os modos de dispositivo StorSimple

O seu dispositivo StorSimple pode funcionar em modo normal, de manutenção ou de recuperação. Cada um destes modos é brevemente descrito abaixo.

### <a name="normal-mode"></a>Modo normal

Isto é definido como o modo operacional normal para um dispositivo StorSimple totalmente configurado. Por defeito, o seu dispositivo deve estar em modo normal.

### <a name="maintenance-mode"></a>Modo de manutenção

Por vezes, o dispositivo StorSimple pode ter de ser colocado no modo de manutenção. Este modo permite-lhe efetuar a manutenção do dispositivo e instalar atualizações disruptivas, como as relacionadas com o firmware do disco.

Só é possível colocar o sistema em modo de manutenção através do Windows PowerShell para o StorSimple. Todos os pedidos de I/O são interrompidos neste modo. Serviços como a memória de acesso aleatório não volátil (NVRAM) ou o serviço de agrupamento também estão parados. Ambos os controladores são reiniciados quando entra ou sai deste modo. Quando sair do modo de manutenção, todos os serviços serão retomados e devem ser saudáveis. Esta operação poderá demorar alguns minutos.

> [!NOTE]
> **O modo de manutenção só é suportado num dispositivo de funcionamento correto. Não é suportado num dispositivo em que um ou ambos os controladores não estejam a funcionar.**


### <a name="recovery-mode"></a>Modo de recuperação

O modo de recuperação pode ser descrito como "Modo De Segurança do Windows com suporte de rede". O modo de recuperação envolve a equipa de Suporte do Microsoft e permite-lhes realizar diagnósticos no sistema. O principal objetivo do modo de recuperação é recuperar os registos do sistema.

Se o seu sistema entrar em modo de recuperação, deverá contactar o Microsoft Support para os próximos passos. Para mais informações, [contacte](storsimple-8000-contact-microsoft-support.md)o Suporte da Microsoft .

> [!NOTE]
> **Não é possível colocar o dispositivo em modo de recuperação. Se o dispositivo estiver em mau estado, o modo de recuperação tenta colocar o dispositivo num estado em que o pessoal do Microsoft Support pode examiná-lo.**

## <a name="determine-storsimple-device-mode"></a>Determine StorSimple device mode (Determinar o modo de dispositivo StorSimple)

#### <a name="to-determine-the-current-device-mode"></a>Para determinar o modo de dispositivo atual

1. Inicie o acesso à consola em série do dispositivo seguindo os passos em Utilizar o [PuTTY para se ligar à consola de série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Veja a mensagem de banner no menu de consola em série do dispositivo. Esta mensagem indica explicitamente se o dispositivo está no modo de manutenção ou recuperação. Se a mensagem não contiver nenhuma informação específica relativa ao modo de sistema, o dispositivo encontra-se em modo normal.

## <a name="change-the-storsimple-device-mode"></a>Alterar o modo de dispositivo StorSimple

Pode colocar o dispositivo StorSimple no modo de manutenção (do modo normal) para efetuar atualizações do modo de manutenção ou instalar atualizações do modo de manutenção. Execute os seguintes procedimentos para entrar ou sair do modo de manutenção.

> [!IMPORTANT]
> Antes de entrar no modo de manutenção, verifique se ambos os controladores do dispositivo estão saudáveis acedendo às definições do **Dispositivo > saúde** de hardware para o seu dispositivo no portal Azure. Se um ou ambos os controladores não estiverem saudáveis, contacte o Microsoft Support para os próximos passos. Para mais informações, [contacte](storsimple-8000-contact-microsoft-support.md)o Suporte da Microsoft .
 

#### <a name="to-enter-maintenance-mode"></a>Para entrar no modo de manutenção

1. Inicie o acesso à consola em série do dispositivo seguindo os passos em Utilizar o [PuTTY para se ligar à consola de série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu de consola em série, escolha a opção 1, **inicie sessão com acesso total**. Quando solicitado, forneça a **palavra-passe**do administrador do dispositivo . A palavra-passe `Password1`padrão é: .
3. No pedido de comando, tipo 
   
    `Enter-HcsMaintenanceMode`
4. Verá uma mensagem de aviso a dizer-lhe que o modo de manutenção irá interromper todos os pedidos de I/O e cortar a ligação ao portal Azure, e será solicitado para confirmação. Digite **Y** para entrar no modo de manutenção.
5. Ambos os controladores vão reiniciar. Quando o reinício estiver concluído, o banner da consola em série indicará que o dispositivo está no modo de manutenção. É apresentada abaixo uma saída de exemplo.

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

1. Inicie sessão na consola em série do dispositivo. Verifique a partir da mensagem de banner que o seu dispositivo está em modo de manutenção.
2. Na linha de comandos, escreva:
   
    `Exit-HcsMaintenanceMode`
3. Aparecerá uma mensagem de aviso e uma mensagem de confirmação. Digite **Y** para sair do modo de manutenção.
4. Ambos os controladores vão reiniciar. Quando o reinício estiver concluído, o banner da consola em série indica que o dispositivo está em modo normal. É apresentada abaixo uma saída de exemplo.

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

## <a name="next-steps"></a>Passos seguintes

Saiba como [aplicar atualizações normais e](storsimple-update-device.md) de modo de manutenção no seu dispositivo StorSimple.

