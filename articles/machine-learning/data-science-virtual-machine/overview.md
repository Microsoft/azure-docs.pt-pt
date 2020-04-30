---
title: O que é a Máquina Virtual de Ciência de Dados Azure
titleSuffix: Azure Data Science Virtual Machine
description: Visão geral da Máquina Virtual de Ciência de Dados Azure - Uma máquina virtual fácil de criar e usar na plataforma cloud Azure com ferramentas e bibliotecas pré-instaladas e configuradas para fazer ciência de dados e desenvolver aplicações inteligentes.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 03bfee258fe96d90c32b6a305b99856a11d9a087
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80754978"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>O que é a Máquina Virtual de Ciência de Dados Azure para Linux e Windows?

A Máquina Virtual de Ciência de Dados (DSVM) é uma imagem VM personalizada na plataforma de nuvem Azure construída especificamente para fazer ciência de dados. Tem muitas ferramentas populares de ciência de dados pré-instaladas e pré-configuradas para iniciar a construção de aplicações inteligentes para análise avançada. 

O DSVM está disponível em:

+ **Windows Server 2019**
+ **Ubuntu 18.04 LTS**
+ Windows Server 2016
+ Ubuntu 16.04 LTS

> [!NOTE]
> Todas as ferramentas VM para aprendizagem profunda foram dobradas na Máquina Virtual da Ciência dos Dados. 

## <a name="why-choose-the-dsvm"></a>Por que escolher o DSVM?

O objetivo da Máquina Virtual da Ciência dos Dados é fornecer aos profissionais de dados de todos os níveis de competênciae em todas as indústrias um ambiente de ciência de dados sem atritos e pré-configurado. Em vez de lançar um espaço de trabalho comparável por conta própria, pode fornecer um DSVM. Essa escolha pode poupar-lhe dias ou mesmo _semanas_ nos processos de instalação, configuração e gestão de pacotes. Quando a DSVM estiver alocada, pode começar a trabalhar imediatamente no seu projeto de ciência de dados.

## <a name="sample-use-cases"></a>Casos de Utilização da Amostra

Abaixo, ilustramos alguns casos de uso comum para clientes DSVM.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Movendo as cargas de trabalho da ciência dos dados para a nuvem

O DSVM fornece uma configuração de base para equipas de ciência de dados que querem substituir os seus desktops locais por um ambiente de trabalho gerido em nuvem, garantindo que todos os cientistas de dados de uma equipa têm uma configuração consistente para verificar experiências e promover a colaboração. Reduz também os custos reduzindo os encargos com a sysadmin. Esta redução de encargos poupa o tempo necessário para avaliar, instalar e manter pacotes de software para análises avançadas.

### <a name="data-science-training-and-education"></a>Formação e educação em ciência de dados

Formadores empresariais e educadores que ensinam aulas de ciências de dados geralmente fornecem uma imagem virtual da máquina. A imagem garante que os alunos tenham uma configuração consistente e que as amostras funcionem previsivelmente. 

O DSVM cria um ambiente a pedido com uma configuração consistente que facilita os desafios de apoio e incompatibilidade. Os casos em que estes ambientes têm de ser criados constantemente, especialmente para aulas de formação mais pequenas, retiram benefícios significativos destas características.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidade elástica a pedido para projetos de grande escala

Hackathons/competições de ciência de dados ou modelação e exploração de dados em larga escala requerem capacidade de hardware escalonada, normalmente por curta duração. O DSVM pode ajudar a replicar rapidamente o ambiente da ciência dos dados a pedido, em servidores dimensionados que permitem experiências que os recursos de computação de alta potência podem executar.

### <a name="custom-compute-power-for-azure-notebooks"></a>Potência de computação personalizada para Cadernos Azure

[O Azure Notebooks](../../notebooks/azure-notebooks-overview.md) é um serviço gratuito para desenvolver, executar e partilhar cadernos Jupyter na nuvem sem instalação. O nível de serviço gratuito está limitado a 4 GB de memória e 1 GB de dados. 

Para libertar todos os limites, pode anexar um projeto de Notebooks a um DSVM ou a qualquer outro VM a funcionar num servidor Jupyter. Se você assinar nos Cadernos Azure com uma conta utilizando o Azure Ative Directory (como uma conta corporativa), os Notebooks mostram automaticamente DSVMs em quaisquer subscrições associadas a essa conta. Pode [anexar um DSVM aos Cadernos Azure](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) para expandir a potência de cálculo disponível.

### <a name="short-term-experimentation-and-evaluation"></a>Experimentação e avaliação a curto prazo

Pode utilizar o DSVM para avaliar ou aprender novas [ferramentas](./tools-included.md)de ciência de dados , especialmente através de algumas das [nossas amostras e walkthroughs publicados.](./dsvm-samples-and-walkthroughs.md)


### <a name="deep-learning-with-gpus"></a>Aprendizagem profunda com GPUs

No DSVM, os seus modelos de formação podem usar algoritmos de aprendizagem profunda em hardware baseados em unidades de processamento gráfico (GPUs). Aproveitando as capacidades de escala VM da plataforma Azure, o DSVM ajuda-o a utilizar hardware baseado em GPU na nuvem de acordo com as suas necessidades. Pode mudar para um VM baseado em GPU quando estiver a treinar grandes modelos, ou quando precisa de computações de alta velocidade, mantendo o mesmo disco osso. Pode escolher qualquer uma das máquinas virtuais ativadas pela série N SKUs com DSVM. Por favor, note que as contas gratuitas do Azure não suportam a gpu ativada pela máquina virtual SKUs.

As edições windows do DSVM vêm pré-instaladas com controladores gpu, quadros e versões GPU de quadros de aprendizagem profunda. Na edição do Linux, a aprendizagem profunda nas GPUs está ativada nos DSVMs ubuntu. 

Também pode implementar as edições Ubuntu ou Windows do DSVM para uma máquina virtual Azure que não é baseada em GPUs. Neste caso, todos os quadros de aprendizagem profunda voltarão ao modo CPU.

Saiba mais sobre a [aprendizagem profunda disponível e os quadros de IA.](dsvm-tools-deep-learning-frameworks.md)

<a name="included"></a>
## <a name="whats-included-on-the-dsvm"></a>O que está incluído no DSVM?

Consulte uma lista completa de ferramentas tanto no Windows como no Linux DSVM's [aqui](tools-included.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais com estes artigos:

+ Windows:
  + [Configurar uma DSVM do Windows](provision-vm.md)
  + [Dez coisas que pode fazer num DSVM do Windows](vm-do-ten-things.md)

+ Linux:
  + [Criar um Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Ciência de dados num DSVM Linux](linux-dsvm-walkthrough.md)
