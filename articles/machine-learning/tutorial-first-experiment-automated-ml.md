---
title: Criar modelos automatizados de classificação ML
titleSuffix: Azure Machine Learning
description: Saiba como treinar e implementar modelos de classificação com a interface automatizada de machine learning (ML) automatizada do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 02/04/2020
ms.openlocfilehash: 84d539f35919293522f05abdeabeca936138c140
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79081629"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Tutorial: Criar um modelo de classificação com ML automatizado em Aprendizagem automática de Máquinas Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste tutorial, aprende-se a criar um modelo de classificação básico sem escrever uma única linha de código utilizando a interface automatizada de machine learning da Azure Machine Learning. Este modelo de classificação prevê se um cliente subscreverá um depósito a prazo fixo com uma instituição financeira.

Com machine learning automatizado, pode automatizar tarefas intensivas de tempo. A aprendizagem automática de máquinas iterates rapidamente sobre muitas combinações de algoritmos e hiperparâmetros para ajudá-lo a encontrar o melhor modelo baseado numa métrica de sucesso da sua escolha.

Neste tutorial, aprende-se a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Crie uma área de trabalho do Azure Machine Learning.
> * Execute uma experiência automatizada de aprendizagem automática de máquinas.
> * Ver detalhes da experiência.
> * Implemente o modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

* Faça o download do ficheiro de dados [**bankmarketing_train.csv.** ](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) A coluna **y** indica se um cliente subscreveu um depósito a prazo fixo, que é mais tarde identificado como a coluna alvo para previsões neste tutorial. 

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implementar modelos de aprendizagem automática. Liga o seu grupo de subscrição e recursos Azure a um objeto facilmente consumido no serviço. 

Cria um espaço de trabalho através do portal Azure, uma consola baseada na web para gerir os seus recursos Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Tome nota do seu espaço de **trabalho** e **subscrição.** Vai precisar disto para garantir que cria a sua experiência no lugar certo. 

## <a name="create-and-run-the-experiment"></a>Criar e executar a experiência

Completa as seguintes etapas de experimentação através da aprendizagem de máquinas Azure em https://ml.azure.com, uma interface web consolidada que inclui ferramentas de machine learning para realizar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade. Esta interface não é suportada nos navegadores do Internet Explorer.

1. Inscreva-se na Azure Machine Learning em https://ml.azure.com.

1. Selecione a sua subscrição e o espaço de trabalho que criou.

1. Selecione **Começar**.

