---
title: Usar VMs do Azure com uso intensivo de computação com o lote
description: Como aproveitar os tamanhos de máquina virtual do HPC e da GPU nos pools do lote do Azure. Saiba mais sobre as dependências do sistema operacional e veja vários exemplos de cenário.
documentationcenter: ''
author: ju-shim
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/17/2018
ms.author: jushiman
ms.openlocfilehash: 1fb490c6db5115edf32ff2562e43cf1084e0a6c8
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026790"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Usar instâncias RDMA ou GPU em pools do lote

Para executar determinados trabalhos do lote, você pode aproveitar os tamanhos de VM do Azure projetados para computação em larga escala. Por exemplo:

* Para executar [cargas de trabalho MPI](batch-mpi.md)de várias instâncias, escolha série H ou outros tamanhos que tenham uma interface de rede para acesso remoto direto à memória (RDMA). Esses tamanhos se conectam a uma rede InfiniBand para comunicação entre nós, que pode acelerar os aplicativos MPI. 

* Para aplicativos CUDA, escolha tamanhos da série N que incluem placas de GPU (unidade de processamento gráfico) do NVIDIA Tesla.

Este artigo fornece orientações e exemplos para usar alguns dos tamanhos especializados do Azure em pools do lote. Para obter especificações e informações básicas, consulte:

