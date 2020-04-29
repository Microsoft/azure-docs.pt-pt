---
title: 'Tutorial 1: Prever risco de crédito'
titleSuffix: ML Studio (classic) - Azure
description: Um tutorial detalhado que mostra como criar uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning Studio (clássico). Este tutorial é parte da primeira parte de uma série tutorial em três partes.  Mostra como criar um espaço de trabalho, carregar dados e criar uma experiência.
keywords: risco de crédito, solução de análise preditiva, avaliação de riscos
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 6fd8573c78d80c950bdeb41ec01e2835def3979a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79204261"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio-classic"></a>Tutorial 1: Prever risco de crédito - Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Neste tutorial, você olha para o processo de desenvolvimento de uma solução de análise preditiva. Desenvolve-se um modelo simples no Machine Learning Studio (clássico).  Em seguida, implementa o modelo como um serviço web Azure Machine Learning.  Este modelo implementado pode fazer previsões usando novos dados. Este tutorial é **parte da primeira parte de uma série tutorial em três partes.**

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação do risco de crédito é um problema complexo, mas este tutorial vai simplificar um pouco. Vais usá-lo como um exemplo de como podes criar uma solução de análise preditiva utilizando o Microsoft Azure Machine Learning Studio (clássico). Você usará o Azure Machine Learning Studio (clássico) e um serviço web machine learning para esta solução.  

Neste tutorial em três partes, começa com dados de risco de crédito disponíveis ao público.  Depois desenvolves e treinas um modelo preditivo.  Finalmente implementa o modelo como um serviço web.

Nesta parte do tutorial: 
 
> [!div class="checklist"]
> * Criar um estúdio de aprendizagem automática (clássico) espaço de trabalho
> * Carregar os dados existentes
> * Criar uma experimentação

