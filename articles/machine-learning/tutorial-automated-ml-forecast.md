---
title: Previsão de partilha de bicicletas com experiência automatizada ml
titleSuffix: Azure Machine Learning
description: Aprenda a treinar e implementar um modelo de previsão da procura com machine learning automatizado no estúdio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 01/27/2020
ms.openlocfilehash: 11e0a8a0076fb2e68c379b279f471ff74846df2e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77088243"
---
# <a name="tutorial-forecast-bike-sharing-demand-with-automated-machine-learning"></a>Tutorial: Previsão de partilha de bicicletas com machine learning automatizado
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste tutorial, você usa machine learning automatizado, ou ML automatizado, no estúdio Azure Machine Learning para criar um modelo de previsão de séries temporais para prever a procura de aluguer de um serviço de partilha de bicicletas.

Neste tutorial, aprende-se a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar e carregar um conjunto de dados.
> * Configure e execute uma experiência ml automatizada.
> * Explore os resultados da experiência.
> * Implementar o melhor modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Um espaço de trabalho da edição da Enterprise Azure Machine Learning. Se não tiver um espaço de trabalho, crie um espaço de trabalho da [edição da Enterprise.](how-to-manage-workspace.md) 
    * O machine learning automatizado no estúdio Azure Machine Learning só está disponível para espaços de trabalho da edição da Enterprise. 
* Descarregue o ficheiro de dados [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)

## <a name="get-started-in-azure-machine-learning-studio"></a>Começar no estúdio Azure Machine Learning

Para este tutorial, você cria a sua experiência automatizada ml executada no estúdio Azure Machine Learning, uma interface consolidada que inclui ferramentas de machine learning para realizar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade. O estúdio não é suportado nos navegadores Do Internet Explorer.

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com)

1. Selecione a sua subscrição e o espaço de trabalho que criou.

1. Selecione **Começar**.

1. No painel esquerdo, **selecione ML automatizado** na secção **Autor.**

1. Selecione **+Nova execução automática ml**. 

## <a name="create-and-load-dataset"></a>Criar e carregar conjunto de dados

Antes de configurar a sua experiência, faça o upload do seu ficheiro de dados para o seu espaço de trabalho sob a forma de um conjunto de dados de Aprendizagem automática Azure. Ao fazê-lo, permite-lhe garantir que os seus dados estão devidamente formatados para a sua experiência.

1. No formulário **Select dataset,** selecione **A partir de ficheiros locais** a partir do +Criar a queda do conjunto de **dados.** 

    1. No formulário **de informação Basic,** dê um nome ao seu conjunto de dados e forneça uma descrição opcional. O tipo de conjunto de dados deve ser predefinido para **Tabular,** uma vez que o ML automatizado no estúdio De aprendizagem automática azure atualmente apenas suporta conjuntos de dados tabular.
    
    1. Selecione **Seguinte** na parte inferior esquerda

    1. No formulário de **datastore e seleção de ficheiros,** selecione a loja de dados predefinida que foi configurada automaticamente durante a sua criação do espaço de trabalho, **workspaceblobstore (Armazenamento Azure Blob)**. Este é o local de armazenamento onde irá fazer o upload do seu ficheiro de dados. 

    1. Selecione **Procurar**. 
    
    1. Escolha o ficheiro **bike-no.csv** no seu computador local. Este é o ficheiro que descarregou como [pré-requisito.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)

    1. Selecione **Next**

       Quando o upload estiver concluído, o formulário Definições e pré-visualização é pré-povoado com base no tipo de ficheiro. 
       
    1. Verifique se o formulário **definições e pré-visualização** está povoado da seguinte forma e selecione **Next**.
        
        Campo|Descrição| Valor para tutorial
        ---|---|---
        Formato de ficheiro|Define o layout e o tipo de dados armazenados num ficheiro.| Delimitado
        Delimitador|Um ou mais caracteres para especificar&nbsp; a fronteira entre regiões separadas e independentes em texto simples ou outros fluxos de dados. |Ponto
        Codificação|Identifica o pouco que a tabela de esquemas de caracteres usar para ler o seu conjunto de dados.| UTF-8
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.| Utilize cabeçalhos do primeiro ficheiro
        Linhas de salto | Indica quantas, se houver, são ignoradas linhas no conjunto de dados.| Nenhuma

    1. O formulário **Schema** permite uma maior configuração dos seus dados para esta experiência. 
    
        1. Para este exemplo, opte por ignorar as colunas **casuais** e **registadas.** Estas colunas são uma avaria da coluna **cnt,** por isso, não as incluímos.

        1. Também para este exemplo, deixe as predefinições para as **Propriedades** e **Tipo**. 
        
        1. Selecione **Seguinte**.

    1. No formulário **confirmar** detalhes, verifique as informações correspondem às anteriormente povoadas nas **informações** básicas e configurações e formulários de **pré-visualização.**

    1. Selecione **Criar** para completar a criação do seu conjunto de dados.

    1. Selecione o seu conjunto de dados assim que aparecer na lista.

    1. Selecione **Next**.

