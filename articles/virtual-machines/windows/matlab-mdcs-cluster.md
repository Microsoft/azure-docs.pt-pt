---
title: Clusters MATLAB em máquinas virtuais
description: Utilize máquinas virtuais do Microsoft Azure para criar clusters de servidores de computação distribuídos MATLAB para executar as suas cargas de trabalho paralelas mATLAB intensivas em computação
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: gwallace
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: a2fb2479f5544b869b51e796085fcb4d0b76121a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038146"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Crie clusters de servidores de computação distribuídos MATLAB em VMs Azure
Utilize máquinas virtuais Do Microsoft Azure para criar um ou mais clusters de servidores de computação distribuídos MATLAB para executar as suas cargas de trabalho paralelas mATLAB intensivas em computação. Instale o seu software MATLAB Distributed Computing Server num VM para utilizar como imagem base e utilize um modelo de quickstart Azure ou um script Azure PowerShell (disponível no [GitHub)](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)para implementar e gerir o cluster. Após a implantação, ligue-se ao cluster para executar as suas cargas de trabalho.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Sobre o MATLAB e o MATLAB Distribuem o Servidor de Computação
A plataforma [MATLAB](https://www.mathworks.com/products/matlab/) está otimizada para resolver problemas de engenharia e científicos. Os utilizadores do MATLAB com simulações em larga escala e tarefas de processamento de dados podem utilizar produtos de computação paralela MathWorks para acelerar as suas cargas de trabalho intensivas em cálculo, tirando partido de clusters de cálculo e serviços de rede. [A Parallel Computing Toolbox](https://www.mathworks.com/products/parallel-computing/) permite que os utilizadores do MATLAB paralelamente a aplicações e tirem partido de processadores multi-core, GPUs e clusters de cálculo. [O MATLAB Distributed Computing Server](https://www.mathworks.com/products/distriben/) permite que os utilizadores do MATLAB utilizem muitos computadores num cluster de cálculo.

Ao utilizar máquinas virtuais Azure, pode criar clusters de servidores de computação distribuídos MATLAB que tenham todos os mesmos mecanismos disponíveis para submeter trabalhoparalelo como clusters no local, tais como empregos interativos, trabalhos de lote, tarefas independentes e comunicação tarefas. A utilização do Azure em conjunto com a plataforma MATLAB tem muitos benefícios em comparação com o fornecimento e utilização de hardware tradicional no local: uma gama de tamanhos de máquinas virtuais, criação de clusters a pedido para que você pague apenas pelos recursos de computação que você usa, e o capacidade de testar modelos à escala.  

## <a name="prerequisites"></a>Pré-requisitos
* **Computador** cliente - Você precisará de um computador cliente baseado no Windows para comunicar com O Azure e o cluster MATLAB Distributed Computing Server após a implementação.
* **Azure PowerShell** - Veja como instalar e configurar o [Azure PowerShell](/powershell/azure/overview) para instalá-lo no computador do seu cliente.
* **Subscrição Azure** - Se não tiver uma subscrição, pode criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos. Para clusters maiores, considere uma subscrição pay-as-you-go ou outras opções de compra.
* **quota vCPUs** - Pode ser necessário aumentar a quota vCPU para implantar um grande cluster ou mais do que um cluster MATLAB Distributed Computing Server. Para aumentar uma quota, [abra um pedido de apoio ao cliente online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.
* **MATLAB, Parallel Computing Toolbox e MATLAB Distribud Computing Server licenças** - Os scripts assumem que o Gestor de [Licenças Hospedado](https://www.mathworks.com/help/install/license-management.html) em MathWorks é usado para todas as licenças.  
* **MATLAB Software Decomputação Distribuída** - Será instalado num VM que será usado como imagem VM base para os VMs do cluster.

## <a name="high-level-steps"></a>Passos de alto nível
Para utilizar máquinas virtuais Azure para os seus clusters MATLAB Distributed Computing Server, são necessários os seguintes passos de alto nível. Instruções detalhadas estão na documentação que acompanha o modelo de arranque rápido e scripts no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Criar uma imagem VM base**  

   * Descarregue e instale o software MATLAB Distributed Computing Server neste VM.

     > [!NOTE]
     > Este processo pode demorar algumas horas, mas só tem de o fazer uma vez por cada versão do MATLAB que utiliza.   
     >
     >
2. **Criar um ou mais aglomerados**  

   * Utilize o script PowerShell fornecido ou utilize o modelo quickstart para criar um cluster a partir da imagem VM base.   
   * Gerencie os clusters utilizando o script PowerShell fornecido que lhe permite listar, fazer uma pausa, retomar e eliminar clusters.

## <a name="cluster-configurations"></a>Configurações de cluster
Atualmente, o script e modelo de criação de cluster permitem criar uma única topologia do Servidor de Computação Distribuída MATLAB. Se quiser, crie um ou mais clusters adicionais, com cada cluster com um número diferente de VMs dos trabalhadores, utilizando diferentes tamanhos de VM, e assim por diante.

### <a name="matlab-client-and-cluster-in-azure"></a>Cliente e cluster do MATLAB em Azure
O nó de cliente MATLAB, o nó do Programador de Trabalho MATLAB e os nós "trabalhadores" do Servidor de Computação Distribuído MATLAB estão todos configurados como VMs Azure numa rede virtual, como mostra a figura seguinte.


* Para utilizar o cluster, ligue-se por Remote Desktop ao nó do cliente. O nó de clientes gere o cliente MATLAB.
* O nó de clientes tem uma quota de arquivo que pode ser acedida por todos os trabalhadores.
* MathWorks Hosted License Manager é usado para as verificações de licença para todo o software MATLAB.
* Por predefinição, um trabalhador do Servidor de Computação Distribuído MATLAB por vCPU é criado nos VMs do trabalhador, mas pode especificar qualquer número.

## <a name="use-an-azure-based-cluster"></a>Use um Cluster baseado em Azure
Tal como acontece com outros tipos de clusters de servidores de computação distribuídos MATLAB, é necessário utilizar o Cluster Profile Manager no cliente MATLAB (no Cliente VM) para criar um perfil de cluster do Programador de Trabalho mATLAB.

![Gestor de Perfil de Cluster](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Passos seguintes
* Para instruções detalhadas para implementar e gerir os clusters do Servidor de Computação Distribuído mATLAB em Azure, consulte o repositório [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) contendo os modelos e scripts.
* Vá ao [site mathWorks](https://www.mathworks.com/) para obter documentação detalhada para o MATLAB e mATLAB Distributed Computing Server.
