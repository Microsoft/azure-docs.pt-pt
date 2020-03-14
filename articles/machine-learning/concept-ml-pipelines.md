---
title: O que são os Gasodutos ML
titleSuffix: Azure Machine Learning
description: Neste artigo, aprenda as vantagens dos gasodutos de machine learning (ML) que pode construir com o Azure Machine Learning SDK para Python. Os gasodutos de aprendizagem automática são usados pelos cientistas de dados para construir, otimizar e gerir os seus fluxos de trabalho de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: 3f1d0e13d9b76c7ef06edb953b59ebfa73c302de
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79296851"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>O que são os oleodutos Azure Machine Learning?

Os oleodutos Azure Machine Learning permitem criar fluxos de trabalho nos seus projetos de aprendizagem automática. Estes fluxos de trabalho têm uma série de benefícios: 

+ Simplicidade
+ velocidade
+ Repetível
+ Flexibilidade
+ Versão e rastreio
+ Modularidade 
+ Garantia de qualidade
+ Controlo de custos

Estes benefícios tornam-se significativos assim que o seu projeto de aprendizagem automática vai além da exploração pura e da iteração. Mesmo os simples oleodutos de um passo podem ser valiosos. Os projetos de aprendizagem automática estão muitas vezes num estado complexo, e pode ser um alívio fazer da realização precisa de um único fluxo de trabalho um processo trivial.

Aprenda a [criar o seu primeiro oleoduto.](how-to-create-your-first-pipeline.md)

