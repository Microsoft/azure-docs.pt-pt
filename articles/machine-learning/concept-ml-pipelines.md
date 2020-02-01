---
title: O que são pipelines de ML
titleSuffix: Azure Machine Learning
description: Neste artigo, aprenda as vantagens dos gasodutos de machine learning (ML) que pode construir com o Azure Machine Learning SDK para Python. Os gasodutos de aprendizagem automática são usados pelos cientistas de dados para construir, otimizar e gerir os seus fluxos de trabalho de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: 840c5cf061658f3210fec963b82b490185b92a4b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905727"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>O que são pipelines Azure Machine Learning?

Azure Machine Learning pipelines permitem que você crie fluxos de trabalho em seus projetos do Machine Learning. Esses fluxos de trabalho têm vários benefícios: 

+ Simplicidade
+ Velocidade
+ Repetição
+ Flexibilidade
+ Controle de versão e acompanhamento
+ Modularidade 
+ Garantia de qualidade
+ Controle de custo

Esses benefícios se tornam significativos assim que o seu projeto de aprendizado de máquina passa além da exploração pura e da iteração. Até mesmo pipelines simples de uma etapa podem ser valiosos. Os projetos de aprendizado de máquina geralmente estão em um estado complexo, e pode ser um alívio para fazer o realização preciso de um único fluxo de trabalho um processo trivial.

Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md).

