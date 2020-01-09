---
title: O que é o Máquina Virtual de Ciência de Dados do Azure
description: Principais cenários e componentes de análise para Máquinas Virtuais de Ciência de Dados do Windows e Linux.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 12/31/2019
ms.openlocfilehash: a63087620d50336c67472348da3b7f37fb380635
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611973"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>O que é o Máquina Virtual de Ciência de Dados do Azure para Linux e Windows?

O Máquina Virtual de Ciência de Dados (DSVM) é uma imagem de VM personalizada na plataforma de nuvem do Azure criada especificamente para a ciência de dados. Ele tem muitas ferramentas de ciência de dados populares instaladas e pré-configuradas para iniciar a criação de aplicativos inteligentes para análise avançada. 

O DSVM está disponível em:
+ **Windows Server 2019 (versão prévia)**
+ **Ubuntu 18, 4 LTS (versão prévia)**
+ Windows Server 2016
+ Ubuntu 16, 4 LTS e CentOS 7,4


> [!NOTE]
> Todas as ferramentas de VM para aprendizado profundo foram dobradas na Máquina Virtual de Ciência de Dados. 


## <a name="why-choose-the-dsvm"></a>Por que escolher o DSVM?
O objetivo do Máquina Virtual de Ciência de Dados é fornecer aos profissionais de dados de todos os níveis de habilidade e entre os setores um ambiente de ciência de dados pré-configurado e sem conflitos. Em vez de distribuir um espaço de trabalho comparável por conta própria, você pode provisionar um DSVM. Essa escolha pode economizar dias ou até mesmo _semanas_ nos processos de instalação, configuração e gerenciamento de pacotes. Quando a DSVM estiver alocada, pode começar a trabalhar imediatamente no seu projeto de ciência de dados.

## <a name="sample-use-cases"></a>Casos de uso de exemplo

Abaixo, ilustramos alguns casos de uso comuns para clientes do DSVM.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Movendo cargas de trabalho de ciência de dados para a nuvem

O DSVM fornece uma configuração de linha de base para as equipes de ciência de dados que desejam substituir suas áreas de trabalho locais por uma área de trabalho de nuvem gerenciada, garantindo que todos os cientistas de dados de uma equipe tenham uma configuração consistente para verificar as experiências e promover a colaboração. Ele também reduz os custos reduzindo a carga sysadmin. Essa redução de carga economiza o tempo necessário para avaliar, instalar e manter pacotes de software para análise avançada.

### <a name="data-science-training-and-education"></a>Formação e educação em ciência de dados
Os treinadores e educadores empresariais que ensinam classes de ciência de dados geralmente fornecem uma imagem de máquina virtual. A imagem garante que os alunos tenham uma configuração consistente e que os exemplos funcionem de modo previsível. 

O DSVM cria um ambiente sob demanda com uma configuração consistente que facilita os desafios de suporte e incompatibilidade. Os casos em que estes ambientes têm de ser criados constantemente, especialmente para aulas de formação mais pequenas, retiram benefícios significativos destas características.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidade elástica a pedido para projetos de grande escala
A ciência de dados hackathons/competições ou a modelagem e a exploração de dados em larga escala exigem capacidade de hardware expandida, normalmente por duração curta. O DSVM pode ajudar a replicar o ambiente de ciência de dados rapidamente sob demanda, em servidores expandidos que permitem testes que os recursos de computação de alta potência podem executar.

### <a name="custom-compute-power-for-azure-notebooks"></a>Potência de computação personalizada para Azure Notebooks
[Azure notebooks](../../notebooks/azure-notebooks-overview.md) é um serviço hospedado gratuito para desenvolver, executar e compartilhar blocos de anotações do Jupyter na nuvem sem instalação. A camada de serviço gratuita é limitada a 4 GB de memória e 1 GB de dados. 

Para liberar todos os limites, você pode anexar um projeto de blocos de anotações a um DSVM ou a qualquer outra VM em execução em um servidor Jupyter. Se você entrar no Azure Notebooks com uma conta usando Azure Active Directory (como uma conta corporativa), os blocos de anotações mostrarão automaticamente DSVMs em todas as assinaturas associadas a essa conta. Você pode [anexar um DSVM ao Azure notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) para expandir a capacidade de computação disponível.

### <a name="short-term-experimentation-and-evaluation"></a>Experimentação e avaliação a curto prazo
Você pode usar o DSVM para avaliar ou aprender novas [ferramentas](./tools-included.md)de ciência de dados, especialmente passando por alguns dos nossos [exemplos publicados e orientações](./dsvm-samples-and-walkthroughs.md).


### <a name="deep-learning-with-gpus"></a>Aprendizado profundo com GPUs
No DSVM, seus modelos de treinamento podem usar algoritmos de aprendizado profundo em hardware baseado em unidades de processamento gráfico (GPUs). Aproveitando os recursos de dimensionamento de VM da plataforma Azure, o DSVM ajuda a usar o hardware baseado em GPU na nuvem de acordo com suas necessidades. Você pode alternar para uma VM baseada em GPU quando estiver treinando modelos grandes ou quando precisar de cálculos de alta velocidade mantendo o mesmo disco do sistema operacional. Você pode escolher qualquer uma das SKUs da máquina virtual da GPU da série N habilitada com DSVM. Observe que as contas gratuitas do Azure não dão suporte a SKUs de máquina virtual habilitadas para GPU.

A edição 2016 do Windows Server do DSVM vem pré-instalada com drivers de GPU, estruturas e versões de GPU de estruturas de aprendizado profundo. Na edição do Linux, o aprendizado profundo sobre GPUs está habilitado no CentOS e no Ubuntu DSVMs. 

Você também pode implantar a edição Ubuntu, CentOS ou Windows 2016 do DSVM em uma máquina virtual do Azure que não se baseia em GPUs. Nesse caso, todas as estruturas de aprendizado profundo retornarão para o modo de CPU.
 
[Saiba mais sobre o aprendizado profundo e as estruturas de ia disponíveis](dsvm-tools-deep-learning-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>O que está incluído no DSVM?

Veja uma lista completa de ferramentas nos DSVM do Windows e Linux [aqui](tools-included.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais com estes artigos:

+ Windows:
  + [Configurar uma DSVM do Windows](provision-vm.md)
  + [Dez coisas que você pode fazer em um DSVM do Windows](vm-do-ten-things.md)

+ Linux:
  + [Configurar um Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Configurar um DSVM do Linux (CentOS)](linux-dsvm-intro.md)
  + [Ciência de dados em um DSVM do Linux](linux-dsvm-walkthrough.md)
