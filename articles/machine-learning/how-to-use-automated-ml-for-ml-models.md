---
title: Utilizar autoML para criar modelos e implementar
titleSuffix: Azure Machine Learning
description: Criar, rever e implementar modelos automatizados de aprendizagem automática com o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 9999d74bf6bef3e8351460add7efc8bdbfcd1045
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270033"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Criar, rever e implementar modelos automatizados de aprendizagem automática com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, aprende-se a criar, explorar e implementar modelos automatizados de aprendizagem automática sem uma única linha de código na interface de estúdio do Azure Machine Learning. Machine learning automatizado é um processo no qual o melhor algoritmo de aprendizagem automática a utilizar para os seus dados específicos é selecionado para si. Este processo permite-lhe gerar modelos de aprendizagem automática rapidamente. [Saiba mais sobre aprendizagem automática de máquinas.](concept-automated-ml.md)
 
Para um exemplo final, experimente o tutorial para criar um modelo de classificação com a [interface ML automatizada do Azure Machine Learning.](tutorial-first-experiment-automated-ml.md) 

Para uma experiência baseada em código Python, [configure as suas experiências automatizadas](how-to-configure-auto-train.md) de aprendizagem automática com o Azure Machine Learning SDK.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Um espaço de trabalho azure machine learning com um tipo de **edição Enterprise.** Consulte Criar um espaço de [trabalho de aprendizagem automática Azure](how-to-manage-workspace.md).  Para atualizar um espaço de trabalho existente para a edição da Enterprise, consulte [upgrade para edição enterprise.](how-to-manage-workspace.md#upgrade)

## <a name="get-started"></a>Introdução

1. Inscreva-se na Azure Machine Learning em https://ml.azure.com. 

1. Selecione a sua subscrição e espaço de trabalho. 

1. Navegue para o painel esquerdo. **Selecione ML automatizado** na secção **Autor.**

[painel de navegação do estúdio de machine learning ![Azure](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Se for a sua primeira vez a fazer experiências, verá uma lista vazia e ligações à documentação. 

Caso contrário, verá uma lista das suas recentes experiências automatizadas de aprendizagem automática, incluindo as criadas com o SDK. 

## <a name="create-and-run-experiment"></a>Criar e executar experiência

1. Selecione **+ Nova execução automática ml** e povoe o formulário.

1. Selecione um conjunto de dados do seu recipiente de armazenamento ou crie um novo conjunto de dados. Os conjuntos de dados podem ser criados a partir de ficheiros locais, urls web, datastores ou conjuntos de dados abertos Azure. 

    >[!Important]
    > Requisitos para os dados de formação:
    >* Os dados devem estar na forma tabular.
    >* O valor que pretende prever (coluna de destino) deve estar presente nos dados.

    1. Para criar um novo conjunto de dados a partir de um ficheiro no seu computador local, **selecione 'Navegar'** e, em seguida, selecione o ficheiro. 

    1. Dê ao seu conjunto de dados um nome único e forneça uma descrição opcional. 

    1. Selecione **Next** para abrir o formulário de seleção de **Datastore e ficheiros**. Neste formulário, selecione onde carregar o seu conjunto de dados; o recipiente de armazenamento predefinido que é criado automaticamente com o seu espaço de trabalho, ou escolha um recipiente de armazenamento que pretende utilizar para a experiência. 

    1. Reveja o formulário **Definições e pré-visualização** para obter precisão. O formulário é inteligentemente povoado com base no tipo de ficheiro. 

        Campo| Descrição
        ----|----
        Formato de ficheiro| Define o layout e o tipo de dados armazenados num ficheiro.
        Delimitador| Um ou mais caracteres para especificar a fronteira entre regiões separadas e independentes em texto simples ou outros fluxos de dados.
        Codificação| Identifica o pouco que a tabela de esquemas de caracteres usar para ler o seu conjunto de dados.
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.
        Linhas de salto | Indica quantas, se houver, são ignoradas linhas no conjunto de dados.
    
        Selecione **Seguinte**.

    1. O formulário **Schema** é inteligentemente povoado com base nas seleções na **forma Definições e pré-visualização.** Aqui configure o tipo de dados para cada coluna, reveja os nomes das colunas e selecione quais as colunas a **não incluir** para a sua experiência. 
            
        Selecione **Seguinte.**

    1. O formulário **de detalhes confirmar** é um resumo das informações previamente povoadas nos **formulários de informação** e definições básicas **e pré-visualização.** Também tem a opção de criar um perfil de dados para o seu conjunto de dados utilizando um cálculo ativado por perfis. Saiba mais sobre [o perfil de dados.](#profile)

        Selecione **Seguinte**.
1. Selecione o seu conjunto de dados recém-criado assim que aparecer. Também é capaz de visualizar uma pré-visualização do conjunto de dados e estatísticas da amostra. 

1. No formulário **de execução Configure,** introduza um nome único de experiência.

1. Selecione uma coluna de destino; esta é a coluna em que gostaria de fazer previsões.

1. Selecione um cálculo para o trabalho de perfis de dados e formação. Uma lista dos seus cálculos existentes está disponível no dropdown. Para criar um novo cálculo, siga as instruções no passo 7.

1. Selecione **Criar um novo cálculo** para configurar o contexto da sua computação para esta experiência.

    Campo|Descrição
    ---|---
    Nome computacional| Introduza um nome único que identifique o seu contexto de computação.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual para a sua computação.
    Nós min / Max (em Definições Avançadas)| Para perfilar os dados, deve especificar 1 ou mais nós. Insira o número máximo de nós para a sua computação. O padrão é de 6 nódosos para uma AmL Compute.
    
    Selecione **Criar**. A criação de um novo cálculo pode demorar alguns minutos.

    >[!NOTE]
    > O seu nome de cálculo indicará se o cálculo que seleciona/cria está *ativado*por perfis . (Consulte o [perfil de dados](#profile) da secção para obter mais detalhes).

    Selecione **Seguinte**.

1. No formulário de **tipo de tarefa e configurações,** selecione o tipo de tarefa: classificação, regressão ou previsão. Veja [como definir tipos de tarefas](how-to-define-task-type.md) para mais informações.

    1. Para classificação, também pode ativar uma aprendizagem profunda que é usada para recursos de texto.

    1. Para previsão:
        1. Selecione a coluna de tempo: Esta coluna contém os dados do tempo a utilizar.

        1. Selecione horizonte de previsão: Indicar quantas unidades de tempo (minutos/horas/dias/semanas/meses/anos) o modelo será capaz de prever para o futuro. Quanto mais longe o modelo for necessário para prever no futuro, menos preciso se tornará. [Saiba mais sobre o horizonte](how-to-auto-train-forecast.md)de previsão e previsão.

1. (Opcional) Ver definições de configuração de adição: configurações adicionais que pode utilizar para controlar melhor o trabalho de formação. Caso contrário, as predefinições são aplicadas com base na seleção de experiências e dados. 

    Configurações adicionais|Descrição
    ------|------
    Métrica primária| Métrica principal usada para marcar o seu modelo. [Saiba mais sobre métricas](how-to-configure-auto-train.md#explore-model-metrics)de modelo.
    Caracterização automática| Selecione para ativar ou desativar o pré-processamento feito por aprendizagem automática de máquinas. O pré-processamento inclui limpeza automática de dados, preparação e transformação para gerar características sintéticas. Não suportado para o tipo de tarefa de previsão da série temporal. [Saiba mais sobre o pré-processamento.](#featurization) 
    Explicar o melhor modelo | Selecione para ativar ou desativar para mostrar explicabilidade do melhor modelo recomendado
    Algoritmo bloqueado| Selecione algoritmos que pretende excluir do trabalho de formação.
    Critério de saída| Quando qualquer um destes critérios é cumprido, o trabalho de formação é interrompido. <br> Tempo de trabalho de *formação (horas)* : Quanto tempo permite que o trabalho de formação decorra. <br> *Limiar de pontuação métrica*: Pontuação mínima métrica para todos os gasodutos. Isto garante que se tiver uma métrica de destino definida que pretende alcançar, não passa mais tempo no trabalho de formação do que o necessário.
    Validação| Selecione uma das opções de validação cruzada para usar no trabalho de formação. [Saiba mais sobre validação cruzada.](how-to-configure-auto-train.md)
    Simultaneidade| *Iterações simultâneas*: Número máximo de gasodutos (iterações) para testar no trabalho de formação. O trabalho não funcionará mais do que o número especificado de iterações.

1. (Opcional) Ver definições de visualização: se optar por ativar a **funcionalidade automática** no formulário de **configuração adicional,** este formulário é onde especifica quais as colunas para executar essas funcionalidades e selecione qual o valor estatístico a utilizar para imputações de valor em falta.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Estatísticas de perfis e resumos de dados

Pode obter uma grande variedade de estatísticas sumárias em todo o seu conjunto de dados para verificar se o seu conjunto de dados está pronto para ML. Para colunas não numéricas, incluem apenas estatísticas básicas como min, máx e contagem de erros. Para colunas numéricas, também pode rever os seus momentos estatísticos e quantificados estimados. Especificamente, o nosso perfil de dados inclui:

>[!NOTE]
> As entradas em branco aparecem para funcionalidades com tipos irrelevantes.

Estatística|Descrição
------|------
Funcionalidade| Nome da coluna que está a ser resumida.
Perfil| Visualização em linha com base no tipo inferido. Por exemplo, cordas, booleans e datas terão contagens de valor, enquanto os decimais (numéricos) têm histogramas aproximados. Isto permite-lhe obter uma compreensão rápida da distribuição dos dados.
Distribuição de tipos| Contagem de valor em linha de tipos dentro de uma coluna. Os nulos são o seu próprio tipo, por isso esta visualização é útil para detetar valores ímpares ou em falta.
Tipo|Tipo inferido da coluna. Os valores possíveis incluem: cordas, booleans, datas e decimais.
Mín.| Valor mínimo da coluna. As entradas em branco aparecem para funcionalidades cujo tipo não tem uma encomenda inerente (por exemplo, booleans).
Máx.| Valor máximo da coluna. 
Contagem| Número total de entradas desaparecidas e não em falta na coluna.
Não falta ndo contagem| Número de entradas na coluna que não faltam. Cordas vazias e erros são tratados como valores, pelo que não contribuirão para a "contagem não em falta".
Quantiles| Valores aproximados em cada quantile para fornecer uma noção da distribuição dos dados.
média| Média aritmética ou média da coluna.
Desvio padrão| Medida da quantidade de dispersão ou variação dos dados desta coluna.
Variância| A medida de quão longe os dados desta coluna são do seu valor médio. 
Assimetrias| Medida de quão diferentes são os dados desta coluna de uma distribuição normal.
Kurtosis| A medida de quão fortemente seguidos os dados desta coluna é comparada a uma distribuição normal.


<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Opções avançadas de caracterização

O machine learning automatizado oferece pré-processamento e guarda-costas de dados automaticamente, para ajudá-lo a identificar e gerir potenciais problemas com os seus dados. 

### <a name="preprocessing"></a>Pré-processamento

|Passos de&nbsp;de pré-processamento| Descrição |
| ------------- | ------------- |
|Remova cardinalidade elevada ou nenhum recurso de variância|Retire-as de conjuntos de formação e validação, incluindo características com todos os valores em falta, o mesmo valor em todas as linhas ou com cardeal extremamente elevado (por exemplo, hashes, IDs ou GUIDs).|
|Impute valores em falta|Para características numéricas, impute com valores médios na coluna.<br/><br/>Para características categóricas, impute com valor mais frequente.|
|Gerar recursos adicionais|Para as funcionalidades de DateTime: ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.<br/><br/>Para as características do texto: Frequência de termo baseada em unigramas, bi-gramas e tri-character-gramas.|
|Transformar e codificar |Características numéricas com poucos valores únicos são transformadas em características categóricas.<br/><br/>A codificação one-hot é realizada para a baixa cardeal categórica; para alta cardinalidade, uma codificação de hash-hot-hash.|
|Incorporações de palavras|O featurizer de texto que converte vetores de tokens de texto em vetores de frase usando um modelo pré-treinado. O vetor de incorporação de cada palavra num documento é agregado em conjunto para produzir um vetor de características documentais.|
|Codificação de alvos|Para características categóricas, mapeie cada categoria com valor-alvo médio para problemas de regressão, e para a probabilidade de classe para cada classe para problemas de classificação. A ponderação baseada na frequência e a validação cruzada k-fold são aplicadas para reduzir a adaptação do mapeamento e do ruído causados por categorias de dados escassas.|
|Codificação do alvo de texto|Para a entrada de texto, um modelo linear empilhado com saco de palavras é usado para gerar a probabilidade de cada classe.|
|Peso da evidência (AI)|Calcula o AE como uma medida de correlação de colunas categóricas com a coluna alvo. É calculado como o registo da relação entre probabilidades in-class vs fora de classe. Este passo produz uma coluna de características numéricas por classe e remove a necessidade de imputar explicitamente valores em falta e tratamento mais distante.|
|Distância de aglomerado|Treina um modelo de agrupamento k-significa em todas as colunas numéricas.  Saídas k novas funcionalidades, uma nova característica numérica por cluster, contendo a distância de cada amostra ao centroide de cada cluster.|

### <a name="data-guardrails"></a>Guarda-costas de dados

Os guarda-costas de dados são aplicados automaticamente para ajudá-lo a identificar potenciais problemas com os seus dados (por exemplo, valores em falta, desequilíbrio de classe) e ajudar a tomar medidas corretivas para melhores resultados. Existem muitas boas práticas que estão disponíveis e podem ser aplicadas para obter resultados fiáveis. 

A tabela que se segue descreve os guarda-costas de dados atualmente suportados, e os estados associados que os utilizadores podem encontrar ao submeter a sua experiência.

Guarda-costas|Estado|Condição&nbsp;para&nbsp;gatilho
---|---|---
Falta de valores de&nbsp;&nbsp;imputação |**Passado** <br> <br> **Fixo**|    Nenhum valor em falta em nenhuma das colunas de entrada&nbsp; <br> <br> Algumas colunas têm valores em falta
Validação cruzada|**Feito**|Se não for fornecido nenhum conjunto de validação explícita
&nbsp;de cardinalidade de alta&nbsp;apresentam&nbsp;deteção|    **Passado** <br> <br>**Feito**|    Não foram detetados grandes características de cardinalidade <br><br> Foram detetadas colunas de entrada de alta cardinalidade
Deteção de equilíbrio de classes    |**Passado** <br><br><br>**Alertado** |As aulas são equilibradas nos dados da formação; Um conjunto de dados é considerado equilibrado se cada classe tiver uma boa representação no conjunto de dados, medido pelo número e relação de amostras <br> <br> As aulas nos dados de formação são desequilibradas
Consistência dos dados da série temporal|**Passado** <br><br><br><br> **Fixo** |<br> Foram analisados os valores de {horizon, lag, rolling window} e não foram detetados potenciais problemas fora da memória. <br> <br>Os valores selecionados {horizon, lag, rolling window} foram analisados e potencialmente causarão a sua experiência a ficar sem memória. O lag ou a janela de rolamento foram desligados.

## <a name="run-experiment-and-view-results"></a>Executar resultados de experiência e visualização

Selecione **Terminar** para executar a sua experiência. O processo de preparação da experimentação pode demorar até 10 minutos. As tarefas de preparação podem demorar mais 2 ou 3 minutos para cada pipeline concluir a execução.

### <a name="view-experiment-details"></a>Ver detalhes da experiência

O ecrã **'Executar Detalhes'** abre-se para o separador **Detalhes.** Este ecrã mostra-lhe um resumo da experiência executada, incluindo uma barra de estado no topo ao lado do número de execução. 

O separador**Modelos** contém uma lista dos modelos criados encomendados pela pontuação de métrica. Por predefinição, o modelo com a classificação mais alta com base na métrica escolhida está no topo da lista. À medida que a tarefa de preparação experimenta mais modelos, estes são adicionados à lista. Utilize-a para obter uma comparação rápida das métricas dos modelos produzidos até agora.

[![Executar detalhes dashboard](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Ver detalhes da execução de treino

Aperte em qualquer um dos modelos concluídos para ver detalhes de execução de treino, [Learn more about charts](how-to-understand-automated-ml.md)como métricas de execução no separador de detalhes do **Modelo** ou gráficos de desempenho no separador **Visualizações.**

[detalhes da ![Iteração](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Implante o seu modelo

Uma vez que tenha o melhor modelo à mão, é hora de implementá-lo como um serviço web para prever em novos dados.

O ML automatizado ajuda-o a implementar o modelo sem escrever código:

1. Tem algumas opções para a implantação. 

    + Opção 1: Para implementar o melhor modelo (de acordo com os critérios métricos definidos), selecione o melhor botão de **modelo de implementação** no separador **Detalhes.**

    + Opção 2: Para implementar uma iteração de modelo específica desta experiência, desaperte o modelo para abrir o separador de detalhes do **Modelo** e selecione **modelo de implantação**.

1. Povoe o painel de **modelos de implantação.**

    Campo| Valor
    ----|----
    Nome| Introduza um nome único para a sua implantação.
    Descrição| Introduza uma descrição para identificar melhor para que é esta implantação.
    Tipo computacional| Selecione o tipo de ponto final que pretende implementar: *Serviço Azure Kubernetes (AKS)* ou Instância de *Contentores Azure (ACI)* .
    Nome computacional| *Aplica-se apenas ao AKS:* Selecione o nome do cluster AKS que deseja implementar.
    Ativar a autenticação | Selecione para permitir a autenticação baseada em tokenou ou baseado em chaves.
    Use ativos de implementação personalizados| Ative esta funcionalidade se quiser carregar o seu próprio script de pontuação e ficheiro ambiente. [Saiba mais sobre a pontuação de scripts.](how-to-deploy-and-where.md#script)

    >[!Important]
    > Os nomes dos ficheiros devem ter menos de 32 caracteres e devem começar e terminar com alfanuméricos. Pode incluir traços, sublinhados, pontos e alfanuméricos entre. Espaços não são permitidos.

    O menu *Avançado* oferece funcionalidades de implementação padrão, tais como [definições](how-to-enable-app-insights.md) de recolha de dados e utilização de recursos. Se desejar anular estes incumprimentos, fá-lo neste menu.

1. Selecione **Implementar**. A implantação pode levar cerca de 20 minutos para ser concluída.

Agora tem um serviço web operacional para gerar previsões! Pode testar as previsões consultando o serviço a partir do suporte de [Machine Learning Azure.](how-to-consume-web-service.md#consume-the-service-from-power-bi)

## <a name="next-steps"></a>Passos Seguintes

* [Aprenda a consumir um serviço web.](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)
* [Compreender os resultados automatizados de aprendizagem automática de máquinas.](how-to-understand-automated-ml.md)
* [Saiba mais sobre machine learning automatizado](concept-automated-ml.md) e Aprendizagem automática de Máquinas Azure.