![Oleodutos de aprendizagem automática em Aprendizagem automática azure](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Que tecnologia de oleoduto Azure devo usar?

A nuvem Azure fornece vários outros oleodutos, cada um com um propósito diferente. A tabela seguinte enumera os diferentes oleodutos e para que são utilizados:

| Cenário | Persona primária | Oferta azure | Oferta osso | Tubo canónico | Pontos fortes | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Orquestração de modelos (Machine learning) | Cientista de dados | Gasodutos de Aprendizagem automática Azure | Gasodutos Kubeflow | Dados -> Modelo | Distribuição, cache, código primeiro, reutilização | 
| Orquestração de dados (Preparação de dados) | Engenheiro de dados | [Pipelines do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Fluxo de ar Apache | Dados -> Dados | Movimento fortemente dactilografado. Atividades centradas em dados. |
| Orquestração de códigos e aplicações (CI/CD) | Desenvolvedor de aplicativos / Operações | [Gasodutos Azure DevOps](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Código + Modelo -> App/Serviço | Mais suporte de atividade aberta e flexibile, filas de aprovação, fases com marcha | 


## <a name="what-can-azure-ml-pipelines-do"></a>O que podem os oleodutos Azure ML fazer?

Um oleoduto Azure Machine Learning é um fluxo de trabalho executável independentemente de uma tarefa completa de aprendizagem automática. As subtarefas são encapsuladas como uma série de passos dentro do pipeline. Um oleoduto azure machine learning pode ser tão simples como aquele que chama um guião Python, por isso _pode_ fazer qualquer coisa. Os gasodutos _devem_ centrar-se em tarefas de aprendizagem automática tais como:

+ Treino de dados, incluindo importação, validação e limpeza, manipulação e transformação, normalização e teste
+ Configuração de preparações, incluindo a parametrização de argumentos, caminhos de ficheiros e registo/comunicação de configurações
+ Treinando e validando de forma eficiente e repetida. A eficiência pode vir da especificação de subconjuntos específicos de dados, diferentes recursos de computação de hardware, processamento distribuído e monitorização de progressos
+ Implementação, incluindo a gestão de versões, o dimensionamento, o aprovisionamento e o controlo de acesso 

Passos independentes permitem que vários cientistas de dados trabalhem no mesmo oleoduto ao mesmo tempo sem sobretaxar os recursos computacionais. Passos separados também facilitam a utilização de diferentes tipos/tamanhos de computação para cada passo.

Após a construção do gasoduto, há muitas vezes uma afinação mais fina em torno do ciclo de treino do gasoduto. Quando se refaz um oleoduto, a corrida salta para os degraus que precisam de ser reexecutados, como um script de treino atualizado. Os passos que não precisam de ser reexecutados são ignorados. A mesma análise aplica-se a scripts inalterados utilizados para a realização do passo. Esta funcionalidade de reutilização ajuda a evitar o funcionamento de passos dispendiosos e intensivos de tempo, como a ingestão e transformação de dados se os dados subjacentes não mudarem.

Com o Azure Machine Learning, pode utilizar vários kits e estruturas de ferramentas, como pyTorch ou TensorFlow, para cada passo do seu pipeline. O Azure coordena os [vários alvos de cálculo](concept-azure-machine-learning-architecture.md) que utiliza, para que os seus dados intermédios possam ser partilhados com os alvos de computação a jusante.

Pode [rastrear as métricas das suas experiências](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) de pipeline diretamente no portal Azure ou na sua página de aterragem no [espaço de trabalho (pré-visualização)](https://ml.azure.com). Depois de publicado um pipeline, pode configurar um ponto final REST, que lhe permite reproduzir o pipeline a partir de qualquer plataforma ou stack.

Em suma, todas as tarefas complexas do ciclo de vida de aprendizagem automática podem ser ajudadas com gasodutos. Outras tecnologias de gasoduto Azure têm os seus próprios pontos fortes, como [os oleodutos Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) para trabalhar com dados e [oleodutos Azure](https://azure.microsoft.com/services/devops/pipelines/) para integração e implantação contínua. Mas se o seu foco é a aprendizagem automática, os oleodutos Azure Machine Learning são provavelmente a melhor escolha para as suas necessidades de fluxo de trabalho. 

## <a name="what-are-azure-ml-pipelines"></a>O que são os oleodutos Azure ML?

Um gasoduto Azure ML realiza um fluxo de trabalho lógico completo com uma sequência ordenada de passos. Cada passo é uma ação de processamento discreta. Os gasodutos funcionam no contexto de uma [experiência](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)de aprendizagem automática Azure.

Nas fases iniciais de um projeto ML, é bom ter um único caderno Jupyter ou um roteiro Python que faça todo o trabalho do espaço de trabalho azure e configuração de recursos, preparação de dados, configuração de execução, formação e validação. Mas assim como as funções e as classes rapidamente se tornam preferíveis a um único bloco imperativo de código, os fluxos de trabalho ml rapidamente tornam-se preferíveis a um caderno monolítico ou script. 

Ao modular as tarefas ml, os oleodutos suportam o imperativo da Ciência da Computação de que um componente deve "fazer (apenas) uma coisa bem". A modularidade é claramente vital para projetar o sucesso na programação em equipas, mas mesmo quando se trabalha sozinho, mesmo um pequeno projeto ML envolve tarefas separadas, cada uma com uma boa quantidade de complexidade. As tarefas incluem: configuração do espaço de trabalho e acesso a dados, preparação de dados, definição e configuração do modelo e implementação. Enquanto as saídas de uma ou mais tarefas formam as inputs para outra, os detalhes exatos de implementação de qualquer tarefa são, na melhor das hipóteses, distrações irrelevantes na próxima. Na pior das hipóteses, o estado computacional de uma tarefa pode causar um inseto noutra. 

### <a name="analyzing-dependencies"></a>Analisar dependências

Muitos ecossistemas de programação têm ferramentas que orquestram dependências de recursos, bibliotecas ou compilações. Geralmente, estas ferramentas usam carimbos de tempo de ficheiropara calcular dependências. Quando um ficheiro é alterado, apenas ele e os seus dependentes são atualizados (descarregados, recompilados ou embalados). Os oleodutos Azure ML estendem este conceito dramaticamente. Tal como as ferramentas tradicionais de construção, os gasodutos calculam dependências entre passos e apenas realizam os recálculos necessários. 

No entanto, a análise da dependência nos oleodutos Azure ML é mais sofisticada do que os simples selos temporais. Cada passo pode correr num ambiente de hardware e software diferente. A preparação de dados pode ser um processo demorado, mas não precisa de ser executado em hardware com GPUs poderosos, certos passos podem requerer software específico para os SISTEMAS, você pode querer usar treino distribuído, e assim por diante. Embora a poupança de custos para otimizar recursos possa ser significativa, pode ser esmagadora para fazer malabarismos manualmente com todas as diferentes variações nos recursos de hardware e software. É ainda mais difícil fazer tudo isso sem nunca cometer um erro nos dados que transfere entre passos. 

Os oleodutos resolvem este problema. O Azure Machine Learning orquestra automaticamente todas as dependências entre os passos do gasoduto. Esta orquestração pode incluir girar para cima e para baixo imagens do Docker, anexar e separar recursos computacionais, e mover dados entre os passos de forma consistente e automática.

### <a name="reusing-results"></a>Reutilizar resultados

Além disso, a saída de um passo pode, se assim o desejar, ser reutilizada. Se especificar a reutilização como uma possibilidade e não houver dependências a montante que desencadeiem um recálculo, o serviço de gasoduto utilizará uma versão em cache dos resultados do passo. Tal reutilização pode diminuir drasticamente o tempo de desenvolvimento. Se tiver uma tarefa complexa de preparação de dados, provavelmente reexecutá-la com mais frequência do que é estritamente necessário. Os oleodutos aliviam-te dessa preocupação: se necessário, o passo vai correr, se não, não vai.

Toda esta análise de dependência, orquestração e ativação são tratadas pela Azure Machine Learning quando instantaneamente um objeto [pipeline,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) passem-no para um `Experiment`, e liguem para `submit()`. 

### <a name="coordinating-the-steps-involved"></a>Coordenar os passos envolvidos

Quando cria e executa um objeto `Pipeline`, ocorrem os seguintes passos de alto nível:

+ Para cada passo, o serviço calcula os requisitos para:
    + Recursos computacionais de hardware
    + Recursos osso (imagem(s) do Docker)
    + Recursos de software (dependências de Conda / virtualenv)
    + Inputs de dados 
+ O serviço determina as dependências entre passos, resultando num gráfico de execução dinâmico
+ Quando cada nó no gráfico de execução corre:
    + O serviço configura o ambiente de hardware e software necessário (talvez reutilizar os recursos existentes)
    + O passo corre, fornecendo informações de exploração e monitorização ao seu objeto de `Experiment` contendo
    + Quando o passo estiver concluído, as suas saídas são preparadas como entradas para o próximo passo e/ou escritas para armazenamento
    + Os recursos que já não são necessários são finalizados e destacados

![Passos de gasoduto](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Construção de oleodutos com o Python SDK

No [Azure Machine Learning Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)um oleoduto é um objeto Python definido no módulo `azureml.pipeline.core`. Um objeto [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) contém uma sequência ordenada de um ou mais objetos [PipelineStep.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) A aula `PipelineStep` é abstrata e os passos reais serão de subclasses como [EstimatorStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py) [PythonScriptStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)ou [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). A classe [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) contém uma sequência reutilizável de passos que podem ser partilhados entre os oleodutos. Um `Pipeline` funciona como parte de um `Experiment`.

Um oleoduto Azure ML está associado a um espaço de trabalho azure machine learning e um passo de pipeline está associado a um alvo de cálculo disponível dentro desse espaço de trabalho. Para mais informações, consulte Criar e gerir espaços de [trabalho azure machine learning no portal Azure](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) ou [Quais são os alvos da computação em Azure Machine Learning?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)

No Azure Machine Learning, um alvo de cálculo é o ambiente em que ocorre uma fase ML. O ambiente do software pode ser um VM remoto, Azure Machine Learning Compute, Azure Databricks, Azure Batch, e assim por diante. O ambiente de hardware também pode variar muito, dependendo do suporte da GPU, memória, armazenamento, etc. Pode especificar o alvo do cálculo para cada passo, o que lhe dá um controlo fino sobre os custos. Pode utilizar recursos mais ou menos poderosos para a ação específica, volume de dados e necessidades de desempenho do seu projeto. 

## <a name="building-pipelines-with-the-designer"></a>Construindo oleodutos com o designer

Os desenvolvedores que preferem uma superfície de design visual podem usar o designer de Machine Learning Azure para criar oleodutos. Pode aceder a esta ferramenta a partir da seleção **do Designer** na página inicial do seu espaço de trabalho.  O designer permite-lhe arrastar e largar passos na superfície do design. Para um desenvolvimento rápido, pode utilizar módulos existentes em todo o espectro de tarefas ML; os módulos existentes cobrem tudo, desde a transformação de dados à seleção de algoritmos até ao treino até à implementação. Ou pode criar um pipeline totalmente personalizado combinando os seus próprios passos definidos nos scripts Python.

Quando se projeta maquetes visualmente, as inputs e saídas de um degrau são exibidas visivelmente. Pode arrastar e largar ligações de dados, permitindo-lhe compreender e modificar rapidamente o fluxo de dados do seu pipeline.
 
![Exemplo de designer de aprendizagem automática azure](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Compreender o gráfico de execução

Os passos dentro de um oleoduto podem ter dependências em outros passos. O serviço de gasoduto SAl Azure faz o trabalho de analisar e orquestrar estas dependências. Os nódosos do "gráfico de execução" resultante são passos de processamento. Cada passo pode envolver a criação ou reutilização de uma combinação particular de hardware e software, reutilização de resultados em cache, e assim por diante. A orquestração e otimização deste gráfico de execução do serviço pode acelerar significativamente uma fase ML e reduzir custos. 

Como os passos são executados de forma independente, os objetos para conter os dados de entrada e de saída que fluem entre passos devem ser definidos externamente. Este é o papel de [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), [PipelineData,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)e classes associadas. Estes objetos de dados estão associados a um objeto [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) que encapsula a sua configuração de armazenamento. A classe base `PipelineStep` é sempre criada com uma corda `name`, uma lista de `inputs`, e uma lista de `outputs`. Normalmente, também tem uma lista de `arguments` e muitas vezes terá uma lista de `resource_inputs`. As subclasses terão geralmente argumentos adicionais também (por exemplo, `PythonScriptStep` requer o nome de ficheiro e o caminho do script para executar). 

O gráfico de execução é acíclico, mas os oleodutos podem ser executados em um horário recorrente e podem executar scripts Python que podem escrever informações do estado para o sistema de ficheiros, tornando possível a criação de perfis complexos. Se conceber o seu pipeline de modo a que determinados passos possam ser executados em paralelo ou assincronicamente, o Azure Machine Learning lida de forma transparente com a análise de dependência e coordenação de fan-out e fan-in. Geralmente não tem que se preocupar com os detalhes do gráfico de execução, mas está disponível através do atributo [Pipeline.graph.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) 


### <a name="a-simple-python-pipeline"></a>Um simples gasoduto Python

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

Para saber mais sobre a ligação do seu pipeline aos seus dados, consulte os artigos [Como Aceder aos Dados](how-to-access-data.md) e Como Registar [Datasets](how-to-create-register-datasets.md). 

## <a name="best-practices-when-using-pipelines"></a>Boas práticas ao utilizar oleodutos

Como pode ver, criar um oleoduto Azure ML é um pouco mais complexo do que iniciar um guião. Os oleodutos requerem que alguns objetos Python sejam configurados e criados. 

Algumas situações que sugerem usar um oleoduto:

* Em ambiente de equipa: divida as tarefas ml em múltiplos passos independentes para que os desenvolvedores possam trabalhar e evoluir os seus programas de forma independente. 

* Quando dentro ou perto da implantação: pregue a configuração e utilize operações programadas e orientadas para o evento para se manter em cima da alteração de dados.

* Nas fases iniciais de um projeto ML ou trabalhando sozinho: utilize gasodutos para automatizar a construção. Se começou a preocupar-se em recriar a configuração e o estado computacional antes de implementar uma nova ideia, é um sinal que pode considerar usar um pipeline para automatizar o fluxo de trabalho. 

É fácil ficar entusiasmado com a reutilização de resultados em cache, o controlo fino sobre os custos da computação e o isolamento do processo, mas os oleodutos têm custos. Alguns anti-padrões incluem:

* Utilizar os gasodutos como único meio para separar as preocupações. As funções, objetos e módulos incorporados da Python vão muito longe para evitar confundir o estado programático! Um passo de oleoduto é muito mais caro do que uma chamada de função.

* Acoplamento pesado entre os degraus do oleoduto. Se refactoring de um passo dependente frequentemente requer modificar as saídas de um passo anterior, é provável que passos separados sejam atualmente mais de um custo do que um benefício. Outra pista de que os passos estão excessivamente acoplados são os argumentos para um passo que não são dados, mas bandeiras para controlar o processamento. 

* Otimizar prematuramente os recursos computacionais. Por exemplo, muitas vezes há várias fases para a preparação de dados e muitas vezes se pode ver "Oh, aqui está um lugar onde eu poderia usar um `MpiStep` para programação paralela, mas aqui é um lugar onde eu poderia usar um `PythonScriptStep` com um alvo de computação menos poderoso", e assim por diante. E talvez, a longo prazo, criar passos finos como este possa ser digno, especialmente se houver a possibilidade de usar resultados em cache em vez de sempre recalcular. Mas os gasodutos não se destinam a substituir o módulo `multiprocessing`. 

Até que um projeto se aproxime ou se aproxime da implantação, os seus oleodutos devem ser mais grossos do que grãos finos. Se você pensa no seu projeto ML como envolvendo _etapas_ e um oleoduto como fornecendo um fluxo de trabalho completo para movê-lo através de uma determinada fase, você está no caminho certo. 

## <a name="key-advantages"></a>Principais vantagens

As principais vantagens da utilização de gasodutos para os seus fluxos de trabalho de aprendizagem automática são:

|Principais vantagens|Descrição|
|:-------:|-----------|
|**Corridas de&nbsp;sem supervisão**|Agende passos para correr em paralelo ou em sequência de forma fiável e sem supervisão. A preparação e modelação de dados podem durar dias ou semanas, e os oleodutos permitem-lhe concentrar-se noutras tarefas enquanto o processo está em curso. |
|**Computação heterogénea**|Utilize vários oleodutos que são coordenados de forma fiável através de recursos e locais de armazenamento de computação heterogénea e escalável. Faça uma utilização eficiente dos recursos computacionais disponíveis executando passos individuais de pipeline em diferentes alvos de cálculo, tais como HDInsight, GPU Data Science VMs e Databricks.|
|**Reutilização**|Crie modelos de gasoduto para cenários específicos, tais como reconversão e pontuação de lotes. Desencadear oleodutos publicados a partir de sistemas externos através de chamadas SIMPLES REST.|
|**Rastreio e versão**|Em vez de rastrear manualmente os dados e os caminhos de resultados à medida que itera, utilize os pipelines SDK para nomear e verizar explicitamente as suas fontes de dados, inputs e saídas. Também pode gerir scripts e dados separadamente para aumentar a produtividade.|
| **Modularidade** | Separar áreas de preocupações e isolar alterações permite que o software evolua a um ritmo mais rápido e com maior qualidade. | 
|**Colaboração**|Os oleodutos permitem que os cientistas de dados colaborem em todas as áreas do processo de design de aprendizagem automática, ao mesmo tempo que são capazes de trabalhar simultaneamente em passos de pipeline.|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>Escolher a subclasse PipelineStep adequada

O `PythonScriptStep` é a subclasse mais flexível do `PipelineStep`abstrato. Outras subclasses, como `EstimatorStep` subclasses e `DataTransferStep` podem realizar tarefas específicas com menos código. Por exemplo, um `EstimatorStep` pode ser criado simplesmente passando um nome para o passo, um `Estimator`e um alvo de cálculo. Ou, pode substituir inputs e saídas, parâmetros de gasoduto e argumentos. Para mais informações, consulte os modelos de comboio com o [Azure Machine Learning utilizando o estimador.](how-to-train-ml-models.md) 

O `DataTransferStep` facilita a movimentação de dados entre fontes de dados e pias. O código para fazer isto manualmente é simples, mas repetitivo. Em vez disso, pode apenas criar um `DataTransferStep` com um nome, referências a uma fonte de dados e um sumidouro de dados, e um alvo de cálculo. O [notebook Azure Machine Learning Pipeline com DataTransferStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb) demonstra esta flexibilidade.

## <a name="modules"></a>Módulos

Embora as etapas do gasoduto permitam a reutilização dos resultados de uma execução anterior, em muitos casos a construção do passo pressupõe que os scripts e ficheiros dependentes necessários devem estar disponíveis localmente. Se um cientista de dados quer construir em cima do código existente, os scripts e dependências muitas vezes devem ser clonados de um repositório separado.

Os módulos são semelhantes na utilização dos passos do gasoduto, mas fornecem versão facilitada através do espaço de trabalho, o que permite a colaboração e reutilização em escala. Os módulos são projetados para serem reutilizados em vários oleodutos e podem evoluir para adaptar uma computação específica a diferentes casos de uso. Os utilizadores podem fazer as seguintes tarefas através do espaço de trabalho, sem utilizar repositórios externos:

* Criar novos módulos e publicar novas versões dos módulos existentes
* Deprecate versões existentes
* Versões de marca desativadas para impedir que os consumidores utilizem esta versão
* Designar versões padrão
* Recupere os módulos por versão do espaço de trabalho, para garantir que as equipas estão a usar o mesmo código

Consulte o [caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) para obter exemplos de código sobre como criar, ligar e utilizar módulos em pipelines de Aprendizagem automática Azure.

## <a name="next-steps"></a>Passos Seguintes

Os oleodutos Azure ML são uma instalação poderosa que começa a fornecer valor nas fases iniciais de desenvolvimento. O valor aumenta à medida que a equipa e o projeto crescem. Este artigo explicou como os oleodutos são especificados com o Azure Machine Learning Python SDK e orquestrados em Azure. Você viu algum código fonte básico e foi introduzido em algumas das aulas de `PipelineStep` que estão disponíveis. Deve ter a sensação de quando usar os oleodutos Azure ML e como o Azure os gere. 


+ Aprenda a [criar o seu primeiro oleoduto.](how-to-create-your-first-pipeline.md)

+ Aprenda a executar previsões de [lotes em grandes dados](tutorial-pipeline-batch-scoring-classification.md ).

+ Consulte os docs de referência SDK para obter passos de núcleo de [gasoduto](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e [de gasoduto](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Experimente exemplo sacanas jupyter mostrando [os oleodutos Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Aprenda a [executar cadernos para explorar este serviço.](samples-notebooks.md)
