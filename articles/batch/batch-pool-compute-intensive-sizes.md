---
title: Utilizar VMs de computação intensiva do Azure com o Batch | Documentos da Microsoft
description: Como tirar partido de HPC e GPU VM tamanhos de conjuntos do Azure Batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.author: lahugh
ms.openlocfilehash: d3152eb4b2512e05cad60772f8c4c75dd929758a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535595"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Utilizar o RDMA ou GPU instâncias em conjuntos do Batch

Para executar determinadas tarefas do Batch, pode tirar partido de tamanhos de VM do Azure concebido para a computação em grande escala. Por exemplo:

* Para executar várias instâncias [cargas de trabalho MPI](batch-mpi.md), escolha a série H ou outros tamanhos de que tem uma interface de rede para acesso remoto direto memória (RDMA). Estes tamanhos de ligar a uma rede InfiniBand para a comunicação entre nós, que pode acelerar aplicações MPI. 

* Para aplicativos de CUDA, escolha os tamanhos de série N que incluem placas (GPU unidade) de processamento de gráfico NVIDIA Tesla.

Este artigo fornece orientação e exemplos para usar alguns dos tamanhos de especializado do Azure em conjuntos do Batch. Para especificações e em segundo plano, consulte:

* Tamanhos de VM de computação de alto desempenho ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Tamanhos de VM ativadas para GPU ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Determinados tamanhos de VM podem não estar disponíveis nas regiões onde criar as contas do Batch. Para verificar que um tamanho está disponível, consulte [produtos disponíveis por região](https://azure.microsoft.com/regions/services/) e [escolha um tamanho VM para um conjunto do Batch](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Dependências

As capacidades RDMA ou GPU de tamanhos de computação intensiva no Batch são suportadas apenas em determinados sistemas operativos. (A lista de sistemas operativos suportados é um subconjunto daqueles suportada para máquinas virtuais criadas nestes tamanhos.) Dependendo de como criar o agrupamento do Batch, poderá ter de instalar ou configurar o controlador adicional ou outro software em nós. As tabelas seguintes resumem essas dependências. Consulte artigos ligados para obter detalhes. Para obter opções configurar os conjuntos do Batch, consulte mais adiante neste artigo.

### <a name="linux-pools---virtual-machine-configuration"></a>Conjuntos de Linux - configuração de Máquina Virtual

| Tamanho | Capacidade | Sistemas operativos | Software necessário | Definições do conjunto |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS, ou<br/>Baseada em centOS HPC<br/>(O azure Marketplace) | Intel MPI 5<br/><br/>Controladores de RDMA do Linux | Ativar a comunicação entre nós, desabilite a execução de tarefa em simultâneo |
| [NC, a NCv2, NCv3, NDv2 série](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (varia por série) | Ubuntu 16.04 LTS, ou<br/>CentOS 7.3 ou 7.4<br/>(O azure Marketplace) | Controladores de NVIDIA CUDA ou CUDA Toolkit | N/A | 
| [NV, NVv2 série](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS, ou<br/>CentOS 7.3<br/>(O azure Marketplace) | Controladores de GRID da NVIDIA | N/A |
<sup>*</sup>Tamanhos de série N com capacidade RDMA também incluem NVIDIA Tesla GPUs

### <a name="windows-pools---virtual-machine-configuration"></a>Agrupamentos do Windows - configuração de Máquina Virtual

| Tamanho | Capacidade | Sistemas operativos | Software necessário | Definições do conjunto |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2, ou<br/>2012 (do azure Marketplace) | Microsoft MPI 2012 R2 ou posterior, ou<br/> Intel MPI 5<br/><br/>Controladores de RDMA do Windows | Ativar a comunicação entre nós, desabilite a execução de tarefa em simultâneo |
| [NC, a NCv2, a NCv3, ND, NDv2 série](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (varia por série) | Windows Server 2016 ou <br/>2012 R2 (do Azure Marketplace) | Controladores de NVIDIA CUDA ou CUDA Toolkit| N/A | 
| [NV, NVv2 série](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 ou<br/>2012 R2 (do Azure Marketplace) | Controladores de GRID da NVIDIA | N/A |
<sup>*</sup>Tamanhos de série N com capacidade RDMA também incluem NVIDIA Tesla GPUs

### <a name="windows-pools---cloud-services-configuration"></a>Agrupamentos do Windows - configuração de serviços Cloud

> [!NOTE]
> Tamanhos de série N não são suportados em conjuntos do Batch com a configuração de serviço em nuvem.
>

| Tamanho | Capacidade | Sistemas operativos | Software necessário | Definições do conjunto |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012, ou<br/>2008 R2 (família de SO convidado) | Microsoft MPI 2012 R2 ou posterior, ou<br/>Intel MPI 5<br/><br/>Controladores de RDMA do Windows | Ativar a comunicação entre nós,<br/> desabilite a execução de tarefa em simultâneo |

## <a name="pool-configuration-options"></a>Opções de configuração de conjunto

Para configurar um tamanho de VM especializado para o seu conjunto do Batch, tem várias opções para instalar o software necessário ou de controladores:

* Para conjuntos na configuração da máquina virtual, escolha um pré-configurada [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) imagem de VM que tenha os drivers e software pré-instalado. Exemplos: 

  * [Com base no centOS 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) -inclui drivers RDMA e Intel MPI 5.1

  * [Máquina de Virtual de ciência de dados](../machine-learning/data-science-virtual-machine/overview.md) para Linux ou do Windows - inclui os controladores de NVIDIA CUDA

  * Imagens do Linux para cargas de trabalho do Batch contentor que também incluem controladores de GPU e RDMA:

    * [CentOS (com controladores GPU e RDMA) para conjuntos de contentor do Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (com controladores GPU e RDMA) para conjuntos de contentor do Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Criar uma [imagem personalizada do Windows ou Linux VM](batch-custom-images.md) no qual instalou drivers, software ou outras configurações necessárias para o tamanho da VM. 

* Criar um lote [pacote de aplicação](batch-application-packages.md) de um driver zipado ou o instalador da aplicação e configurar o Batch para implementar o pacote para nós do conjunto e instalar uma vez quando cada nó é criado. Por exemplo, se o pacote de aplicação for um instalador, crie uma [iniciar tarefa](batch-api-basics.md#start-task) linha de comandos para instalar automaticamente a aplicação em todos os nós do conjunto. Considere a utilização de um pacote de aplicação e um conjunto de tarefa de início, se a sua carga de trabalho depende de uma versão de controlador específico.

  > [!NOTE] 
  > A tarefa de início deve ser executado com permissões elevadas (administrador), e ele deve esperar para o sucesso. Tarefas de longa execução irão aumentar o tempo para aprovisionar um conjunto do Batch.
  >

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) configura automaticamente os controladores GPU e RDMA para trabalhar transparentemente com cargas de trabalho em contentores no Azure Batch. Batch Shipyard é totalmente controlado por com ficheiros de configuração. Existem muitos receita configurações de exemplo disponíveis que permitem cargas de trabalho GPU e RDMA, tais como o [CNTK GPU receita](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) que pré-configura os controladores de GPU em VMs de série N e carrega o software de ferramentas cognitivas da Microsoft como uma imagem do Docker.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Exemplo: Controladores de NVIDIA GPU no pool de VM de NC do Windows

Para executar aplicativos de CUDA num conjunto de nós de NC do Windows, terá de instalar os controladores de NVDIA GPU. Os seguintes passos de exemplo utilizam um pacote de aplicação para instalar os controladores de GPU da NVIDIA. Poderá escolher esta opção se a sua carga de trabalho depende de uma versão específica de controladores GPU.

1. Transferir um pacote de configuração para os controladores GPU no Windows Server 2016 a partir da [Web site da NVIDIA](https://www.nvidia.com/Download/index.aspx) , por exemplo, [versão 411.82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Guarde o ficheiro localmente com um nome abreviado como *GPUDriverSetup.exe*.
2. Crie um ficheiro zip do pacote.
3. Carregar o pacote para a sua conta do Batch. Para obter os passos, consulte a [pacotes de aplicações](batch-application-packages.md) orientações. Especifique um id de aplicação, como *GPUDriver*e uma versão como *411.82*.
1. Utilizar as APIs do Batch ou o portal do Azure, crie um conjunto na configuração da máquina virtual com o número pretendido de nós e dimensionamento. A tabela seguinte mostra as definições de exemplo para instalar os controladores de NVIDIA GPU silenciosamente usando uma tarefa de início:

| Definição | Value |
| ---- | ----- | 
| **Tipo de Imagem** | Marketplace (Linux/Windows) |
| **Publicador** | MicrosoftWindowsServer |
| **Oferta** | WindowsServer |
| **Sku** | 2016-Datacenter |
| **Tamanho do nó** | NC6 Standard |
| **Referências do pacote de aplicação** | GPUDriver, versão 411.82 |
| **Iniciar a tarefa ativada** | Verdadeiro<br>**Linha de comandos** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Identidade do usuário** -utilizador automático de agrupamento, o administrador<br/>**Aguardar execução com êxito** – verdadeiro

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Exemplo: Controladores de NVIDIA GPU num agrupamento de VM de NC do Linux

Para executar aplicativos de CUDA num conjunto de nós do Linux NC, terá de instalar os controladores de NVIDIA Tesla GPU necessários do CUDA Toolkit. Os seguintes passos de exemplo, criar e implementar uma imagem de Ubuntu 16.04 LTS personalizada com os controladores GPU:

1. Implemente uma VM da série NC do Azure a executar o Ubuntu 16.04 LTS. Por exemplo, crie a VM na região e.u.a. centro-Sul. 
2. Adicionar a [extensão de controladores de GPU da NVIDIA](../virtual-machines/extensions/hpccompute-gpu-linux.md
) à VM com o portal do Azure, um computador cliente que liga à subscrição do Azure ou ao Azure Cloud Shell. Em alternativa, siga os passos para ligar à VM e [instalar controladores CUDA](../virtual-machines/linux/n-series-driver-setup.md) manualmente.
3. Siga os passos para criar uma [instantâneo e de imagem de VM do Linux personalizada](batch-custom-images.md) para o Batch.
4. Crie uma conta do Batch numa região que suporta VMs do NC.
5. Usando as APIs do Batch ou o portal do Azure, criar um conjunto [com a imagem personalizada](batch-custom-images.md) e com o número pretendido de nós e dimensionamento. A tabela seguinte mostra exemplos de definições de agrupamento para a imagem:

| Definição | Value |
| ---- | ---- |
| **Tipo de Imagem** | Imagem Personalizada |
| **Imagem personalizada** | *Nome da imagem* |
| **SKU do agente de nó** | batch.node.ubuntu 16.04 |
| **Tamanho do nó** | NC6 Standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Exemplo: Microsoft MPI num pool de VMS do Windows H16r

Para executar aplicações MPI do Windows num conjunto de nós de VM do Azure H16r, terá de configurar a extensão de HpcVmDrivers e instale [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). Seguem-se passos de exemplo para implementar uma imagem personalizada do Windows Server 2016 com o software e os controladores necessários:

1. Implemente uma VM de H16r do Azure com o Windows Server 2016. Por exemplo, crie a VM na região E.u.a. oeste. 
2. Adicione a extensão de HpcVmDrivers à VM [executar um comando do PowerShell do Azure](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
) de um computador cliente que se liga a sua subscrição do Azure ou utilizar o Azure Cloud Shell. 
1. Estabeleça uma ligação de ambiente de trabalho remoto à VM.
1. Transfira o [pacote de configuração](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) para a versão mais recente do Microsoft MPI e instalação Microsoft MPI.
1. Siga os passos para criar uma [instantâneo e de imagem de VM do Windows personalizada](batch-custom-images.md) para o Batch.
1. Usando as APIs do Batch ou o portal do Azure, criar um conjunto [com a imagem personalizada](batch-custom-images.md) e com o número pretendido de nós e dimensionamento. A tabela seguinte mostra exemplos de definições de agrupamento para a imagem:

| Definição | Value |
| ---- | ---- |
| **Tipo de Imagem** | Imagem Personalizada |
| **Imagem personalizada** | *Nome da imagem* |
| **SKU do agente de nó** | batch.node.Windows amd64 |
| **Tamanho do nó** | H16r Standard |
| **Comunicação internós ativada** | Verdadeiro |
| **Máx. tarefas por nó** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Exemplo: Intel MPI num pool de VMS do Linux H16r

Para executar aplicações MPI num conjunto do Linux a série H nós, uma opção é utilizar o [baseada em CentOS 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) imagem no Azure Marketplace. Drivers de RDMA do Linux e Intel MPI pré-instalados. Esta imagem também oferece suporte a cargas de trabalho de contentor de Docker.

Usando as APIs do Batch ou o portal do Azure, criar um conjunto com esta imagem e com o número pretendido de nós e dimensionamento. A tabela seguinte mostra exemplos de definições de agrupamento:

| Definição | Value |
| ---- | ---- |
| **Tipo de Imagem** | Marketplace (Linux/Windows) |
| **Publicador** | OpenLogic |
| **Oferta** | CentOS-HPC |
| **Sku** | 7.4 |
| **Tamanho do nó** | H16r Standard |
| **Comunicação internós ativada** | Verdadeiro |
| **Máx. tarefas por nó** | 1 |

## <a name="next-steps"></a>Passos Seguintes

* Para executar trabalhos de MPI num conjunto do Batch do Azure, consulte a [Windows](batch-mpi.md) ou [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) exemplos.

* Para obter exemplos de cargas de trabalho GPU no Batch, consulte a [Batch Shipyard](https://github.com/Azure/batch-shipyard/) receitas.