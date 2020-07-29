---
title: O que são gasodutos de aprendizagem de máquinas Azure
description: Saiba como os oleodutos de machine learning (ML) ajudam a construir, otimizar e gerir fluxos de trabalho de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.custom: tracking-python
ms.openlocfilehash: 7d34b54349a681061f80798a19ce65d2266f589d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84559475"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>O que são os oleodutos Azure Machine Learning?

Neste artigo, você pode aprender como os oleodutos Azure Machine Learning ajudam a construir, otimizar e gerir fluxos de trabalho de aprendizagem automática. Estes fluxos de trabalho têm uma série de benefícios: 

+ Simplicidade
+ Velocidade
+ Repetibilidade
+ Flexibilidade
+ Veragem e rastreio
+ Modularidade 
+ Garantia de qualidade
+ Controlo de custos

Estes benefícios tornam-se significativos assim que o seu projeto de aprendizagem automática se move para além da pura exploração e para a iteração. Mesmo os simples oleodutos de um passo podem ser valiosos. Os projetos de aprendizagem automática estão muitas vezes num estado complexo, e pode ser um alívio fazer da realização precisa de um único fluxo de trabalho um processo trivial.

Saiba como [criar o seu primeiro oleoduto.](how-to-create-your-first-pipeline.md)

![Oleodutos de aprendizagem automática em Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Que tecnologia de gasoduto Azure devo usar?

A nuvem Azure fornece vários outros oleodutos, cada um com um propósito diferente. A tabela que se segue enumera os diferentes oleodutos e para que são utilizados:

| Scenario | Persona primária | Oferta azul | Oferta de OSS | Tubo canónico | Pontos fortes | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Orquestração modelo (Machine learning) | Cientista de dados | Gasodutos de aprendizagem de máquinas Azure | Gasodutos Kubeflow | Modelo data -> | Distribuição, caching, código-primeiro, reutilização | 
| Orquestração de dados (preparação de dados) | Engenheiro de dados | [Pipelines do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Fluxo de ar Apache | Dados -> Dados | Movimento fortemente dactilografado. Atividades centradas em dados. |
| Código & orquestração de aplicações (CI/CD) | Desenvolvedor de Aplicativos / Ops | [Gasodutos Azure DevOps](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Código + Modelo -> App/Serviço | Suporte de atividade mais aberto e flexível, filas de aprovação, fases com gating | 

## <a name="what-can-azure-ml-pipelines-do"></a>O que podem os oleodutos Azure ML fazer?

Um pipeline Azure Machine Learning é um fluxo de trabalho executável independentemente de uma tarefa completa de aprendizagem automática. As subtarefas são encapsuladas como uma série de passos dentro do pipeline. Um oleoduto Azure Machine Learning pode ser tão simples como aquele que chama um roteiro python, por isso _pode_ fazer qualquer coisa. Os gasodutos _devem_ concentrar-se em tarefas de aprendizagem automática, tais como:

+ Treino de dados, incluindo importação, validação e limpeza, manipulação e transformação, normalização e teste
+ Configuração de preparações, incluindo a parametrização de argumentos, caminhos de ficheiros e registo/comunicação de configurações
+ Treinar e validar de forma eficiente e repetida. A eficiência pode vir da especificação de subconjuntos de dados específicos, diferentes recursos de computação de hardware, processamento distribuído e monitorização do progresso
+ Implementação, incluindo a gestão de versões, o dimensionamento, o aprovisionamento e o controlo de acesso 

As etapas independentes permitem que vários cientistas de dados trabalhem no mesmo oleoduto ao mesmo tempo sem sobrecarregar os recursos computacional. Passos separados também facilitam a utilização de diferentes tipos/tamanhos computacional para cada passo.

Depois de o gasoduto ter sido concebido, há muitas vezes uma afinação mais fina em torno do laço de treino do gasoduto. Quando se volta a executar um oleoduto, a corrida salta para os passos que precisam de ser reexecutado, como um script de treino atualizado. Os passos que não precisam de ser re-re-serem reexecutados são ignorados. A mesma análise aplica-se a scripts inalterados utilizados para a realização do passo. Esta funcionalidade de reutilização ajuda a evitar o funcionamento de passos dispendiosos e intensivos de tempo, como a ingestão de dados e a transformação se os dados subjacentes não mudarem.

Com a Azure Machine Learning, pode utilizar vários kits de ferramentas e estruturas, como PyTorch ou TensorFlow, para cada passo do seu pipeline. O Azure coordena os vários [alvos de computação](concept-azure-machine-learning-architecture.md) que utiliza, para que os seus dados intermédios possam ser partilhados com os alvos de computação a jusante.

Pode [rastrear as métricas das suas experiências](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) de pipeline diretamente no portal Azure ou na [sua página de aterragem no espaço de trabalho (pré-visualização)](https://ml.azure.com). Depois de ter sido publicado um gasoduto, pode configurar um ponto final REST, que lhe permite refazer o gasoduto a partir de qualquer plataforma ou pilha.

Em suma, todas as tarefas complexas do ciclo de vida de aprendizagem automática podem ser ajudadas com os gasodutos. Outras tecnologias de gasodutos Azure têm os seus próprios pontos fortes. [Os oleodutos Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) primam pelo trabalho com dados e a [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) é a ferramenta certa para integração e implantação contínua. Mas se o seu foco é a aprendizagem automática, os oleodutos Azure Machine Learning são provavelmente a melhor escolha para as suas necessidades de fluxo de trabalho. 

## <a name="what-are-azure-ml-pipelines"></a>O que são os oleodutos Azure ML?

Um gasoduto Azure ML executa um fluxo de trabalho lógico completo com uma sequência ordenada de passos. Cada passo é uma ação de processamento discreta. Os gasodutos funcionam no contexto de uma experiência de aprendizagem automática [Azure.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)

Nas fases iniciais de um projeto ML, é bom ter um único caderno Jupyter ou script Python que faz todo o trabalho do espaço de trabalho e configuração de recursos do Azure, preparação de dados, configuração de execução, treino e validação. Mas assim como as funções e as classes rapidamente se tornam preferíveis a um único bloco imperativo de código, os fluxos de trabalho ml rapidamente tornam-se preferíveis a um caderno ou script monolítico. 

Ao modular as tarefas de ML, os oleodutos suportam o imperativo da Ciência da Computação de que um componente deve "fazer (apenas) uma coisa bem". A modularidade é claramente vital para projetar o sucesso na programação em equipas, mas mesmo quando se trabalha sozinho, mesmo um pequeno projeto ML envolve tarefas separadas, cada uma com uma boa quantidade de complexidade. As tarefas incluem: configuração do espaço de trabalho e acesso a dados, preparação de dados, definição e configuração do modelo e implementação. Enquanto as saídas de uma ou mais tarefas formam as entradas para outra, os detalhes exatos de implementação de qualquer tarefa são, na melhor das hipóteses, distrações irrelevantes na próxima. Na pior das hipóteses, o estado computacional de uma tarefa pode causar um bug noutra. 

### <a name="analyzing-dependencies"></a>Analisar dependências

Muitos ecossistemas de programação têm ferramentas que orquestram dependências de recursos, bibliotecas ou compilações. Geralmente, estas ferramentas usam os tempos de ficheiro para calcular as dependências. Quando um ficheiro é alterado, apenas ele e os seus dependentes são atualizados (descarregados, recompiliados ou embalados). Os oleodutos Azure ML estendem este conceito dramaticamente. Tal como as ferramentas de construção tradicionais, os gasodutos calculam as dependências entre os passos e apenas realizam os recálculos necessários. 

A análise de dependência nos oleodutos Azure ML é mais sofisticada do que os simples timetamps. Cada passo pode ser executado num ambiente diferente de hardware e software. A preparação de dados pode ser um processo demorado, mas não precisa de funcionar em hardware com GPUs poderosos, certos passos podem requerer software específico para o OS, você pode querer usar treino distribuído, e assim por diante. Embora a poupança de custos para otimizar recursos possa ser significativa, pode ser esmagador para fazer malabarismos manualmente com todas as diferentes variações nos recursos de hardware e software. É ainda mais difícil fazer tudo isso sem nunca cometer um erro nos dados que transfere entre passos. 

Os oleodutos resolvem este problema. A Azure Machine Learning orquestra automaticamente todas as dependências entre os passos do gasoduto. Esta orquestração pode incluir girar para cima e para baixo imagens de Docker, anexar e desvincular recursos compute, e mover dados entre os passos de forma consistente e automática.

### <a name="reusing-results"></a>Reutilização de resultados

Além disso, a saída de um passo pode, se quiser, ser reutilizada. Se especificar a reutilização como uma possibilidade e não houver dependências a montante que desencadeiem o recálculo, o serviço de gasoduto utilizará uma versão em cache dos resultados do passo. Tal reutilização pode reduzir drasticamente o tempo de desenvolvimento. Se tiver uma tarefa complexa de preparação de dados, provavelmente reencamúndia-a com mais frequência do que é estritamente necessário. Os oleodutos aliviam-no dessa preocupação: se necessário, o passo vai correr, se não, não vai.

Toda esta análise de dependência, orquestração e ativação são tratadas pela Azure Machine Learning quando instantaneamente um objeto [pipeline,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) passá-lo para um `Experiment` , e chamar `submit()` . 

### <a name="coordinating-the-steps-involved"></a>Coordenação dos passos envolvidos

Quando cria e executa um `Pipeline` objeto, ocorrem os seguintes passos de alto nível:

+ Para cada etapa, o serviço calcula os requisitos para:
    + Recursos de computação de hardware
    + Recursos oss (imagem estiva))
    + Recursos de software (Dependências Conda / virtualenv)
    + Entradas de dados 
+ O serviço determina as dependências entre etapas, resultando num gráfico de execução dinâmico
+ Quando cada nó no gráfico de execução corre:
    + O serviço configura o ambiente de hardware e software necessário (talvez reutilizando os recursos existentes)
    + O passo corre, fornecendo informações de registo e monitorização ao seu objeto contendo `Experiment`
    + Quando o passo termina, as suas saídas são preparadas como entradas para o próximo passo e/ou escritas para armazenamento
    + Os recursos que já não são necessários são finalizados e destacados

![Etapas de gasoduto](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Oleodutos de construção com o Python SDK

No [Azure Machine Learning Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)um oleoduto é um objeto Python definido no `azureml.pipeline.core` módulo. Um objeto [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) contém uma sequência ordenada de um ou mais objetos [PipelineStep.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) A `PipelineStep` classe é abstrata e os passos reais serão de subclasses como [EstimatorStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py) [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)ou [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). A classe [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) contém uma sequência reutilizável de passos que podem ser partilhados entre os oleodutos. Uma `Pipeline` corrida como parte de um `Experiment` .

Um oleoduto Azure ML está associado a um espaço de trabalho Azure Machine Learning e um passo de pipeline está associado a um alvo de computação disponível dentro desse espaço de trabalho. Para obter mais informações, consulte [Criar e gerir espaços de trabalho de Aprendizagem automática Azure no portal Azure](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) ou Quais são os [alvos de computação em Azure Machine Learning?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)

Em Azure Machine Learning, um alvo de computação é o ambiente em que ocorre uma fase ML. O ambiente de software pode ser um VM remoto, Azure Machine Learning Compute, Azure Databricks, Azure Batch, e assim por diante. O ambiente de hardware também pode variar muito, dependendo do suporte da GPU, memória, armazenamento, etc. Pode especificar o alvo do cálculo para cada passo, o que lhe dá um controlo fino sobre os custos. Você pode usar recursos mais ou menos poderosos para a ação específica, volume de dados e necessidades de desempenho do seu projeto. 

## <a name="building-pipelines-with-the-designer"></a>Construindo oleodutos com o designer

Os desenvolvedores que preferem uma superfície de design visual podem usar o designer de Machine Learning Azure para criar oleodutos. Pode aceder a esta ferramenta a partir da seleção **designer** na página inicial do seu espaço de trabalho.  O designer permite arrastar e largar passos sobre a superfície de design. Para um rápido desenvolvimento, pode utilizar módulos existentes em todo o espectro de tarefas ML; os módulos existentes cobrem tudo, desde a transformação de dados até à seleção de algoritmos até à formação até à implantação. Ou pode criar um oleoduto totalmente personalizado combinando os seus próprios passos definidos em scripts Python.

Ao conceber os oleodutos visualmente, as entradas e saídas de um degrau são visualizadas visivelmente. Pode arrastar e largar ligações de dados, permitindo-lhe compreender e modificar rapidamente o fluxo de dados do seu pipeline.
 
![Exemplo de designer de aprendizagem de máquinas Azure](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Compreender o gráfico de execução

Os passos dentro de um oleoduto podem ter dependências de outros passos. O serviço de gasodutoS Azure ML faz o trabalho de analisar e orquestrar estas dependências. Os nós no "gráfico de execução" resultante são passos de processamento. Cada passo pode envolver a criação ou reutilização de uma combinação particular de hardware e software, reutilização de resultados em cache, e assim por diante. A orquestração e otimização do serviço deste gráfico de execução pode acelerar significativamente uma fase ML e reduzir custos. 

Como os passos são executados de forma independente, os objetos para segurar os dados de entrada e saída que fluem entre passos devem ser definidos externamente. Esta é a função de [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)e [PipelineData,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)objetos. Estes objetos de dados estão associados a um objeto [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) que encapsula a sua configuração de armazenamento. A `PipelineStep` classe base é sempre criada com uma `name` corda, uma lista `inputs` de, e uma lista de `outputs` . Normalmente, também tem uma lista de `arguments` e muitas vezes terá uma lista de `resource_inputs` . As subclasses geralmente também terão argumentos adicionais (por exemplo, `PythonScriptStep` requer o nome de arquivo e o caminho do script para executar). 

O gráfico de execução é aciclico, mas os oleodutos podem ser executados num horário recorrente e podem executar scripts Python que podem escrever informações estatais para o sistema de ficheiros, tornando possível criar perfis complexos. Se conceber o seu oleoduto para que certos passos possam ser executados paralelamente ou assíncronamente, a Azure Machine Learning lida de forma transparente com a análise e coordenação da dependência e coordenação do fan-out e do fan-in. Geralmente não tem que se preocupar com os detalhes do gráfico de execução, mas está disponível através do atributo [Pipeline.graph.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) 


### <a name="a-simple-python-pipeline"></a>Um simples pipeline Python

Este corte mostra os objetos e chamadas necessárias para criar e executar um básico `Pipeline` :

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

output_data = PipelineData("output_data", datastore=blob_store)

input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

O snippet começa com objetos comuns de Aprendizagem automática Azure, `Workspace` um `Datastore` , um [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py), e um `Experiment` . Em seguida, o código cria os objetos para segurar `input_data` e `output_data` . A matriz `steps` contém um único elemento, um que `PythonScriptStep` utilizará os objetos de dados e funcionará no `compute_target` . Em seguida, o código instantaneamente o `Pipeline` objeto em si, passando no espaço de trabalho e passos matriz. A chamada para `experiment.submit(pipeline)` iniciar o gasoduto Azure ML. A chamada para `wait_for_completion()` bloquear até que o oleoduto esteja terminado. 

Para saber mais sobre a ligação do seu oleoduto aos seus dados, consulte os artigos [Acesso de dados em Azure Machine Learning](concept-data.md) e Moving datas dentro e entre etapas de gasoduto [ML (Python)](how-to-move-data-in-out-of-pipelines.md). 

## <a name="best-practices-when-using-pipelines"></a>Melhores práticas ao utilizar gasodutos

Como pode ver, criar um oleoduto Azure ML é um pouco mais complexo do que iniciar um script. Os oleodutos requerem que alguns objetos Python sejam configurados e criados. 

Algumas situações que sugerem a utilização de um oleoduto:

* Num ambiente de equipa: divida as tarefas ML em múltiplos passos independentes para que os desenvolvedores possam trabalhar e evoluir os seus programas de forma independente. 

* Quando estiver dentro ou perto da implementação: pregue a configuração e use operações programadas e orientadas para o evento para se manter além da mudança de dados.

* Nas fases iniciais de um projeto ML ou trabalhando sozinho: utilize gasodutos para automatizar a construção. Se começou a preocupar-se em recriar a configuração e o estado computacional antes de implementar uma nova ideia, é um sinal que poderá considerar usar um pipeline para automatizar o fluxo de trabalho. 

É fácil ficar entusiasmado com a reutilização de resultados em cache, o controlo fino sobre os custos de computação e o isolamento do processo, mas os gasodutos têm custos. Alguns anti-padrões incluem:

* Utilizar os gasodutos como único meio para separar as preocupações. As funções, objetos e módulos incorporados da Python vão muito longe para evitar confundir o estado programático! Um passo de oleoduto é muito mais caro do que uma chamada de função.

* Acoplamento pesado entre os degraus do gasoduto. Se refactorar um passo dependente frequentemente requer modificar as saídas de um passo anterior, é provável que passos separados sejam atualmente mais um custo do que um benefício. Outra pista de que os passos são excessivamente acopdos são argumentos para um passo que não são dados, mas bandeiras para controlar o processamento. 

* Otimizando prematuramente os recursos computacional. Por exemplo, há muitas vezes várias fases para a preparação de dados e muitas vezes se pode ver "Oh, aqui está um lugar onde eu poderia usar um `MpiStep` para programação paralela, mas aqui é um lugar onde eu poderia usar um `PythonScriptStep` com um alvo de computação menos poderoso", e assim por diante. E talvez, a longo prazo, criar passos de grão fino como este possa ser útil, especialmente se houver a possibilidade de usar resultados em cache em vez de sempre recalcular. Mas os oleodutos não se destinam a substituir o módulo nativo de `multiprocessing` Python. 

Até que um projeto se aproxime de uma grande ou próxima implantação, os seus oleodutos devem ser mais grossos do que finos. Se pensares no teu projeto ML como envolvendo _etapas_ e um oleoduto como um fluxo de trabalho completo para te mover por uma determinada fase, estás no caminho certo. 

## <a name="key-advantages"></a>Principais vantagens

As principais vantagens da utilização de oleodutos para os seus fluxos de trabalho de aprendizagem automática são:

|Vantagem chave|Descrição|
|:-------:|-----------|
|**Corridas sem &nbsp; supervisão**|Agende os passos para ser executado em paralelo ou em sequência de forma fiável e sem supervisão. A preparação e modelação de dados podem durar dias ou semanas, e os oleodutos permitem-lhe focar-se noutras tarefas enquanto o processo está em curso. |
|**Cálculo heterogéneo**|Utilize vários oleodutos que são coordenados de forma fiável através de recursos de computação heterogéneos e escaláveis e locais de armazenamento. Faça uma utilização eficiente dos recursos de computação disponíveis executando etapas individuais de gasodutos em diferentes alvos de computação, tais como HDInsight, GPU Data Science VMs e Databricks.|
|**Reutilização**|Crie modelos de pipeline para cenários específicos, tais como reconversão e pontuação de lotes. Desencadeie os oleodutos publicados a partir de sistemas externos através de chamadas REST simples.|
|**Rastreio e versão**|Em vez de rastrear manualmente dados e trajetórias de resultados à medida que iterar, use os pipelines SDK para nomear e vergrafar explicitamente as suas fontes de dados, entradas e saídas. Também é possível gerir scripts e dados separadamente para aumentar a produtividade.|
| **Modularidade** | Separar áreas de preocupações e isolar alterações permite que o software evolua a um ritmo mais rápido com maior qualidade. | 
|**Colaboração**|Os oleodutos permitem que os cientistas de dados colaborem em todas as áreas do processo de design de machine learning, ao mesmo tempo que podem trabalhar simultaneamente em etapas de gasoduto.|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>Escolher a subclasse PipelineStep adequada

A `PythonScriptStep` subclasse mais flexível do `PipelineStep` resumo. Outras subclasses, como `EstimatorStep` subclasses e `DataTransferStep` podem realizar tarefas específicas com menos código. Por exemplo, um `EstimatorStep` pode ser criado apenas passando um nome para o passo, um `Estimator` , e um alvo de computação. Ou, pode sobrepor entradas e saídas, parâmetros de pipeline e argumentos. Para obter mais informações, consulte [os modelos Train com Azure Machine Learning utilizando o estimador](how-to-train-ml-models.md). 

`DataTransferStep`Torna-se fácil mover dados entre fontes de dados e pias. O código para fazer esta transferência manualmente é simples, mas repetitivo. Em vez disso, pode apenas criar um `DataTransferStep` com um nome, referências a uma fonte de dados e um sumidouro de dados, e um alvo de computação. O pipeline [de aprendizagem de máquinas Azure com DataTransferStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb) demonstra esta flexibilidade.

## <a name="modules"></a>Módulos

Embora os passos de gasoduto permitam a reutilização dos resultados de uma execução anterior, em muitos casos a construção do passo pressupõe que os scripts e ficheiros dependentes necessários devem estar disponíveis localmente. Se um cientista de dados quer construir em cima do código existente, os scripts e dependências muitas vezes devem ser clonados de um repositório separado.

Os módulos são semelhantes no uso dos passos de pipeline, mas fornecem versões facilitadas através do espaço de trabalho, o que permite a colaboração e reutilização em escala. Os módulos são projetados para serem reutilizados em vários oleodutos e podem evoluir para adaptar uma computação específica a diferentes casos de utilização. Os utilizadores podem fazer as seguintes tarefas através do espaço de trabalho, sem utilizar repositórios externos:

* Crie novos módulos e publique novas versões de módulos existentes
* Depreciar as versões existentes
* Versões de marca desativadas para impedir que os consumidores utilizem esta versão
* Designar versões predefinições
* Recuperar módulos por versão do espaço de trabalho, para garantir que as equipas estão a usar o mesmo código

Consulte o [caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) para obter exemplos de código sobre como criar, ligar e utilizar módulos em pipelines Azure Machine Learning.

## <a name="next-steps"></a>Próximos passos

Os oleodutos Azure ML são uma instalação poderosa que começa a fornecer valor nas fases iniciais de desenvolvimento. O valor aumenta à medida que a equipa e o projeto crescem. Este artigo explicou como os oleodutos são especificados com o Azure Machine Learning Python SDK e orquestrados em Azure. Viu um código-fonte básico e foi introduzido em algumas das `PipelineStep` aulas que estão disponíveis. Deve ter uma noção de quando usar os oleodutos Azure ML e como o Azure os gere. 


+ Saiba como [criar o seu primeiro oleoduto.](how-to-create-your-first-pipeline.md)

+ Saiba como [executar previsões de lotes em dados grandes.](tutorial-pipeline-batch-scoring-classification.md )

+ Consulte os documentos de referência SDK para os [passos do núcleo do gasoduto](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e do [gasoduto](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Experimente o exemplo de cadernos Jupyter que mostram [os oleodutos Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Saiba como [executar cadernos para explorar este serviço.](samples-notebooks.md)