1. No painel esquerdo, **selecione ML automatizado** na secção **Autor.**

   Uma vez que esta é a sua primeira experiência automatizada em ML, você verá uma lista vazia e links para documentação.

   ![Página Introdução](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Selecione **Nova execução automática ml**. 

1. Crie um novo conjunto de dados selecionando **a partir de ficheiros locais** a partir do **+Create dataset** drop-down. 

    1. No formulário **de informação Basic,** dê um nome ao seu conjunto de dados e forneça uma descrição opcional. A interface ML automatizada suporta atualmente apenas tabularDatasets, pelo que o tipo de conjunto de dados deve ser predefinido para *Tabular*.

    1. Selecione **Seguinte** na parte inferior esquerda

    1. No formulário de **datastore e seleção de ficheiros,** selecione a loja de dados predefinida que foi configurada automaticamente durante a sua criação do espaço de trabalho, **workspaceblobstore (Armazenamento Azure Blob)** . É aqui que irá fazer o upload do seu ficheiro de dados para o disponibilizar ao seu espaço de trabalho.

    1. Selecione **Procurar**.
    
    1. Escolha o ficheiro **bankmarketing_train.csv** no seu computador local. Este é o ficheiro que descarregou como [pré-requisito.](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)

    1. Dê ao seu conjunto de dados um nome único e forneça uma descrição opcional. 

    1. Selecione **Next** na parte inferior esquerda, para carregá-lo para o recipiente predefinido que foi configurado automaticamente durante a sua criação do espaço de trabalho.  
    
       Quando o upload estiver concluído, o formulário Definições e pré-visualização é pré-povoado com base no tipo de ficheiro. 
       
    1. Verifique se o formulário **definições e pré-visualização** está povoado da seguinte forma e selecione **Next**.
        
        Campo|Descrição| Valor para tutorial
        ---|---|---
        Formato de ficheiro|Define o layout e o tipo de dados armazenados num ficheiro.| Delimitado
        Delimitador|Um ou mais caracteres para especificar a fronteira entre&nbsp; regiões separadas e independentes em texto simples ou outros fluxos de dados. |Víris
        Codificação|Identifica o pouco que a tabela de esquemas de caracteres usar para ler o seu conjunto de dados.| UTF-8
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.| Todos os ficheiros têm os mesmos cabeçalhos
        Linhas de salto | Indica quantas, se houver, são ignoradas linhas no conjunto de dados.| Nenhum

    1. O formulário **Schema** permite uma maior configuração dos seus dados para esta experiência. Para este exemplo, selecione o interruptor de alternância para a **funcionalidade day_of_week,** de modo a não incluí-lo para esta experiência. Selecione **Seguinte**.

        ![Configuração do separador de pré-visualização](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. No formulário **confirmar** detalhes, verifique as informações correspondem às anteriormente povoadas nas **informações** básicas e configurações e formulários de **pré-visualização.**
    1. Selecione **Criar** para completar a criação do seu conjunto de dados.
    1. Selecione o seu conjunto de dados assim que aparecer na lista.
    1. Reveja a **pré-visualização de Dados** para garantir que não incluiu **day_of_week** então, selecione **OK**.

    1. Selecione **Seguinte**.

1. Povoar o formulário **Configure Run** da seguinte forma:
    1. Introduza este nome da experiência: `my-1st-automl-experiment`

    1. Selecione **y** como a coluna do alvo, o que pretende prever. Esta coluna indica se o cliente subscreveu ou não um depósito a prazo.
    1. Selecione **Criar um novo cálculo** e configurar o seu alvo de cálculo. Um alvo computacional é um ambiente de recursos local ou baseado na nuvem usado para executar o seu script de treino ou hospedar a sua implementação de serviço. Para esta experiência, usamos um cálculo baseado em nuvem. 

        Campo | Descrição | Valor para tutorial
        ----|---|---
        Nome computacional |Um nome único que identifica o contexto da computação.|automl-compute
        Tamanho&nbsp;máquina de&nbsp;virtual| Selecione o tamanho da máquina virtual para a sua computação.|Standard_DS12_V2
        Nós min / Max (em Definições Avançadas)| Para perfilar os dados, deve especificar 1 ou mais nós.|Nósodes: 1<br>Nómáximo: 6
  
        1. Selecione **Criar** para obter o alvo da computação. 

            **Isto leva alguns minutos para ser concluído.** 

        1. Após a criação, selecione o seu novo alvo computacional da lista de lançamentos.

    1. Selecione **Seguinte**.

1. No formulário de **tipo de tarefa e configurações,** selecione **Classificação** como o tipo de tarefa de aprendizagem automática.

    1. Selecione **Ver configurações adicionais** de configuração e povoar os campos da seguinte forma. Estas configurações são para controlar melhor o trabalho de treino. Caso contrário, as predefinições são aplicadas com base na seleção de experiências e dados.

        >[!NOTE]
        > Neste tutorial, você não vai definir uma pontuação métrica ou núcleos máximos por limiar de iterações. Nem vai bloquear algoritmos de serem testados.
   
        Configurações adicionais de&nbsp;|Descrição|Valor&nbsp;para tutoriais&nbsp;
        ------|---------|---
        Métrica primária| Métrica de avaliação pela quais o algoritmo de aprendizagem automática será medido.|AUC_weighted
        Caracterização automática| Permite o pré-processamento. Isto inclui limpeza automática de dados, preparação e transformação para gerar características sintéticas.| Ativar
        Algoritmos bloqueados | Algoritmos que quer excluir do trabalho de formação| Nenhum
        Critério de saída| Se um critério for cumprido, o trabalho de formação é interrompido. |Formação&nbsp;trabalho&nbsp;horário (horas): 1 <br> Pontuação de&nbsp;métrica&nbsp;limiar: Nenhum
        Validação | Escolha um tipo de validação cruzada e número de testes.|Tipo de validação:<br>&nbsp;k-fold&nbsp;validação cruzada <br> <br> Número de validações: 2
        Simultaneidade| O número máximo de iterações paralelas executadas por iteração| Iterações&nbsp;simultâneas&nbsp;: 5
        
        Selecione **Guardar**.

1. Selecione **Terminar** para executar a experiência. O ecrã **'Executar Detail'** abre com o **estado de Execução** na parte superior à medida que a preparação da experiência começa.

>[!IMPORTANT]
> A preparação leva **10-15 minutos** para preparar a experiência.
> Uma vez em execução, leva **2-3 minutos a mais para cada iteração**.  
> Selecione **Refresh** periodicamente para ver o estado da execução à medida que a experiência progride.
>
> Na produção, provavelmente ias-te embora um pouco. Mas para este tutorial, sugerimos que comece a explorar os algoritmos testados no separador **Models** à medida que completam enquanto os outros ainda estão em execução. 

##  <a name="explore-models"></a>Explore modelos

Navegue para o separador **Models** para ver os algoritmos (modelos) testados. Por padrão, os modelos são encomendados por pontuação métrica à medida que completam. Para este tutorial, o modelo que marca mais com base na métrica **de AUC_weighted** escolhida está no topo da lista.

Enquanto espera que todos os modelos de experimentação terminem, selecione o **nome Algoritmo** de um modelo completo para explorar os seus detalhes de desempenho. 

Os seguintes navegam através dos detalhes do **Modelo** e dos separadores **de Visualização** para visualizar as propriedades, métricas e gráficos de desempenho do modelo selecionado. 

![Executar detalhes da iteração](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>Implementar o melhor modelo

A interface automatizada de machine learning permite-lhe implementar o melhor modelo como serviço web em poucos passos. A implantação é a integração do modelo para que possa prever novos dados e identificar potenciais áreas de oportunidade. 

Para esta experiência, a implantação para um serviço web significa que a instituição financeira tem agora uma solução web iterativa e escalável para identificar potenciais clientes de depósitos a prazo fixo. 

Uma vez concluída a execução, navegue de volta para a página **'Executar Detalhes'** e selecione o separador **Modelos.**

Neste contexto de experiência, o **VoteEnsemble** é considerado o melhor modelo, com base na **métrica AUC_weighted.**  Implementamos este modelo, mas seja aconselhável, a implementação leva cerca de 20 minutos para ser concluída. O processo de implementação implica várias etapas, incluindo o registo do modelo, a geração de recursos e a configuração para o serviço web.

1. Selecione o **botão de melhor modelo de implantação** no canto inferior esquerdo.

1. Povoar o painel **de modelos da** seguinte forma:

    Campo| Valor
    ----|----
    Nome de implantação| my-automl-deploy
    Descrição da implantação| A minha primeira implementação automatizada de experiências de aprendizagem automática
    Tipo computacional | Selecione Azure Compute Instance (ACI)
    Ativar a autenticação| Desativar. 
    Utilize implementações personalizadas| Desativar. Permite que o ficheiro de condutor padrão (script de pontuação) e o ficheiro ambiente sejam autogerados. 
    
    Para este exemplo, utilizamos as predefinições fornecidas no menu *Avançado.* 

1. Selecione **Implementar**.  

    Uma mensagem de sucesso verde aparece na parte superior do ecrã **Executar,** e no painel do **modelo Recomendado,** uma mensagem de estado aparece sob o estado de **Implantação**. Selecione **Refresh** periodicamente para verificar o estado de implantação.
    
Agora tem um serviço web operacional para gerar previsões. 

Proceda aos [**Próximos Passos**](#next-steps) para saber mais sobre como consumir o seu novo serviço web e testar as suas previsões utilizando o Power BI's construído em suporte azure machine learning.

## <a name="clean-up-resources"></a>Limpar recursos

Os ficheiros de implementação são maiores do que ficheiros de dados e experiências, por isso custam mais para armazenar. Elimine apenas os ficheiros de implementação para minimizar os custos da sua conta, ou se pretender manter o seu espaço de trabalho e ficheiros de experiências. Caso contrário, elimine todo o grupo de recursos, se não pretender utilizar nenhum dos ficheiros.  

### <a name="delete-the-deployment-instance"></a>Eliminar a instância de implantação

Elimine apenas a instância de implantação do Azure Machine Learning em https://ml.azure.com/, se quiser manter o grupo de recursos e espaço de trabalho para outros tutoriais e exploração. 

1. Vá ao Azure Machine Learning em https://ml.azure.com/. Navegue para o seu espaço de trabalho e à esquerda sob o painel **De Ativos,** selecione **Pontos Finais**. 

1. Selecione a implementação que pretende eliminar e selecionar **Eliminar**. 

1. Selecione **Proceder**.

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial automatizado de machine learning, utilizou a interface ML automatizada do Azure Machine Learning para criar e implementar um modelo de classificação. Consulte estes artigos para obter mais informações e próximos passos:

> [!div class="nextstepaction"]
> [Consumir um serviço web](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Saiba mais sobre [aprendizagem automática de máquinas.](concept-automated-ml.md)
+ Para obter mais informações sobre métricas e gráficos de classificação, consulte o artigo de [resultados automatizados](how-to-understand-automated-ml.md#classification) de machine learning.+ Saiba mais sobre [a caracterização](how-to-create-portal-experiments.md#featurization).
+ Saiba mais sobre [o perfil de dados.](how-to-create-portal-experiments.md#profile)


>[!NOTE]
> Este conjunto de dados de Marketing Bancário é disponibilizado ao abrigo da [Licença Creative Commons (CCO: Domínio Público).](https://creativecommons.org/publicdomain/zero/1.0/) Quaisquer direitos em conteúdo individual da base de dados são licenciados na Licença de [Conteúdo da Base](https://creativecommons.org/publicdomain/zero/1.0/) de Dados e disponíveis em [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Este conjunto de dados estava originalmente disponível na Base de Dados de [Aprendizagem automática da UCI.](https://archive.ics.uci.edu/ml/datasets/bank+marketing)<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez e P. Rita. Uma abordagem baseada em dados para prever o sucesso do Telemarketing Bancário. Sistemas de Apoio à Decisão, Elsevier, 62:22-31, junho de 2014.
