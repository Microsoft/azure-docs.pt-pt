---
title: 'Tutorial: Previsão da procura & AutoML'
titleSuffix: Azure Machine Learning
description: Aprenda a treinar e implementar um modelo de previsão de procura com aprendizagem automática de máquinas no estúdio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: 2653161b5828d89858234a9ca98fe432e0eacb5c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879364"
---
# <a name="tutorial-forecast-demand-with-automated-machine-learning"></a>Tutorial: Previsão da procura com aprendizagem automática de máquinas


Neste tutorial, você usa machine learning automatizado, ou ML automatizado, no estúdio Azure Machine Learning para criar um modelo de previsão de séries temporizadas para prever a procura de aluguer de um serviço de partilha de bicicletas.

Para um exemplo de modelo de classificação, consulte [Tutorial: Criar um modelo de classificação com ML automatizado em Aprendizagem automática de máquinas.](tutorial-first-experiment-automated-ml.md)

Neste tutorial, aprende-se a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar e carregar um conjunto de dados.
> * Configure e execute uma experiência automatizada de ML.
> * Especifique as definições de previsão.
> * Explore os resultados da experiência.
> * Implemente o melhor modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Ver [Criar um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md). 

* Descarregue o ficheiro de dados [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)

## <a name="get-started-in-azure-machine-learning-studio"></a>Começar no estúdio Azure Machine Learning

Para este tutorial, você cria a sua experiência automatizada de ML executada no estúdio Azure Machine Learning, uma interface web consolidada que inclui ferramentas de machine learning para executar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade. O estúdio não é suportado nos navegadores do Internet Explorer.

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com).

1. Selecione a sua subscrição e o espaço de trabalho que criou.

1. **Selecione Começar**.

1. No painel esquerdo, selecione **ML automatizado** na secção **Autor.**

1. Selecione **+Nova execução ML automatizada**. 

## <a name="create-and-load-dataset"></a>Criar e carregar conjunto de dados

Antes de configurar a sua experiência, faça o upload do seu ficheiro de dados para o seu espaço de trabalho sob a forma de um conjunto de dados Azure Machine Learning. Ao fazê-lo, permite-lhe garantir que os seus dados são formatados adequadamente para a sua experiência.

1. No formulário **Select dataset,** selecione **From local files** from the  **+Create dataset** drop-down. 

    1. No formulário **de informações Básicas,** forneça um nome ao seu conjunto de dados e forneça uma descrição opcional. O tipo de conjunto de dados deve ser padrão para **Tabular,** uma vez que mL automatizado no estúdio Azure Machine Learning suporta atualmente apenas conjuntos de dados tabulares.
    
    1. **Selecione** Em seguida, no canto inferior esquerdo

    1. No formulário **de datastore e seleção de ficheiros,** selecione a loja de dados predefinida que foi configurada automaticamente durante a criação do seu espaço de **trabalho, workspaceblobstore (Azure Blob Storage)**. Este é o local de armazenamento onde irá fazer o upload do seu ficheiro de dados. 

    1. Selecione **Procurar**. 
    
    1. Escolha o ficheiro **bike-no.csv** no seu computador local. Este é o ficheiro que descarregou como [pré-requisito.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)

    1. Selecione **Seguinte**

       Quando o upload estiver concluído, o formulário de definições e pré-visualização é pré-povoado com base no tipo de ficheiro. 
       
    1. Verifique se as Definições e o formulário **de pré-visualização são** preenchidos da seguinte forma e selecione **Next**.
        
        Campo|Descrição| Valor para tutorial
        ---|---|---
        Formato do ficheiro|Define o layout e o tipo de dados armazenados num ficheiro.| Delimitado
        Delimitador|Um ou mais caracteres para especificar a fronteira entre &nbsp; regiões separadas e independentes em texto simples ou outros fluxos de dados. |Ponto
        Encoding|Identifica o bit da tabela de esquemas de caracteres para usar para ler o seu conjunto de dados.| UTF-8
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.| Use cabeçalhos do primeiro ficheiro
        Saltar filas | Indica quantas, se houver, são ignoradas no conjunto de dados.| Nenhum

    1. O **formulário Schema** permite uma configuração adicional dos seus dados para esta experiência. 
    
        1. Para este exemplo, opte por ignorar as colunas **casuais** e **registadas.** Estas colunas são uma desagregação da coluna  **cnt,** então, portanto, não as incluímos.

        1. Também para este exemplo, deixe as predefinições para os **Imóveis** e **Tipo.** 
        
        1. Selecione **Seguinte**.

    1. No formulário **De Detalhes Confirm,** verifique as informações correspondem ao que foi previamente preenchido nas **informações** básicas e **definições e formulários de pré-visualização.**

    1. Selecione **Criar** para completar a criação do seu conjunto de dados.

    1. Selecione o seu conjunto de dados assim que aparecer na lista.

    1. Selecione  **Seguinte**.

