---
title: Utilize VMs Azure intensivos computacional com Lote
description: Como tirar partido dos tamanhos de máquina virtual HPC e GPU nas piscinas Azure Batch. Conheça as dependências do OS e veja vários exemplos de cenários.
ms.topic: how-to
ms.date: 12/17/2018
ms.openlocfilehash: 016da7669c9e6a6586a53d379f9665c9ea048b64
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86147334"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Utilize casos de RDMA ou GPU em piscinas de Lote

Para executar certos trabalhos em Batch, você pode aproveitar os tamanhos Azure VM projetados para computação em larga escala. Por exemplo:

* Para executar cargas de trabalho de MPI em [várias instâncias,](batch-mpi.md)escolha série H ou outros tamanhos que tenham uma interface de rede para Acesso à Memória Direta Remota (RDMA). Estes tamanhos ligam-se a uma rede InfiniBand para comunicação inter-nó, que pode acelerar as aplicações de MPI. 

* Para aplicações CUDA, escolha tamanhos da série N que incluam cartões da unidade de processamento gráfico nvidia Tesla (GPU).

Este artigo fornece orientações e exemplos para usar alguns dos tamanhos especializados da Azure em piscinas de Lote. Para especificações e antecedentes, consulte:

* Tamanhos VM compute de alto desempenho[(Linux,](../virtual-machines/sizes-hpc.md) [Windows)](../virtual-machines/sizes-hpc.md) 

* Tamanhos VM ativados por GPU[(Linux,](../virtual-machines/sizes-gpu.md) [Windows)](../virtual-machines/sizes-gpu.md) 

