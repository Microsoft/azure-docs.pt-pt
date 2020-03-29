---
title: Configuração do piloto da GPU da série N Azure para windows
description: Como configurar os controladores GPU da AMD para VMs da série N que executam o Windows Server ou o Windows em Azure
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 164f07f6545c1c225814958bba5722536b11a9b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269427"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instale controladores GPU AMD em VMs da série N executando Windows

Para tirar partido das capacidades de GPU dos novos VMs da série Azure NVv4 que executam o Windows, os controladores GPU da AMD devem ser instalados. A extensão do condutor da AMD estará disponível nas próximas semanas. Este artigo fornece sistemas operativos suportados, controladores e passos de instalação e verificação manuais.

Para especificações básicas, capacidades de armazenamento e detalhes do disco, consulte os [tamanhos de VM do Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

| SO | Controlador |
| -------- |------------- |
| Windows 10 EVD - Construção 1903 <br/><br/>Windows 10 - Construção 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Instalação do condutor

1. Ligue-se por Remote Desktop a cada VM da série NVv4.

2. Se é um cliente de pré-visualização NVv4, por favor pare o VM e espere que este se mude para o estado Stop(Deallocated).

3. Por favor, inicie o VM e, em seguida, desinstale o controlador de pré-visualização executando "amdcleanuputility-x64.exe" localizado na pasta "...\AMDCleanUninstallUtility". O caminho exato variará em função dos ficheiros de instalação do condutor anteriores.  

4. Faça o download e instale o mais recente controlador.

5. Reiniciar o VM.

## <a name="verify-driver-installation"></a>Verificar a instalação do condutor

Pode verificar a instalação do controlador no Gestor de Dispositivos. O exemplo seguinte mostra uma configuração bem sucedida do cartão Radeon Instinct MI25 num VM Azure NVv4.
<br />
![Propriedades do condutor da GPU](./media/n-series-amd-driver-setup/device-manager.png)

Pode utilizar o dxdiag para verificar as propriedades de exibição de GPU, incluindo a RAM de vídeo. O exemplo seguinte mostra uma partição de 1/8 do cartão Radeon Instinct MI25 num VM Azure NVv4.
<br />
![Propriedades do condutor da GPU](./media/n-series-amd-driver-setup/dxdiag.png)
