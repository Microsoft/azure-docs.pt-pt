---
title: Criar seu primeiro experimento de ML automatizado
titleSuffix: Azure Machine Learning
description: Saiba como treinar e implantar um modelo de classificação com o Machine Learning automatizado no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 02/04/2020
ms.openlocfilehash: 70fcdb1c22664a0bd3091fea88c8e23e3d1b81e5
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048289"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Tutorial: criar seu primeiro modelo de classificação com o Machine Learning automatizado
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste tutorial, você aprenderá a criar seu primeiro experimento de aprendizado de máquina automatizado por meio do Azure Machine Learning Studio sem escrever uma única linha de código. Este exemplo cria um modelo de classificação para prever se um cliente assinará um depósito de termo fixo com uma instituição financeira.

Com o Machine Learning automatizado, você pode automatizar tarefas demoradas. O aprendizado de máquina automatizado itera rapidamente em várias combinações de algoritmos e hiperparâmetros para ajudá-lo a encontrar o melhor modelo com base em uma métrica de sucesso de sua escolha.

Neste tutorial, você aprenderá a executar as seguintes tarefas:

> [!div class="checklist"]
> * Crie uma área de trabalho do Azure Machine Learning.
> * Execute um experimento de aprendizado de máquina automatizado.
> * Exibir detalhes do experimento.
> * Implemente o modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://aka.ms/AMLFree)

* Faça o download do ficheiro de dados [**bankmarketing_train.csv.** ](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) A coluna **y** indica se um cliente subscreveu um depósito a prazo fixo, que é mais tarde identificado como a coluna alvo para previsões neste tutorial. 

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de aprendizado de máquina. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no serviço. 

Você cria um espaço de trabalho por meio do Azure Machine Learning Studio, um console baseado na Web para gerenciar seus recursos do Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Tome nota do seu espaço de **trabalho** e **subscrição.** Você precisará delas para garantir que crie seu experimento no lugar certo. 

## <a name="create-and-run-the-experiment"></a>Criar e executar o experimento

Você conclui as seguintes etapas de configuração e execução do experimento no Azure Machine Learning Studio, uma interface consolidada que inclui ferramentas de Machine Learning para executar cenários de ciência de dados para profissionais de ciência de dados de todos os níveis de habilidade. Não há suporte para o estúdio em navegadores do Internet Explorer.

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com)

1. Selecione sua assinatura e o espaço de trabalho que você criou.

1. Selecione **Começar**.

