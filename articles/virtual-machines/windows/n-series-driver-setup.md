---
title: Configuração do controlador GPU da série NVIDIA para Windows
description: Como configurar controladores GPU NVIDIA para VMs da série N que executam o Windows Server ou Windows em Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05487db1d0522634eda606dcb2ba22876fc341db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557442"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Instale controladores GPU da NVIDIA em VMs da série N que executam o Windows 

Para tirar partido das capacidades da GPU de VMs da série Azure N apoiados pelos GPUs da NVIDIA, tem de instalar os controladores gpu da NVIDIA. A [extensão do condutor do GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) instala controladores NVIDIA CUDA ou GRID apropriados num VM da série N. Instale ou gerencie a extensão utilizando o portal Azure ou ferramentas como os modelos Azure PowerShell ou Azure Resource Manager. Consulte a documentação da [extensão do condutor do GPU da NVIDIA](../extensions/hpccompute-gpu-windows.md) para sistemas operativos suportados e etapas de implantação.

Se optar por instalar manualmente os controladores da NVIDIA GPU, este artigo fornece sistemas operativos suportados, controladores e etapas de instalação e verificação. Informações de configuração manual do controlador também estão disponíveis para [Os VMs Linux](../linux/n-series-driver-setup.md).

Para especificações básicas, capacidades de armazenamento e detalhes do disco, consulte [os tamanhos VM do GPU Windows](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalação do condutor

1. Conecte-se por Remote Desktop a cada VM da série N.

2. Faça o download, extraia e instale o controlador suportado para o seu sistema operativo Windows.

Após a instalação do controlador GRID num VM, é necessário reiniciar. Após a instalação do controlador CUDA, não é necessário reiniciar um reinício.

## <a name="verify-driver-installation"></a>Verificar a instalação do condutor

Tenha em atenção que o painel de controlo da Nvidia só está acessível com a instalação do controlador GRID. Se tiver instalado os controladores CUDA, o painel de controlo da Nvidia não será visível.

Pode verificar a instalação do controlador no Gestor de Dispositivos. O exemplo a seguir mostra uma configuração bem sucedida do cartão Tesla K80 num VM Azure NC.

![Propriedades do condutor da GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Para consultar o estado do dispositivo GPU, corram o utilitário da linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o condutor.

1. Abra uma solicitação de comando e mude para o **diretório C:\Program Files\NVIDIA Corporation\NVSMI.**

2. Execute `nvidia-smi`. Se o controlador estiver instalado, verá uma saída semelhante à seguinte. O **GPU-Util** mostra **0%** a menos que esteja a executar uma carga de trabalho gpu no VM. A versão do seu condutor e os detalhes da GPU podem ser diferentes dos apresentados.

![Estado do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Conectividade da rede RDMA

A conectividade da rede RDMA pode ser ativada em VMs da série N com capacidade RDMA, tais como NC24r implantados no mesmo conjunto de disponibilidade ou num único grupo de colocação num conjunto de escala de máquina virtual. A extensão HpcVmDrivers deve ser adicionada para instalar controladores de dispositivos de rede Windows que permitem a conectividade RDMA. Para adicionar a extensão VM a um VM da série N ativado por RDMA, utilize cmdlets [Azure PowerShell](/powershell/azure/) para O Gestor de Recursos Azure.

Para instalar a mais recente extensão 1.1 HpcVMDrivers numa VM existente com capacidade de RDMA chamada myVM na região dos EUA Ocidentais:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Para obter mais informações, consulte [extensões e funcionalidades da máquina Virtual para Windows](../extensions/features-windows.md).

A rede RDMA suporta o tráfego de Interface de Passagem de Mensagens (MPI) para aplicações em execução com [Microsoft MPI](/message-passing-interface/microsoft-mpi) ou Intel MPI 5.x. 


## <a name="next-steps"></a>Passos seguintes

* Os desenvolvedores que coam aplicações aceleradas pela GPU para os GPUs da NVIDIA Tesla também podem descarregar e instalar o mais recente [KIT de Ferramentas CUDA](https://developer.nvidia.com/cuda-downloads). Para mais informações, consulte o [Guia de Instalação da CUDA.](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)