## <a name="configure-experiment-run"></a>Configure execução de experiência

Depois de carregar e configurar os seus dados, configure o seu alvo de cálculo remoto e selecione qual a coluna dos seus dados que pretende prever.

1. Povoar o formulário de **execução configure** da seguinte forma:
    1. Introduza um nome de experiência:`automl-bikeshare`

    1. Selecione **o CNT** como a coluna alvo, o que pretende prever. Esta coluna indica o número de alugueres totais de partilha de bicicletas.

    1. Selecione **Criar um novo cálculo** e configurar o seu alvo de cálculo. Ml automatizado apenas suporta a computação Azure Machine Learning. 

        Campo | Descrição | Valor para tutorial
        ----|---|---
        Nome computacional |Um nome único que identifica o contexto da computação.|bike-compute
        Tamanho&nbsp;&nbsp;da máquina virtual| Selecione o tamanho da máquina virtual para a sua computação.|Standard_DS12_V2
        Nós min / Max (em Definições Avançadas)| Para perfilar os dados, deve especificar 1 ou mais nós.|Nósodes: 1<br>Nómáximo: 6
  
        1. Selecione **Criar** para obter o alvo da computação. 

            **Isto leva alguns minutos para ser concluído.** 

        1. Após a criação, selecione o seu novo alvo computacional da lista de lançamentos.

    1. Selecione **Seguinte**.

## <a name="select-task-type-and-settings"></a>Selecione o tipo de tarefa e as definições

Complete a configuração para a sua experiência ml automatizada especificando o tipo de tarefa de aprendizagem automática e as definições de configuração.

1. No formulário de **tipo de tarefa e configurações,** selecione a previsão da série de **tempo** como o tipo de tarefa de aprendizagem automática.

1. Selecione **a data** como **coluna time** e deixe o Grupo **por coluna(s)** em branco. 

    1. Selecione **Ver configurações adicionais** de configuração e povoar os campos da seguinte forma. Estas configurações são para controlar melhor o trabalho de treino. Caso contrário, as predefinições são aplicadas com base na seleção de experiências e dados.

  
        Configurações adicionais&nbsp;|Descrição|Valor&nbsp;&nbsp;para tutorial
        ------|---------|---
        Métrica primária| Métrica de avaliação pela quais o algoritmo de aprendizagem automática será medido.|Erro quadrado da raiz normalizada
        Caracterização automática| Permite o pré-processamento. Isto inclui limpeza automática de dados, preparação e transformação para gerar características sintéticas.| Ativar
        Explique o melhor modelo (pré-visualização)| Mostra automaticamente a explicabilidade do melhor modelo criado por ML automatizado.| Ativar
        Algoritmos bloqueados | Algoritmos que quer excluir do trabalho de formação| Árvores aleatórias extremas
        Definições adicionais de previsão| Estas definições ajudam a melhorar a precisão do seu modelo <br><br> _**Horizonte**_ de previsão : duração do tempo para o futuro que quer prever <br> _**O objetivo da previsão fica atrasado:**_ até onde quer construir os lags de uma variável-alvo <br> _**Janela de rolamento**_ do alvo : especifica o tamanho da janela rolante sobre a qual serão geradas características, como o *máximo, o min* e o *montante.* |Horizonte de previsão: 14 <br> Previsão&nbsp;&nbsp;de intervalos de destino: Nenhum <br> Tamanho&nbsp;&nbsp;da&nbsp;janela de rolamento do alvo: Nenhum
        Critério de saída| Se um critério for cumprido, o trabalho de formação é interrompido. |Tempo&nbsp;&nbsp;de trabalho de formação (horas): 3 <br> Limiar&nbsp;&nbsp;de pontuação métrica: Nenhum
        Validação | Escolha um tipo de validação cruzada e número de testes.|Tipo de validação:<br>&nbsp;k-fold&nbsp;validação cruzada <br> <br> Número de validações: 5
        Simultaneidade| O número máximo de iterações paralelas executadas por iteração| Iterações&nbsp;simultâneas:&nbsp;6
        
        Selecione **Guardar**.

## <a name="run-experiment"></a>Executar experiência

Para executar a sua experiência, selecione **Finish**. O ecrã **de detalhes run** abre com o estado **executar** na parte superior ao lado do número de execução. Este estado atualiza à medida que a experiência progride.

>[!IMPORTANT]
> A preparação leva **10-15 minutos** para preparar a experiência.
> Uma vez em execução, leva **2-3 minutos a mais para cada iteração**.  <br> <br>
> Na produção, provavelmente sais um pouco, pois este processo leva tempo. Enquanto espera, sugerimos que comece a explorar os algoritmos testados no separador **Models** à medida que eles completam. 

##  <a name="explore-models"></a>Explore modelos

