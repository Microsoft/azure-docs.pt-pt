---
title: Use VMs Azure intensivos com lote
description: Como aproveitar os tamanhos das máquinas virtuais HPC e GPU nas piscinas do Lote Azure. Conheça as dependências do OS e veja vários exemplos de cenário.
ms.topic: article
ms.date: 12/17/2018
ms.author: labrenne
ms.openlocfilehash: 674ee6c5b96c7aaf2926b51824488d03fc56d0a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115963"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Utilize instâncias DE RDMA ou GPU em piscinas de lotes

Para executar certos trabalhos de Lote, você pode aproveitar os tamanhos de VM Azure projetados para uma computação em larga escala. Por exemplo:

* Para executar cargas de trabalho de MPI em [várias instâncias,](batch-mpi.md)escolha série H ou outros tamanhos que tenham uma interface de rede para acesso remoto à memória direta (RDMA). Estes tamanhos ligam-se a uma rede InfiniBand para comunicação inter-nó, que pode acelerar aplicações de MPI. 

* Para aplicações CUDA, escolha tamanhos da série N que incluam cartões da unidade de processamento de gráficos NVIDIA Tesla (GPU).

Este artigo fornece orientações e exemplos para usar alguns dos tamanhos especializados do Azure em piscinas de lotes. Para especificações e antecedentes, consulte:

* Tamanhos VM de computação de alto desempenho[(Linux,](../virtual-machines/linux/sizes-hpc.md) [Windows)](../virtual-machines/windows/sizes-hpc.md) 

* Tamanhos VM ativados por GPU[(Linux,](../virtual-machines/linux/sizes-gpu.md) [Windows)](../virtual-machines/windows/sizes-gpu.md) 