> [!NOTE]
> Certos tamanhos VM podem não estar disponíveis nas regiões onde cria as suas contas Batch. Para verificar se existe um tamanho, consulte [os produtos disponíveis por região](https://azure.microsoft.com/regions/services/) e [escolha um tamanho VM para uma piscina de Lote.](batch-pool-vm-sizes.md)

## <a name="dependencies"></a>Dependências

As capacidades de RDMA ou GPU de tamanhos computacionalmente intensivos em Batch são suportadas apenas em determinados sistemas operativos. (A lista de sistemas operativos suportados é um subconjunto dos suportados para máquinas virtuais criadas nestes tamanhos.) Dependendo da forma como cria a sua piscina Batch, poderá necessitar de instalar ou configurar um controlador adicional ou outro software nos nós. As tabelas que se seguem resumem estas dependências. Consulte os artigos ligados para mais detalhes. Para opções para configurar piscinas de Lote, consulte mais tarde neste artigo.

### <a name="linux-pools---virtual-machine-configuration"></a>Piscinas Linux - Configuração de máquina virtual

| Tamanho | Funcionalidade | Sistemas operativos | Software necessário | Configurações de piscina |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS, ou<br/>HPC baseado em CentOS<br/>(Mercado Azure) | Intel MPI 5<br/><br/>Condutores linux RDMA | Ativar a comunicação inter-node, desativar a execução de tarefas simultâneas |
| [Série NC, NCv2, NCv3, NDv2](../virtual-machines/linux/n-series-driver-setup.md) | GPU NVIDIA Tesla (varia por série) | Ubuntu 16.04 LTS, ou<br/>CentOS 7.3 ou 7.4<br/>(Mercado Azure) | Condutores de kits de ferramentas NVIDIA CUDA ou CUDA | N/D | 
| [NV, série NVv2](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS, ou<br/>CentOS 7.3<br/>(Mercado Azure) | Condutores da NVIDIA GRID | N/D |

<sup>*</sup>Os tamanhos da série N com capacidade RDMA também incluem GPUs NVIDIA Tesla

### <a name="windows-pools---virtual-machine-configuration"></a>Piscinas Windows - Configuração de máquina virtual

| Tamanho | Funcionalidade | Sistemas operativos | Software necessário | Configurações de piscina |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2, ou<br/>2012 (Mercado Azure) | Microsoft MPI 2012 R2 ou mais tarde, ou<br/> Intel MPI 5<br/><br/>Controladores RDMA do Windows | Ativar a comunicação inter-node, desativar a execução de tarefas simultâneas |
| [Série NC, NCv2, NCv3, ND, NDv2](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla (varia por série) | Windows Server 2016 ou <br/>2012 R2 (Mercado Azure) | Condutores de kits de ferramentas NVIDIA CUDA ou CUDA| N/D | 
| [NV, série NVv2](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 ou<br/>2012 R2 (Mercado Azure) | Condutores da NVIDIA GRID | N/D |

<sup>*</sup>Os tamanhos da série N com capacidade RDMA também incluem GPUs NVIDIA Tesla

### <a name="windows-pools---cloud-services-configuration"></a>Piscinas Windows - Configuração de serviços em nuvem

> [!NOTE]
> Os tamanhos da série N não são suportados em piscinas de Lote com a configuração do Cloud Service.
>

| Tamanho | Funcionalidade | Sistemas operativos | Software necessário | Configurações de piscina |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md) | RDMA | Windows Server 2016, 2012 R2, 2012, ou<br/>2008 R2 (família guest OS) | Microsoft MPI 2012 R2 ou mais tarde, ou<br/>Intel MPI 5<br/><br/>Controladores RDMA do Windows | Ativar a comunicação entre nó,<br/> desativar execução de tarefas simultâneas |

## <a name="pool-configuration-options"></a>Opções de configuração da piscina

Para configurar um tamanho VM especializado para a sua piscina Batch, tem várias opções para instalar software ou controladores necessários:

* Para piscinas na configuração da máquina virtual, escolha uma imagem VM pré-configurada [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) que tenha controladores e software pré-instalados. Exemplos: 

  * [CentOS baseado em 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) - inclui motoristas RDMA e Intel MPI 5.1

  * [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) para Linux ou Windows - inclui controladores NVIDIA CUDA

  * Imagens Linux para cargas de trabalho de contentores de lote que também incluem condutores de GPU e RDMA:

    * [CentOS (com condutores de GPU e RDMA) para piscinas de contentores Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (com controladores GPU e RDMA) para piscinas de contentores Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Crie uma [imagem personalizada do Windows ou Do Linux VM](batch-sig-images.md) na qual instalou controladores, software ou outras definições necessárias para o tamanho VM. 

* Crie um pacote de [aplicação](batch-application-packages.md) Batch a partir de um controlador ou instalador de aplicações com fecho e configuure o Batch para implantar a embalagem para os nós de piscina e instalar uma vez quando cada nó for criado. Por exemplo, se o pacote de aplicações for um instalador, crie uma linha de comando [de tarefa inicial](jobs-and-tasks.md#start-task) para instalar silenciosamente a aplicação em todos os nós de piscina. Considere usar um pacote de aplicações e uma tarefa inicial de pool se a sua carga de trabalho depender de uma determinada versão do controlador.

  > [!NOTE] 
  > A tarefa inicial deve ser executada com permissões elevadas (administradores) e deve aguardar pelo sucesso. Tarefas de longa duração aumentarão o tempo para a disponibilização de um pool de Lote.
  >

* [O Batch Shipyard](https://github.com/Azure/batch-shipyard) configura automaticamente os condutores da GPU e da RDMA para trabalharem de forma transparente com cargas de trabalho contentorizadas em Azure Batch. O Estaleiro do Lote é inteiramente conduzido com ficheiros de configuração. Existem muitas configurações de receitas de amostras disponíveis que permitem cargas de trabalho gpu e RDMA, como a [Receita GPU da CNTK](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) que configura os controladores de GPU em VMs da série N e carrega o software Microsoft Cognitive Toolkit como uma imagem docker.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Exemplo: Controladores gpu da NVIDIA na piscina VM do Windows NC

Para executar aplicações CUDA num conjunto de nós Windows NC, é necessário instalar controladores NVDIA GPU. Os seguintes passos de amostra utilizam um pacote de aplicação para instalar os controladores gpu da NVIDIA. Pode escolher esta opção se a sua carga de trabalho depender de uma versão específica do controlador gpu.

1. Descarregue um pacote de configuração para os controladores da GPU no Windows Server 2016 a partir do website da [NVIDIA](https://www.nvidia.com/Download/index.aspx) - por exemplo, [versão 411.82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Guarde o ficheiro localmente utilizando um nome curto como *GPUDriverSetup.exe*.
2. Crie um ficheiro zip do pacote.
3. Faça o upload do pacote para a sua conta Batch. Para etapas, consulte a orientação dos [pacotes de aplicação.](batch-application-packages.md) Especifique um ID de aplicação como *GPUDriver,* e uma versão como *411.82*.
1. Utilizando as APIs do lote ou portal Azure, crie uma piscina na configuração da máquina virtual com o número desejado de nós e escala. A tabela a seguir mostra as definições de amostra para instalar os controladores do GPU NVIDIA em silêncio utilizando uma tarefa inicial:

| Definição | Valor |
| ---- | ----- | 
| **Tipo de imagem** | Marketplace (Linux/Windows) |
| **Publisher** | MicrosoftWindowsServer |
| **Oferta** | WindowsServer |
| **Sku** | Centro de Dados de 2016 |
| **Tamanho do nó** | Norma NC6 |
| **Referências de pacote de aplicação** | GPUDriver, versão 411.82 |
| **Iniciar tarefa ativada** | Verdadeiro<br>**Linha de comando** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Identidade do utilizador** - Pool autouser, administrador<br/>**Esperar pelo sucesso** - Verdade

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Exemplo: Condutores da NVIDIA GPU numa piscina Linux NC VM

Para executar aplicações CUDA em um conjunto de nós Linux NC, você precisa instalar os controladores GPU da NVIDIA Tesla necessários a partir do KIT de Ferramentas CUDA. Os seguintes passos de amostra criam e implementam uma imagem Ubuntu 16.04 LTS personalizada com os controladores da GPU:

1. Implemente um VM da série Azure NC que executa Ubuntu 16.04 LTS. Por exemplo, criar o VM na região centro-sul dos EUA. 
2. Adicione a extensão de [Motores GPU da NVIDIA](../virtual-machines/extensions/hpccompute-gpu-linux.md
) ao VM utilizando o portal Azure, um computador cliente que se conecta à subscrição Azure, ou Azure Cloud Shell. Em alternativa, siga os passos para ligar ao VM e [instale os controladores CUDA](../virtual-machines/linux/n-series-driver-setup.md) manualmente.
3. Siga os passos para criar uma [imagem da Galeria de Imagens Partilhada](batch-sig-images.md) para Batch.
4. Criar uma conta Batch numa região que suporte VMs NC.
5. Utilizando as APIs do lote ou portal Azure, crie uma piscina [utilizando a imagem personalizada](batch-sig-images.md) e com o número desejado de nós e escala. A tabela a seguir mostra as definições da amostra de piscina para a imagem:

| Definição | Valor |
| ---- | ---- |
| **Tipo de imagem** | Imagem Personalizada |
| **Imagem personalizada** | *Nome da imagem* |
| **Agente de nó SKU** | lote.node.ubuntu 16.04 |
| **Tamanho do nó** | Norma NC6 |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Exemplo: Microsoft MPI em uma piscina VM Do Windows H16r

Para executar as aplicações MPI do Windows num conjunto de nós VM Azure H16r, é necessário configurar a extensão HpcVmDrivers e instalar o [Microsoft MPI](/message-passing-interface/microsoft-mpi). Aqui estão os passos da amostra para implementar uma imagem personalizada do Windows Server 2016 com os controladores e software necessários:

1. Implementar um VM Azure H16r com funcionamento do Windows Server 2016. Por exemplo, criar o VM na região oeste dos EUA. 
2. Adicione a extensão HpcVmDrivers ao VM [executando um comando Azure PowerShell](../virtual-machines/sizes-hpc.md) a partir de um computador cliente que se conecta à sua subscrição Azure, ou utilizando a Azure Cloud Shell. 
1. Faça uma ligação de ambiente de trabalho remoto ao VM.
1. Descarregue o [pacote de configuração](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) para a versão mais recente do Microsoft MPI e instale o Microsoft MPI.
1. Siga os passos para criar uma [imagem da Galeria de Imagens Partilhada](batch-sig-images.md) para Batch.
1. Utilizando o portal APIs ou Azure, crie uma piscina [utilizando a Galeria de Imagens Partilhadas](batch-sig-images.md) e com o número desejado de nós e escala. A tabela a seguir mostra as definições da amostra de piscina para a imagem:

| Definição | Valor |
| ---- | ---- |
| **Tipo de imagem** | Imagem Personalizada |
| **Imagem personalizada** | *Nome da imagem* |
| **Agente de nó SKU** | lote.node.windows amd64 |
| **Tamanho do nó** | Norma H16r |
| **Comunicação internade ativada** | Verdadeiro |
| **Tarefas máximas por nó** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Exemplo: Intel MPI em uma piscina Linux H16r VM

Para executar aplicações MPI em um conjunto de nós da série H Linux, uma opção é usar a imagem [7.4 HPC baseada no CentOS](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) do Azure Marketplace. Os controladores Linux RDMA e o MPI da Intel estão pré-instalados. Esta imagem também suporta cargas de trabalho de contentores Docker.

Utilizando as APIs do lote ou portal Azure, crie uma piscina utilizando esta imagem e com o número desejado de nós e escala. A tabela a seguir mostra as definições do pool de amostras:

| Definição | Valor |
| ---- | ---- |
| **Tipo de imagem** | Marketplace (Linux/Windows) |
| **Publisher** | OpenLogic |
| **Oferta** | CentOS-HPC |
| **Sku** | 7.4 |
| **Tamanho do nó** | Norma H16r |
| **Comunicação internade ativada** | Verdadeiro |
| **Tarefas máximas por nó** | 1 |

## <a name="next-steps"></a>Passos seguintes

* Para executar trabalhos de MPI numa piscina do Azure Batch, consulte os exemplos [windows](batch-mpi.md) ou [Linux.](/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch)

* Por exemplo, as cargas de trabalho da GPU em Batch, consulte as receitas do [Estaleiro batch.](https://github.com/Azure/batch-shipyard/)