![Pipelines do Machine Learning no Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Qual tecnologia de pipeline do Azure devo usar?

A nuvem do Azure fornece vários outros pipelines, cada um com uma finalidade diferente. A tabela a seguir lista os pipelines diferentes e para que eles são usados:

| Pipeline | O que faz | Pipe canônico |
| ---- | ---- | ---- |
| Pipelines de Azure Machine Learning | Define fluxos de trabalho de aprendizado de máquina reutilizáveis que podem ser usados como um modelo para seus cenários de aprendizado de máquina. | Modelo de > de dados |
| [Pipelines do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Agrupa as atividades de movimentação de dados, transformação e controle necessárias para executar uma tarefa.  | Dados > dados |
| [Oleodutos Azure](https://azure.microsoft.com/services/devops/pipelines/) | Integração e entrega contínuas de seu aplicativo para qualquer plataforma/qualquer nuvem  | Aplicativo/serviço de > de código |

## <a name="what-can-azure-ml-pipelines-do"></a>O que os pipelines do Azure ML podem fazer?

Um pipeline de Azure Machine Learning é um fluxo de trabalho executável independente de uma tarefa de aprendizado de máquina completa. As subtarefas são encapsuladas como uma série de passos dentro do pipeline. Um oleoduto azure machine learning pode ser tão simples como aquele que chama um guião Python, por isso _pode_ fazer qualquer coisa. Os gasodutos _devem_ centrar-se em tarefas de aprendizagem automática tais como:

+ Treino de dados, incluindo importação, validação e limpeza, manipulação e transformação, normalização e teste
+ Configuração de preparações, incluindo a parametrização de argumentos, caminhos de ficheiros e registo/comunicação de configurações
+ Treinamento e validação de forma eficiente e repetida, o que pode incluir a especificação de subconjuntos de dados específicos, diferentes recursos de computação de hardware, processamento distribuído e monitoramento de progresso
+ Implementação, incluindo a gestão de versões, o dimensionamento, o aprovisionamento e o controlo de acesso 

As etapas independentes permitem que vários cientistas de dados funcionem no mesmo pipeline ao mesmo tempo sem sobrecarregar recursos de computação. Etapas separadas também facilitam o uso de diferentes tipos/tamanhos de computação para cada etapa.

Depois que o pipeline é criado, geralmente há um ajuste mais fino em relação ao loop de treinamento do pipeline. Quando você executa novamente um pipeline, a execução salta para as etapas que precisam ser executadas novamente, como um script de treinamento atualizado. As etapas que não precisam ser executadas novamente são ignoradas. A mesma análise se aplica a scripts inalterados usados para a realização da etapa. Essa funcionalidade de reutilização ajuda a evitar a execução de etapas dispendiosas e demoradas, como a ingestão de dados e a transformação se os dados subjacentes não forem alterados.

Com o Azure Machine Learning, você pode usar vários kits de instruções e estruturas, como PyTorch ou TensorFlow, para cada etapa em seu pipeline. O Azure coordena os [vários alvos de cálculo](concept-azure-machine-learning-architecture.md) que utiliza, para que os seus dados intermédios possam ser partilhados com os alvos de computação a jusante.

Pode [rastrear as métricas das suas experiências](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) de pipeline diretamente no portal Azure ou na sua página de aterragem no [espaço de trabalho (pré-visualização)](https://ml.azure.com). Depois que um pipeline tiver sido publicado, você poderá configurar um ponto de extremidade REST, que permite executar novamente o pipeline de qualquer plataforma ou pilha.

Em suma, todas as tarefas complexas do ciclo de vida do Machine Learning podem ser ajudado com pipelines. Outras tecnologias de gasoduto Azure têm os seus próprios pontos fortes, como [os oleodutos Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) para trabalhar com dados e [oleodutos Azure](https://azure.microsoft.com/services/devops/pipelines/) para integração e implantação contínua. Mas se o seu foco for aprendizado de máquina, Azure Machine Learning pipelines provavelmente serão a melhor opção para suas necessidades de fluxo de trabalho. 

## <a name="what-are-azure-ml-pipelines"></a>O que são pipelines do Azure ML?

Um pipeline do Azure ML executa um fluxo de trabalho lógico completo com uma sequência ordenada de etapas. Cada etapa é uma ação de processamento discreta. Os gasodutos funcionam no contexto de uma [experiência](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)de aprendizagem automática Azure.

Nos estágios muito iniciais de um projeto de ML, é bom ter um único script de Jupyter ou de Python que faz todo o trabalho de configuração de espaço de trabalho e recursos do Azure, preparação de dados, configuração de execução, treinamento e validação. Mas, assim como as funções e as classes rapidamente se tornam preferíveis a um único bloco de código imperativo, os fluxos de trabalho do ML rapidamente se tornam preferíveis a um bloco de anotações ou script monolítico. 

Ao modularizar as tarefas do ML, os pipelines dão suporte à ciência da computação que um componente deve "fazer (apenas) uma coisa bem". A modularidade é claramente vital para o sucesso do projeto durante a programação em equipes, mas mesmo ao trabalhar sozinho, mesmo um pequeno projeto de ML envolve tarefas separadas, cada uma com uma boa quantidade de complexidade. As tarefas incluem: configuração de espaço de trabalho e acesso a dados, preparação de dados, definição de modelo e configuração e implantação. Embora as saídas de uma ou mais tarefas retenham as entradas para outra, os detalhes exatos de implementação de qualquer tarefa são, no melhor, distrações irrelevantes na próxima. No pior, o estado computacional de uma tarefa pode causar um bug em outro. 

### <a name="analyzing-dependencies"></a>Analisando dependências

Muitos ecossistemas de programação têm ferramentas que orquestram dependências de recursos, biblioteca ou compilação. Em geral, essas ferramentas usam carimbos de data/hora de arquivo para calcular dependências. Quando um arquivo é alterado, apenas ele e seus dependentes são atualizados (baixados, recompilados ou empacotados). Os pipelines do Azure ML ampliam esse conceito drasticamente. Como as ferramentas de Build tradicionais, os pipelines calculam as dependências entre as etapas e só executam os recálculos necessários. 

No entanto, a análise de dependência em pipelines do Azure ML é mais sofisticada do que carimbos de data/hora simples. Cada etapa pode ser executada em um ambiente de hardware e software diferente. A preparação de dados pode ser um processo demorado, mas não precisa ser executada em hardware com GPUs poderosas, determinadas etapas podem exigir software específico do sistema operacional, talvez você queira usar o treinamento distribuído e assim por diante. Embora a economia de custos para otimizar os recursos possa ser significativa, pode ser difícil manipular manualmente todas as diferentes variações em recursos de hardware e software. É ainda mais difícil fazer tudo isso sem jamais cometer um erro nos dados transferidos entre as etapas. 

Os pipelines resolvem esse problema. Azure Machine Learning orquestra automaticamente todas as dependências entre as etapas do pipeline. Essa orquestração pode incluir a rotação de imagens do Docker para cima e para baixo, anexando e desanexando recursos de computação e movendo dados entre as etapas de maneira consistente e automática.

### <a name="reusing-results"></a>Reutilizando resultados

Além disso, a saída de uma etapa pode, se você escolher, ser reutilizada. Se você especificar reutilização como uma possibilidade e não houver nenhuma dependência upstream disparando o recálculo, o serviço de pipeline usará uma versão armazenada em cache dos resultados da etapa. Essa reutilização pode diminuir drasticamente o tempo de desenvolvimento. Se você tiver uma tarefa de preparação de dados complexa, provavelmente será executada novamente com mais frequência do que é estritamente necessário. Os pipelines aliviam isso de preocupação: se necessário, a etapa será executada, caso contrário, não será.

Toda esta análise de dependência, orquestração e ativação são tratadas pela Azure Machine Learning quando instantaneamente um objeto [pipeline,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) passem-no para um `Experiment`, e liguem para `submit()`. 

### <a name="coordinating-the-steps-involved"></a>Coordenando as etapas envolvidas

Quando cria e executa um objeto `Pipeline`, ocorrem os seguintes passos de alto nível:

+ Para cada etapa, o serviço calcula os requisitos para:
    + Recursos de computação de hardware
    + Recursos do sistema operacional (imagem (ns) do Docker)
    + Recursos de software (dependências de Conda/virtualenv)
    + Entradas de dados 
+ O serviço determina as dependências entre etapas, resultando em um grafo de execução dinâmica
+ Quando cada nó no grafo de execução é executado:
    + O serviço configura o ambiente de hardware e software necessário (talvez reutilizando os recursos existentes)
    + O passo corre, fornecendo informações de exploração e monitorização ao seu objeto de `Experiment` contendo
    + Quando a etapa for concluída, suas saídas serão preparadas como entradas para a próxima etapa e/ou gravadas no armazenamento
    + Os recursos que não são mais necessários são finalizados e desanexados

![Etapas de pipeline](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Construção de oleodutos com o Python SDK

No [Azure Machine Learning Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)um oleoduto é um objeto Python definido no módulo `azureml.pipeline.core`. Um objeto [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) contém uma sequência ordenada de um ou mais objetos [PipelineStep.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) A aula `PipelineStep` é abstrata e os passos reais serão de subclasses como [EstimatorStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py) [PythonScriptStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)ou [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). A classe [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) contém uma sequência reutilizável de passos que podem ser partilhados entre os oleodutos. Um `Pipeline` funciona como parte de um `Experiment`.

Um pipeline do Azure ML está associado a um espaço de trabalho Azure Machine Learning e uma etapa de pipeline está associada a um destino de computação disponível nesse espaço de trabalho. Para mais informações, consulte Criar e gerir espaços de [trabalho azure machine learning no portal Azure](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) ou [Quais são os alvos da computação em Azure Machine Learning?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)

No Azure Machine Learning, um destino de computação é o ambiente no qual ocorre uma fase de ML. O ambiente de software pode ser uma VM remota, Azure Machine Learning computação, Azure Databricks, lote do Azure e assim por diante. O ambiente de hardware também pode variar muito, dependendo do suporte de GPU, memória, armazenamento e assim por diante. Você pode especificar o destino de computação para cada etapa, o que lhe dá um controle refinado sobre os custos. Você pode usar recursos mais ou menos poderosos para a ação específica, o volume de dados e as necessidades de desempenho do seu projeto. 

## <a name="building-pipelines-with-the-designer"></a>Construindo oleodutos com o designer

Os desenvolvedores que preferem uma superfície de design visual podem usar o designer de Machine Learning Azure para criar oleodutos. Pode aceder a esta ferramenta a partir da seleção **do Designer** na página inicial do seu espaço de trabalho.  O designer permite-lhe arrastar e largar passos na superfície do design. Para um desenvolvimento rápido, pode utilizar módulos existentes em todo o espectro de tarefas ML; os módulos existentes cobrem tudo, desde a transformação de dados à seleção de algoritmos até ao treino até à implementação. Ou pode criar um pipeline totalmente personalizado combinando os seus próprios passos definidos nos scripts Python.

Quando se projeta maquetes visualmente, as inputs e saídas de um degrau são exibidas visivelmente. Pode arrastar e largar ligações de dados, permitindo-lhe compreender e modificar rapidamente o fluxo de dados do seu pipeline.
 
![Exemplo de designer de Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Compreendendo o grafo de execução

As etapas em um pipeline podem ter dependências de outras etapas. O serviço de pipeline do Azure ML faz o trabalho de analisar e orquestrar essas dependências. Os nós no "grafo de execução" resultante são etapas de processamento. Cada etapa pode envolver a criação ou reutilização de uma combinação específica de hardware e software, reutilização de resultados em cache e assim por diante. A orquestração e a otimização do serviço desse grafo de execução podem acelerar significativamente uma fase de ML e reduzir os custos. 

Como as etapas são executadas de forma independente, os objetos para manter os dados de entrada e saída que fluem entre as etapas devem ser definidos externamente. Este é o papel de [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), [PipelineData,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)e classes associadas. Estes objetos de dados estão associados a um objeto [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) que encapsula a sua configuração de armazenamento. A classe base `PipelineStep` é sempre criada com uma corda `name`, uma lista de `inputs`, e uma lista de `outputs`. Normalmente, também tem uma lista de `arguments` e muitas vezes terá uma lista de `resource_inputs`. As subclasses terão geralmente argumentos adicionais também (por exemplo, `PythonScriptStep` requer o nome de ficheiro e o caminho do script para executar). 

O grafo de execução é acíclico, mas os pipelines podem ser executados em um agendamento recorrente e podem executar scripts Python que podem gravar informações de estado no sistema de arquivos, tornando possível criar perfis complexos. Se você projetar seu pipeline para que determinadas etapas possam ser executadas em paralelo ou assincronamente, Azure Machine Learning tratar de forma transparente a análise de dependência e a coordenação do fan-out e do Fan-in. Geralmente não tem que se preocupar com os detalhes do gráfico de execução, mas está disponível através do atributo [Pipeline.graph.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) 


### <a name="a-simple-python-pipeline"></a>Um pipeline Python simples

Este corte mostra os objetos e chamadas necessários para criar e executar um `Pipeline`básico:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = DataReference(
    datastore=Datastore(ws, blob_store),
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")

output_data = PipelineData(
    "output_data",
    datastore=blob_store,
    output_name="output_data1")

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_data, "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

O corte começa com objetos comuns de Aprendizagem automática Azure, um `Workspace`, um `Datastore`, um [ComputeTarget,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)e um `Experiment`. Em seguida, o código cria os objetos para segurar `input_data` e `output_data`. A matriz `steps` detém um único elemento, um `PythonScriptStep` que utilizará os objetos de dados e será executado na `compute_target`. Em seguida, o código instantaneamente o `Pipeline` objeto em si, passando no espaço de trabalho e na matriz de passos. A chamada para `experiment.submit(pipeline)` inicia a execução do gasoduto Azure ML. A chamada para `wait_for_completion()` blocos até que o oleoduto esteja terminado. 

Para saber mais sobre como conectar seu pipeline a seus dados, confira os artigos [como acessar dados](how-to-access-data.md) e [como registrar os DataSets](how-to-create-register-datasets.md). 

## <a name="best-practices-when-using-pipelines"></a>Boas práticas ao utilizar oleodutos

Como você pode ver, a criação de um pipeline do Azure ML é um pouco mais complexa do que iniciar um script. Os pipelines exigem que alguns objetos Python sejam configurados e criados. 

Algumas situações que sugerem o uso de um pipeline:

* Em um ambiente de equipe: divida as tarefas de ML em várias etapas independentes para que os desenvolvedores possam trabalhar e desenvolver seus programas de forma independente. 

* Quando na implantação ou próxima: a configuração e o uso de operações agendadas e orientadas a eventos se mantêm no topo da alteração de dados.

* Nos estágios iniciais de um projeto de ML ou trabalhando sozinho: Use pipelines para automatizar a compilação. Se você começou a se preocupar com a recriação da configuração e do estado computacional antes de implementar uma nova ideia, esse é um sinal que você pode considerar ao usar um pipeline para automatizar o fluxo de trabalho. 

É fácil se tornar entusiasmado com a reutilização de resultados armazenados em cache, o controle refinado sobre os custos de computação e o isolamento do processo, mas os pipelines têm custos. Alguns antipadrões incluem:

* Usando pipelines como o único meio de separar as preocupações. As funções, os objetos e os módulos internos do Python passam a um longo caminho para evitar um estado programático confuso! Uma etapa de pipeline é muito mais cara do que uma chamada de função.

* Acoplamento pesado entre os degraus do oleoduto. Se a refatoração de uma etapa dependente frequentemente exigir a modificação das saídas de uma etapa anterior, é provável que etapas separadas sejam atualmente mais de um custo do que um benefício. Outra pista de que as etapas são muito ligadas é argumentos para uma etapa que não são dados, mas sinalizadores para controlar o processamento. 

* Otimização prematura dos recursos de computação. Por exemplo, muitas vezes há várias fases para a preparação de dados e muitas vezes se pode ver "Oh, aqui está um lugar onde eu poderia usar um `MpiStep` para programação paralela, mas aqui é um lugar onde eu poderia usar um `PythonScriptStep` com um alvo de computação menos poderoso", e assim por diante. E talvez, a longo prazo, a criação de etapas refinadas como essa pode ser válida, especialmente se houver a possibilidade de usar resultados em cache em vez de sempre recalcular. Mas os gasodutos não se destinam a substituir o módulo `multiprocessing`. 

Até que um projeto fique grande ou próximo da implantação, seus pipelines devem ser mais esparsos em vez de refinados. Se você pensa no seu projeto ML como envolvendo _etapas_ e um oleoduto como fornecendo um fluxo de trabalho completo para movê-lo através de uma determinada fase, você está no caminho certo. 

## <a name="key-advantages"></a>Principais vantagens

As principais vantagens de usar pipelines para seus fluxos de trabalho de aprendizado de máquina são:

|Principais vantagens|Descrição|
|:-------:|-----------|
|**Autónoma&nbsp;é executado**|Agende as etapas para execução em paralelo ou em sequência de maneira confiável e autônoma. A preparação e a modelagem de dados podem durar dias ou semanas, e os pipelines permitem que você se concentre em outras tarefas enquanto o processo está em execução. |
|**Computação heterogénea**|Use vários pipelines que são coordenados de forma confiável entre os recursos de computação heterogêneos e escalonáveis e os locais de armazenamento. Faça uso eficiente dos recursos de computação disponíveis executando etapas de pipeline individuais em diferentes destinos de computação, como o HDInsight, VMs de ciência de dados de GPU e databricks.|
|**Capacidade de reutilização**|Crie modelos de pipeline para cenários específicos, como retreinamento e pontuação de lote. Disparar pipelines publicados de sistemas externos por meio de chamadas REST simples.|
|**Controlo de versões e controlo**|Em vez de controlar manualmente os dados e os caminhos de resultados à medida que você itera, use o SDK de pipelines para nomear e controlar explicitamente suas fontes de dados, entradas e saídas. Você também pode gerenciar scripts e dados separadamente para aumentar a produtividade.|
| **Modularidade** | Separar áreas de preocupações e isolar alterações permite que o software evolua a uma taxa mais rápida com maior qualidade. | 
|**Colaboração**|Os pipelines permitem que os cientistas de dados colaborem em todas as áreas do processo de design do Machine Learning, ao mesmo tempo em que podem trabalhar simultaneamente em etapas de pipeline.|

## <a name="modules"></a>Módulos

Embora as etapas do gasoduto permitam a reutilização dos resultados de uma execução anterior, em muitos casos a construção do passo pressupõe que os scripts e ficheiros dependentes necessários devem estar disponíveis localmente. Se um cientista de dados quer construir em cima do código existente, os scripts e dependências muitas vezes devem ser clonados de um repositório separado.

Os módulos são semelhantes na utilização dos passos do gasoduto, mas fornecem versão facilitada através do espaço de trabalho, o que permite a colaboração e reutilização em escala. Os módulos são projetados para serem reutilizados em vários oleodutos e podem evoluir para adaptar uma computação específica a diferentes casos de uso. Os utilizadores podem fazer as seguintes tarefas através do espaço de trabalho, sem utilizar repositórios externos:

* Criar novos módulos e publicar novas versões dos módulos existentes
* Deprecate versões existentes
* Versões de marca desativadas para impedir que os consumidores utilizem esta versão
* Designar versões padrão
* Recupere os módulos por versão do espaço de trabalho, para garantir que as equipas estão a usar o mesmo código

Consulte o [caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) para obter exemplos de código sobre como criar, ligar e utilizar módulos em pipelines de Aprendizagem automática Azure.

## <a name="next-steps"></a>Passos seguintes

Os pipelines do Azure ML são um recurso poderoso que começa a fornecer valor nos primeiros estágios de desenvolvimento. O valor aumenta conforme a equipe e o projeto crescem. Este artigo explicou como pipelines são especificados com o SDK Azure Machine Learning Python e orquestrados no Azure. Você viu algum código fonte básico e foi introduzido em algumas das aulas de `PipelineStep` que estão disponíveis. Você deve ter uma noção de quando usar pipelines do Azure ML e como o Azure os executa. 


+ Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md).

+ Aprenda a executar previsões de [lotes em grandes dados](tutorial-pipeline-batch-scoring-classification.md ).

+ Consulte os docs de referência SDK para obter passos de núcleo de [gasoduto](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e [de gasoduto](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Experimente exemplo sacanas jupyter mostrando [os oleodutos Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Aprenda a [executar cadernos para explorar este serviço.](samples-notebooks.md)