1. No painel esquerdo, **selecione ML automatizado** na secção **Autor.**

   Como este é seu primeiro experimento de ML automatizado, você verá uma lista vazia e links para a documentação.

   ![Azure Machine Learning studio](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Selecione **Nova execução automática ml**. 

1. Crie um novo conjunto de dados selecionando **a partir de ficheiros locais** a partir do **+Create dataset** drop-down. 

    1. No formulário **de informação Basic,** dê um nome ao seu conjunto de dados e forneça uma descrição opcional. O ML automatizado no estúdio Azure Machine Learning atualmente apenas suporta conjuntos de dados tabular, pelo que o tipo de conjunto de dados deve ser padrão para Tabular.

    1. Selecione **Seguinte** na parte inferior esquerda

    1. No formulário de **datastore e seleção de ficheiros,** selecione a loja de dados predefinida que foi configurada automaticamente durante a sua criação do espaço de trabalho, **workspaceblobstore (Armazenamento Azure Blob)** . É aqui que irá fazer o upload do seu ficheiro de dados para o disponibilizar ao seu espaço de trabalho.

    1. Selecione **Procurar**.
    
    1. Escolha o ficheiro **bankmarketing_train.csv** no seu computador local. Este é o ficheiro que descarregou como [pré-requisito.](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)

    1. Dê um nome exclusivo ao seu conjunto de dado e forneça uma descrição opcional. 

    1. Selecione **Next** na parte inferior esquerda, para carregá-lo para o recipiente predefinido que foi configurado automaticamente durante a sua criação do espaço de trabalho.  
    
       Quando o upload for concluído, as configurações e o formulário de visualização serão preenchidos previamente com base no tipo de arquivo. 
       
    1. Verifique se o formulário **definições e pré-visualização** está povoado da seguinte forma e selecione **Next**.
        
        Campo|Descrição| Valor do tutorial
        ---|---|---
        Formato de ficheiro|Define o layout e o tipo de dados armazenados em um arquivo.| Delimitados
        Delimitador|Um ou mais caracteres para especificar a fronteira entre&nbsp; regiões separadas e independentes em texto simples ou outros fluxos de dados. |Víris
        Codificação|Identifica o bit para a tabela de esquema de caractere a ser usada para ler seu conjunto de seus.| UTF-8
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de uma, se houver, serão tratados.| Todos os arquivos têm os mesmos cabeçalhos
        Ignorar linhas | Indica quantas linhas, se houver, são ignoradas no conjunto de registros.| Nenhum

    1. O formulário **Schema** permite uma maior configuração dos seus dados para esta experiência. Para este exemplo, selecione o interruptor de alternância para a **funcionalidade day_of_week,** de modo a não incluí-lo para esta experiência. Selecione **Seguinte**.

        ![Configuração da guia de visualização](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. No formulário **confirmar** detalhes, verifique as informações correspondem às anteriormente povoadas nas **informações** básicas e configurações e formulários de **pré-visualização.**
    1. Selecione **Criar** para completar a criação do seu conjunto de dados.
    1. Selecione o conjunto de seus conjuntos de seus quando ele aparecer na lista.
    1. Reveja a **pré-visualização de Dados** para garantir que não incluiu **day_of_week** então, selecione **OK**.

    1. Selecione **Seguinte**.

1. Povoar o formulário **Configure Run** da seguinte forma:
    1. Introduza este nome da experiência: `my-1st-automl-experiment`

    1. Selecione **y** como a coluna do alvo, o que pretende prever. Esta coluna indica se o cliente assinou um termo de depósito ou não.
    1. Selecione **Criar um novo cálculo** e configurar o seu alvo de cálculo. Um destino de computação é um ambiente de recursos local ou baseado em nuvem usado para executar o script de treinamento ou hospedar sua implantação de serviço. Para este experimento, usamos uma computação baseada em nuvem. 

        Campo | Descrição | Valor do tutorial
        ----|---|---
        Nome da computação |Um nome exclusivo que identifica o contexto de computação.|automl-computação
        Tamanho&nbsp;máquina de&nbsp;virtual| Selecione o tamanho da máquina virtual para sua computação.|Standard_DS12_V2
        Nós mín./máx. (em configurações avançadas)| Para criar o perfil de dados, você deve especificar um ou mais nós.|Nós mínimos: 1<br>Máximo de nós: 6
  
        1. Selecione **Criar** para obter o alvo da computação. 

            **Isto leva alguns minutos para ser concluído.** 

        1. Após a criação, selecione o novo destino de computação na lista suspensa.

    1. Selecione **Seguinte**.

1. No formulário de **tipo de tarefa e configurações,** selecione **Classificação** como o tipo de tarefa de aprendizagem automática.

    1. Selecione **Ver configurações adicionais** de configuração e povoar os campos da seguinte forma. Essas configurações são para controlar melhor o trabalho de treinamento. Caso contrário, os padrões serão aplicados com base na seleção de experimento e nos dados.

        >[!NOTE]
        > Neste tutorial, você não definirá uma pontuação de métrica ou um limite máximo de núcleos por iterações. Nem você bloqueará o teste de algoritmos.
   
        Configurações adicionais de&nbsp;|Descrição|Valor&nbsp;para tutoriais&nbsp;
        ------|---------|---
        Métrica primária| Métrica de avaliação para a qual o algoritmo de aprendizado de máquina será medido.|AUC_weighted
        Personalização automática| Habilita o pré-processamento. Isso inclui a limpeza automática de dados, a preparação e a transformação para gerar recursos sintéticos.| Ativar
        Algoritmos bloqueados | Algoritmos que você deseja excluir do trabalho de treinamento| Nenhum
        Critério de saída| Se um critério for atendido, o trabalho de treinamento será interrompido. |Formação&nbsp;trabalho&nbsp;horário (horas): 1 <br> Pontuação de&nbsp;métrica&nbsp;limiar: Nenhum
        Validação | Escolha um tipo de validação cruzada e um número de testes.|Tipo de validação:<br>&nbsp;k-fold&nbsp;validação cruzada <br> <br> Número de validações: 2
        Simultaneidade| O número máximo de iterações paralelas executadas por iteração| Iterações&nbsp;simultâneas&nbsp;: 5
        
        Selecione **Guardar**.

1. Selecione **Terminar** para executar a experiência. O ecrã **'Executar Detail'** abre com o **estado de Execução** na parte superior à medida que a preparação da experiência começa.

>[!IMPORTANT]
> A preparação leva **10-15 minutos** para preparar a experiência.
> Uma vez em execução, leva **2-3 minutos a mais para cada iteração**.  
> Selecione **Refresh** periodicamente para ver o estado da execução à medida que a experiência progride.
>
> Em produção, você provavelmente se desapareceria por um pouco. Mas para este tutorial, sugerimos que comece a explorar os algoritmos testados no separador **Models** à medida que completam enquanto os outros ainda estão em execução. 

##  <a name="explore-models"></a>Explorar modelos

Navegue para o separador **Models** para ver os algoritmos (modelos) testados. Por padrão, os modelos são ordenados pela pontuação da métrica à medida que são concluídos. Para este tutorial, o modelo que marca mais com base na métrica **de AUC_weighted** escolhida está no topo da lista.

Enquanto espera que todos os modelos de experimentação terminem, selecione o **nome Algoritmo** de um modelo completo para explorar os seus detalhes de desempenho. 

Os seguintes navegam através dos detalhes do **Modelo** e dos separadores **de Visualização** para visualizar as propriedades, métricas e gráficos de desempenho do modelo selecionado. 

![Detalhes da execução de iteração](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Implementar o modelo

O Machine Learning automatizado no Azure Machine Learning Studio permite que você implante o melhor modelo como um serviço Web em algumas etapas. A implantação é a integração do modelo para que ele possa prever novos dados e identificar possíveis áreas de oportunidade. 

Para este experimento, a implantação em um serviço Web significa que a instituição financeira agora tem uma solução Web iterativa e escalonável para identificar clientes de depósito de prazo fixo. 

Uma vez concluída a execução, navegue de volta para a página **'Executar Detalhes'** e selecione o separador **Modelos.**

Neste contexto de experiência, o **VoteEnsemble** é considerado o melhor modelo, com base na **métrica AUC_weighted.**  Implantamos esse modelo, mas é recomendável que a implantação demore cerca de 20 minutos para ser concluída. O processo de implantação envolve várias etapas, incluindo o registro do modelo, a geração de recursos e a configuração deles para o serviço Web.

1. Selecione o **botão de melhor modelo de implantação** no canto inferior esquerdo.

1. Povoar o painel **de modelos da** seguinte forma:

    Campo| Valor
    ----|----
    Nome da implantação| meu-automl-implantar
    Descrição da implantação| Minha primeira implantação de experimento automatizada do Machine Learning
    Tipo computacional | Selecionar instância de computação do Azure (ACI)
    Ativar a autenticação| Desativar. 
    Usar implantações personalizadas| Desativar. Permite que o arquivo de driver padrão (script de pontuação) e o arquivo de ambiente sejam gerados automaticamente. 
    
    Para este exemplo, utilizamos as predefinições fornecidas no menu *Avançado.* 

1. Selecione **Implementar**.  

    Uma mensagem de sucesso verde aparece na parte superior do ecrã **Executar,** e no painel do **modelo Recomendado,** uma mensagem de estado aparece sob o estado de **Implantação**. Selecione **Refresh** periodicamente para verificar o estado de implantação.
    
Agora você tem um serviço Web operacional para gerar previsões. 

Proceda aos [**Próximos Passos**](#next-steps) para saber mais sobre como consumir o seu novo serviço web e testar as suas previsões utilizando o Power BI's construído em suporte azure machine learning.

## <a name="clean-up-resources"></a>Limpar recursos

Os arquivos de implantação são maiores que os dados e os arquivos de teste, portanto, eles custam mais para serem armazenados. Exclua somente os arquivos de implantação para minimizar os custos em sua conta ou se você quiser manter o espaço de trabalho e os arquivos de teste. Caso contrário, exclua o grupo de recursos inteiro, se você não planeja usar nenhum dos arquivos.  

### <a name="delete-the-deployment-instance"></a>Excluir a instância de implantação

Exclua apenas a instância de implantação do Azure Machine Learning Studio, se você quiser manter o grupo de recursos e o espaço de trabalho para outros tutoriais e explorações. 

1. Vá ao [estúdio de Aprendizagem automática Azure.](https://ml.azure.com/) Navegue para o seu espaço de trabalho e à esquerda sob o painel **De Ativos,** selecione **Pontos Finais**. 

1. Selecione a implementação que pretende eliminar e selecionar **Eliminar**. 

1. Selecione **Proceder**.

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de aprendizado de máquina automatizado, você usou o Azure Machine Learning Studio para criar e implantar um modelo de classificação. Consulte estes artigos para obter mais informações e as próximas etapas:

> [!div class="nextstepaction"]
> [Consumir um serviço web](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Saiba mais sobre [a caracterização.](how-to-create-portal-experiments.md#featurization)
+ Saiba mais sobre [o perfil de dados.](how-to-create-portal-experiments.md#profile)
+ Saiba mais sobre [aprendizagem automática de máquinas.](concept-automated-ml.md)
+ Para obter mais informações sobre métricas e gráficos de classificação, consulte o artigo de [resultados automatizados](how-to-understand-automated-ml.md#classification) de machine learning.

>[!NOTE]
> Este conjunto de dados de Marketing Bancário é disponibilizado ao abrigo da [Licença Creative Commons (CCO: Domínio Público).](https://creativecommons.org/publicdomain/zero/1.0/) Quaisquer direitos em conteúdo individual da base de dados são licenciados na Licença de [Conteúdo da Base](https://creativecommons.org/publicdomain/zero/1.0/) de Dados e disponíveis em [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Este conjunto de dados estava originalmente disponível na Base de Dados de [Aprendizagem automática da UCI.](https://archive.ics.uci.edu/ml/datasets/bank+marketing)<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez e P. Rita. Uma abordagem baseada em dados para prever o sucesso do Telemarketing Bancário. Sistemas de suporte a decisões, Elsevier, 62:22-31, junho 2014.
