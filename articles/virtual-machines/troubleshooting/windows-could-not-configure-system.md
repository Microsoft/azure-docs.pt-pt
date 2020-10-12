---
title: Resolução de problemas em que o Windows não consegue concluir a configuração do sistema
titlesuffix: Azure Virtual Machines
description: Este artigo fornece medidas para resolver problemas em que o processo Sysprep impede o arranque de um Azure VM.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 9c170607d6300c4921285e85ac78db5a8a18ad9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90078813"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Resolução de problemas em que o Windows não consegue concluir a configuração do sistema

Este artigo fornece medidas para resolver problemas em que o processo Sysprep impede o arranque de uma máquina virtual Azure (VM).

## <a name="symptom"></a>Sintoma

Quando utilizar [diagnósticos boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a imagem do VM, verá que a imagem exibe um erro de instalação do Windows enquanto a configuração do Windows está a iniciar os serviços. O erro mostrará a mensagem:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![A Figura 1 apresenta um erro de instalação do Windows com a mensagem: "O Windows não conseguiu terminar a configuração do sistema. Para tentar retomar a configuração, reinicie o computador. A configuração está a iniciar serviços"](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Causa

Este erro é causado quando o sistema operativo (OS) não consegue completar o [processo Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-process-overview). Este erro ocorrerá quando se tenta uma bota inicial de um VM generalizado. Se encontrar este problema, recrie a imagem generalizada, uma vez que a imagem está num estado intransmissível e não pode ser recuperada.

## <a name="solution"></a>Solução

Para corrigir este problema, siga a orientação do [Azure sobre a preparação/captura de uma imagem](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed) e prepare uma nova imagem generalizada.
