---
title: Clusters MATLAB em máquinas virtuais | Microsoft Docs
description: Use Microsoft Azure máquinas virtuais para criar clusters de servidor de computação distribuída MATLAB para executar suas cargas de trabalho do MATLAB paralelos de computação intensiva
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
ms.openlocfilehash: d57d9bfa964759e639c2cf40d86bd603b1900ce7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103017"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Criar clusters de servidor de computação distribuída MATLAB em VMs do Azure
Use Microsoft Azure máquinas virtuais para criar um ou mais clusters de servidor de computação distribuída MATLAB para executar suas cargas de trabalho de MATLAB paralelas de computação intensiva. Instale o software de servidor de computação distribuída MATLAB em uma VM para usar como uma imagem base e use um modelo de início rápido do Azure ou um script de Azure PowerShell (disponível no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) para implantar e gerenciar o cluster. Após a implantação, conecte-se ao cluster para executar suas cargas de trabalho.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Sobre o servidor de computação distribuída MATLAB e MATLAB
A plataforma [MATLAB](https://www.mathworks.com/products/matlab/) é otimizada para resolver problemas de engenharia e científica. Os usuários do MATLAB com simulações em larga escala e tarefas de processamento de dados podem usar produtos de computação paralela MathWorks para acelerar suas cargas de trabalho de computação intensiva aproveitando os clusters de computação e os serviços de grade. A [caixa de ferramentas de computação paralela](https://www.mathworks.com/products/parallel-computing/) permite que os usuários do MATLAB paralelizam aplicativos e tirem proveito de processadores de vários núcleos, GPUs e clusters de computadores. O [servidor de computação distribuída MATLAB](https://www.mathworks.com/products/distriben/) permite que os usuários do MATLAB utilizem muitos computadores em um cluster de computação.

Usando as máquinas virtuais do Azure, você pode criar clusters de servidor de computação distribuída MATLAB que tenham todos os mesmos mecanismos disponíveis para enviar o trabalho paralelo como clusters locais, como trabalhos interativos, trabalhos em lotes, tarefas independentes e comunicação tarefa. O uso do Azure em conjunto com a plataforma MATLAB tem muitos benefícios em comparação com o provisionamento e o uso de hardware local tradicional: uma variedade de tamanhos de máquina virtual, criação de clusters sob demanda para que você pague apenas pelos recursos de computação que usa e o capacidade de testar modelos em escala.  

## <a name="prerequisites"></a>Pré-requisitos
* **Computador cliente** -você precisará de um computador cliente baseado no Windows para se comunicar com o Azure e o cluster de servidor de computação distribuída MATLAB após a implantação.
* **Azure PowerShell** -consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para instalá-lo no computador cliente.
* **Assinatura do Azure** -se você não tiver uma assinatura, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos. Para clusters maiores, considere uma assinatura paga conforme o uso ou outras opções de compra.
* **cota de vCPUs** – talvez seja necessário aumentar a cota de vCPU para implantar um cluster grande ou mais de um cluster de servidor de computação distribuída MATLAB. Para aumentar uma cota, [abra uma solicitação](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) de atendimento ao cliente online sem encargos.
* **Licenças do MATLAB, caixa de ferramentas de computação paralela e servidor de computação distribuída MATLAB** – os scripts pressupõem que o [Gerenciador de licenças hospedados MathWorks](https://www.mathworks.com/help/install/license-management.html) é usado para todas as licenças.  
* O **software do servidor de computação distribuída MATLAB** -será instalado em uma VM que será usada como a imagem da VM base para as VMs do cluster.

## <a name="high-level-steps"></a>Etapas de alto nível
Para usar máquinas virtuais do Azure para seus clusters de servidor de computação distribuída MATLAB, as seguintes etapas de alto nível são necessárias. Instruções detalhadas estão na documentação que acompanha o modelo de início rápido e os scripts no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Criar uma imagem de VM de base**  

   * Baixe e instale o software de servidor de computação distribuída MATLAB nessa VM.

     > [!NOTE]
     > Esse processo pode levar algumas horas, mas você só precisa fazê-lo uma vez para cada versão do MATLAB que usar.   
     >
     >
2. **Criar um ou mais clusters**  

   * Use o script do PowerShell fornecido ou use o modelo de início rápido para criar um cluster a partir da imagem da VM de base.   
   * Gerencie os clusters usando o script do PowerShell fornecido que permite listar, pausar, retomar e excluir clusters.

## <a name="cluster-configurations"></a>Configurações de cluster
Atualmente, o script e o modelo de criação de cluster permitem que você crie uma única topologia de servidor de computação distribuída MATLAB. Se desejar, crie um ou mais clusters adicionais, com cada cluster com um número diferente de VMs de trabalho, usando tamanhos de VM diferentes e assim por diante.

### <a name="matlab-client-and-cluster-in-azure"></a>Cliente e cluster do MATLAB no Azure
O nó do cliente MATLAB, nó do Agendador de trabalhos do MATLAB e nós "Worker" do servidor de computação distribuída MATLAB são todos configurados como VMs do Azure em uma rede virtual, conforme mostrado na figura a seguir.


* Para usar o cluster, conecte-se pelo Área de Trabalho Remota ao nó do cliente. O nó cliente executa o cliente MATLAB.
* O nó do cliente tem um compartilhamento de arquivos que pode ser acessado por todos os trabalhos.
* O Gerenciador de licenças hospedados do MathWorks é usado para as verificações de licença para todos os softwares do MATLAB.
* Por padrão, um trabalho de servidor de computação distribuída MATLAB por vCPU é criado nas VMs de trabalho, mas você pode especificar qualquer número.

## <a name="use-an-azure-based-cluster"></a>Usar um cluster baseado no Azure
Assim como ocorre com outros tipos de clusters de servidores de computação distribuída MATLAB, você precisa usar o Gerenciador de perfis de cluster no cliente do MATLAB (na VM do cliente) para criar um perfil de cluster do Agendador de trabalhos do MATLAB.

![Gerenciador de perfis de cluster](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Passos Seguintes
* Para obter instruções detalhadas sobre como implantar e gerenciar clusters de servidor de computação distribuída MATLAB no Azure, consulte o repositório [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) que contém os modelos e scripts.
* Acesse o [site do MathWorks](https://www.mathworks.com/) para obter documentação detalhada para o servidor de computação distribuída MATLAB e Matlab.
