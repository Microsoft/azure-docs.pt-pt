---
title: Utilize o AutoML para criar modelos & implementar
titleSuffix: Azure Machine Learning
description: Crie, reveja e implemente modelos automatizados de aprendizagem automática com Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: nibaccam
author: aniththa
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/20/2020
ms.openlocfilehash: 9871d2ef46a4bbcaa0de7a2aee7d2c91f2bfefab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831918"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Criar, rever e implementar modelos automatizados de aprendizagem automática com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, aprende-se a criar, explorar e implementar modelos automatizados de aprendizagem automática de máquinas sem uma única linha de código na interface de estúdio do Azure Machine Learning. O machine learning automatizado é um processo no qual é selecionado para si o melhor algoritmo de aprendizagem automática para os seus dados específicos. Este processo permite-lhe gerar modelos de aprendizagem automática rapidamente. [Saiba mais sobre aprendizagem automática de máquinas.](concept-automated-ml.md)
 
Para um exemplo final, experimente o [tutorial para criar um modelo de classificação com a interface ML automatizada da Azure Machine Learning.](tutorial-first-experiment-automated-ml.md) 

Para uma experiência baseada em código Python, [configuure as suas experiências automatizadas](how-to-configure-auto-train.md) de aprendizagem automática com o Azure Machine Learning SDK.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Um espaço de trabalho de aprendizagem automática Azure com um tipo de **edição Enterprise.** Ver [Criar um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md).  Para atualizar um espaço de trabalho existente para a edição enterprise, consulte [a edição de Upgrade para Enterprise](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Introdução

1. Inscreva-se no Azure Machine Learning em https://ml.azure.com . 

1. Selecione a sua subscrição e espaço de trabalho. 

1. Navegue para o painel esquerdo. Selecione **ML automatizado** na secção **Autor.**

[![Painel de navegação do estúdio Azure Machine Learning](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Se esta for a sua primeira vez a fazer experiências, verá uma lista vazia e liga-se à documentação. 

Caso contrário, verá uma lista das suas recentes experiências automatizadas de aprendizagem automática de máquinas, incluindo as criadas com o SDK. 

## <a name="create-and-run-experiment"></a>Criar e executar experiência

1. Selecione **+ Nova execução de ML automatizada** e povoe o formulário.

1. Selecione um conjunto de dados do seu recipiente de armazenamento ou crie um novo conjunto de dados. Os conjuntos de dados podem ser criados a partir de ficheiros locais, urls web, datastores ou conjuntos de dados abertos Azure. 

    >[!Important]
    > Requisitos para dados de formação:
    >* Os dados devem estar em forma tabular.
    >* O valor que pretende prever (coluna-alvo) deve estar presente nos dados.

    1. Para criar um novo conjunto de dados a partir de um ficheiro no seu computador local, **selecione Procurar** e, em seguida, selecione o ficheiro. 

    1. Dê ao seu conjunto de dados um nome único e forneça uma descrição opcional. 

    1. Selecione **Seguinte** para abrir o **formulário de seleção de datastore e ficheiros**. Neste formulário seleciona para onde carregar o conjunto de dados; o recipiente de armazenamento predefinido que é automaticamente criado com o seu espaço de trabalho, ou escolha um recipiente de armazenamento que deseja usar para a experiência. 

    1. Reveja as Definições e o formulário **de pré-visualização** para obter uma precisão. O formulário é inteligentemente povoado com base no tipo de ficheiro. 

        Campo| Descrição
        ----|----
        Formato do ficheiro| Define o layout e o tipo de dados armazenados num ficheiro.
        Delimitador| Um ou mais caracteres para especificar a fronteira entre regiões separadas e independentes em texto simples ou outros fluxos de dados.
        Encoding| Identifica o bit da tabela de esquemas de caracteres para usar para ler o seu conjunto de dados.
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.
        Saltar filas | Indica quantas, se houver, são ignoradas no conjunto de dados.
    
        Selecione **Seguinte**.

    1. O **formulário Schema** é inteligentemente povoado com base nas seleções nas Definições e na forma **de pré-visualização.** Aqui configurar o tipo de dados para cada coluna, rever os nomes das colunas e selecionar quais as colunas a **não incluir** para a sua experiência. 
            
        Selecione **Seguinte.**

    1. O formulário **de detalhes confirme** é um resumo das informações anteriormente povoadas nas **informações** básicas e **definições e formulários de pré-visualização.** Também tem a opção de criar um perfil de dados para o seu conjunto de dados utilizando um cálculo habilitado para perfis. Saiba mais sobre [perfis de dados.](#profile)

        Selecione **Seguinte**.
1. Selecione o conjunto de dados recém-criado assim que aparecer. Também é possível visualizar uma pré-visualização do conjunto de dados e estatísticas da amostra. 

1. No **formulário de execução Configure,** insira um nome único de experiência.

1. Selecione uma coluna-alvo; esta é a coluna sobre a qual gostaria de fazer previsões.

1. Selecione um compute para o trabalho de perfis de dados e formação. Uma lista dos seus cálculos existentes está disponível no dropdown. Para criar um novo cálculo, siga as instruções no passo 7.

1. Selecione **Crie um novo computamento** para configurar o seu contexto de computação para esta experiência.

    Campo|Descrição
    ---|---
    Nome computacional| Insira um nome único que identifique o seu contexto de computação.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual para o seu cálculo.
    Nós Min / Max (em Definições Avançadas)| Para perfilar os dados, tem de especificar 1 ou mais nós. Introduza o número máximo de nós para o seu cálculo. O padrão é de 6 nós para um AML Compute.
    
    Selecione **Criar**. A criação de um novo cálculo pode demorar alguns minutos.

    >[!NOTE]
    > O seu nome de cálculo indicará se o cálculo que seleciona/criar está *ativado no perfil*. (Consulte o perfil de [dados](#profile) da secção para obter mais detalhes).

    Selecione **Seguinte**.

1. No tipo de Tarefa e no formulário **de definições,** selecione o tipo de tarefa: classificação, regressão ou previsão. Consulte [os tipos de tarefas suportados](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) para obter mais informações.

    1. Para a classificação, também pode ativar uma aprendizagem profunda que é usada para ações de texto.

    1. Para previsão:
        1. Selecione coluna de tempo: Esta coluna contém os dados de tempo a utilizar.

        1. Selecione o horizonte de previsão: Indique quantas unidades de tempo (minutos/horas/dias/semanas/meses/anos) serão capazes de prever para o futuro. Quanto mais o modelo for necessário para prever o futuro, menos preciso se tornará. [Saiba mais sobre previsão e previsão do horizonte.](how-to-auto-train-forecast.md)

1. (Opcional) Ver definições de configuração de adição: definições adicionais que pode utilizar para controlar melhor o trabalho de treino. Caso contrário, os padrão são aplicados com base na seleção de experiências e dados. 

    Configurações adicionais|Descrição
    ------|------
    Métrica primária| Métrica principal usada para marcar o seu modelo. [Saiba mais sobre as métricas dos modelos.](how-to-configure-auto-train.md#explore-model-metrics)
    Caracterização automática| Selecione para ativar ou desativar a caracterização feita através de machine learning automatizado. A caracterização automática inclui limpeza automática de dados, preparação e transformação para gerar características sintéticas. Não suportado para o tipo de tarefa de previsão de séries de tempo. [Saiba mais sobre a exibição.](how-to-configure-auto-features.md#featurization) 
    Explicar o melhor modelo | Selecione para permitir ou desativar para mostrar a explicabilidade do melhor modelo recomendado
    Algoritmo bloqueado| Selecione algoritmos que pretende excluir do trabalho de treino.
    Critério de saída| Quando qualquer um destes critérios é cumprido, o trabalho de formação é interrompido. <br> *Tempo de formação (horas)*: Quanto tempo para permitir que o trabalho de formação corra. <br> *Limiar de pontuação métrica*: Pontuação métrica mínima para todos os oleodutos. Isto garante que se tiver uma métrica de destino definida que deseja alcançar, não gasta mais tempo no trabalho de formação do que o necessário.
    Validação| Selecione uma das opções de validação cruzada para utilizar no trabalho de treino. [Saiba mais sobre validação cruzada.](how-to-configure-cross-validation-data-splits.md#prerequisites)
    Simultaneidade| *Iterações máximas simultâneas*: Número máximo de condutas (iterações) para testar no trabalho de formação. O trabalho não funcionará mais do que o número especificado de iterações.

1. (Opcional) Ver definições de visualização: se optar por ativar a **caracterização automática** no formulário **configurações de configuração adicionais,** aplicam-se as técnicas de apri metragem de atenção predefinidos. Nas **definições** de visualização do View pode alterar estas padrão e personalizar em conformidade. Saiba como [personalizar as ações.](#customize-featurization) 

    ![Formulário de tipo de tarefa de estúdio Azure Machine Learning](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Estatísticas de perfis de dados &

Pode obter uma grande variedade de estatísticas sumárias através do seu conjunto de dados para verificar se o seu conjunto de dados está pronto para ML. Para colunas não numéricas, incluem apenas estatísticas básicas como min, max e contagem de erros. Para colunas numéricas, também pode rever os seus momentos estatísticos e quânticos estimados. Especificamente, o nosso perfil de dados inclui:

>[!NOTE]
> As entradas em branco aparecem para funcionalidades com tipos irrelevantes.

Estatística|Descrição
------|------
Funcionalidade| Nome da coluna que está a ser resumida.
Perfil| Visualização em linha com base no tipo inferido. Por exemplo, cordas, booleanas e datas terão contagens de valor, enquanto decimais (numéricos) têm histogramas aproximados. Isto permite-lhe obter uma compreensão rápida da distribuição dos dados.
Distribuição de tipo| Contagem de valor em linha de tipos dentro de uma coluna. Os nulos são do seu próprio tipo, pelo que esta visualização é útil para detetar valores ímpares ou em falta.
Tipo|Tipo inferido da coluna. Os valores possíveis incluem: cordas, booleans, datas e decimais.
Mín.| Valor mínimo da coluna. As entradas em branco aparecem para funcionalidades cujo tipo não tenha uma encomenda inerente (por exemplo, booleans).
Máx.| Valor máximo da coluna. 
Contagem| Número total de entradas desaparecidas e não desaparecidas na coluna.
Contagem não faltando| Número de entradas na coluna que não faltam. Cordas e erros vazios são tratados como valores, para que não contribuam para a "contagem não em falta".
Rio Quantiles| Valores aproximados em cada quântico para fornecer uma sensação de distribuição dos dados.
Média| Média aritmética ou média da coluna.
Desvio padrão| Medida da quantidade de dispersão ou variação dos dados desta coluna.
Desvio| Medida da distância que os dados desta coluna estão a partir do seu valor médio. 
Skewness| Medida de quão diferentes os dados desta coluna são de uma distribuição normal.
Kurtose| Medida de quão fortemente seguido os dados desta coluna é comparado a uma distribuição normal.

## <a name="customize-featurization"></a>Personalizar a exibição

No formulário **Desativação,** pode ativar/desativar a caracterização automática e personalizar as definições automáticas de exibição para a sua experiência. Para abrir este formulário, consulte o passo 10 na secção [Criar e executar experiências.](#create-and-run-experiment) 

A tabela seguinte resume as personalizações atualmente disponíveis através do estúdio. 

Coluna| Personalização
---|---
Incluídos | Especifica quais as colunas a incluir para o treino.
Tipo de recurso| Altere o tipo de valor para a coluna selecionada.
Impute com| Selecione com que valor imputar valores em falta nos seus dados.

![Formulário de tipo de tarefa de estúdio Azure Machine Learning](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Executar experiência e ver resultados

**Selecione Finish** para executar a sua experiência. O processo de preparação da experimentação pode demorar até 10 minutos. As tarefas de preparação podem demorar mais 2 ou 3 minutos para cada pipeline concluir a execução.

### <a name="view-experiment-details"></a>Ver detalhes da experimentação

O ecrã **'Detalhes'** abre-se para o separador **Detalhes.** Este ecrã mostra-lhe um resumo da experiência, incluindo uma barra de estado na parte superior ao lado do número de execução. 

O separador**Modelos** contém uma lista dos modelos criados encomendados pela pontuação de métrica. Por predefinição, o modelo com a classificação mais alta com base na métrica escolhida está no topo da lista. À medida que a tarefa de preparação experimenta mais modelos, estes são adicionados à lista. Utilize-a para obter uma comparação rápida das métricas dos modelos produzidos até agora.

[![Executar o painel de detalhes](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Ver detalhes da execução de treino

Desacompra qualquer um dos modelos preenchidos para ver detalhes de execução de treino, como métricas de execução no separador detalhes do **Modelo** ou gráficos de desempenho no separador **Visualizações.** Saiba mais [sobre gráficos](how-to-understand-automated-ml.md).

[![Detalhes da iteração](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Implemente o seu modelo

Uma vez que tenha o melhor modelo à mão, é hora de implantá-lo como um serviço web para prever novos dados.

O ML automatizado ajuda-o a implementar o modelo sem código de escrita:

1. Tem algumas opções para a implantação. 

    + Opção 1: Para implementar o melhor modelo (de acordo com os critérios métricos definidos), selecione o botão **de melhor modelo implementar** no separador **Detalhes.**

    + Opção 2: Para implementar uma iteração específica de modelo a partir desta experiência, perfure o modelo para abrir o separador **de detalhes do Modelo** e selecione o modelo de **implementação**.

1. Povoar o **painel de modelos de implantação.**

    Campo| Valor
    ----|----
    Nome| Insira um nome único para a sua implantação.
    Descrição| Introduza uma descrição para identificar melhor para que é esta implantação.
    Tipo de computação| Selecione o tipo de ponto final que pretende implantar: *Serviço Azure Kubernetes (AKS)* ou *Instância de Contentores Azure (ACI)*.
    Nome computacional| *Aplica-se apenas a AKS:* Selecione o nome do cluster AKS para o quais pretende implementar.
    Ativar a autenticação | Selecione para permitir a autenticação baseada em símbolos ou em teclas.
    Utilize ativos de implantação personalizados| Ative esta funcionalidade se quiser carregar o seu próprio script de pontuação e ficheiro ambiente. [Saiba mais sobre a pontuação dos scripts.](how-to-deploy-and-where.md#script)

    >[!Important]
    > Os nomes dos ficheiros devem ter menos de 32 caracteres e devem começar e terminar com alfanuméricos. Pode incluir traços, sublinhados, pontos e alfanuméricos entre. Espaços não são permitidos.

    O menu *Advanced* oferece funcionalidades de implementação predefinidos, tais como [configurações de recolha de dados](how-to-enable-app-insights.md) e utilização de recursos. Se desejar anular estas predefinições, faça-o neste menu.

1. Selecione **Implementar**. A implantação pode demorar cerca de 20 minutos a ser concluída.
    Assim que a implementação começar, o separador **de detalhes** do Modelo aparece. Consulte o progresso da implantação na secção de estado de **implantação** do painel **propriedades.** 

Agora tem um serviço web operacional para gerar previsões! Pode testar as previsões consultando o serviço a partir do suporte de [aprendizagem automática Azure.](how-to-consume-web-service.md#consume-the-service-from-power-bi)

## <a name="next-steps"></a>Próximos passos

* [Saiba como consumir um serviço web.](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)
* [Compreenda os resultados automatizados de aprendizagem automática de máquinas.](how-to-understand-automated-ml.md)
* [Saiba mais sobre machine learning automatizado](concept-automated-ml.md) e aprendizagem automática Azure.