## <a name="configure-run"></a>Configure corrida

Depois de carregar e configurar os seus dados, configure o seu alvo de computação remota e selecione qual a coluna nos seus dados que pretende prever.

1. Povoar o formulário **de execução Configure** da seguinte forma:
    1. Insira um nome de experiência: `automl-bikeshare`

    1. Selecione **cnt** como a coluna-alvo, o que pretende prever. Esta coluna indica o número total de alugueres de partilha de bicicletas.

    1. Selecione **Crie um novo compute e** configuure o seu alvo de computação. O ML automatizado suporta apenas o cálculo Azure Machine Learning. 

        1. Preencha o formulário **Máquina Virtual** para configurar o seu cálculo.

            Campo | Descrição | Valor para tutorial
            ----|---|---
            Prioridade da máquina virtual &nbsp; &nbsp; |Selecione qual a prioridade que a sua experiência deve ter| Dedicada
            Tipo de máquina virtual &nbsp; &nbsp;| Selecione o tipo de máquina virtual para o seu cálculo.|CPU (Unidade Central de Processamento)
            Tamanho da máquina virtual &nbsp; &nbsp;| Selecione o tamanho da máquina virtual para o seu cálculo. Uma lista de tamanhos recomendados é fornecida com base nos seus dados e tipo de experiência. |Standard_DS12_V2
        
        1. Selecione **Seguinte** para preencher o **formulário configurações configurações de configuração**.
        
             Campo | Descrição | Valor para tutorial
            ----|---|---
            Nome da computação |  Um nome único que identifica o seu contexto computacional. | bike-compute
            Nós min / max| Para perfilar os dados, tem de especificar 1 ou mais nós.|Min nosdes: 1<br>Nó máximo: 6
            Ocioso segundos antes de baixar a escala | Tempo de marcha lenta antes do cluster ser automaticamente reduzido à contagem mínima de nós.|120 (padrão)
            Definições avançadas | Configurações para configurar e autorizar uma rede virtual para a sua experiência.| Nenhum 
  
        1. Selecione **Criar** para obter o alvo do cálculo. 

            **Isto leva alguns minutos para ser concluído.** 

        1. Após a criação, selecione o seu novo alvo de computação a partir da lista de drop-down.

    1. Selecione **Seguinte**.

## <a name="select-forecast-settings"></a>Selecione definições de previsão

Complete a configuração para a sua experiência automatizada de ML especificando as definições de tipo de tarefa e configuração de machine learning.

1. No tipo de tarefa e no formulário **de definições,** selecione **a previsão da série tempo como** o tipo de tarefa de aprendizagem automática.

1. Selecione **a data** como a **sua coluna Tempo** e deixe os **identificadores da série tempo** em branco. 

1. O horizonte de **previsão** é o tempo que se pretende prever no futuro.  Deselelect Autodetect e tipo 14 no campo. 

