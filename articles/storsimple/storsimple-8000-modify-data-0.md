---
title: Modificar dados 0 definições no dispositivo da série StorSimple 8000 | Documentos da Microsoft
description: Saiba como utilizar o Windows PowerShell para StorSimple para reconfigurar a interface de rede 0 de dados no dispositivo StorSimple.
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
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 3cf136c5ddec8f4998d15c597914e1f806453945
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60631588"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Modificar as definições de interface de rede 0 de dados no seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral

O seu dispositivo do Microsoft Azure StorSimple tem seis interfaces de rede, dos dados 0 a 5 de dados. Os dados 0 interface é sempre configurada por meio da interface do Windows PowerShell ou da consola de série e é ativado automaticamente na cloud. Tenha em atenção que não é possível configurar interface rede DATA 0 através do portal do Azure.

Os dados 0 a interface de rede pela primeira vez é configurada através do Assistente de configuração durante a inicial de implementação do dispositivo StorSimple. Quando o dispositivo estiver em modo operacional, poderá ter de reconfigurar os dados 0 definições. Este tutorial fornece dois métodos para modificar dados 0 definições, ambas as PowerShell através do Windows para o StorSimple de rede.

Depois de ler este tutorial, será capaz de:

* Modificar dados 0 definição através do Assistente de configuração de rede
* Modificar as definições de rede 0 de dados por meio do `Set-HcsNetInterface` cmdlet

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Modificar as definições de rede 0 de dados por meio do Assistente de configuração
Pode reconfigurar as definições de rede 0 de dados ao ligar à interface do Windows PowerShell do dispositivo StorSimple e iniciar uma sessão de Assistente de configuração. Execute os seguintes passos para modificar dados 0 definições:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Para modificar as definições de rede 0 de dados por meio do Assistente de configuração
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Quando lhe for pedido fornecem a **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é `Password1`.
2. Na linha de comandos, escreva:
   
    `Invoke-HcsSetupWizard`
3. É apresentado um Assistente de configuração ajudar a configurar os dados 0 interface do seu dispositivo. Fornece novos valores para o endereço IP, o gateway e a máscara de rede.

> [!NOTE]
> Os controladores de fixos IPs serão têm de ser reconfiguradas através da **as definições de rede** painel do dispositivo StorSimple no portal do Azure. Para obter mais informações, aceda a [modificar interfaces de rede](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modificar as definições de rede 0 de dados através do cmdlet Set-HcsNetInterface
Outra forma de dados 0 é a interface de rede através da utilização de reconfigurar o `Set-HcsNetInterface` cmdlet. O cmdlet é executado a partir da interface do Windows PowerShell do dispositivo StorSimple. Ao utilizar este procedimento, o IPs fixos do controlador também pode ser configurado aqui. Execute os seguintes passos para modificar os dados 0 definições: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Para modificar as definições de rede 0 de dados através do cmdlet Set-HcsNetInterface
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Quando lhe for solicitado a fornecer a palavra-passe de administrador do dispositivo. A palavra-passe predefinida é `Password1`.
2. Na linha de comandos, escreva:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    Inclinado entre colchetes, escreva os seguintes valores para os dados 0:
   
   * Endereço IPv4
   * IPv4 gateway
   * IPv4 subnet mask
   * Endereço IPv4 fixo para o controlador 0
   * Endereço IPv4 fixo para o controlador 1
     
     Para obter mais informações sobre a utilização deste cmdlet, aceda a [Windows PowerShell para StorSimple referência de cmdlet](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Passos Seguintes
* Para configurar as interfaces de rede que não sejam dados 0, pode utilizar o [configurar definições de rede no portal do Azure](storsimple-8000-modify-device-config.md). 
* Se tiver quaisquer problemas ao configurar as interfaces de rede, consulte [resolver problemas de implementação](storsimple-troubleshoot-deployment.md).