> [!NOTE]
> Certos tamanhos de VM podem não estar disponíveis nas regiões onde cria as suas contas de Lote. Para verificar se existe um tamanho disponível, consulte [os produtos disponíveis por região](https://azure.microsoft.com/regions/services/) e escolha um tamanho [VM para uma piscina de lote](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Dependências

As capacidades de RDMA ou GPU de tamanhos intensivos de computação em Lot são suportadas apenas em certos sistemas operativos. (A lista de sistemas operativos suportados é um subconjunto dos suportados para máquinas virtuais criadas nestes tamanhos.) Dependendo da forma como cria o seu Pool Batch, poderá ter de instalar ou configurar um controlador adicional ou outro software nos nós. As tabelas que se sintetizam estas dependências. Consulte artigos ligados para mais detalhes. Para opções para configurar piscinas de Lote, consulte mais tarde neste artigo.

### <a name="linux-pools---virtual-machine-configuration"></a>Piscinas Linux - Configuração de máquina virtual

| Tamanho | Capacidade | Sistemas operativos | Software necessário | Configurações da piscina |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS, ou<br/>HPC baseado em CentOS<br/>(Mercado Azure) | Intel MPI 5<br/><br/>Condutores linux RDMA | Permitir a comunicação inter-nó, desativar a execução de tarefas simultânea |
| [NC, NCv2, NCv3, série NDv2](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (varia por série) | Ubuntu 16.04 LTS, ou<br/>CentOS 7.3 ou 7.4<br/>(Mercado Azure) | Condutores nvidia CUDA ou CUDA Toolkit | N/D | 
| [Série NV, NVv2](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS, ou<br/>Centos 7.3<br/>(Mercado Azure) | Condutores da NVIDIA GRID | N/D |

<sup>*</sup>Os tamanhos da série N com capacidade rdma também incluem GPUs NVIDIA Tesla

### <a name="windows-pools---virtual-machine-configuration"></a>Piscinas windows - Configuração de máquina virtual

| Tamanho | Capacidade | Sistemas operativos | Software necessário | Configurações da piscina |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2, ou<br/>2012 (Mercado Azure) | Microsoft MPI 2012 R2 ou mais tarde, ou<br/> Intel MPI 5<br/><br/>Controladores RDMA windows | Permitir a comunicação inter-nó, desativar a execução de tarefas simultânea |
| [Série NC, NCv2, NCv3, ND, NDv2](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (varia por série) | Windows Server 2016 ou <br/>2012 R2 (Mercado Azure) | Condutores nvidia CUDA ou CUDA Toolkit| N/D | 
| [Série NV, NVv2](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 ou<br/>2012 R2 (Mercado Azure) | Condutores da NVIDIA GRID | N/D |

<sup>*</sup>Os tamanhos da série N com capacidade rdma também incluem GPUs NVIDIA Tesla

### <a name="windows-pools---cloud-services-configuration"></a>Windows pools - Configuração de serviços em nuvem

> [!NOTE]
> Os tamanhos da série N não são suportados em piscinas de Lote com a configuração do Serviço cloud.
>

| Tamanho | Capacidade | Sistemas operativos | Software necessário | Configurações da piscina |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md) | RDMA | Windows Server 2016, 2012 R2, 2012, ou<br/>2008 R2 (família Guest OS) | Microsoft MPI 2012 R2 ou mais tarde, ou<br/>Intel MPI 5<br/><br/>Controladores RDMA windows | Permitir a comunicação entre nós,<br/> desativar execução de tarefas simultâneas |

## <a name="pool-configuration-options"></a>Opções de configuração do pool

Para configurar um tamanho de VM especializado para a sua piscina de Lote, tem várias opções para instalar software ou controladores necessários:

* Para piscinas na configuração da máquina virtual, escolha uma imagem VM [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) preconfigurada que tenha controladores e software pré-instalados. Exemplos: 

  * [CentOS-based 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) - inclui condutores RDMA e Intel MPI 5.1

  * [Máquina Virtual](../machine-learning/data-science-virtual-machine/overview.md) de Ciência de Dados para Linux ou Windows - inclui controladores NVIDIA CUDA

  * Imagens linux para cargas de trabalho de contentores de lote que também incluem condutores de GPU e RDMA:

    * [CentOS (com condutores de GPU e RDMA) para piscinas de contentores azure batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (com condutores de GPU e RDMA) para piscinas de contentores do Lote Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Crie uma [imagem Personalizada do Windows ou Do Linux VM](batch-sig-images.md) na qual instalou controladores, software ou outras definições necessárias para o tamanho VM. 

* Crie um pacote de [aplicação](batch-application-packages.md) Batch a partir de um controlador ou instalador de aplicações com fecho, e configure o Batch para implantar a embalagem em nós de piscina e instalar uma vez quando cada nó for criado. Por exemplo, se o pacote de aplicação for um instalador, crie uma linha de comando de [tarefa](batch-api-basics.md#start-task) inicial para instalar silenciosamente a aplicação em todos os nós da piscina. Considere utilizar um pacote de aplicação e uma tarefa de início de piscina se a sua carga de trabalho depender de uma versão específica do condutor.

  > [!NOTE] 
  > A tarefa inicial deve ser executada com permissões elevadas (administração) e deve esperar pelo sucesso. As tarefas de longa duração aumentarão o tempo para fornecer uma piscina de lote.
  >

* [O Estaleiro de Lote](https://github.com/Azure/batch-shipyard) suprima automaticamente os condutores da GPU e rdMA para trabalharem de forma transparente com cargas de trabalho contentorizadas no Lote Azure. O Estaleiro de Lote é inteiramente conduzido com ficheiros de configuração. Existem muitas configurações de receitas de amostradisponíveis que permitem cargas de trabalho de GPU e RDMA como a [Receita GPU CNTK](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) que pré-reconfigura os condutores de GPU em VMs da série N e carrega o software Microsoft Cognitive Toolkit como uma imagem do Docker.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Exemplo: Condutores de GPU da NVIDIA na piscina VM do Windows NC

Para executar aplicações CUDA em uma piscina de nós do Windows NC, você precisa instalar controladores GPU NVDIA. As seguintes etapas de amostra utilizam um pacote de aplicação para instalar os controladores GPU da NVIDIA. Pode escolher esta opção se a sua carga de trabalho depender de uma versão específica do controlador da GPU.

1. Faça o download de um pacote de configuração para os controladores GPU no Windows Server 2016 a partir do site da [NVIDIA](https://www.nvidia.com/Download/index.aspx) - por exemplo, [versão 411.82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Guarde o ficheiro localmente utilizando um nome curto como *GPUDriverSetup.exe*.
2. Crie um ficheiro zip da embalagem.
3. Faça o upload do pacote para a sua conta Batch. Para obter passos, consulte a orientação dos pacotes de [aplicação.](batch-application-packages.md) Especifique um ID de aplicação como *GPUDriver,* e uma versão como *411.82*.
1. Utilizando o portal Batch APIs ou Azure, crie uma piscina na configuração da máquina virtual com o número desejado de nós e escala. A tabela que se segue mostra as definições da amostra para instalar os controladores GPU da NVIDIA silenciosamente utilizando uma tarefa inicial:

| Definição | Valor |
| ---- | ----- | 
| **Tipo de Imagem** | Marketplace (Linux/Windows) |
| **Editora** | MicrosoftWindowsServer |
| **Oferta** | WindowsServer |
| **Rio Sku** | Centro de Dados 2016 |
| **Tamanho do nó** | Norma NC6 |
| **Referências de pacotede aplicação** | GPUDriver, versão 411.82 |
| **Iniciar tarefa ativada** | Verdadeiro<br>**Linha de comando** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Identidade do utilizador** - Utilizador de piscina, administrador<br/>**Esperar pelo sucesso** - Verdadeiro

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Exemplo: Pilotos da NVIDIA GPU numa piscina De VM Linux NC

Para executar aplicações CUDA em uma piscina de nós Linux NC, você precisa instalar os necessários controladores GPU NVIDIA Tesla do KIT de Ferramentas CUDA. Os seguintes passos de amostra criam e implantam uma imagem Ubuntu 16.04 LTS personalizada com os condutores de GPU:

1. Implemente um VM da série Nc Azur a executar Ubuntu 16.04 LTS. Por exemplo, criar o VM na região centro-sul dos EUA. 
2. Adicione a extensão da [GpU Da NVIDIA](../virtual-machines/extensions/hpccompute-gpu-linux.md
) ao VM utilizando o portal Azure, um computador cliente que se conecta à subscrição Azure, ou Azure Cloud Shell. Em alternativa, siga os passos para ligar ao VM e [instale manualmente os controladores CUDA.](../virtual-machines/linux/n-series-driver-setup.md)
3. Siga os passos para criar uma imagem partilhada da Galeria de [Imagens](batch-sig-images.md) para O Lote.
4. Crie uma conta Batch numa região que apoie os NC VMs.
5. Utilizando o portal Batch APIs ou Azure, crie uma piscina [utilizando a imagem personalizada](batch-sig-images.md) e com o número desejado de nós e escala. A tabela seguinte mostra as definições do conjunto de amostras para a imagem:

| Definição | Valor |
| ---- | ---- |
| **Tipo de Imagem** | Imagem Personalizada |
| **Imagem personalizada** | *Nome da imagem* |
| **Agente do nó SKU** | lote.node.ubuntu 16.04 |
| **Tamanho do nó** | Norma NC6 |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Exemplo: Microsoft MPI em uma piscina VM Windows H16r

Para executar aplicações DEMPI do Windows numa piscina de nós VM Azure H16r, é necessário configurar a extensão HpcVmDrivers e instalar [o MICROSOFT MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). Aqui estão os passos da amostra para implementar uma imagem personalizada do Windows Server 2016 com os controladores e software necessários:

1. Implemente um Azure H16r VM executando o Windows Server 2016. Por exemplo, criar o VM na região oeste dos EUA. 
2. Adicione a extensão HpcVmDrivers ao VM [executando um comando Azure PowerShell](../virtual-machines/sizes-hpc.md) a partir de um computador cliente que se conecta à sua subscrição Azure, ou utilizando a Azure Cloud Shell. 
1. Faça uma ligação remota de ambiente de trabalho ao VM.
1. Descarregue o pacote de [configuração](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) para a versão mais recente do MICROSOFT MPI e instale o MPI da Microsoft.
1. Siga os passos para criar uma imagem partilhada da Galeria de [Imagens](batch-sig-images.md) para O Lote.
1. Utilizando o portal Batch APIs ou Azure, crie uma piscina [utilizando a Galeria de Imagem Partilhada](batch-sig-images.md) e com o número desejado de nós e escala. A tabela seguinte mostra as definições do conjunto de amostras para a imagem:

| Definição | Valor |
| ---- | ---- |
| **Tipo de Imagem** | Imagem Personalizada |
| **Imagem personalizada** | *Nome da imagem* |
| **Agente do nó SKU** | lote.node.janelas amd64 |
| **Tamanho do nó** | Padrão H16r |
| **Comunicação Internade ativada** | Verdadeiro |
| **Tarefas max por nó** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Exemplo: Intel MPI em uma piscina VM Linux H16r

Para executar aplicações de MPI em um conjunto de nós da série Linux H, uma opção é usar a imagem [7.4 HPC baseada no CentOS](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) do Azure Marketplace. Os controladores Linux RDMA e o INTEL MPI estão pré-instalados. Esta imagem também suporta as cargas de carga de contentores do Docker.

Utilizando o portal Batch APIs ou Azure, crie uma piscina utilizando esta imagem e com o número desejado de nós e escala. A tabela seguinte mostra as definições do conjunto de amostras:

| Definição | Valor |
| ---- | ---- |
| **Tipo de Imagem** | Marketplace (Linux/Windows) |
| **Editora** | OpenLogic |
| **Oferta** | CentOS-HPC |
| **Rio Sku** | 7.4 |
| **Tamanho do nó** | Padrão H16r |
| **Comunicação Internade ativada** | Verdadeiro |
| **Tarefas max por nó** | 1 |

## <a name="next-steps"></a>Passos seguintes

* Para executar trabalhos de MPI numa piscina de Lote Azure, consulte os exemplos [do Windows](batch-mpi.md) ou [Linux.](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/)

* Por exemplo, em cargas de trabalho da GPU no Batch, consulte as receitas do [Estaleiro de Lote.](https://github.com/Azure/batch-shipyard/)
