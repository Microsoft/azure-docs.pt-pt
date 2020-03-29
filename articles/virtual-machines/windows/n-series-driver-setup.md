---
title: Configuração do piloto da GPU da série N Azurpara windows
description: Como configurar os controladores GPU da NVIDIA para VMs da série N que executam o Windows Server ou o Windows em Azure
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
ms.openlocfilehash: 631266f983886e3ca34d609b425f8a71b808b39f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919401"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Instale os controladores GPU da NVIDIA em VMs da série N que executam o Windows 

Para tirar partido das capacidades de GPU dos VMs da série N Azure que executam o Windows, os controladores GPU da NVIDIA devem ser instalados. A Extensão do [Condutor GPU da NVIDIA](../extensions/hpccompute-gpu-windows.md) instala os condutores adequados da NVIDIA CUDA ou grid num VM da série N. Instale ou gerea extensão utilizando o portal Azure ou ferramentas como os modelos Azure PowerShell ou Azure Resource Manager. Consulte a documentação de extensão do [condutor da NVIDIA GPU](../extensions/hpccompute-gpu-windows.md) para sistemas operativos suportados e etapas de implementação.

Se optar por instalar manualmente os controladores GPU, este artigo fornece sistemas operativos, controladores e etapas de instalação e verificação suportados. A informação manual de configuração do controlador também está disponível para [VMs Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para especificações básicas, capacidades de armazenamento e detalhes do disco, consulte os [tamanhos de VM do Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalação do condutor

1. Ligue-se por Remote Desktop a cada VM da série N.

2. Descarregue, extraie e instale o controlador suportado para o seu sistema operativo Windows.

Após a instalação do controlador GRID num VM, é necessário reiniciar. Após a instalação do controlador CUDA, não é necessário reiniciar.

## <a name="verify-driver-installation"></a>Verificar a instalação do condutor

Por favor, note que o painel de controlo nvidia só está acessível com a instalação do controlador GRID. Se tiver instalado controladores CUDA, o painel de controlo Nvidia não será visível.

Pode verificar a instalação do controlador no Gestor de Dispositivos. O exemplo seguinte mostra uma configuração bem sucedida do cartão Tesla K80 num VM Nc Azure.

![Propriedades do condutor da GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Para consultar o estado do dispositivo GPU, execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o controlador.

1. Abra um pedido de comando e altere para o diretório **C:\Program Files\NVIDIA Corporation\NVSMI.**

2. Execute `nvidia-smi`. Se o controlador estiver instalado, verá uma saída semelhante à seguinte. O **GPU-Util** mostra **0%** a menos que esteja atualmente a executar uma carga de trabalho de GPU no VM. A versão do seu condutor e os detalhes da GPU podem ser diferentes dos mostrados.

![Estado do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Conectividade da rede RDMA

A conectividade da rede RDMA pode ser ativada em VMs de série N com capacidade RDMA, tais como NC24r implantados no mesmo conjunto de disponibilidade ou num único grupo de colocação num conjunto de escala de máquina virtual. A extensão HpcVmDrivers deve ser adicionada para instalar controladores de dispositivos de rede Windows que permitam a conectividade RDMA. Para adicionar a extensão VM a um VM de série N ativado por RDMA, utilize cmdlets [Azure PowerShell](/powershell/azure/overview) para O Gestor de Recursos Azure.

Para instalar a mais recente extensão da versão 1.1 HpcVMDrivers num VM com capacidade rdma existente chamado myVM na região dos EUA Ocidentais:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Para mais informações, consulte [extensões e funcionalidades da máquina Virtual para Windows](extensions-features.md).

A rede RDMA suporta o tráfego de Interface de Passagem de Mensagens (MPI) para aplicações em execução com [MPI microsoft](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) ou Intel MPI 5.x. 


## <a name="next-steps"></a>Passos seguintes

* Os desenvolvedores que construem aplicações gpu-accelerated para as GPUs nVIDIA Tesla também podem descarregar e instalar o mais recente KIT de [Ferramentas CUDA](https://developer.nvidia.com/cuda-downloads). Para mais informações, consulte o Guia de Instalação da [CUDA.](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)