Navegue para o separador **Models** para ver os algoritmos (modelos) testados. Por padrão, os modelos são encomendados por pontuação métrica à medida que completam. Para este tutorial, o modelo que marca mais com base na métrica de erro quadrado da **raiz normalizada** escolhida está no topo da lista.

Enquanto espera que todos os modelos de experimentação terminem, selecione o **nome Algoritmo** de um modelo completo para explorar os seus detalhes de desempenho. 

O exemplo que se segue navega através dos detalhes do **Modelo** e dos separadores **de Visualização** para visualizar as propriedades, métricas e gráficos de desempenho do modelo selecionado. 

![Executar detalhes](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>Implementar o modelo

Machine learning automatizado no estúdio Azure Machine Learning permite-lhe implementar o melhor modelo como serviço web em poucos passos. A implantação é a integração do modelo para que possa prever novos dados e identificar potenciais áreas de oportunidade. 

Para esta experiência, a implementação para um serviço web significa que a empresa de partilha de bicicletas tem agora uma solução web iterativa e escalável para a previsão da procura de aluguer de partilha de bicicletas. 

Uma vez concluída a execução, navegue de volta para a página **de detalhes executar** e selecione o separador **Modelos.**

Neste contexto de experiência, o **StackEnsemble** é considerado o melhor modelo, com base na métrica de erro quadrado da **raiz normalizada.**  Implementamos este modelo, mas seja aconselhável, a implementação leva cerca de 20 minutos para ser concluída. O processo de implementação implica várias etapas, incluindo o registo do modelo, a geração de recursos e a configuração para o serviço web.

1. Selecione o **botão de melhor modelo de implantação** no canto inferior esquerdo.

1. Povoar o painel **de modelos da** seguinte forma:

    Campo| Valor
    ----|----
    Nome de implantação| bikeshare-deploy
    Descrição da implantação| implantação da procura de partilha de bicicletas
    Tipo computacional | Selecione Azure Compute Instance (ACI)
    Ativar a autenticação| Desativar. 
    Use ativos de implementação personalizados| Desativar. A desativação permite que o ficheiro do controlador padrão (script de pontuação) e o ficheiro ambiente sejam autogerados. 
    
    Para este exemplo, utilizamos as predefinições fornecidas no menu *Avançado.* 

1. Selecione **Implementar**.  

    Uma mensagem de sucesso verde aparece no topo do ecrã **Run** afirmando que a implementação foi iniciada com sucesso. O progresso da implantação pode ser encontrado  
    no painel de **modelo recomendado** sob o estado de **implantação**.
    
Uma vez que a implementação seja bem sucedida, você tem um serviço web operacional para gerar previsões. 

Proceda aos [**próximos passos**](#next-steps) para saber mais sobre como consumir o seu novo serviço web e testar as suas previsões utilizando o Power BI's construído em suporte azure machine learning.

## <a name="clean-up-resources"></a>Limpar recursos

Os ficheiros de implementação são maiores do que ficheiros de dados e experiências, por isso custam mais para armazenar. Elimine apenas os ficheiros de implementação para minimizar os custos da sua conta, ou se pretender manter o seu espaço de trabalho e ficheiros de experiências. Caso contrário, elimine todo o grupo de recursos, se não pretender utilizar nenhum dos ficheiros.  

### <a name="delete-the-deployment-instance"></a>Eliminar a instância de implantação

Elimine apenas a instância de implantação do estúdio Azure Machine Learning, se quiser manter o grupo de recursos e espaço de trabalho para outros tutoriais e exploração. 

1. Vá ao [estúdio de Aprendizagem automática Azure.](https://ml.azure.com/) Navegue para o seu espaço de trabalho e à esquerda sob o painel **De Ativos,** selecione **Pontos Finais**. 

1. Selecione a implementação que pretende eliminar e selecionar **Eliminar**. 

1. Selecione **Proceder**.

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou ml automatizado no estúdio Azure Machine Learning para criar e implementar um modelo de previsão de séries temporais que prevê a procura de aluguer de partilha de bicicletas. 

Consulte este artigo para obter passos sobre como criar um esquema suportado pelo Power BI para facilitar o consumo do seu serviço web recentemente implantado:

> [!div class="nextstepaction"]
> [Consumir serviços Web](how-to-consume-web-service.md#consume-the-service-from-power-bi)


>[!NOTE]
> Este conjunto de dados de partilha de bicicletas foi modificado para este tutorial. Este conjunto de dados foi disponibilizado como parte de um [concurso kaggle](https://www.kaggle.com/c/bike-sharing-demand/data) e estava originalmente disponível via [Capital Bikeshare.](https://www.capitalbikeshare.com/system-data) Também pode ser encontrado na base de dados de [aprendizagem automática da UCI.](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset)<br><br>
> Fonte: Fanaee-T, Hadi, e Gama, João, Rotulagem de eventos combinando detetores de conjuntos e conhecimento de fundo, Progress in Artificial Intelligence (2013): pp. 1-15, Springer Berlin Heidelberg.