1. Selecione **Ver as definições de configuração adicionais** e povoar os campos da seguinte forma. Estas definições são para controlar melhor o trabalho de treino e especificar as definições para a sua previsão. Caso contrário, os padrão são aplicados com base na seleção de experiências e dados.

    &nbsp;Configurações adicionais|Descrição|Valor &nbsp; para &nbsp; tutorial
    ------|---------|---
    Métrica primária| Métrica de avaliação pela que o algoritmo de aprendizagem automática será medido.|Raiz normalizada significa erro quadrado
    Explicar o melhor modelo| Mostra automaticamente a explicabilidade do melhor modelo criado pela ML automatizada.| Ativar
    Algoritmos bloqueados | Algoritmos que pretende excluir do trabalho de formação| Árvores aleatórias extremas
    Definições de previsão adicionais| Estas definições ajudam a melhorar a precisão do seu modelo. <br><br> _**Previsão de meta fica para trás:**_ até onde quer construir os lags da variável-alvo <br> _**Janela de rolamento do alvo**_: especifica o tamanho da janela rolante sobre as características, tais como o *máximo, min* e *soma,* que serão geradas. | <br><br>Previsões &nbsp; de metas &nbsp; desfasamento: Nenhuma <br> Tamanho &nbsp; da janela de rolamento do &nbsp; &nbsp; alvo: Nenhum
    Critério de saída| Se um critério for cumprido, o trabalho de formação é interrompido. |Tempo &nbsp; de trabalho de &nbsp; formação (horas): 3 <br> Limiar de &nbsp; pontuação &nbsp; métrica: Nenhum
    Validação | Escolha um tipo de validação cruzada e número de testes.|Tipo de validação:<br>&nbsp;k-dobrar &nbsp; validação cruzada <br> <br> Número de validações: 5
    Simultaneidade| O número máximo de iterações paralelas executadas por iteração| &nbsp;Iterações &nbsp; concorrâneas máximas: 6
    
    Selecione **Guardar**.

## <a name="run-experiment"></a>Experiência de execução

Para executar a sua experiência, **selecione Finish**. O ecrã **de detalhes do Run**  abre com o estado de **Execução** na parte superior ao lado do número de execução. Este estado atualiza à medida que a experiência progride. As notificações também aparecem no canto superior direito do estúdio, para informá-lo sobre o estado da sua experiência.

>[!IMPORTANT]
> A preparação leva **10 a 15 minutos** para preparar a experiência.
> Uma vez em funcionamento, demora **mais 2 a 3 minutos para cada iteração**.<br> <br>
> Na produção, provavelmente afastar-se-ia um pouco enquanto este processo leva tempo. Enquanto espera, sugerimos que comece a explorar os algoritmos testados no separador **Modelos** à medida que eles se completam. 

##  <a name="explore-models"></a>Explore modelos

Navegue no separador **Modelos** para ver os algoritmos (modelos) testados. Por predefinição, os modelos são encomendados por pontuação métrica à medida que completam. Para este tutorial, o modelo que obtém a maior pontuação com base na **raiz normalizada** escolhida significa métrica de erro quadrado está no topo da lista.

Enquanto espera que todos os modelos de experiência terminem, selecione o **nome Algoritmo** de um modelo completo para explorar os seus detalhes de desempenho. 

O exemplo a seguir navega através dos separadores **Detalhes** e **Métricas** para ver as propriedades, métricas e gráficos de desempenho do modelo selecionado. 

![Detalhe de execução](./media/tutorial-automated-ml-forecast/explore-models.gif)

## <a name="deploy-the-model"></a>Implementar o modelo

A aprendizagem automática de máquinas no estúdio Azure Machine Learning permite-lhe implementar o melhor modelo como serviço web em alguns passos. A implementação é a integração do modelo para que possa prever novos dados e identificar potenciais áreas de oportunidade. 

Para esta experiência, a implementação para um serviço web significa que a empresa de partilha de bicicletas tem agora uma solução web iterativa e escalável para a previsão da procura de aluguer de partilha de bicicletas. 

Uma vez concluída a execução, volte à página de execução dos pais selecionando **Run 1** na parte superior do ecrã.

Na secção de resumo do **modelo Best,** o **StackEnsemble** é considerado o melhor modelo no contexto desta experiência, com base na **raiz normalizada médiamétrica de erro quadrado.**  

