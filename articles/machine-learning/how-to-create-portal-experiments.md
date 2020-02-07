---
title: Criar & implantar modelos de ML automatizados
titleSuffix: Azure Machine Learning
description: Crie, gerencie e implante experimentos automatizados de aprendizado de máquina no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/04/2020
ms.openlocfilehash: 620aab2d2104c9e08de6e7ea47511ff45a482ec4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046105"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learning-studio"></a>Crie, explore e implante experimentos automatizados de aprendizado de máquina com o Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

 Neste artigo, você aprenderá a criar, explorar e implantar experimentos automatizados de aprendizado de máquina no Azure Machine Learning Studio sem uma única linha de código. O aprendizado de máquina automatizado automatiza o processo de seleção do melhor algoritmo a ser usado para seus dados específicos, para que você possa gerar um modelo de aprendizado de máquina rapidamente. [Saiba mais sobre aprendizagem automática de máquinas.](concept-automated-ml.md)

 Se preferir uma experiência mais baseada em códigos, também pode [configurar as suas experiências automatizadas](how-to-configure-auto-train.md) de aprendizagem automática em Python com o [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Um espaço de trabalho azure machine learning com um tipo de **edição Enterprise.** Consulte Criar um espaço de [trabalho de aprendizagem automática Azure](how-to-manage-workspace.md).  Para atualizar um espaço de trabalho existente para a edição da Enterprise, consulte [upgrade para edição enterprise.](how-to-manage-workspace.md#upgrade)

## <a name="get-started"></a>Introdução

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com) 

1. Selecione sua assinatura e seu espaço de trabalho. 

1. Navegue até o painel esquerdo. **Selecione ML automatizado** na secção **Autor.**

[painel de navegação do estúdio de machine learning ![Azure](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Se esta for a primeira vez que você faz qualquer experimento, você verá uma lista vazia e links para a documentação. 

Caso contrário, você verá uma lista de experiências de aprendizado de máquina automatizadas recentes, incluindo aquelas criadas com o SDK. 

## <a name="create-and-run-experiment"></a>Criar e executar experimento

1. Selecione **+ Nova execução automática ml** e povoe o formulário.

1. Selecione um conjunto de uma do seu contêiner de armazenamento ou crie um novo conjunto de um. Os conjuntos de valores podem ser criados a partir de arquivos locais, URLs da Web, repositórios de armazenamento ou conjuntos de os Azure Open DataSets. 

    >[!Important]
    > Requisitos para dados de treinamento:
    >* Os dados devem estar no formato de tabela.
    >* O valor que você deseja prever (coluna de destino) deve estar presente nos dados.

    1. Para criar um novo conjunto de dados a partir de um ficheiro no seu computador local, **selecione 'Navegar'** e, em seguida, selecione o ficheiro. 

    1. Dê um nome exclusivo ao seu conjunto de dado e forneça uma descrição opcional. 

    1. Selecione **Seguinte**, para carregá-lo para o recipiente de armazenamento predefinido que é criado automaticamente com o seu espaço de trabalho, ou escolha um recipiente de armazenamento que pretende utilizar para a experiência. 

    1. Reveja o formulário **Definições e pré-visualização** para obter precisão. O formulário é populado de forma inteligente com base no tipo de arquivo. 

        Campo| Descrição
        ----|----
        Formato de ficheiro| Define o layout e o tipo de dados armazenados em um arquivo.
        Delimitador| Um ou mais caracteres para especificar o limite entre regiões separadas e independentes em texto sem formatação ou outros fluxos de dados.
        Codificação| Identifica o bit para a tabela de esquema de caractere a ser usada para ler seu conjunto de seus.
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de uma, se houver, serão tratados.
        Ignorar linhas | Indica quantas linhas, se houver, são ignoradas no conjunto de registros.
    
        Selecione **Seguinte**.

    1. O formulário **Schema** é inteligentemente povoado com base nas seleções na **forma Definições e pré-visualização.** Aqui configure o tipo de dados para cada coluna, reveja os nomes das colunas e selecione quais as colunas a **não incluir** para a sua experiência. 
            
        Selecione **Seguinte.**

    1. O formulário **de detalhes confirmar** é um resumo das informações previamente povoadas nos **formulários de informação** e definições básicas **e pré-visualização.** Você também tem a opção de criar o perfil de seu conjunto de um usando uma computação de criação de perfil habilitada. Saiba mais sobre [o perfil de dados.](#profile)

        Selecione **Seguinte**.
1. Selecione o conjunto de seus conjuntos de seu recém-criado quando ele for exibido. Você também pode exibir uma visualização do conjunto de exemplos e estatísticas de exemplo. 

1. No formulário **de execução Configure,** introduza um nome único de experiência.

1. Selecione uma coluna de destino; Essa é a coluna na qual você gostaria de fazer previsões.

1. Selecione uma computação para o trabalho de criação de perfil de dados e treinamento. Uma lista de suas computações existentes está disponível na lista suspensa. Para criar uma nova computação, siga as instruções na etapa 7.

1. Selecione **Criar um novo cálculo** para configurar o contexto da sua computação para esta experiência.

    Campo|Descrição
    ---|---
    Nome da computação| Insira um nome exclusivo que identifique o contexto de computação.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual para sua computação.
    Nós mín./máx. (em configurações avançadas)| Para criar o perfil de dados, você deve especificar um ou mais nós. Insira o número máximo de nós para sua computação. O padrão é 6 nós para uma computação AML.
    
    Selecione **Criar**. A criação de uma nova computação pode levar alguns minutos.

    >[!NOTE]
    > O seu nome de cálculo indicará se o cálculo que seleciona/cria está *ativado*por perfis . (Consulte o [perfil de dados](#profile) da secção para obter mais detalhes).

    Selecione **Seguinte**.

1. No formulário de **tipo de tarefa e configurações,** selecione o tipo de tarefa: classificação, regressão ou previsão. Veja [como definir tipos de tarefas](how-to-define-task-type.md) para mais informações.

    1. Para classificação, você também pode habilitar o aprendizado profundo, que é usado para featurizations de texto.

    1. Para previsão:
        1. Selecionar coluna de tempo: esta coluna contém os dados de hora a serem usados.

        1. Selecione previsão Horizonte: indica quantas unidades de tempo (minutos/horas/dias/semanas/meses/anos) o modelo será capaz de prever para o futuro. Quanto mais o modelo for necessário para prever no futuro, menor será a sua precisão. [Saiba mais sobre o horizonte](how-to-auto-train-forecast.md)de previsão e previsão.

1. (Opcional) Ver definições de configuração de adição: configurações adicionais que pode utilizar para controlar melhor o trabalho de formação. Caso contrário, os padrões serão aplicados com base na seleção de experimento e nos dados. 

    Configurações adicionais|Descrição
    ------|------
    Métrica primária| Métrica principal usada para pontuar seu modelo. [Saiba mais sobre métricas](how-to-configure-auto-train.md#explore-model-metrics)de modelo.
    Personalização automática| Selecione para habilitar ou desabilitar o pré-processamento feito pelo Machine Learning automatizado. O pré-processamento inclui a limpeza, preparação e transformação automáticas de dados para gerar recursos sintéticos. Não suportado para o tipo de tarefa de previsão da série temporal. [Saiba mais sobre o pré-processamento.](#featurization) 
    Explicar o melhor modelo | Selecione para ativar ou desativar para mostrar explicabilidade do melhor modelo recomendado
    Algoritmo bloqueado| Selecione os algoritmos que você deseja excluir do trabalho de treinamento.
    Critério de saída| Quando qualquer um desses critérios for atendido, o trabalho de treinamento será interrompido. <br> Tempo de trabalho de *formação (horas)* : Quanto tempo permite que o trabalho de formação decorra. <br> *Limiar de pontuação métrica*: Pontuação mínima métrica para todos os gasodutos. Isso garante que, se você tiver uma métrica de destino definida que deseja alcançar, não gaste mais tempo no trabalho de treinamento do que o necessário.
    Validação| Selecione uma das opções de validação cruzada para usar no trabalho de treinamento. [Saiba mais sobre validação cruzada.](how-to-configure-auto-train.md)
    Simultaneidade| *Iterações simultâneas*: Número máximo de gasodutos (iterações) para testar no trabalho de formação. O trabalho não será executado mais do que o número especificado de iterações.

1. (Opcional) Ver definições de visualização: se optar por ativar a **funcionalidade automática** no formulário de **configuração adicional,** este formulário é onde especifica quais as colunas para executar essas funcionalidades e selecione qual o valor estatístico a utilizar para imputações de valor em falta.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Criação de perfil de dados & estatísticas de resumo

Você pode obter uma grande variedade de estatísticas de resumo em seu conjunto de dados para verificar se o conjunto de dados está pronto para ML. Para colunas não numéricas, elas incluem apenas estatísticas básicas, como mín., máx. e contagem de erros. Para colunas numéricas, você também pode revisar seus momentos estatísticos e quantis estimados. Especificamente, nosso perfil de dados inclui:

>[!NOTE]
> Entradas em branco aparecem para recursos com tipos irrelevantes.

Estatísticas|Descrição
------|------
Funcionalidade| Nome da coluna que está sendo resumida.
Perfil| Visualização embutida com base no tipo inferido. Por exemplo, cadeias de caracteres, Boolianos e datas terão contagens de valor, enquanto decimais (numéricos) têm histogramas aproximados. Isso permite que você tenha uma compreensão rápida da distribuição dos dados.
Distribuição de tipo| Valor na linha contagem de tipos dentro de uma coluna. Os nulos são de seu próprio tipo, portanto, essa visualização é útil para detectar valores ímpares ou ausentes.
Tipo|Tipo inferido da coluna. Os valores possíveis incluem: cadeias de caracteres, Boolianos, datas e decimais.
Mín.| Valor mínimo da coluna. Entradas em branco aparecem para recursos cujo tipo não tem uma ordenação inerente (por exemplo, Boolianos).
Máx.| Valor máximo da coluna. 
Contagem| Número total de entradas ausentes e não ausentes na coluna.
Contagem não ausente| Número de entradas na coluna que não estão ausentes. Cadeias de caracteres e erros vazios são tratados como valores, portanto, eles não contribuirão para a "contagem não encontrada".
Quantis| Valores aproximados em cada Quantil para fornecer uma noção da distribuição dos dados.
média| Média aritmética ou médio da coluna.
Desvio padrão| Medida da quantidade de dispersão ou variação dos dados desta coluna.
Variância| A medida de difundir os dados desta coluna é de seu valor médio. 
Assimetrias| Medida de quão diferentes os dados dessa coluna são de uma distribuição normal.
Kurtosis| A medida de quão cauda os dados desta coluna é comparada a uma distribuição normal.


<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Opções avançadas de caracterização

O machine learning automatizado oferece pré-processamento e guarda-costas de dados automaticamente, para ajudá-lo a identificar e gerir potenciais problemas com os seus dados. 

### <a name="preprocessing"></a>Pré-processamento

|Passos de&nbsp;de pré-processamento| Descrição |
| ------------- | ------------- |
|Remova cardinalidade elevada ou nenhum recurso de variância|Descarte-os dos conjuntos de treinamento e validação, incluindo recursos com todos os valores ausentes, o mesmo valor em todas as linhas ou com cardinalidade extremamente alta (por exemplo, hashes, IDs ou GUIDs).|
|Impute valores em falta|Para recursos numéricos, imputar com a média de valores na coluna.<br/><br/>Para recursos categóricos, imputar com o valor mais frequente.|
|Gerar recursos adicionais|Para as funcionalidades de DateTime: ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.<br/><br/>Para recursos de texto: a frequência de termos com base em unigrams, bi-grams e Tri-Character-grams.|
|Transformar e codificar |Recursos numéricos com poucos valores exclusivos são transformados em recursos categóricos.<br/><br/>A codificação One-Hot é executada para uma baixa cardinalidade categórica; para alta cardinalidade, codificação One-Hot-hash.|
|Incorporações de palavras|Texto featurizer que converte vetores de tokens de texto em vetores de sentença usando um modelo pré-treinado. O vetor de incorporação de cada palavra em um documento é agregado em conjunto para produzir um vetor de recurso de documento.|
|Codificações de destino|Para recursos categóricos, o mapeia cada categoria com o valor de destino médio para problemas de regressão e a probabilidade de classe de cada classe para problemas de classificação. O peso baseado em frequência e a validação cruzada de k-fold são aplicados para reduzir o ajuste do mapeamento e do ruído causados por categorias de dados esparsas.|
|Codificação de destino de texto|Para entrada de texto, um modelo linear empilhado com conjunto de palavras é usado para gerar a probabilidade de cada classe.|
|Peso de evidência (WoE)|Calcula WoE como uma medida de correlação de colunas categóricas para a coluna de destino. Ele é calculado como o log da taxa de probabilidades de fora de classe vs in-Class. Esta etapa gera uma coluna de recurso numérico por classe e remove a necessidade de imputar explicitamente os valores ausentes e o tratamento de exceção.|
|Distância do cluster|Treina um modelo de clustering k-means em todas as colunas numéricas.  Gera novos recursos e um novo recurso numérico por cluster, contendo a distância de cada amostra para o centróide de cada cluster.|

### <a name="data-guardrails"></a>Guardrails de dados

Os guarda-costas de dados são aplicados automaticamente para ajudá-lo a identificar potenciais problemas com os seus dados (por exemplo, valores em falta, desequilíbrio de classe) e ajudar a tomar medidas corretivas para melhores resultados. Há muitas práticas recomendadas que estão disponíveis e podem ser aplicadas para obter resultados confiáveis. 

A tabela a seguir descreve os dados atualmente com suporte guardrails e os status associados que os usuários podem chegar ao enviar o experimento.

Guardrail|Estado|Condição&nbsp;para&nbsp;gatilho
---|---|---
Falta de valores de&nbsp;&nbsp;imputação |**Passado** <br> <br> **Fixo**|    Nenhum valor em falta em nenhuma das colunas de entrada&nbsp; <br> <br> Algumas colunas têm valores ausentes
Validação cruzada|**Feito**|Se nenhum conjunto de validação explícito for fornecido
&nbsp;de cardinalidade de alta&nbsp;apresentam&nbsp;deteção|  **Passado** <br> <br>**Feito**|   Nenhum recurso de cardinalidade alta foi detectado <br><br> Colunas de entrada de alta cardinalidade foram detectadas
Detecção de balanceamento de classe |**Passado** <br><br><br>**Alertado** |As classes são equilibradas nos dados de treinamento; Um conjunto de um DataSet é considerado balanceado se cada classe tem uma boa representação no DataSet, conforme medido por número e proporção de amostras <br> <br> As classes nos dados de treinamento são desbalanceadas
Consistência de dados de série temporal|**Passado** <br><br><br><br> **Fixo** |<br> Os valores de {horizonte, atraso, janela sem interrupção} selecionados foram analisados e não foram detectados problemas de falta de memória em potencial. <br> <br>Os valores selecionados {Horizonte, lag, janela sem interrupção} foram analisados e, potencialmente, farão com que o teste fique sem memória. A janela de atraso ou rolagem foi desativada.

## <a name="run-experiment-and-view-results"></a>Executar experimento e exibir resultados

Selecione **Terminar** para executar a sua experiência. O processo de preparação do experimento pode levar até 10 minutos. Os trabalhos de treinamento podem levar mais 2-3 minutos para que cada pipeline termine a execução.

### <a name="view-experiment-details"></a>Exibir detalhes do experimento

O ecrã **'Executar Detalhes'** abre-se para o separador **Detalhes.** Este ecrã mostra-lhe um resumo da experiência executada, incluindo uma barra de estado no topo ao lado do número de execução. 

O separador **Models** contém uma lista dos modelos criados encomendados pela pontuação métrica. Por padrão, o modelo que classifica o mais alto com base na métrica escolhida está no topo da lista. Como o trabalho de treinamento tenta mais modelos, eles são adicionados à lista. Use isso para obter uma comparação rápida das métricas para os modelos produzidos até agora.

[![Executar detalhes dashboard](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Exibir detalhes da execução de treinamento

Aperte em qualquer um dos modelos concluídos para ver detalhes de execução de treino, [](how-to-understand-automated-ml.md)como métricas de execução no separador de detalhes do **Modelo** ou gráficos de desempenho no separador **Visualizações.**

[detalhes da ![Iteração](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Implantar seu modelo

Quando você tem o melhor modelo em mãos, é hora de implantá-lo como um serviço Web para prever novos dados.

O ML automatizado ajuda você a implantar o modelo sem escrever código:

1. Você tem algumas opções de implantação. 

    + Opção 1: Para implementar o melhor modelo (de acordo com os critérios métricos definidos), selecione o melhor botão de **modelo de implementação** no separador **Detalhes.**

    + Opção 2: Para implementar uma iteração de modelo específica desta experiência, desaperte o modelo para abrir o separador de detalhes do **Modelo** e selecione **modelo de implantação**.

1. Povoe o painel de **modelos de implantação.**

    Campo| Valor
    ----|----
    Nome| Insira um nome exclusivo para sua implantação.
    Descrição| Insira uma descrição para identificar melhor a finalidade dessa implantação.
    Tipo computacional| Selecione o tipo de ponto final que pretende implementar: *Serviço Azure Kubernetes (AKS)* ou Instância de *Contentores Azure (ACI)* .
    Nome da computação| *Aplica-se apenas ao AKS:* Selecione o nome do cluster AKS que deseja implementar.
    Ativar a autenticação | Selecione para permitir a autenticação baseada em token ou baseada em chave.
    Usar ativos de implantação personalizados| Habilite esse recurso se você quiser carregar seu próprio script de Pontuação e arquivo de ambiente. [Saiba mais sobre a pontuação de scripts.](how-to-deploy-and-where.md#script)

    >[!Important]
    > Os nomes de arquivo devem ter menos de 32 caracteres e devem começar e terminar com alfanuméricos. Pode incluir traços, sublinhados, pontos e alfanuméricos entre. Não são permitidos espaços.

    O menu *Avançado* oferece funcionalidades de implementação padrão, tais como [definições](how-to-enable-app-insights.md) de recolha de dados e utilização de recursos. Se você quiser substituir esses padrões, faça isso neste menu.

1. Selecione **Implementar**. A implantação pode levar cerca de 20 minutos para ser concluída.

Agora você tem um serviço Web operacional para gerar previsões! Pode testar as previsões consultando o serviço a partir do suporte de [Machine Learning Azure.](how-to-consume-web-service.md#consume-the-service-from-power-bi)

## <a name="next-steps"></a>Passos seguintes

* Experimente o tutorial final para criar [a sua primeira experiência ml automatizada com o estúdio Azure Machine Learning.](tutorial-first-experiment-automated-ml.md) 
* [Saiba mais sobre machine learning automatizado](concept-automated-ml.md) e Aprendizagem automática de Máquinas Azure.
* [Compreender os resultados automatizados de aprendizagem automática de máquinas.](how-to-understand-automated-ml.md)
* [Aprenda a consumir um serviço web.](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)
