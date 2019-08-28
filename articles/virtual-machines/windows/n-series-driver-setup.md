---
title: Instalação do driver de GPU da série N do Azure para Windows | Microsoft Docs
description: Como configurar Drivers NVIDIA GPU para VMs da série N que executam o Windows Server ou o Windows no Azure
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc6e37b088c6bcbb2de4693eb50be661db869ecd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089196"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Instalar drivers NVIDIA GPU em VMs da série N que executam o Windows 

Para aproveitar os recursos de GPU das VMs da série N do Azure que executam o Windows, os drivers NVIDIA GPU devem ser instalados. A [extensão de Driver Nvidia GPU](../extensions/hpccompute-gpu-windows.md) instala drivers NVIDIA CUDA ou Grid apropriados em uma VM da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas como Azure PowerShell ou Azure Resource Manager modelos. Consulte a [documentação de extensão do Driver Nvidia GPU](../extensions/hpccompute-gpu-windows.md) para obter os sistemas operacionais e as etapas de implantação com suporte.

Se você optar por instalar drivers de GPU manualmente, este artigo fornecerá os sistemas operacionais, drivers e etapas de instalação e verificação com suporte. As informações de configuração manual do driver também estão disponíveis para [VMs do Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter especificações básicas, capacidades de armazenamento e detalhes de disco, consulte [tamanhos de VM Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalação do driver

1. Conecte-se por Área de Trabalho Remota a cada VM da série N.

2. Baixe, extraia e instale o driver com suporte para seu sistema operacional Windows.

Após a instalação do driver de grade em uma VM, uma reinicialização é necessária. Após a instalação do driver CUDA, uma reinicialização não é necessária.

## <a name="verify-driver-installation"></a>Verificar a instalação do driver

Você pode verificar a instalação do driver no Device Manager. O exemplo a seguir mostra a configuração bem-sucedida do cartão Tesla K80 em uma VM NC do Azure.

![Propriedades do driver GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Para consultar o estado do dispositivo GPU, execute o utilitário de linha de comando [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver.

1. Abra um prompt de comando e altere para o diretório **C:\Program Files\NVIDIA Corporation\NVSMI**

2. Execute `nvidia-smi`. Se o driver estiver instalado, você verá uma saída semelhante à seguinte. O **GPU-util** mostra **0%** , a menos que você esteja executando uma carga de trabalho de GPU na VM no momento. A versão do driver e os detalhes da GPU podem ser diferentes dos mostrados.

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Conectividade de rede RDMA

A conectividade de rede RDMA pode ser habilitada em VMs da série N compatíveis com RDMA, como o NC24r implantado no mesmo conjunto de disponibilidade ou em um grupo de posicionamento único em um conjunto de dimensionamento de máquinas virtuais. A extensão HpcVmDrivers deve ser adicionada para instalar drivers de dispositivo de rede do Windows que habilitam a conectividade RDMA. Para adicionar a extensão de VM a uma VM da série N habilitada para RDMA, use [Azure PowerShell](/powershell/azure/overview) cmdlets para Azure Resource Manager.

Para instalar a extensão mais recente da versão 1,1 do HpcVMDrivers em uma VM compatível com RDMA existente chamada myVM na região oeste dos EUA:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Para obter mais informações, consulte [extensões e recursos de máquina virtual do Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

A rede RDMA dá suporte ao tráfego de interface de transmissão de mensagens (MPI) para aplicativos executados com [o Microsoft MPI ou o](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) Intel MPI 5. x. 


## <a name="next-steps"></a>Passos Seguintes

* Os desenvolvedores que criam aplicativos acelerados por GPU para GPUs NVIDIA Tesla também podem baixar e instalar o [Kit de ferramentas do CUDA](https://developer.nvidia.com/cuda-downloads)mais recente. Para obter mais informações, consulte o [Guia de instalação do CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