Em seguida, pode utilizar esta experiência para [treinar modelos na parte 2](tutorial-part2-credit-risk-train.md) e, em seguida, [implantá-los na parte 3](tutorial-part3-credit-risk-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial assume que já usou o Machine Learning Studio (clássico) pelo menos uma vez, e que tem alguma compreensão dos conceitos de aprendizagem automática. Porém, não parte do princípio de que é um especialista.

Se nunca usou o **Azure Machine Learning Studio (clássico)** antes, talvez queira começar com o quickstart, Crie a sua primeira experiência em ciência de [dados no Azure Machine Learning Studio (clássico)](create-experiment.md). O quickstart leva-o pela primeira vez ao Machine Learning Studio (clássico). Mostra-lhe as noções básicas de como arrastar e largar módulos na experimentação, ligá-los entre si, executar a experimentação e analisar os resultados.


> [!TIP] 
> Você pode encontrar uma cópia de trabalho da experiência que desenvolve neste tutorial na [Galeria Azure AI.](https://gallery.azure.ai) Vá ao **[Tutorial - Preveja o risco](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** de crédito e clique em Open in **Studio** para descarregar uma cópia da experiência no seu espaço de trabalho machine learning studio (clássico).
> 


## <a name="create-a-machine-learning-studio-classic-workspace"></a>Criar um estúdio de aprendizagem automática (clássico) espaço de trabalho

Para utilizar o Machine Learning Studio (clássico), é necessário ter um espaço de trabalho do Microsoft Azure Machine Learning Studio (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.  

Para criar um espaço de trabalho, consulte [Create e partilhe um espaço de trabalho Azure Machine Learning Studio (clássico).](create-workspace.md)

Depois de criado o seu espaço de trabalho, abra o Estúdio de Aprendizagem automática (clássico) ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Se tiver mais de um espaço de trabalho, pode selecionar o espaço de trabalho na barra de ferramentas no canto superior direito da janela.

![Selecione espaço de trabalho em Studio (clássico)](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Se você é dono do espaço de trabalho, você pode compartilhar as experiências em que você está trabalhando convidando outros para o espaço de trabalho. Pode fazê-lo no Machine Learning Studio (clássico) na página **SETTINGS.** Basta a conta da Microsoft ou conta organizacional para cada utilizador.
> 
> Na página **DEFINIÇÕES,** clique em **UTILIZADORES**e clique em **CONVIDAR MAIS UTILIZADORES** na parte inferior da janela.
> 

## <a name="upload-existing-data"></a><a name="upload"></a>Carregar os dados existentes

Para desenvolver um modelo preditivo para o risco de crédito, precisa de dados que possa usar para treinar e depois testar o modelo. Para este tutorial, utilizará o "UCI Statlog (Dados de Crédito Alemão) do repositório de Machine Learning da UC Irvine. Pode encontrá-lo aqui.  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Usará o ficheiro chamado **german.data**. Descarregue este ficheiro para o seu disco rígido local.  

O conjunto de dados **germano.data** contém linhas de 20 variáveis para 1000 candidatos anteriores para crédito. Estas 20 variáveis representam o conjunto de características do conjunto de dados (o vetor de *recurso),* que fornece características identificantes para cada candidato a crédito. Uma coluna adicional em cada linha representa o risco de crédito calculado do requerente, com 700 requerentes identificados como um baixo risco de crédito e 300 como um risco elevado.

O site da UCI fornece uma descrição dos atributos do vetor de recurso para estes dados. Estes dados incluem informação financeira, histórico de crédito, estado do emprego e informação pessoal. Para cada candidato, foi dada uma classificação binária indicando se se trata de um risco de crédito baixo ou elevado. 

Usará estes dados para treinar um modelo de análise preditivo. Quando terminar, o seu modelo deve ser capaz de aceitar um vetor de recurso para um novo indivíduo e prever se eles são um risco de crédito baixo ou alto.  

Aqui está uma reviravolta interessante.

A descrição do conjunto de dados no site da UCI menciona o que custa se classificar mal o risco de crédito de uma pessoa.
Se o modelo prevê um alto risco de crédito para alguém que é na verdade um baixo risco de crédito, o modelo fez uma classificação errada.

Mas a má classificação inversa é cinco vezes mais dispendiosa para a instituição financeira: se o modelo prevê um baixo risco de crédito para alguém que é, na verdade, um alto risco de crédito.

Por isso, pretende treinar o seu modelo para que o custo deste último tipo de classificação seja cinco vezes superior ao de classificação errada do contrário.

Uma forma simples de o fazer ao treinar o modelo na sua experiência é duplicando (cinco vezes) as entradas que representam alguém com um alto risco de crédito. 

Então, se o modelo classifica mal alguém como um baixo risco de crédito quando na verdade é um alto risco, o modelo faz essa mesma classificação errada cinco vezes, uma vez por cada duplicado. Isto aumentará o custo deste erro nos resultados da formação.


### <a name="convert-the-dataset-format"></a>Converter o formato dataset

O conjunto de dados original utiliza um formato separado em branco. O Machine Learning Studio (clássico) funciona melhor com um ficheiro de valor separado de vírgula (CSV), para que converta o conjunto de dados substituindo espaços por vírgulas.  

Há muitas formas de converter estes dados. Uma das formas é utilizar o seguinte comando Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Outra forma é usando o comando sed Unix:  

    sed 's/ /,/g' german.data > german.csv  

Em qualquer dos casos, criou uma versão separada da vírcula dos dados num ficheiro chamado **german.csv** que pode utilizar na sua experiência.

### <a name="upload-the-dataset-to-machine-learning-studio-classic"></a>Faça upload do conjunto de dados para O Estúdio de Aprendizagem Automática (clássico)

Uma vez que os dados foram convertidos para o formato CSV, você precisa carregá-lo em Machine Learning Studio (clássico). 

1. Abra a página inicial do Estúdio[https://studio.azureml.net](https://studio.azureml.net)de Aprendizagem automática (clássico). 

2. Clique no ![](./media/tutorial-part1-credit-risk/menu.png) menu Menu no canto superior esquerdo da janela, clique em **Azure Machine Learning,** selecione **Studio**, e inscreva-se.

3. Clique em **+NOVO** na parte inferior da janela.

4. Selecione **DATASET**.

5. Selecione **a partir de ficheiros LOCAIS**.

    ![Adicione um conjunto de dados de um ficheiro local](./media/tutorial-part1-credit-risk/add-dataset.png)

6. No **Upload um novo** diálogo de conjunto de dados, clique em Navegar e encontre o ficheiro **german.csv** que criou.

7. Introduza um nome para o conjunto de dados. Para este tutorial, chame-lhe "DADOS do Cartão de Crédito Alemão UCI".

8. Para o tipo de dados, selecione **Genérico CSV File Sem cabeçalho (.nh.csv)**.

9. Adicione uma descrição, se quiser.

10. Clique na marca de verificação **OK.**  

    ![Faça upload do conjunto de dados](./media/tutorial-part1-credit-risk/upload-dataset.png)

Isto envia os dados para um módulo de conjunto de dados que pode utilizar numa experiência.

Pode gerir conjuntos de dados que fez upload para Studio (clássico) clicando no separador **DATASETS** à esquerda da janela Studio (clássica).

![Gerir conjuntos de dados](./media/tutorial-part1-credit-risk/dataset-list.png)

Para obter mais informações sobre a importação de outros tipos de dados numa experiência, consulte importe os seus dados de [formação para o Azure Machine Learning Studio (clássico)](import-data.md).

## <a name="create-an-experiment"></a>Criar uma experimentação

O próximo passo neste tutorial é criar uma experiência no Machine Learning Studio (clássico) que usa o conjunto de dados que você carregou.  

1. Em Studio (clássico), clique **em +NEW** na parte inferior da janela.
1. Selecione **EXPERIMENT**, e, em seguida, selecione "Experiência em Branco". 

    ![Criar uma nova experimentação](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Selecione o nome da experiência padrão no topo da tela e mude o nome para algo significativo.

    ![Experiência de renomeação](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > É uma boa prática preencher **resumo** e **descrição** para a experiência no painel **Properties.** Estas propriedades dão-lhe a oportunidade de documentar a experiência para que quem olhar mais tarde compreenda os seus objetivos e metodologia.
   > 
   > ![Propriedades de experimentação](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. Na paleta de módulos à esquerda da tela de experiência, expanda os Conjuntos de **Dados Guardados**.
1. Encontre o conjunto de dados que criou sob **os meus Conjuntos** de Dados e arraste-o para a tela. Também pode encontrar o conjunto de dados inserindo o nome na caixa **de pesquisa** acima da paleta.  

    ![Adicione o conjunto de dados à experiência](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Preparar os dados

Pode ver as primeiras 100 linhas dos dados e algumas informações estatísticas para todo o conjunto de dados: Clique na porta de saída do conjunto de dados (o pequeno círculo na parte inferior) e **selecione Visualize**.  

Como o ficheiro de dados não veio com rubricas de colunas, o Studio (clássico) forneceu rubricas genéricas (Col1, Col2, *etc.*). Os bons títulos não são essenciais para criar um modelo, mas facilitam o trabalho com os dados da experiência. Além disso, quando eventualmente publica este modelo num serviço web, os títulos ajudam a identificar as colunas ao utilizador do serviço.  

Pode adicionar títulos de coluna utilizando o módulo [Dedados editar.][edit-metadata]

Utiliza o módulo [Dedados editar][edit-metadata] para alterar metadados associados a um conjunto de dados. Neste caso, usa-o para fornecer nomes mais amigáveis para títulos de colunas. 

Para utilizar [metadados editar,][edit-metadata]primeiro especifique quais as colunas a modificar (neste caso, todas elas.) Em seguida, especifica a ação a realizar nessas colunas (neste caso, alterando as rubricas das colunas.)

1. Na paleta de módulos, digite "metadados" na caixa **de pesquisa.** Os [Metadados de Edição][edit-metadata] aparecem na lista de módulos.

1. Clique e arraste o módulo [de Metadados editar][edit-metadata] para a tela e deixe-o abaixo do conjunto de dados que adicionou anteriormente.

1. Ligue o conjunto de dados aos [Metadados de Edição][edit-metadata]: clique na porta de saída do conjunto de dados (o pequeno círculo na parte inferior do conjunto de dados), arraste para a porta de entrada de [Metadados de Edição][edit-metadata] (o pequeno círculo na parte superior do módulo), em seguida, liberte o botão do rato. O conjunto de dados e o módulo permanecem ligados mesmo que se mova em torno da tela.
 
    A experiência deve agora ser algo assim:  

    ![Adicionar Metadados de Edição](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    A marca de exclamação vermelha indica que ainda não definiu as propriedades para este módulo. Fará isto a seguir.

    > [!TIP]
    > Pode adicionar um comentário a um módulo, fazendo duplo clique no módulo e introduzindo o texto. Isto pode ajudá-lo a ver rapidamente o que o módulo está a fazer na sua experimentação. Neste caso, clique duas vezes no módulo [Dedados editar][edit-metadata] e digite o comentário "Adicionar títulos de coluna". Clique em qualquer outro lugar da tela para fechar a caixa de texto. Para visualizar o comentário, clique na seta para baixo no módulo.
    > 
    > ![Editar módulo de metadados com comentários adicionados](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. [Selecione Editar Metadados][edit-metadata], e no painel **'Propriedades'** à direita da tela, clique no seletor de **colunas de lançamento**.

1. No diálogo de **colunas Select,** selecione todas as linhas nas **Colunas Disponíveis** e clique em > para movê-las para **Colunas Selecionadas**.
   O diálogo deve ser assim:

   ![Seletor de colunas com todas as colunas selecionadas](./media/tutorial-part1-credit-risk/select-columns.png)


1. Clique na marca de verificação **OK.**

1. De volta ao painel **properties,** procure o parâmetro de nomes de **coluna nova.** Neste campo, introduza uma lista de nomes para as 21 colunas no conjunto de dados, separadas por vírgulas e por ordem de coluna. Pode obter os nomes das colunas a partir da documentação do conjunto de dados no site da UCI, ou por conveniência pode copiar e colar a seguinte lista:  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   O painel properties é assim:

   ![Propriedades para Editar Metadados](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > Se quiser verificar as rubricas da coluna, faça a experiência (clique em **RUN** abaixo da tela de experimentação). Quando terminar de funcionar (aparece uma marca de verificação verde nos [Metadados de Edição),][edit-metadata]clique na porta de saída do módulo [Dedados editar][edit-metadata] e selecione **Visualize**. Pode ver a saída de qualquer módulo da mesma forma para ver o progresso dos dados através da experiência.
   > 
   > 

### <a name="create-training-and-test-datasets"></a>Criar conjuntos de dados de treino e teste

Precisa de alguns dados para treinar o modelo e alguns para testá-lo.
Assim, no próximo passo da experiência, divide o conjunto de dados em dois conjuntos de dados separados: um para treinar o nosso modelo e outro para testá-lo.

Para isso, utiliza o módulo [Split Data.][split]  

1. Encontre o módulo [De dados divididos,][split] arraste-o para a tela e ligue-o ao módulo [Deedição de Metadados.][edit-metadata]

1. Por predefinição, a relação de divisão é de 0,5 e o parâmetro **de divisão aleatório** está definido. Isto significa que uma metade aleatória dos dados é saída através de uma porta do módulo [De dados divididos,][split] e metade através da outra. Pode ajustar estes parâmetros, bem como o parâmetro de **sementes Aleatórias,** para alterar a divisão entre os dados de treino e teste. Por este exemplo, deixa-os como está.
   
   > [!TIP]
   > A propriedade **Fração de linhas no primeiro conjunto** de dados de saída determina quanto dos dados é output através da porta de saída *esquerda.* Por exemplo, se definir o rácio para 0,7, então 70% dos dados são de saída através da porta esquerda e 30% através da porta direita.  
   > 
   > 

1. Clique duas vezes no módulo [Split Data][split] e introduza o comentário: "Os dados de treino/teste dividem-se 50%". 

Pode utilizar as saídas do módulo [Split Data][split] como quiser, mas vamos optar por utilizar a saída esquerda como dados de treino e a saída certa como dados de teste.  

Como referido na [etapa anterior,](tutorial-part1-credit-risk.md#upload)o custo de classificação errada de um risco de crédito elevado como baixo é cinco vezes superior ao custo de classificação errada de um baixo risco de crédito tão elevado. Para ter em conta isto, gera um novo conjunto de dados que reflete esta função de custo. No novo conjunto de dados, cada exemplo de alto risco é replicado cinco vezes, enquanto cada exemplo de baixo risco não é replicado.   

Pode fazer esta replicação usando o código R:  

1. Encontre e arraste o módulo [Execute R Script][execute-r-script] para a tela da experiência. 

1. Ligue a porta de saída esquerda do módulo [Split Data][split] à primeira porta de entrada ("Dataset1") do módulo Execute [R Script.][execute-r-script]

1. Clique duas vezes no módulo [execute R Script][execute-r-script] e introduza o comentário, "Ajuste de custos".

1. No painel **'Propriedades',** elimine o texto predefinido no parâmetro **R script** e introduza este script:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Script R no módulo Execute R Script](./media/tutorial-part1-credit-risk/execute-r-script.png)

É necessário fazer esta mesma operação de replicação para cada saída do módulo [Split Data][split] para que os dados de treino e teste tenham o mesmo ajuste de custos. A maneira mais fácil de o fazer é duplicando o módulo [execute R Script][execute-r-script] que acabou de fazer e ligando-o à outra porta de saída do módulo De dados [split.][split]

1. Clique no módulo [execute R Script][execute-r-script] e selecione **Copiar**.

1. Clique na tela da experiência e selecione **Pasta**.

1. Arraste o novo módulo para a posição e, em seguida, ligue a porta de saída direita do módulo [Split Data][split] à primeira porta de entrada deste novo módulo [execute R Script.][execute-r-script] 

1. Na parte inferior da tela, clique em **Executar**. 

> [!TIP]
> A cópia do módulo Execute R Script contém o mesmo script que o módulo original. Quando copia e cola um módulo na tela, a cópia retém todas as propriedades do original.  
> 
>

A nossa experiência agora é mais ou menos assim:

![Adicionar módulo split e scripts R](./media/tutorial-part1-credit-risk/experiment.png)

Para obter mais informações sobre a utilização de scripts R nas suas experiências, consulte [Extend your experiment with R](extend-your-experiment-with-r.md).


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial completou estes passos: 
 
> [!div class="checklist"]
> * Criar um estúdio de aprendizagem automática (clássico) espaço de trabalho
> * Faça upload de dados existentes para o espaço de trabalho
> * Criar uma experimentação

Está agora pronto para treinar e avaliar modelos para estes dados.

> [!div class="nextstepaction"]
> [Tutorial 2 - Treine e avalie modelos](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
