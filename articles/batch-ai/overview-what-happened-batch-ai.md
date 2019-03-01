---
title: O que está a acontecer ao Azure Batch AI? | Microsoft Docs
description: Saiba mais sobre o que está a acontecer ao Azure Batch AI e a opção de computação de serviço do Azure Machine Learning.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: edd6a7e5f385d766d51631d77f5889233af2469a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194507"
---
# <a name="whats-happening-to-azure-batch-ai"></a>O que está a acontecer ao Azure Batch AI?

**O serviço Azure Batch AI está desativando em Março.** A preparação de à escala e classificação de recursos do Batch AI estão agora disponíveis na [serviço Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), que foi disponibilizado no dia 4 de Dezembro de 2018.

Juntamente com muitas outras capacidades de machine learning, o serviço Azure Machine Learning inclui um destino de computação gerida baseado na nuvem para treinamento, implementação e modelos de aprendizagem automática de classificação. Este destino de computação é chamado [computação do Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Começar a migrar e usá-lo hoje](#migrate). Pode interagir com o serviço Azure Machine Learning através da respetiva [SDKs de Python](../machine-learning/service/quickstart-create-workspace-with-python.md), interface de linha de comandos e o [portal do Azure](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Linha cronológica de suporte

Neste momento, pode usar as suas subscrições do Azure Batch AI existentes, como antes. No entanto, não existem **novas subscrições** são possíveis e não existem novos investimentos que estão a ser efetuados.

A partir de Março&nbsp;31&#x2c;&nbsp;2019, subscrições não utilizadas do Batch AI deixará de funcionar.

## <a name="compare-to-azure-machine-learning"></a>Comparar com o Azure Machine Learning
É um serviço em nuvem que utiliza para preparar, implementar, automatizar e gerir modelos de aprendizagem automática, tudo na escala amplo que a cloud oferece. Obter uma compreensão geral do [serviço do Azure Machine Learning nesta visão geral](../machine-learning/service/overview-what-is-azure-ml.md).
 

Um ciclo de vida de desenvolvimento do modelo comum envolve a preparação de dados, treinamento e experimentação e uma fase de implementação. Este ciclo de ponto a ponto pode ser organizado, utilizando pipelines de Machine Learning.

![Diagrama de fluxo](./media/overview-what-happened-batch-ai/lifecycle.png)


[Saiba mais sobre como funciona o serviço e seus conceitos principais](../machine-learning/service/concept-azure-machine-learning-architecture.md). Muitos dos conceitos do fluxo de trabalho de treinamento de modelo são semelhantes aos conceitos existentes do Batch AI. 

Especificamente, este é um mapeamento de que forma deverá encará-los:
 
|Serviço batch AI|  Serviço Azure Machine Learning|
|:--:|:---:|
|Área de trabalho|Área de trabalho|
|Cluster|   Computação do tipo `AmlCompute`|
|Servidores de ficheiros|DataStores|
|Experimentações|Experimentações|
|Tarefas|É executada (permite execuções aninhadas)|
 
Esta é outra vista da mesma tabela que irá ajudá-lo a visualizar ainda mais as coisas:
 
### <a name="batch-ai-hierarchy"></a>Hierarquia de IA do batch
![Diagrama de fluxo](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
### <a name="azure-machine-learning-service-hierarchy"></a>Hierarquia de serviço do Azure Machine Learning
![Diagrama de fluxo](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

## <a name="platform-capabilities"></a>Capacidades da plataforma
O Azure Machine Learning, serviço traz um ótimo conjunto de novas funcionalidades, incluindo um treinamento de ponta a ponta -> a pilha de implementação que pode utilizar para o desenvolvimento de IA sem ter de gerir todos os recursos do Azure. Esta tabela compara o suporte de funcionalidades para formação entre os dois serviços.

|Funcionalidade|Serviço batch AI|Serviço Azure Machine Learning|
|-------|:-------:|:-------:|
|Opção de tamanho VM |CPU/GPU    |CPU/GPU. Também suporta FPGA para inferência|
|Cluster de pronto para IA (Drivers, Docker, etc.)|  Sim |Sim|
|Preparação do nó| Sim|    Não|
|Escolha de família de SO   |Parcial    |Não|
|Dedicado e LowPriority VMs  |Sim    |Sim|
|Dimensionamento Automático   |Sim    |Sim (por predefinição)|
|Tempo para o dimensionamento automático de espera  |Não |Sim|
|SSH    |Sim|   Sim|
|Montagem de nível de cluster |Sim (existem FileShares, Blobs, NFS, personalizado)   |Sim (montar ou Baixe o arquivo de dados)|
|Formação distribuída|  Sim |Sim|
|Modo de execução de tarefa|    VM ou de contentor|    Contentor|
|Imagem de contentor personalizado|    Sim |Sim|
|Qualquer Toolkit    |Sim    |Sim (Script de Python de execução)|
|JobPreparation|    Sim |Ainda não|
|Montagem de nível de tarefa |Sim (existem FileShares, Blobs, NFS, personalizado)   |Sim (existem FileShares, Blobs)|
|Monitorização de tarefas     |via GetJob|    através do histórico de execuções de mensagens em fila (informações mais detalhadas, tempo de execução personalizado para enviar por push mais métricas)|
|Recuperar ficheiros/modelos e os registos da tarefa |   por meio ListFiles e APIs de armazenamento  |por meio do serviço de artefacto|
 |Suporte para Tensorboard   |Não|    Sim|
|Quotas de nível de famílias de VM |Sim    |Sim (com a capacidade do anterior transportada)|
 
Para além da tabela anterior, existem funcionalidades no serviço do Azure Machine Learning que tradicionalmente não eram suportadas no BatchAI.

|Funcionalidade|Serviço batch AI|Serviço Azure Machine Learning|
|-------|:-------:|:-------:|
|Preparação do ambiente    |Não |Sim (Conda preparar e carregar para o ACR)|
|A otimização de hiper-parâmetros  |Não|    Sim|
|Gestão de modelos   |Não |Sim|
|Implementação/operacionalização| Não  |Através do AKS e do ACI|
|Preparação de dados   |Não |Sim|
|Destinos de computação    |VMs do Azure  |Local, BatchAI (como AmlCompute), o DataBricks, o HDInsight|
|Aprendizagem automática |Não|    Sim|
|Pipelines  |Não |Sim|
|Classificação de lote  |Sim    |Sim|
|Suporte do portal/CLI|    Sim |Sim|


## <a name="programming-interfaces"></a>Interfaces de programação

Esta tabela apresenta as várias interfaces de programação disponíveis para cada serviço.
    
|Funcionalidade|Serviço de BatchAI|Serviço Azure Machine Learning|
|-------|:-------:|:-------:|
|SDK    |Java, C#, Python, Nodejs   |Python (com base de configuração de execução e estimator com base para arquiteturas comuns)|
|CLI    |Sim    |Ainda não|
|Portal do Azure   |Sim    |Sim (exceto a submissão da tarefa)|
|API REST   |Sim    |Sim, mas distribuída em microsserviços|


A atualização do Batch AI a pré-visualização para o serviço GA'ed do Azure Machine Learning dá-lhe uma experiência melhor os conceitos que são mais fáceis de usar como avaliadores e arquivos de dados. Esta ação garante também GA SLAs de nível de serviço do Azure e o suporte ao cliente.

O Azure Machine Learning serviço também traz a nova funcionalidade como automatizado machine learning, a otimização de hiper-parâmetros e pipelines de ML, que são úteis para cargas de trabalho de IA mais em grande escala. A capacidade de implementar um modelo preparado sem mudar para um serviço separado ajuda a concluir o loop de ciência de dados de preparação de dados (com o SDK de preparação de dados) até à monitorização do modelo e de operacionalização.

<a name="migrate"></a>
## <a name="migrate"></a>Migrar

Saiba como migrar e como o código que uso é mapeada para o código no serviço do Azure Machine Learning no [migre para o serviço Azure Machine Learning](how-to-migrate.md) artigo.

## <a name="get-support"></a>Obter suporte

O batch AI está programado para extinguir em 31 de Março e já está a bloquear as subscrições novas impedir registo contra o serviço, a menos que seja na lista de permissões ao gerar uma exceção através do suporte.  Contacte-na [pré-visualização do Azure Batch AI treinamento](mailto:AzureBatchAITrainingPreview@service.microsoft.com) com alguma questão ou se tiver comentários, ao migrar para o serviço Azure Machine Learning.

O serviço do Azure Machine Learning é um serviço disponível em geral. Isso significa que ele vem com um SLA de compromisso e vários planos de suporte à sua escolha.

Não devem variar de preços para a utilização de infraestrutura do Azure através do serviço de Batch AI ou através do serviço Azure Machine Learning, à medida que só paga o preço de computação e armazenamento em ambos os casos. Para obter mais informações, consulte a [Calculadora de preços](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Ver a disponibilidade regional entre os dois serviços sobre o [portal do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Passos Seguintes

+ Saiba mais [como migrar](how-to-migrate.md) e como o código que utiliza agora mapeia para o código no serviço Azure Machine Learning.

+ Leitura a [descrição geral do serviço Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Configurar um destino de computação para a preparação de modelos](../machine-learning/service/how-to-set-up-training-targets.md) com o serviço Azure Machine Learning.

+ Reveja os [mapa do Azure](https://azure.microsoft.com/updates/) para saber de outras atualizações de serviço do Azure.