* Tamanhos de VM de computação de alto desempenho ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Tamanhos de VM habilitados para GPU ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Certos tamanhos de VM podem não estar disponíveis nas regiões em que você cria suas contas do lote. Para verificar se há um tamanho disponível, consulte [produtos disponíveis por região](https://azure.microsoft.com/regions/services/) e [escolha um tamanho de VM para um pool do lote](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Dependências

Os recursos de RDMA ou GPU de tamanhos de computação intensiva no lote têm suporte apenas em determinados sistemas operacionais. (A lista de sistemas operacionais com suporte é um subconjunto daqueles com suporte para máquinas virtuais criadas nesses tamanhos.) Dependendo de como você cria o pool do lote, talvez seja necessário instalar ou configurar um driver adicional ou outro software nos nós. As tabelas a seguir resumem essas dependências. Consulte os artigos vinculados para obter detalhes. Para obter opções para configurar pools do lote, consulte mais adiante neste artigo.

### <a name="linux-pools---virtual-machine-configuration"></a>Pools do Linux – configuração de máquina virtual

| Tamanho | Capacidade | Sistemas operativos | Software necessário | Definições do conjunto |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16, 4 LTS ou<br/>HPC baseado em CentOS<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Drivers RDMA do Linux | Habilitar a comunicação entre nós, desabilitar a execução de tarefas simultâneas |
| [NC, NCv2, NCv3, NDv2 Series](../virtual-machines/linux/n-series-driver-setup.md) | GPU NVIDIA Tesla (varia por série) | Ubuntu 16, 4 LTS ou<br/>CentOS 7,3 ou 7,4<br/>(Azure Marketplace) | Drivers NVIDIA CUDA ou CUDA Toolkit | N/A | 
| [NV, série NVv2](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16, 4 LTS ou<br/>CentOS 7,3<br/>(Azure Marketplace) | Drivers NVIDIA GRID | N/A |

<sup>*</sup> Os tamanhos da série N compatíveis com RDMA também incluem GPUs NVIDIA Tesla

### <a name="windows-pools---virtual-machine-configuration"></a>Pools do Windows – configuração de máquina virtual

| Tamanho | Capacidade | Sistemas operativos | Software necessário | Definições do conjunto |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 ou<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 ou posterior, ou<br/> Intel MPI 5<br/><br/>Drivers RDMA do Windows | Habilitar a comunicação entre nós, desabilitar a execução de tarefas simultâneas |
| [NC, NCv2, NCv3, ND, NDv2 Series](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla (varia por série) | Windows Server 2016 ou <br/>2012 R2 (Azure Marketplace) | Drivers NVIDIA CUDA ou CUDA Toolkit| N/A | 
| [NV, série NVv2](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 ou<br/>2012 R2 (Azure Marketplace) | Drivers NVIDIA GRID | N/A |

<sup>*</sup> Os tamanhos da série N compatíveis com RDMA também incluem GPUs NVIDIA Tesla

### <a name="windows-pools---cloud-services-configuration"></a>Pools do Windows-configuração de serviços de nuvem

> [!NOTE]
> Não há suporte para os tamanhos da série N em pools do lote com a configuração do serviço de nuvem.
>

| Tamanho | Capacidade | Sistemas operativos | Software necessário | Definições do conjunto |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012 ou<br/>2008 R2 (família do SO convidado) | Microsoft MPI 2012 R2 ou posterior, ou<br/>Intel MPI 5<br/><br/>Drivers RDMA do Windows | Habilitar a comunicação entre nós,<br/> desabilitar a execução de tarefas simultâneas |

## <a name="pool-configuration-options"></a>Opções de configuração de pool

Para configurar um tamanho de VM especializado para o pool do lote, você tem várias opções para instalar software ou drivers necessários:

* Para pools na configuração de máquina virtual, escolha uma imagem de VM [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) pré-configurada que tenha drivers e software pré-instalado. Exemplos: 

  * [HPC-based 7,4 com base em CentOS](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) – inclui drivers RDMA e Intel MPI 5,1

  * [Máquina virtual de ciência de dados](../machine-learning/data-science-virtual-machine/overview.md) para Linux ou Windows-inclui drivers NVIDIA CUDA

  * Imagens do Linux para cargas de trabalho de contêiner do lote que também incluem drivers de GPU e RDMA:

    * [CentOS (com drivers de GPU e RDMA) para pools de contêineres do lote do Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Servidor Ubuntu (com drivers de GPU e RDMA) para pools de contêineres do lote do Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Crie uma [imagem de VM personalizada do Windows ou do Linux](batch-sig-images.md) na qual você instalou drivers, software ou outras configurações necessárias para o tamanho da VM. 

* Crie um [pacote de aplicativo](batch-application-packages.md) do lote por meio de um instalador de aplicativo ou driver compactado e configure o lote para implantar o pacote em nós de pool e instale uma vez quando cada nó for criado. Por exemplo, se o pacote de aplicativos for um instalador, crie uma linha de comando de [tarefa inicial](batch-api-basics.md#start-task) para instalar silenciosamente o aplicativo em todos os nós do pool. Considere usar um pacote de aplicativos e uma tarefa de início de pool se sua carga de trabalho depender de uma versão de driver específica.

  > [!NOTE] 
  > A tarefa inicial deve ser executada com permissões elevadas (administrador) e deve aguardar o sucesso. Tarefas de execução longa aumentarão o tempo para provisionar um pool do lote.
  >

* O [Shipyard do lote](https://github.com/Azure/batch-shipyard) configura automaticamente os drivers de GPU e RDMA para trabalhar de forma transparente com cargas de trabalho em contêineres no lote do Azure. O Shipyard do lote é totalmente controlado com arquivos de configuração. Há muitas configurações de receita de exemplo disponíveis que habilitam cargas de trabalho de GPU e RDMA, como a [receita de GPU CNTK](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) , que pré-configuradas os drivers de GPU em VMs da série N e carrega Microsoft cognitive Toolkit software como uma imagem do Docker.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Exemplo: Drivers NVIDIA GPU no pool de VMs do Windows NC

Para executar aplicativos CUDA em um pool de nós do Windows NC, você precisa instalar os drivers de GPU do NVDIA. As etapas de exemplo a seguir usam um pacote de aplicativos para instalar os drivers NVIDIA GPU. Você pode escolher essa opção se sua carga de trabalho depender de uma versão específica do driver de GPU.

1. Baixe um pacote de instalação para os drivers de GPU no Windows Server 2016 do [site Nvidia](https://www.nvidia.com/Download/index.aspx) , por exemplo, [versão 411,82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Salve o arquivo localmente usando um nome curto como *GPUDriverSetup. exe*.
2. Crie um arquivo zip do pacote.
3. Carregue o pacote em sua conta do lote. Para obter as etapas, consulte as diretrizes de [pacotes de aplicativos](batch-application-packages.md) . Especifique uma ID de aplicativo, como *GPUDriver*, e uma versão como *411,82*.
1. Usando as APIs do lote ou portal do Azure, crie um pool na configuração da máquina virtual com o número de nós e escala desejados. A tabela a seguir mostra as configurações de exemplo para instalar os drivers NVIDIA GPU silenciosamente usando uma tarefa de início:

| Definição | Valor |
| ---- | ----- | 
| **Tipo de Imagem** | Marketplace (Linux/Windows) |
| **Publicador** | MicrosoftWindowsServer |
| **Oferta** | WindowsServer |
| **Sku** | 2016-datacenter |
| **Tamanho do nó** | NC6 padrão |
| **Referências do pacote de aplicativos** | GPUDriver, versão 411,82 |
| **Iniciar tarefa habilitada** | Verdadeiro<br> - de **linha de comando** `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Identidade do usuário** -autousuário do pool, administrador<br/>**Aguardar sucesso** -verdadeiro

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Exemplo: Drivers NVIDIA GPU em um pool de VMs do Linux NC

Para executar aplicativos CUDA em um pool de nós do Linux NC, você precisa instalar os drivers de GPU NVIDIA Tesla necessários do kit de ferramentas do CUDA. As etapas de exemplo a seguir criam e implantam uma imagem personalizada do Ubuntu 16, 4 LTS com os drivers de GPU:

1. Implante uma VM da série NC do Azure executando o Ubuntu 16, 4 LTS. Por exemplo, crie a VM na região centro-sul dos EUA. 
2. Adicione a [extensão de drivers de GPU NVIDIA](../virtual-machines/extensions/hpccompute-gpu-linux.md
) à VM usando o portal do Azure, um computador cliente que se conecta à assinatura do Azure ou Azure cloud Shell. Como alternativa, siga as etapas para se conectar à VM e [instalar os drivers do CUDA](../virtual-machines/linux/n-series-driver-setup.md) manualmente.
3. Siga as etapas para criar uma [imagem da Galeria de imagens compartilhadas](batch-sig-images.md) para o lote.
4. Crie uma conta do lote em uma região que dê suporte a VMs NC.
5. Usando as APIs do lote ou portal do Azure, crie um pool [usando a imagem personalizada](batch-sig-images.md) e com o número desejado de nós e escala. A tabela a seguir mostra as configurações de pool de exemplo para a imagem:

| Definição | Valor |
| ---- | ---- |
| **Tipo de Imagem** | Imagem Personalizada |
| **Imagem personalizada** | *Nome da imagem* |
| **SKU do agente do nó** | batch.node.ubuntu 16.04 |
| **Tamanho do nó** | NC6 padrão |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Exemplo: Microsoft MPI em um pool de VMs H16r do Windows

Para executar aplicativos MPI do Windows em um pool de nós de VM H16r do Azure, você precisa configurar a extensão HpcVmDrivers e instalar o [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). Aqui estão as etapas de exemplo para implantar uma imagem personalizada do Windows Server 2016 com os drivers e software necessários:

1. Implante uma VM H16r do Azure que executa o Windows Server 2016. Por exemplo, crie a VM na região oeste dos EUA. 
2. Adicione a extensão HpcVmDrivers à VM [executando um comando Azure PowerShell](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
) de um computador cliente que se conecta à sua assinatura do Azure ou usando Azure cloud Shell. 
1. Faça uma conexão Área de Trabalho Remota com a VM.
1. Baixe o [pacote de instalação](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup. exe) para obter a versão mais recente do Microsoft MPI e instale o Microsoft MPI.
1. Siga as etapas para criar uma [imagem da Galeria de imagens compartilhadas](batch-sig-images.md) para o lote.
1. Usando as APIs ou portal do Azure do lote, crie um pool [usando a Galeria de imagens compartilhadas](batch-sig-images.md) e com o número desejado de nós e escala. A tabela a seguir mostra as configurações de pool de exemplo para a imagem:

| Definição | Valor |
| ---- | ---- |
| **Tipo de Imagem** | Imagem Personalizada |
| **Imagem personalizada** | *Nome da imagem* |
| **SKU do agente do nó** | lote. Node. Windows AMD64 |
| **Tamanho do nó** | H16r padrão |
| **Comunicação entre nós habilitada** | Verdadeiro |
| **Máximo de tarefas por nó** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Exemplo: Intel MPI em um pool de VMs H16r do Linux

Para executar aplicativos MPI em um pool de nós da série H do Linux, uma opção é usar a imagem do [HPC 7,4 baseada em CentOS](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) do Azure Marketplace. Os drivers RDMA do Linux e o Intel MPI são pré-instalados. Essa imagem também dá suporte a cargas de trabalho de contêiner do Docker.

Usando as APIs ou portal do Azure do lote, crie um pool usando essa imagem e com o número desejado de nós e escala. A tabela a seguir mostra as configurações de pool de exemplo:

| Definição | Valor |
| ---- | ---- |
| **Tipo de Imagem** | Marketplace (Linux/Windows) |
| **Publicador** | OpenLogic |
| **Oferta** | CentOS-HPC |
| **Sku** | 7.4 |
| **Tamanho do nó** | H16r padrão |
| **Comunicação entre nós habilitada** | Verdadeiro |
| **Máximo de tarefas por nó** | 1 |

## <a name="next-steps"></a>Passos seguintes

* Para executar trabalhos MPI em um pool do lote do Azure, consulte os exemplos do [Windows](batch-mpi.md) ou [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) .

* Para obter exemplos de cargas de trabalho de GPU no lote, consulte o [lote Shipyard](https://github.com/Azure/batch-shipyard/) receitas.