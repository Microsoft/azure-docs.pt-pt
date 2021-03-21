---
title: Modificar as definições data 0 no dispositivo da série StorSimple 8000 | Microsoft Docs
description: Saiba como utilizar o Windows PowerShell para o StorSimple para reconfigurar a interface de rede DATA 0 no seu dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 85d7114f419266124d0d23368b24700af025758a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94961044"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Modifique as definições de interface de rede DATA 0 no seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral

O seu dispositivo Microsoft Azure StorSimple tem seis interfaces de rede, de DATA 0 a DATA 5. A interface DATA 0 é sempre configurada através da interface Windows PowerShell ou da consola em série, e está automaticamente ativada pela nuvem. Note que não é possível configurar a interface de rede DATA 0 através do portal Azure.

A interface DATA 0 é configurada pela primeira vez através do assistente de configuração durante a implementação inicial do dispositivo StorSimple. Quando o dispositivo estiver em modo operacional, poderá ser necessário reconfigurar as definições DATA 0. Este tutorial fornece dois métodos para modificar as definições de rede DATA 0, tanto através do Windows PowerShell para StorSimple.

Depois de ler este tutorial, poderá:

* Modificar a definição de rede DATA 0 através do assistente de configuração
* Modificar as definições de rede DATA 0 através do `Set-HcsNetInterface` cmdlet

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Modificar as definições de rede DATA 0 através do assistente de configuração
Pode reconfigurar as definições de rede DATA 0 ligando-a à interface Windows PowerShell do seu dispositivo StorSimple e lançando uma sessão de assistente de configuração. Execute os seguintes passos para modificar as definições DATA 0:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Para modificar as definições de rede DATA 0 através do assistente de configuração
1. No menu de consola em série, escolha a opção 1, **faça login com acesso total**. Quando solicitado, forneça a **palavra-passe do administrador do dispositivo**. A palavra-passe predefinida é `Password1` .
2. Na linha de comandos, escreva:
   
    `Invoke-HcsSetupWizard`
3. Um assistente de configuração parece ajudar a configurar a interface DATA 0 do seu dispositivo. Forneça novos valores para o endereço IP, gateway e netmask.

> [!NOTE]
> Os IPs dos controladores fixos terão de ser reconfigurados através da lâmina de **definição** de rede do dispositivo StorSimple no portal Azure. Para mais informações, aceda a [Modificar interfaces de rede.](storsimple-8000-modify-device-config.md#modify-network-interfaces)

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modificar as definições de rede DATA 0 através de Set-HcsNetInterface cmdlet
Uma forma alternativa de reconfigurar a interface de rede DATA 0 é através da utilização do `Set-HcsNetInterface` cmdlet. O cmdlet é executado a partir da interface Windows PowerShell do seu dispositivo StorSimple. Ao utilizar este procedimento, os IPs fixos do controlador também podem ser configurados aqui. Execute os seguintes passos para modificar as definições DATA 0: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Para modificar as definições de rede DATA 0 através do Set-HcsNetInterface cmdlet
1. No menu de consola em série, escolha a opção 1, **faça login com acesso total**. Quando solicitado, forneça a palavra-passe do administrador do dispositivo. A palavra-passe predefinida é `Password1` .
2. Na linha de comandos, escreva:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    Nos suportes angulares, digite os seguintes valores para DATA 0:
   
   * Endereço IPv4
   * Gateway IPv4
   * Máscara de sub-rede IPv4
   * Endereço IPv4 fixo para controlador 0
   * Endereço IPv4 fixo para controlador 1
     
     Para obter mais informações sobre a utilização deste cmdlet, aceda ao [Windows PowerShell para obter referência de cmdlet StorSimple](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)).

## <a name="next-steps"></a>Passos seguintes
* Para configurar interfaces de rede que não o DATA 0, pode utilizar as definições de [rede Configure no portal Azure](storsimple-8000-modify-device-config.md). 
* Se tiver algum problema ao configurar as suas interfaces de rede, consulte [os problemas de implementação da Resolução de Problemas](./storsimple-8000-troubleshoot-deployment.md).