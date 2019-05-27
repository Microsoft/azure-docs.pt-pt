---
title: Criar e explorar experimentações no Portal
titleSuffix: Azure Machine Learning service
description: Saiba como criar e gerir automatizada experimentações de machine learning no portal
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: a2a281fda9272fb794692becb0ca08f3cf791458
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989915"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Criar e explorar automatizada experimentações de machine learning no portal do Azure (pré-visualização)

 Neste artigo, saiba como criar, executar e explore automatizada experimentações de machine learning no portal do Azure sem uma única linha de código. Aprendizagem automática automatiza o processo de selecionar o melhor algoritmo a utilizar para os seus dados específicos, para que pode gerar rapidamente um modelo de aprendizagem automática. [Saiba mais sobre a aprendizagem automática](concept-automated-ml.md).

 Se preferir uma experiência mais baseada em código, também pode [configurar seu automatizada experimentações de machine learning em Python](how-to-configure-auto-train.md) com o [do Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Uma área de trabalho de serviço do Azure Machine Learning. Ver [criar uma área de trabalho do serviço do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Introdução

Navegue até ao painel do lado esquerdo da área de trabalho. Selecione automatizada Machine Learning na secção de criação de conteúdos (pré-visualização).

![Painel de navegação do portal do Azure](media/how-to-create-portal-experiments/nav-pane.png)

 Se esta for a primeira vez, fazer qualquer experimentações com o Machine Learning automatizada, verá o seguinte:

![Página de destino de experiência de portal do Azure](media/how-to-create-portal-experiments/landing-page.png)

Caso contrário, irá ver o dashboard de aprendizado de máquina automatizada com uma visão geral de todos os seus automatizada experimentações de machine learning, incluindo aqueles criados com o SDK. Aqui pode filtrar e explore suas execuções por data, nome de experimentação e estado de execução.

![Dashboard de experiência de portal do Azure](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Criar uma experimentação

Selecione o botão Criar experimentação para preencher o formulário seguinte.

![Criar o formulário da experimentação](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Introduza o nome de experimentação.

1. Selecione uma computação para a criação de perfis de dados e a tarefa de preparação. Uma lista de sua computações existentes está disponível na lista pendente. Para criar uma nova computação, siga as instruções no passo 3.

1. Selecione o criar um novo botão de computação para abrir o abaixo do painel e configurar o contexto de cálculo para esta fase experimental.

    ![Criar nova computação para experimentação](media/how-to-create-portal-experiments/create-new-compute.png)

    Campo|Descrição
    ---|---
    Nome de computação| Introduza um nome exclusivo que identifica o contexto de cálculo.
    Tamanho da máquina virtual| Selecione o tamanho de máquina virtual para a computação.
    Definições adicionais| *Nó de min*: Introduza o número mínimo de nós para a computação. O número mínimo de nós de computação AML é 0. Para ativar a criação de perfis de dados, tem de ter 1 ou mais nós. <br> *Nó de Max*: Introduza o número máximo de nós para a computação. A predefinição é 6 nós para um tipo de computação de AML.

      Para iniciar a criação de sua nova computação, selecione **criar**. Esta ação pode demorar alguns minutos.

      >[!NOTE]
      > O nome da sua computação indica se a computação que selecione/cria está *ativado a criação de perfis*. (Consulte 7b para obter mais detalhes sobre a criação de perfis de dados).

1. Selecione uma conta de armazenamento para os seus dados. Pré-visualização pública só suporta carregamentos de ficheiros locais e contas de armazenamento de Blobs do Azure.

1. Selecione um contentor de armazenamento.

1. Selecione um ficheiro de dados a partir do seu contentor de armazenamento ou carregar um ficheiro do computador local para o contentor.

    ![Selecione o ficheiro de dados para experimentação](media/how-to-create-portal-experiments/select-file.png)

1. Utilize os separadores de pré-visualização e o perfil de configuração do seus dados para esta fase experimental.

    1. No separador pré-visualização, indicar se os seus dados incluem cabeçalhos e selecione as funcionalidades (colunas) para a utilização de treinamento o **incluída** mudar botões em cada coluna de funcionalidade.

        ![Pré-visualização de dados](media/how-to-create-portal-experiments/data-preview.png)

    1. Na guia perfil, pode ver o [perfil de dados](#profile) por funcionalidade, bem como a distribuição, tipo e estatísticas de resumo (média, mediana, Máx. por minuto e assim por diante) de cada.

        ![Separador de perfil de dados](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > A seguinte mensagem de erro será apresentada se for seu contexto de cálculo **não** ativado a criação de perfis: *A criação de perfis de dados só está disponível para os destinos de computação que já estão em execução*.

1. Selecione o tipo de tarefa de preparação: classificação, regressão ou de previsão.

1. Selecione a coluna de destino. A coluna que gostaria de fazer as previsões no.

1. Para a previsão:
    1. Selecione a coluna de hora: Esta coluna contém os dados de tempo para ser utilizado.
    1. Selecione horizonte previsão: Indica o número de unidades de tempo (minutos/horas/dias/semanas/meses/anos) o modelo será capaz de prever o futuro. Quanto mais o modelo é necessário para prever o futuro, quanto menos precisas torna-se. [Saiba mais sobre previsão e previsão horizonte](https://docs.microsoft.com/azure/machine-learning/service/how-to-auto-train-forecast#configure-experiment).

1. (Opcional) Definições avançadas: definições adicionais que pode utilizar para controlar melhor a tarefa de preparação.

    Definições avançadas|Descrição
    ------|------
    Métrica primária| Métrica principal utilizada para o seu modelo de classificação. [Saiba mais sobre as métricas de modelo](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#explore-model-metrics).
    Critérios de saída| Quando qualquer um desses critérios forem atendidos, a tarefa de preparação termina antes da conclusão completa. <br> *O tempo de tarefa (minutos) de treinamento*: Quanto para permitir que a tarefa de preparação executar.  <br> *Número máximo de iterações*: Número máximo de pipelines (iterações) para testar a tarefa de preparação. A tarefa não será executada mais do que o número especificado de iterações. <br> *Métrica de pontuação limiar*:  Pontuação de métrica mínimo para todos os pipelines. Isto garante que se tiver uma métrica de destino definido que deseja alcançar, que não passar mais tempo na tarefa de treinamento que o necessário.
    O pré-processamento| Selecione para ativar ou desativar o pré-processamento feito pela aprendizagem automática. O pré-processamento inclui a limpeza de dados automática, preparar e transformação para gerar recursos sintéticos. [Saiba mais sobre o processamento prévio de](#preprocess).
    Validação| Selecione uma das opções de validação cruzada para utilizar a tarefa de preparação. [Saiba mais sobre cruzada validação](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#cross-validation-split-options).
    Simultaneidade| Selecione os limites de vários núcleos para utilizar quando utilizar vários núcleo computação.
    Algoritmo de bloqueados| Selecione algoritmos que pretende excluir da tarefa de preparação.

   ![Formulário de definições avançadas](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> Para obter mais informações nos campos, clique a dica de ferramenta de informações.

<a name="profile"></a>

### <a name="data-profiling"></a>Dados de criação de perfis

Pode obter uma ampla variedade de estatísticas de resumo em seu conjunto de dados para verificar se o seu conjunto de dados está preparado para o ML. Para colunas não numéricos, eles incluem estatísticas apenas básicas, como Mín, Máx e contagem de erros. Para colunas numéricas, também pode rever os momentos de estatísticos e quantiles estimado. Mais concretamente, inclui o nosso perfil de dados:

* **Funcionalidade**: nome da coluna que está a ser resumida.

* **Perfil**: uma visualização em linha com base no tipo inferido. Por exemplo, cadeias de caracteres, booleanos e datas terá contagens de valores, enquanto casas decimais (numéricos) tem aproximadas histogramas. Isto permite-lhe obter uma compreensão rápida da distribuição dos dados.

* **Distribuição do tipo**: uma contagem de valor na linha de tipos dentro de uma coluna. Nulls são seu próprio tipo, para que esta visualização é útil para a detecção de valores em falta ou ímpares.

* **Tipo de**: o tipo inferido da coluna. Os valores possíveis incluem: cadeias de caracteres, booleanos, datas e casas decimais.

* **Min**: o valor mínimo da coluna. As entradas em branco são apresentados para recursos cujo tipo não tem uma ordem inerente (por exemplo, booleanos).

* **Máx.**: o valor máximo da coluna. Como "min", entradas em branco são apresentados para recursos com tipos irrelevantes.

* **Contagem de**: o número total de entradas em falta e não em falta na coluna.

* **Não tem em falta contagem**: o número de entradas na coluna que não estão em falta. Tenha em atenção que as cadeias vazias e erros são tratados como valores, para que eles não contribuem para a "contagem não em falta".

* **Quantiles** (em intervalos de 0,1, 1, 5, 25, 50, 75, 95, 99 e 99,9%): os valores approximated em cada quantile para fornecer uma noção da distribuição dos dados. As entradas em branco são apresentados para recursos com tipos irrelevantes.

* **Significa**: a média aritmética da coluna. As entradas em branco são apresentados para recursos com tipos irrelevantes.

* **Desvio padrão**: o desvio-padrão da coluna. As entradas em branco são apresentados para recursos com tipos irrelevantes.

* **Variância**: a variância da coluna. As entradas em branco são apresentados para recursos com tipos irrelevantes.

* **Assimetrias**: assimetrias da coluna. As entradas em branco são apresentados para recursos com tipos irrelevantes.

* **Kurtosis**: kurtosis da coluna. As entradas em branco são apresentados para recursos com tipos irrelevantes.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Advanced pré-processamento

Quando configurar as suas experimentações, pode ativar a definição avançada `Preprocess`. Ao fazê-lo por isso, significa que os seguintes passos de pré-processamento e featurization de dados são efetuados automaticamente.

|O pré-processamento&nbsp;passos| Descrição |
| ------------- | ------------- |
|Remova cardinalidade elevada ou nenhum recurso de variância|Remova estes de conjuntos de formação e validação, incluindo recursos com todos os valores em falta, mesmo valor em todas as linhas ou com uma cardinalidade muito elevada (por exemplo, hashes, IDs ou GUIDs).|
|Impute valores em falta|De funcionalidades numéricas, impute com a média dos valores na coluna.<br/><br/>Para obter recursos categóricos, impute com o valor mais frequente.|
|Gerar recursos adicionais|Para as funcionalidades de DateTime: Ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.<br/><br/>Para as funcionalidades de texto: Frequência de prazo com base em unigrams, bi-grams e chamamos-caractere-grams.|
|Transformar e codificar |Funcionalidades numérico com poucos valores exclusivos são transformadas em funcionalidades categóricas.<br/><br/>Um-hot codificação é executada para baixa cardinalidade categórica; para elevada cardinalidade, codificação de hash um acesso frequente.|
|Incorporações|Featurizer de texto que converte vetores de tokens de texto em vetores de sentença usando um modelo com formação prévia. Vetor de incorporação de cada palavra num documento é agregado em conjunto para produzir um vetor de funcionalidade do documento.|
|Codificações de destino|Para obter recursos categóricos, mapeia cada categoria com valor de destino média para problemas de regressão e a probabilidade de classe para cada classe de problemas de classificação. Com base na frequência de peso e k fases de validação cruzam é aplicada para reduzir ao longo do ajuste do mapeamento e ruído causado por categorias de dados dispersos.|
|Codificação de destino de texto|Para introdução de texto, um modelo linear empilhado com matriz de palavras é utilizado para gerar a probabilidade de cada classe.|
|Peso da evidência (WoE)|Calcula WoE como uma medida de correlação de colunas categóricas para a coluna de destino. Esta é calculada como o log da proporção de probabilidades de fora da classe na classe vs. Este passo produz uma coluna de funcionalidades numéricas por classe e remove a necessidade de explicitamente impute valores em falta e tratamento de exceção.|
|Distância de cluster|Prepara um modelo de clustering de k-means em todas as colunas numéricas.  Saídas k novos recursos, um novo recurso numérico por cluster, que contém a distância de cada exemplo, para o centroide de cada cluster.|

## <a name="run-experiment-and-view-results"></a>Execute a experimentação e ver resultados

Para executar a experimentação, clique em Iniciar. A experimentação a preparar o processo demora alguns minutos.

### <a name="view-experiment-details"></a>Ver detalhes de experimentação

Depois de fazer a fase de preparação de experimentação, verá o ecrã de detalhes de execução. Isto dá-lhe uma lista completa dos modelos criados. Por predefinição, o modelo que pontua a mais alta com base nos seus parâmetros na parte superior da lista. Como a tarefa de preparação tenta horizontalmente mais modelos, eles são adicionados à sua lista de iteração e o gráfico. Utilize o gráfico de iteração para obter uma comparação rápida das métricas para os modelos de produzidos até agora.

Tarefas de preparação pode demorar algum tempo para cada termine a execução de pipeline.

![Dashboard de detalhes da execução](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>Treinamento de vista Detalhes da execução

Faça uma busca detalhada em qualquer um dos modelos de saída para ver detalhes de execução, como gráficos de métricas e distribuição de desempenho de treinamento. [Saiba mais sobre os gráficos](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments#understanding-automated-ml-charts).

![Detalhes de iteração](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>Implementar modelo

Depois de ter o melhor modelo em mãos, é hora de implantá-lo como um serviço web para prever sobre novos dados.

ML automatizada ajuda-o com a implementação do modelo sem escrever código:

1. Tem algumas opções para a implementação. 
    1. Se pretender implementar o melhor modelo com base nos critérios métrica definido para a experimentação, selecione **implementar o melhor modelo** partir a **executar detalhes** página.

        ![Implementar o botão de modelo](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. Se pretender implementar uma iteração do modelo específico, faça uma busca detalhada sobre o modelo para abrir a página de detalhes de execução específico e selecione **implementar modelo**.

        ![Implementar o botão de modelo](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. Primeira etapa é registrar o modelo para o serviço. Selecione "Registar o modelo de" e aguarde pela conclusão do processo de registo.

    ![Implementar o painel de modelo](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. Assim que o modelo estiver registado, poderá transferir o script de classificação (scoring.py) e o script de ambiente (condaEnv.yml) a ser utilizado durante a implementação.

1. Quando o script de classificação e o script de ambiente são transferidos, vá para o **ativos** painel do painel de navegação esquerda e selecione **modelos**.

    ![Modelos de painel de navegação](media/how-to-create-portal-experiments/nav-pane-models.png)

1. Selecione o modelo que registou e selecione "Criar a imagem".

    Pode identificar o modelo, sua descrição, que incluirá o ID de execução, o número de iteração, no seguinte formato: *< Run_ID > _ < Iteration_number > _Model*

    ![Modelos: Criar imagem](media/how-to-create-portal-experiments/model-create-image.png)

1. Introduza um nome para a imagem. 
1. Selecione o **procurar** botão junto à caixa "Ficheiro de classificação" para carregar o ficheiro de classificação (scoring.py) que transferiu anteriormente.

1. Selecione o **procurar** botão junto à caixa "Conda File" para carregar o ficheiro de ambiente (condaEnv.yml) que transferiu anteriormente.

    Pode usar o seu script de classificação e o arquivo de conda, bem como carregar ficheiros adicionais. [Saiba mais sobre o script de classificação](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#script).

      >[!Important]
      > Os nomes de ficheiros devem ser em 32 carateres e deve começar e terminar com carateres alfanuméricos. Pode incluir travessões, carateres de sublinhado, pontos e carateres de alfanuméricos entre. Não são permitidos espaços.

    ![Criar imagem](media/how-to-create-portal-experiments/create-image.png)

1. Selecione o botão "Criar" para iniciar a criação de imagem. Esta ação irá demorar alguns minutos a concluir, assim que estiver pronto, verá uma mensagem na barra superior.
1. Aceda ao separador "Imagens", selecione a caixa de verificação junto a imagem que pretende implementar e selecione "Criar a implementação". [Saiba mais sobre implementações](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where).

    Existem 2 opções para a implantação.
     + Instância de contentor do Azure (ACI) - Isto é utilizado mais para fins de teste de finalidade em vez de implementação operacional em escala. Lembre-se de que preencha os valores para, pelo menos, um núcleo para _capacidade de reserva de CPU_e, pelo menos, um gigabyte (GB) para _capacidade de reserva de memória_
     + Serviço Kubernetes do Azure (AKS)) – esta opção é para implementação em escala. Terá de ter uma computação com base do AKS pronta.

     ![Imagens: Criar implementação](media/how-to-create-portal-experiments/images-create-deployment.png)

1. Quando terminar, selecione **Criar**. Implementação do modelo, pode demorar alguns minutos para cada termine a execução de pipeline.

1. Já está! Tem um serviço web operacionais para gerar as previsões de indisponibilidade.

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre a aprendizagem automática](concept-automated-ml.md) e Azure Machine Learning.
* [Saiba como consumir um serviço web](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