Implementamos este modelo, mas seja avisado, a implantação leva cerca de 20 minutos para ser concluída. O processo de implementação implica várias etapas, incluindo registar o modelo, gerar recursos e configurá-los para o serviço web.

1. Selecione **StackEnsemble** para abrir a página específica do modelo.

1. Selecione o botão **'Implementar'** localizado na área superior esquerda do ecrã.

1. Povoar o **painel de modelos** da seguinte forma:

    Campo| Valor
    ----|----
    Nome de implantação| bikeshare-deploy
    Descrição da implantação| implementação da procura de partilha de bicicletas
    Tipo de computação | Selecione Azure Compute Instance (ACI)
    Ative a autenticação| Desativar. 
    Utilize ativos de implantação personalizados| Desativar. A desativação permite que o ficheiro do controlador predefinido (script de pontuação) e o ficheiro ambiente sejam autogeridos. 
    
    Para este exemplo, utilizamos os predefinidos fornecidos no menu *Advanced.* 

1. Selecione **Implementar**.  

    Uma mensagem de sucesso verde aparece no topo do ecrã **Run** indicando que a implementação foi iniciada com sucesso. O progresso da implantação pode ser encontrado no **painel de resumo** do Modelo sob o estado de **implantação**.
    
Uma vez que a implementação tenha sucesso, você tem um serviço web operacional para gerar previsões. 

Siga para os [**próximos passos**](#next-steps) para saber mais sobre como consumir o seu novo serviço web e teste as suas previsões usando o suporte de Power BI incorporado no suporte Azure Machine Learning.

## <a name="clean-up-resources"></a>Limpar os recursos

Os ficheiros de implantação são maiores do que os ficheiros de dados e experiências, pelo que custam mais para armazenar. Elimine apenas os ficheiros de implementação para minimizar os custos da sua conta, ou se pretender manter o seu espaço de trabalho e experimentar ficheiros. Caso contrário, elimine todo o grupo de recursos, se não pretender utilizar nenhum dos ficheiros.  

### <a name="delete-the-deployment-instance"></a>Eliminar a instância de implantação

Elimine apenas a instância de implantação do estúdio Azure Machine Learning, se quiser manter o grupo de recursos e o espaço de trabalho para outros tutoriais e exploração. 

1. Vá ao [estúdio Azure Machine Learning.](https://ml.azure.com/) Navegue para o seu espaço de trabalho e à esquerda sob o painel **De ativos,** selecione **Endpoints**. 

1. Selecione a implementação deseja eliminar e selecione **Eliminar**. 

1. **Selecione Proceder**.

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximos passos

Neste tutorial, você usou ML automatizado no estúdio Azure Machine Learning para criar e implementar um modelo de previsão de séries temporizadas que prevê a procura de aluguer de partilha de bicicletas. 

Consulte este artigo para ver como criar um esquema suportado por Power BI para facilitar o consumo do seu serviço web recém-implementado:

> [!div class="nextstepaction"]
> [Consumir serviços Web](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ Saiba mais sobre [aprendizagem automática de máquinas.](concept-automated-ml.md)
+ Para obter mais informações sobre métricas e gráficos de classificação, consulte o artigo [de resultados automatizados de machine learning da Máquina.](how-to-understand-automated-ml.md)
+ Saiba mais sobre [a exibição.](how-to-configure-auto-features.md#featurization)
+ Saiba mais sobre [perfis de dados.](how-to-connect-data-ui.md#profile)

>[!NOTE]
> Este conjunto de dados de partilha de bicicletas foi modificado para este tutorial. Este conjunto de dados foi disponibilizado como parte de um [concurso kaggle](https://www.kaggle.com/c/bike-sharing-demand/data) e estava originalmente disponível via [Capital Bikeshare.](https://www.capitalbikeshare.com/system-data) Também pode ser encontrado dentro da [Base de Dados de Aprendizagem automática da UCI.](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset)<br><br>
> Fonte: Fanaee-T, Hadi, e Gama, João, Rotulagem de evento combinando detetores de conjunto e conhecimento de fundo, Progress in Artificial Intelligence (2013): pp. 1-15, Springer Berlin Heidelberg.