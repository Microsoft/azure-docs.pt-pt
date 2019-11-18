---
title: O que são pipelines de ML
titleSuffix: Azure Machine Learning
description: Neste artigo, Aprenda as vantagens dos pipelines do Machine Learning que você pode criar com o SDK do Azure Machine Learning para Python. Pipelines (ML) de aprendizado de máquina são utilizados por cientistas de dados para criar, otimizar e gerir os seus fluxos de trabalho de aprendizagem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: 21aa869d53a35ee974fb2f852b9be9b10eb929b0
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112412"
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
| [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Integração e entrega contínuas de seu aplicativo para qualquer plataforma/qualquer nuvem  | Aplicativo/serviço de > de código |

## <a name="what-can-azure-ml-pipelines-do"></a>O que os pipelines do Azure ML podem fazer?

Um pipeline de Azure Machine Learning é um fluxo de trabalho executável independente de uma tarefa de aprendizado de máquina completa. As subtarefas são encapsuladas como uma série de etapas no pipeline. Um pipeline Azure Machine Learning pode ser tão simples quanto um que chama um script Python, portanto, _pode_ fazer praticamente qualquer coisa. Os pipelines _devem_ se concentrar em tarefas de aprendizado de máquina, como:

+ Preparação de dados, incluindo importação, validação e limpeza, mudanças irreversíveis e transformação, normalização e preparo
+ Configuração de treinamento, incluindo argumentos de parametrização, filePaths e configurações de log/relatório
+ Treinamento e validação com eficiência e repetição, o que pode incluir a especificação de subconjuntos de dados específicos, diferentes recursos de computação de hardware, processamento distribuído e monitoramento de progresso
+ Implantação, incluindo versionamento, dimensionamento, provisionamento e controle de acesso 

As etapas independentes permitem que vários cientistas de dados funcionem no mesmo pipeline ao mesmo tempo sem sobrecarregar recursos de computação. Etapas separadas também facilitam o uso de diferentes tipos/tamanhos de computação para cada etapa.

Depois que o pipeline é criado, geralmente há um ajuste mais fino em relação ao loop de treinamento do pipeline. Quando você executa novamente um pipeline, a execução salta para as etapas que precisam ser executadas novamente, como um script de treinamento atualizado. As etapas que não precisam ser executadas novamente são ignoradas. A mesma análise se aplica a scripts inalterados usados para a realização da etapa. Essa funcionalidade de reutilização ajuda a evitar a execução de etapas dispendiosas e demoradas, como a ingestão de dados e a transformação se os dados subjacentes não forem alterados.

Com o Azure Machine Learning, você pode usar vários kits de instruções e estruturas, como PyTorch ou TensorFlow, para cada etapa em seu pipeline. O Azure coordena os vários [destinos de computação](concept-azure-machine-learning-architecture.md) que você usa, para que seus dados intermediários possam ser compartilhados com os destinos de computação downstream.

Você pode [acompanhar as métricas de seus experimentos de pipeline](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) diretamente no portal do Azure ou na [página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com). Depois que um pipeline tiver sido publicado, você poderá configurar um ponto de extremidade REST, que permite executar novamente o pipeline de qualquer plataforma ou pilha.

Em suma, todas as tarefas complexas do ciclo de vida do Machine Learning podem ser ajudado com pipelines. Outras tecnologias de pipeline do Azure têm seus próprios pontos fortes, como [Azure data Factory pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) para trabalhar com dados e [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/) para integração e implantação contínuas. Mas se o seu foco for aprendizado de máquina, Azure Machine Learning pipelines provavelmente serão a melhor opção para suas necessidades de fluxo de trabalho. 

## <a name="what-are-azure-ml-pipelines"></a>O que são pipelines do Azure ML?

Um pipeline do Azure ML executa um fluxo de trabalho lógico completo com uma sequência ordenada de etapas. Cada etapa é uma ação de processamento discreta. Os pipelines são executados no contexto de um [experimento](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)Azure Machine Learning.

Nos estágios muito iniciais de um projeto de ML, é bom ter um único script de Jupyter ou de Python que faz todo o trabalho de configuração de espaço de trabalho e recursos do Azure, preparação de dados, configuração de execução, treinamento e validação. Mas, assim como as funções e as classes rapidamente se tornam preferíveis a um único bloco de código imperativo, os fluxos de trabalho do ML rapidamente se tornam preferíveis a um bloco de anotações ou script monolítico. 

Ao modularizar as tarefas do ML, os pipelines dão suporte à ciência da computação que um componente deve "fazer (apenas) uma coisa bem". A modularidade é claramente vital para o sucesso do projeto durante a programação em equipes, mas mesmo ao trabalhar sozinho, mesmo um pequeno projeto de ML envolve tarefas separadas, cada uma com uma boa quantidade de complexidade. As tarefas incluem: configuração de espaço de trabalho e acesso a dados, preparação de dados, definição de modelo e configuração e implantação. Embora as saídas de uma ou mais tarefas retenham as entradas para outra, os detalhes exatos de implementação de qualquer tarefa são, no melhor, distrações irrelevantes na próxima. No pior, o estado computacional de uma tarefa pode causar um bug em outro. 

### <a name="analyzing-dependencies"></a>Analisando dependências

Muitos ecossistemas de programação têm ferramentas que orquestram dependências de recursos, biblioteca ou compilação. Em geral, essas ferramentas usam carimbos de data/hora de arquivo para calcular dependências. Quando um arquivo é alterado, apenas ele e seus dependentes são atualizados (baixados, recompilados ou empacotados). Os pipelines do Azure ML ampliam esse conceito drasticamente. Como as ferramentas de Build tradicionais, os pipelines calculam as dependências entre as etapas e só executam os recálculos necessários. 

No entanto, a análise de dependência em pipelines do Azure ML é mais sofisticada do que carimbos de data/hora simples. Cada etapa pode ser executada em um ambiente de hardware e software diferente. A preparação de dados pode ser um processo demorado, mas não precisa ser executada em hardware com GPUs poderosas, determinadas etapas podem exigir software específico do sistema operacional, talvez você queira usar o treinamento distribuído e assim por diante. Embora a economia de custos para otimizar os recursos possa ser significativa, pode ser difícil manipular manualmente todas as diferentes variações em recursos de hardware e software. É ainda mais difícil fazer tudo isso sem jamais cometer um erro nos dados transferidos entre as etapas. 

Os pipelines resolvem esse problema. Azure Machine Learning orquestra automaticamente todas as dependências entre as etapas do pipeline. Essa orquestração pode incluir a rotação de imagens do Docker para cima e para baixo, anexando e desanexando recursos de computação e movendo dados entre as etapas de maneira consistente e automática.

### <a name="reusing-results"></a>Reutilizando resultados

Além disso, a saída de uma etapa pode, se você escolher, ser reutilizada. Se você especificar reutilização como uma possibilidade e não houver nenhuma dependência upstream disparando o recálculo, o serviço de pipeline usará uma versão armazenada em cache dos resultados da etapa. Essa reutilização pode diminuir drasticamente o tempo de desenvolvimento. Se você tiver uma tarefa de preparação de dados complexa, provavelmente será executada novamente com mais frequência do que é estritamente necessário. Os pipelines aliviam isso de preocupação: se necessário, a etapa será executada, caso contrário, não será.

Toda essa análise de dependência, orquestração e ativação são manipuladas por Azure Machine Learning ao instanciar um objeto de [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) , passá-lo para um `Experiment`e chamar `submit()`. 

### <a name="coordinating-the-steps-involved"></a>Coordenando as etapas envolvidas

Quando você cria e executa um objeto `Pipeline`, ocorrem as seguintes etapas de alto nível:

+ Para cada etapa, o serviço calcula os requisitos para:
    + Recursos de computação de hardware
    + Recursos do sistema operacional (imagem (ns) do Docker)
    + Recursos de software (dependências de Conda/virtualenv)
    + Entradas de dados 
+ O serviço determina as dependências entre etapas, resultando em um grafo de execução dinâmica
+ Quando cada nó no grafo de execução é executado:
    + O serviço configura o ambiente de hardware e software necessário (talvez reutilizando os recursos existentes)
    + A etapa é executada, fornecendo informações de registro em log e monitoramento para o objeto que os contém `Experiment`
    + Quando a etapa for concluída, suas saídas serão preparadas como entradas para a próxima etapa e/ou gravadas no armazenamento
    + Os recursos que não são mais necessários são finalizados e desanexados

![Etapas de pipeline](media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Criando pipelines com o SDK do Python

No [SDK do Azure Machine Learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), um pipeline é um objeto Python definido no módulo `azureml.pipeline.core`. Um objeto de [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) contém uma sequência ordenada de um ou mais objetos [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) . A classe `PipelineStep` é abstrata e as etapas reais serão de subclasses, como [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)ou [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). A classe [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) mantém uma sequência reutilizável de etapas que podem ser compartilhadas entre pipelines. Um `Pipeline` é executado como parte de um `Experiment`.

Um pipeline do Azure ML está associado a um espaço de trabalho Azure Machine Learning e uma etapa de pipeline está associada a um destino de computação disponível nesse espaço de trabalho. Para obter mais informações, consulte [criar e gerenciar espaços de trabalho do Azure Machine Learning no portal do Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace) ou [quais são os destinos de computação no Azure Machine Learning?](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).

No Azure Machine Learning, um destino de computação é o ambiente no qual ocorre uma fase de ML. O ambiente de software pode ser uma VM remota, Azure Machine Learning computação, Azure Databricks, lote do Azure e assim por diante. O ambiente de hardware também pode variar muito, dependendo do suporte de GPU, memória, armazenamento e assim por diante. Você pode especificar o destino de computação para cada etapa, o que lhe dá um controle refinado sobre os custos. Você pode usar recursos mais ou menos poderosos para a ação específica, o volume de dados e as necessidades de desempenho do seu projeto. 

## <a name="building-pipelines-with-the-designer"></a>Criando pipelines com o designer

Os desenvolvedores que preferem uma superfície de Design Visual podem usar o designer de Azure Machine Learning para criar pipelines. Você pode acessar essa ferramenta por meio da seleção de **Designer** na home page do seu espaço de trabalho.  O designer permite que você arraste e solte as etapas na superfície de design. Para o desenvolvimento rápido, você pode usar módulos existentes em todo o espectro de tarefas de ML; os módulos existentes abrangem tudo, desde a transformação de dados até a seleção de algoritmo até o treinamento até a implantação. Ou você pode criar um pipeline totalmente personalizado combinando suas próprias etapas definidas em scripts Python.

Quando você cria pipelines visualmente, as entradas e saídas de uma etapa são exibidas visivelmente. Você pode arrastar e soltar conexões de dados, permitindo que você entenda rapidamente e modifique o fluxo de armazenamento de seu pipeline.
 
![Exemplo de designer de Azure Machine Learning](./media/concept-ml-pipelines/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Compreendendo o grafo de execução

As etapas em um pipeline podem ter dependências de outras etapas. O serviço de pipeline do Azure ML faz o trabalho de analisar e orquestrar essas dependências. Os nós no "grafo de execução" resultante são etapas de processamento. Cada etapa pode envolver a criação ou reutilização de uma combinação específica de hardware e software, reutilização de resultados em cache e assim por diante. A orquestração e a otimização do serviço desse grafo de execução podem acelerar significativamente uma fase de ML e reduzir os custos. 

Como as etapas são executadas de forma independente, os objetos para manter os dados de entrada e saída que fluem entre as etapas devem ser definidos externamente. Essa é a função de [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)e classes associadas. Esses objetos de dados são associados a um objeto [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) que encapsula sua configuração de armazenamento. A classe base de `PipelineStep` é sempre criada com uma cadeia de caracteres de `name`, uma lista de `inputs`e uma lista de `outputs`. Normalmente, ele também tem uma lista de `arguments` e, muitas vezes, terá uma lista de `resource_inputs`. Em geral, as subclasses também terão argumentos adicionais (por exemplo, `PythonScriptStep` exigirá o nome do arquivo e o caminho do script a ser executado). 

O grafo de execução é acíclico, mas os pipelines podem ser executados em um agendamento recorrente e podem executar scripts Python que podem gravar informações de estado no sistema de arquivos, tornando possível criar perfis complexos. Se você projetar seu pipeline para que determinadas etapas possam ser executadas em paralelo ou assincronamente, Azure Machine Learning tratar de forma transparente a análise de dependência e a coordenação do fan-out e do Fan-in. Normalmente, você não precisa se preocupar com os detalhes do grafo de execução, mas está disponível por meio do atributo [pipeline. Graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) . 


### <a name="a-simple-python-pipeline"></a>Um pipeline Python simples

Este trecho de código mostra os objetos e as chamadas necessárias para criar e executar um `Pipeline`básico:

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

O trecho de código começa com objetos Azure Machine Learning comuns, um `Workspace`, um `Datastore`, um [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)e um `Experiment`. Em seguida, o código cria os objetos para manter `input_data` e `output_data`. A matriz `steps` mantém um único elemento, uma `PythonScriptStep` que usará os objetos de dados e será executada no `compute_target`. Em seguida, o código instancia o próprio objeto `Pipeline`, passando o espaço de trabalho e a matriz de etapas. A chamada para `experiment.submit(pipeline)` começa a execução do pipeline do Azure ML. A chamada para `wait_for_completion()` blocos até que o pipeline seja concluído. 

## <a name="best-practices-when-using-pipelines"></a>Práticas recomendadas ao usar pipelines

Como você pode ver, a criação de um pipeline do Azure ML é um pouco mais complexa do que iniciar um script. Os pipelines exigem que alguns objetos Python sejam configurados e criados. 

Algumas situações que sugerem o uso de um pipeline:

* Em um ambiente de equipe: divida as tarefas de ML em várias etapas independentes para que os desenvolvedores possam trabalhar e desenvolver seus programas de forma independente. 

* Quando na implantação ou próxima: a configuração e o uso de operações agendadas e orientadas a eventos se mantêm no topo da alteração de dados.

* Nos estágios iniciais de um projeto de ML ou trabalhando sozinho: Use pipelines para automatizar a compilação. Se você começou a se preocupar com a recriação da configuração e do estado computacional antes de implementar uma nova ideia, esse é um sinal que você pode considerar ao usar um pipeline para automatizar o fluxo de trabalho. 

É fácil se tornar entusiasmado com a reutilização de resultados armazenados em cache, o controle refinado sobre os custos de computação e o isolamento do processo, mas os pipelines têm custos. Alguns antipadrões incluem:

* Usando pipelines como o único meio de separar as preocupações. As funções, os objetos e os módulos internos do Python passam a um longo caminho para evitar um estado programático confuso! Uma etapa de pipeline é muito mais cara do que uma chamada de função.

* Acoplamento pesado entre as etapas do pipeline. Se a refatoração de uma etapa dependente frequentemente exigir a modificação das saídas de uma etapa anterior, é provável que etapas separadas sejam atualmente mais de um custo do que um benefício. Outra pista de que as etapas são muito ligadas é argumentos para uma etapa que não são dados, mas sinalizadores para controlar o processamento. 

* Otimização prematura dos recursos de computação. Por exemplo, muitas vezes há vários estágios para a preparação de dados e, muitas vezes, pode ver "Ah, aqui está um lugar onde eu poderia usar um `MpiStep` para a programação paralela, mas aqui está um lugar onde eu poderia usar um `PythonScriptStep` com um destino de computação menos potente , "e assim por diante. E talvez, a longo prazo, a criação de etapas refinadas como essa pode ser válida, especialmente se houver a possibilidade de usar resultados em cache em vez de sempre recalcular. Mas pipelines não se destinam a ser um substituto para o módulo `multiprocessing`. 

Até que um projeto fique grande ou próximo da implantação, seus pipelines devem ser mais esparsos em vez de refinados. Se você considerar seu projeto ML como envolvendo _estágios_ e um pipeline como fornecer um fluxo de trabalho completo para movê-lo por um estágio específico, você está no caminho certo. 

## <a name="key-advantages"></a>Principais vantagens

As principais vantagens de usar pipelines para seus fluxos de trabalho de aprendizado de máquina são:

|Principais vantagens|Descrição|
|:-------:|-----------|
|**Autónoma&nbsp;é executado**|Agende as etapas para execução em paralelo ou em sequência de maneira confiável e autônoma. A preparação e a modelagem de dados podem durar dias ou semanas, e os pipelines permitem que você se concentre em outras tarefas enquanto o processo está em execução. |
|**Computação heterogênea**|Use vários pipelines que são coordenados de forma confiável entre os recursos de computação heterogêneos e escalonáveis e os locais de armazenamento. Faça uso eficiente dos recursos de computação disponíveis executando etapas de pipeline individuais em diferentes destinos de computação, como o HDInsight, VMs de ciência de dados de GPU e databricks.|
|**Capacidade de reutilização**|Crie modelos de pipeline para cenários específicos, como retreinamento e pontuação de lote. Disparar pipelines publicados de sistemas externos por meio de chamadas REST simples.|
|**Controlo de versões e controlo**|Em vez de controlar manualmente os dados e os caminhos de resultados à medida que você itera, use o SDK de pipelines para nomear e controlar explicitamente suas fontes de dados, entradas e saídas. Você também pode gerenciar scripts e dados separadamente para aumentar a produtividade.|
| **Modularidade** | Separar áreas de preocupações e isolar alterações permite que o software evolua a uma taxa mais rápida com maior qualidade. | 
|**Colaboração**|Os pipelines permitem que os cientistas de dados colaborem em todas as áreas do processo de design do Machine Learning, ao mesmo tempo em que podem trabalhar simultaneamente em etapas de pipeline.|

## <a name="next-steps"></a>Passos seguintes

Os pipelines do Azure ML são um recurso poderoso que começa a fornecer valor nos primeiros estágios de desenvolvimento. O valor aumenta conforme a equipe e o projeto crescem. Este artigo explicou como pipelines são especificados com o SDK Azure Machine Learning Python e orquestrados no Azure. Você viu algum código-fonte básico e foi apresentado a algumas das `PipelineStep` classes disponíveis. Você deve ter uma noção de quando usar pipelines do Azure ML e como o Azure os executa. 


+ Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md).

+ Saiba como [executar previsões em lote em dados grandes](tutorial-pipeline-batch-scoring-classification.md ).

+ Consulte os documentos de referência do SDK para [pipeline Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e [as etapas de pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Experimente o exemplo Jupyter notebooks mostrando [Azure Machine Learning pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Saiba como [executar blocos de anotações para explorar esse serviço](samples-notebooks.md).
