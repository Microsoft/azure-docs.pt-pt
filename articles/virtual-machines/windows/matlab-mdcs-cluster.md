---
title: MATLAB clusters em máquinas virtuais | Documentos da Microsoft
description: Utilizar máquinas virtuais do Microsoft Azure para criar clusters de servidor de computação de Distributed MATLAB para executar a computação intensivas e paralelas MATLAB cargas de trabalho
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: jeconnoc
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: fd5ae375dff80c8b1179d2fd73566d07c5861e4a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000348"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Criar clusters de servidor de computação distribuída da MATLAB em VMs do Azure
Utilize as máquinas virtuais do Microsoft Azure para criar um ou mais clusters de servidor de computação de Distributed MATLAB para executar intensivas de computação paralelas MATLAB cargas de trabalho. Instalar o software de servidor de computação distribuída da MATLAB numa VM para utilizar como uma imagem base e utilizar um modelo de início rápido do Azure ou o script do Azure PowerShell (disponível no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) para implementar e gerir o cluster. Após a implementação, ligar ao cluster para executar cargas de trabalho.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Sobre MATLAB e o servidor de computação MATLAB distribuído
O [MATLAB](https://www.mathworks.com/products/matlab/) plataforma está otimizada para resolver problemas de engenharia e científicos. Utilizadores MATLAB com simulações em grande escala e as tarefas de processamento de dados podem utilizar os produtos de computação paralela de MathWorks para acelerar as cargas de trabalho de computação intensiva, tirando partido dos clusters de computadores e serviços de grade. [Caixa de ferramentas de computação em paralelo](https://www.mathworks.com/products/parallel-computing/) permite que os utilizadores MATLAB paralelizar aplicativos e tirar partido de processadores de vários núcleos, GPUs e clusters de computadores. [Servidor de computação distribuída da MATLAB](https://www.mathworks.com/products/distriben/) permite que os usuários MATLAB utilizar muitos computadores num cluster de computação.

Ao utilizar máquinas virtuais do Azure, pode criar clusters de servidor de computação distribuída da MATLAB com todos os mesmos mecanismos disponíveis para submeter o trabalho de paralelo como clusters no local, como tarefas interativas, tarefas de lote, tarefas independentes e comunicação tarefas. Utilizar o Azure em conjunto com a plataforma MATLAB tem muitos benefícios em comparação com o aprovisionamento e usando tradicional hardware no local: tamanhos de um intervalo de máquina virtual, criação de clusters a pedido, pelo que paga apenas os recursos de computação utilizar, e o capacidade de testar modelos à escala.  

## <a name="prerequisites"></a>Pré-requisitos
* **Computador cliente** – precisará de um computador cliente baseado no Windows, para comunicar com o Azure e o cluster de servidor de computação distribuída da MATLAB após a implementação.
* **O Azure PowerShell** -veja [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para instalá-lo no seu computador cliente.
* **Subscrição do Azure** -se não tiver uma subscrição, pode criar um [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos. Para clusters maiores, considere uma subscrição pay as you go ou outras opções de compra.
* **quota de vCPUs** -poderá ter de aumentar a quota de vCPU para implementar um cluster de grandes dimensões ou de mais de um cluster de servidor de computação distribuída da MATLAB. Para aumentar uma quota [abra um pedido de suporte do cliente online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sem encargos.
* **Licenças MATLAB, a caixa de ferramentas de computação paralela e o servidor de computação distribuída da MATLAB** -os scripts partem do princípio de que o [Gestor de licenças alojado MathWorks](https://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) é utilizado para todas as licenças.  
* **Software de servidor de computação distribuída da MATLAB** -serão instalados numa VM que será utilizada como a imagem VM base para as VMs do cluster.

## <a name="high-level-steps"></a>Passos de nível altos
Para utilizar máquinas virtuais do Azure para os seus clusters de servidor de computação distribuída da MATLAB, os seguintes passos de alto nível são necessários. Instruções detalhadas são na documentação que acompanha o modelo de início rápido e os scripts no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Criar uma imagem de VM base**  

   * Baixe e instale o software de servidor de computação distribuída da MATLAB para esta VM.

     > [!NOTE]
     > Este processo pode demorar algumas horas, mas apenas terá de fazê-lo uma vez para cada versão do MATLAB que utilizar.   
     >
     >
2. **Criar um ou mais clusters**  

   * Utilize o script do PowerShell fornecido ou utilize o modelo de início rápido para criar um cluster a partir da imagem VM base.   
   * Gerir clusters com o script do PowerShell fornecido, que permite que listar, colocar em pausa, retomar e eliminar os clusters.

## <a name="cluster-configurations"></a>Configurações de cluster
Atualmente, o script de criação do cluster e o modelo permitem-lhe criar uma única topologia de servidor de computação distribuída da MATLAB. Se desejar, criar um ou mais clusters adicionais, com cada cluster ter um número diferente de função de trabalho de VMs, com diferentes tamanhos de VM e assim por diante.

### <a name="matlab-client-and-cluster-in-azure"></a>Cliente MATLAB e cluster no Azure
O nó de cliente MATLAB, Programador de tarefas MATLAB nó e nós de servidor de computação distribuída da MATLAB "trabalho" estão todos configurados como VMs do Azure numa rede virtual, conforme mostrado na figura a seguir.


* Para utilizar o cluster, ligar-se ao ambiente de trabalho remoto para o nó de cliente. O nó de cliente executa o cliente MATLAB.
* O nó de cliente tem uma partilha de ficheiros que pode ser acessada por todos os trabalhadores.
* Gestor de licenças alojado MathWorks é utilizado para as verificações de licença para todo o software MATLAB.
* Por predefinição, uma função de trabalho do servidor de computação distribuída da MATLAB por vCPU é criada na função de trabalho VMs, mas pode especificar qualquer número.

## <a name="use-an-azure-based-cluster"></a>Utilizar um Cluster baseado no Azure
Como com outros tipos de clusters de servidor de computação distribuída da MATLAB, precisa usar o Gerenciador de perfis de Cluster no cliente MATLAB (na VM cliente) para criar um perfil de cluster MATLAB Programador de tarefas.

![Gestor de clusters de perfil](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Passos Seguintes
* Para obter instruções detalhadas implementar e gerir clusters de servidor de computação distribuída da MATLAB no Azure, consulte a [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) repositório que contém os modelos e scripts.
* Vá para o [MathWorks site](https://www.mathworks.com/) para obter documentação detalhada para MATLAB e o servidor de computação distribuída da MATLAB.
