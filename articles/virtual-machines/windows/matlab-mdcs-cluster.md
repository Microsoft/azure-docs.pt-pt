---
title: Clusters MATLAB em máquinas virtuais
description: Utilize máquinas virtuais do Microsoft Azure para criar clusters de servidor de computação distribuído MATLAB para executar as suas cargas de trabalho matlab paralelas intensivas em computação
author: mscurrell
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: c2561a6dc3ad8c0af1c266b3822a80c76f45c174
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88639686"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Criar clusters de servidor de computação distribuído MATLAB em VMs Azure
Utilize máquinas virtuais do Microsoft Azure para criar um ou mais clusters de Servidor de Computação Distribuído MATLAB para executar as suas cargas de trabalho matlab paralelas intensivas em computação. Instale o software DO SERVIDOR de Computação Distribuído MATLAB num VM para utilizar como imagem base e utilize um modelo de arranque rápido Azure ou script Azure PowerShell (disponível no [GitHub)](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)para implementar e gerir o cluster. Após a colocação, ligue-se ao cluster para executar as suas cargas de trabalho.

> [!IMPORTANT]
> Uma vez que este artigo foi escrito, existe agora um apoio formal para a utilização de aplicações MATLAB em Azure. Recomenda-se que estas capacidades mais recentes sejam usadas em vez do modelo e scripts referenciados neste artigo. Procure no [Mercado Azure](https://azuremarketplace.microsoft.com/) "matlab"; mais informações sobre a execução de aplicações MATLAB no Azure estão disponíveis na [MathWorks](https://www.mathworks.com/solutions/cloud.html#public-cloud).

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Sobre o MATLAB e o MATLAB Servidor de Computação Distribuído
A plataforma [MATLAB](https://www.mathworks.com/products/matlab/) está otimizada para resolver problemas de engenharia e ciência. Os utilizadores do MATLAB com simulações em larga escala e tarefas de processamento de dados podem utilizar produtos de computação paralelos MathWorks para acelerar as suas cargas de trabalho intensivas em computação, tirando partido de clusters de computação e serviços de rede. [A Parallel Computing Toolbox](https://www.mathworks.com/products/parallel-computing/) permite que os utilizadores do MATLAB paralizem as aplicações e tirem partido de processadores multi-core, GPUs e clusters de computação. [O MATLAB Distributed Computing Server](https://www.mathworks.com/products/distriben/) permite que os utilizadores do MATLAB utilizem muitos computadores num cluster de computação.

Ao utilizar máquinas virtuais Azure, pode criar clusters MATLAB Distributed Computing Server que têm todos os mesmos mecanismos disponíveis para submeter trabalhos paralelos como clusters no local, tais como trabalhos interativos, trabalhos em lote, tarefas independentes e tarefas de comunicação. A utilização do Azure em conjunto com a plataforma MATLAB tem muitos benefícios em comparação com o fornecimento e utilização de hardware tradicional no local: uma gama de tamanhos de máquinas virtuais, criação de clusters a pedido para que pague apenas pelos recursos de computação que utiliza, e a capacidade de testar modelos em escala.  

## <a name="prerequisites"></a>Pré-requisitos
* **Computador cliente** - Você precisará de um computador cliente baseado no Windows para comunicar com o Azure e o cluster do Servidor de Computação Distribuído MATLAB após a implementação.
* **Azure PowerShell** - Ver [como instalar e configurar a Azure PowerShell](/powershell/azure/) para a instalar no computador cliente.
* **Subscrição do Azure** - Se não tiver uma subscrição, pode criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos. Para agrupamentos maiores, considere uma subscrição pay-as-you-go ou outras opções de compra.
* **vCPUs quota** - Você pode precisar aumentar a quota vCPU para implantar um grande cluster ou mais de um cluster MATLAB Distributed Computing Server. Para aumentar uma quota, [abra gratuitamente um pedido de apoio](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) ao cliente online.
* **MATLAB, Parallel Computing Toolbox e MATLAB Distributed Computing Server** - Os scripts assumem que o [Gestor de Licenças Hospedados em MathWorks](https://www.mathworks.com/help/install/license-management.html) é utilizado para todas as licenças.  
* **Software DO Servidor de Computação Distribuído MATLAB** - Será instalado num VM que será usado como a imagem VM base para os VMs do cluster.

## <a name="high-level-steps"></a>Passos de alto nível
Para utilizar máquinas virtuais Azure para os seus clusters DE Servidor de Computação Distribuído MATLAB, são necessários os seguintes passos de alto nível. Instruções detalhadas estão na documentação que acompanha o modelo de arranque rápido e scripts no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Criar uma imagem VM base**  

   * Descarregue e instale o software DO Servidor de Computação Distribuído MATLAB neste VM.

     > [!NOTE]
     > Este processo pode demorar algumas horas, mas só tem de o fazer uma vez para cada versão do MATLAB que utilizar.   
     >
     >
2. **Criar um ou mais aglomerados**  

   * Utilize o script PowerShell fornecido ou use o modelo de arranque rápido para criar um cluster a partir da imagem VM base.   
   * Gerencie os clusters utilizando o script PowerShell fornecido que lhe permite listar, fazer uma pausa, retomar e eliminar clusters.

## <a name="cluster-configurations"></a>Configurações de clusters
Atualmente, o script e o modelo de criação de cluster permitem-lhe criar uma única topologia do Servidor de Computação Distribuída MATLAB. Se quiser, crie um ou mais aglomerados adicionais, com cada cluster a ter um número diferente de VMs operários, utilizando diferentes tamanhos VM, e assim por diante.

### <a name="matlab-client-and-cluster-in-azure"></a>Cliente e cluster MATLAB em Azure
O nó do cliente MATLAB, o nó do Programador de Trabalho MATLAB e os nós "trabalhadores" do Servidor de Computação Distribuído MATLAB estão configurados como VMs Azure numa rede virtual, como mostra a seguinte figura.


* Para utilizar o cluster, ligue-se por Remote Desktop ao nó do cliente. O nó do cliente dirige o cliente MATLAB.
* O nó do cliente tem uma partilha de ficheiros que pode ser acedida por todos os trabalhadores.
* MathWorks Hosted License Manager é usado para as verificações de licença para todo o software MATLAB.
* Por predefinição, um trabalhador do Servidor de Computação Distribuído MATLAB por vCPU é criado nos VMs do trabalhador, mas pode especificar qualquer número.

## <a name="use-an-azure-based-cluster"></a>Use um cluster baseado em Azure
Tal como acontece com outros tipos de clusters de Servidor de Computação Distribuído MATLAB, é necessário utilizar o Cluster Profile Manager no cliente MATLAB (no VM cliente) para criar um perfil de cluster de programadores de trabalho MATLAB.

![Gestor de Perfil de Cluster](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Passos seguintes
* Para obter instruções detalhadas para implementar e gerir os clusters do Servidor de Computação Distribuído MATLAB em Azure, consulte o repositório [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) contendo os modelos e scripts.
* Vá ao [site MathWorks](https://www.mathworks.com/) para obter documentação detalhada para o MATLAB e o MATLAB Distributed Computing Server.
