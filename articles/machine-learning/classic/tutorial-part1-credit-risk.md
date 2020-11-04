---
title: 'ML Studio (clássico) tutorial: Prever risco de crédito - Azure'
description: Um tutorial detalhado que mostra como criar uma solução de análise preditiva para a avaliação do risco de crédito no Azure Machine Learning Studio (clássico). Este tutorial é parte um de uma série tutorial em três partes.  Mostra como criar um espaço de trabalho, carregar dados e criar uma experiência.
keywords: risco de crédito, solução de análise preditiva, avaliação de riscos
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 946435175ea5cd366103bc1254bae0d9afe0926e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325808"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio-classic"></a>Tutorial 1: Prever o risco de crédito - Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ sim ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) no ![ ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Neste tutorial, você dá uma olhada alargada no processo de desenvolvimento de uma solução de análise preditiva. Desenvolve-se um modelo simples no Machine Learning Studio (clássico).  Em seguida, implementa o modelo como um serviço web Azure Machine Learning.  Este modelo implementado pode fazer previsões usando novos dados. Este tutorial é **parte um de uma série tutorial em três partes.**

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação do risco de crédito é um problema complexo, mas este tutorial irá simplificar um pouco. Você vai usá-lo como um exemplo de como você pode criar uma solução de análise preditiva usando Microsoft Azure Machine Learning Studio (clássico). Você usará o Azure Machine Learning Studio (clássico) e um serviço web machine learning para esta solução.  

Neste tutorial em três partes, começa-se com dados de risco de crédito disponíveis ao público.  Depois desenvolve-se e treina-se um modelo preditivo.  Finalmente implementa o modelo como um serviço web.

Nesta parte do tutorial: 
 
> [!div class="checklist"]
> * Criar um espaço de trabalho machine learning studio (clássico)
> * Carregar os dados existentes
> * Criar uma experimentação

Em seguida, pode utilizar esta experiência para [formar modelos na parte 2](tutorial-part2-credit-risk-train.md) e, em seguida, [implantá-los na parte 3](tutorial-part3-credit-risk-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que já usou o Machine Learning Studio (clássico) pelo menos uma vez, e que tem alguma compreensão dos conceitos de aprendizagem automática. Porém, não parte do princípio de que é um especialista.

Se nunca usou **o Azure Machine Learning Studio (clássico)** antes, talvez queira começar com o quickstart, Crie a [sua primeira experiência de ciência de dados no Azure Machine Learning Studio (clássico)](create-experiment.md). O quickstart leva-o pelo Machine Learning Studio (clássico) pela primeira vez. Mostra-lhe as noções básicas de como arrastar e largar módulos na experimentação, ligá-los entre si, executar a experimentação e analisar os resultados.


> [!TIP] 
> Você pode encontrar uma cópia de trabalho da experiência que você desenvolve neste tutorial na [Galeria Azure AI](https://gallery.azure.ai). Vá ao **[Tutorial - Preveja o risco](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** de crédito e clique em Open in **Studio** para descarregar uma cópia da experiência no seu espaço de trabalho Machine Learning Studio (clássico).
> 


## <a name="create-a-machine-learning-studio-classic-workspace"></a>Criar um espaço de trabalho machine learning studio (clássico)

Para utilizar o Machine Learning Studio (clássico), precisa de ter um espaço de trabalho (clássico) do Microsoft Azure Machine Learning Studio (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.  

Para criar um espaço de trabalho, consulte [Criar e partilhar um espaço de trabalho (clássico) do Azure Machine Learning Studio](create-workspace.md)( clássico).

Após a criação do seu espaço de trabalho, abra o Machine Learning Studio (clássico) ( [https://studio.azureml.net/Home](https://studio.azureml.net/Home) ). Se tiver mais do que um espaço de trabalho, pode selecionar o espaço de trabalho na barra de ferramentas no canto superior direito da janela.

![Selecione espaço de trabalho em Studio (clássico)](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Se você é dono do espaço de trabalho, você pode compartilhar as experiências em que você está trabalhando convidando outros para o espaço de trabalho. Pode fazê-lo no Machine Learning Studio (clássico) na página **DEFINIÇÕES.** Só precisa da conta microsoft ou da conta organizacional para cada utilizador.
> 
> Na página **DEFINIÇÕES,** clique em **UTILIZADORES,** clique em **CONVIDAR MAIS UTILIZADORES** na parte inferior da janela.
> 

## <a name="upload-existing-data"></a><a name="upload"></a>Carregar os dados existentes

Para desenvolver um modelo preditivo para o risco de crédito, precisa de dados que pode usar para treinar e depois testar o modelo. Para este tutorial, utilizará o "Conjunto de Dados de Statlog (Dados de Crédito Alemão) da UC Irvine Machine Learning. Pode encontrá-lo aqui:  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Utilizará o ficheiro chamado **german.data**. Descarregue este ficheiro para o seu disco rígido local.  

O **conjunto de dados german.data** contém linhas de 20 variáveis para 1000 candidatos anteriores para crédito. Estas 20 variáveis representam o conjunto de funcionalidades do conjunto de dados (o vetor de *recurso),* que fornece características de identificação para cada candidato a crédito. Uma coluna adicional em cada linha representa o risco calculado de crédito do requerente, com 700 candidatos identificados como um baixo risco de crédito e 300 como um risco elevado.

O site da UCI fornece uma descrição dos atributos do vetor de recurso para estes dados. Estes dados incluem informação financeira, histórico de crédito, estado do emprego e informações pessoais. Para cada candidato, foi dada uma notação binária que indica se se trata de um risco de crédito baixo ou elevado. 

Usará estes dados para treinar um modelo de análise preditiva. Quando terminar, o seu modelo deve ser capaz de aceitar um vetor de recurso para um novo indivíduo e prever se eles são um risco de crédito baixo ou alto.  

Aqui está uma reviravolta interessante.

A descrição do conjunto de dados no site da UCI menciona o custo se classificar mal o risco de crédito de uma pessoa.
Se o modelo prevê um alto risco de crédito para alguém que é na verdade um baixo risco de crédito, o modelo fez uma classificação errada.

Mas a classificação inversa é cinco vezes mais dispendiosa para a instituição financeira: se o modelo prevê um baixo risco de crédito para alguém que é, na verdade, um risco elevado de crédito.

Por isso, quer treinar o seu modelo para que o custo deste último tipo de classificação seja cinco vezes maior do que classificar mal o contrário.

Uma forma simples de o fazer quando treina o modelo na sua experiência é duplicando (cinco vezes) as entradas que representam alguém com um alto risco de crédito. 

Então, se o modelo classifica mal alguém como um baixo risco de crédito quando na verdade é um risco elevado, o modelo faz a mesma classificação errada cinco vezes, uma vez por cada duplicado. Isto aumentará o custo deste erro nos resultados da formação.


### <a name="convert-the-dataset-format"></a>Converter o formato do conjunto de dados

O conjunto de dados original utiliza um formato separado em branco. O Machine Learning Studio (clássico) funciona melhor com um ficheiro de valor separado em vírgula (CSV), para que converta o conjunto de dados substituindo espaços por vírgulas.  

Há muitas formas de converter estes dados. Uma das formas é utilizar o seguinte comando Windows PowerShell:   

```powershell
cat german.data | %{$_ -replace " ",","} | sc german.csv  
```

Outra forma é utilizar o comando unix sed:  

```console
sed 's/ /,/g' german.data > german.csv
```

Em qualquer dos casos, criou uma versão separada de vírgula dos dados num ficheiro chamado **german.csv** que pode utilizar na sua experiência.

### <a name="upload-the-dataset-to-machine-learning-studio-classic"></a>Faça o upload do conjunto de dados para o Machine Learning Studio (clássico)

Uma vez convertidos os dados para formato CSV, é necessário enviá-lo para o Machine Learning Studio (clássico). 

1. Abra a página inicial do Machine Learning Studio [https://studio.azureml.net](https://studio.azureml.net) (clássico) (). 

2. Clique ![ no menu Menu no canto superior esquerdo da ](./media/tutorial-part1-credit-risk/menu.png) janela, clique em **Azure Machine Learning,** selecione **Studio** e inscreva-se.

3. Clique **em +NEW** na parte inferior da janela.

4. Selecione **DATASET**.

5. Selecione **A PARTIR DO ARQUIVO LOCAL**.

    ![Adicione um conjunto de dados de um ficheiro local](./media/tutorial-part1-credit-risk/add-dataset.png)

6. No **Upload um novo** diálogo do conjunto de dados, clique em Procurar e encontre o **german.csv** ficheiro que criou.

7. Introduza um nome para o conjunto de dados. Para este tutorial, chame-lhe "Dados do Cartão de Crédito Alemão da UCI".

8. Para o tipo de dados, selecione **Ficheiro Genérico CSV sem cabeçalho (.nh.csv)**.

9. Adicione uma descrição, se quiser.

10. Clique na marca de verificação **OK.**  

    ![Faça o upload do conjunto de dados](./media/tutorial-part1-credit-risk/upload-dataset.png)

Isto envia os dados para um módulo de conjunto de dados que pode usar numa experiência.

Pode gerir conjuntos de dados que fez o upload para o Studio (clássico) clicando no separador **DATASETS** à esquerda da janela Studio (clássico).

![Gerir conjuntos de dados](./media/tutorial-part1-credit-risk/dataset-list.png)

Para obter mais informações sobre a importação de outros tipos de dados numa experiência, consulte [Importe os seus dados de formação no Azure Machine Learning Studio (clássico)](import-data.md).

## <a name="create-an-experiment"></a>Criar uma experimentação

O próximo passo neste tutorial é criar uma experiência no Machine Learning Studio (clássico) que usa o conjunto de dados que carregou.  

1. Em Studio (clássico), clique **em +NEW** na parte inferior da janela.
1. Selecione **EXPERIMENT** , e, em seguida, selecione "Blank Experiment". 

    ![Criar uma nova experimentação](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Selecione o nome da experiência padrão na parte superior da tela e rebatize-o para algo significativo.

    ![Experiência de renomeado](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > É uma boa prática preencher **resumo** e **descrição** para a experiência no painel **de propriedades.** Estas propriedades dão-lhe a oportunidade de documentar a experiência para que qualquer pessoa que olhe para ela mais tarde compreenda os seus objetivos e metodologia.
   > 
   > ![Propriedades experimentais](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. Na paleta de módulos à esquerda da tela experimentada, expanda os **Conjuntos de Dados Guardados**.
1. Encontre o conjunto de dados que criou nos **meus conjuntos de dados** e arraste-o para a tela. Também pode encontrar o conjunto de dados introduzindo o nome na caixa **de Pesquisa** acima da paleta.  

    ![Adicione o conjunto de dados à experiência](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Preparar os dados

Pode ver as primeiras 100 linhas dos dados e algumas informações estatísticas para todo o conjunto de dados: Clique na porta de saída do conjunto de dados (o pequeno círculo na parte inferior) e **selecione Visualize**.  

Como o ficheiro de dados não veio com títulos de coluna, o Studio (clássico) forneceu títulos genéricos (Col1, Col2, *etc.* ). Os bons títulos não são essenciais para criar um modelo, mas facilitam o trabalho com os dados da experiência. Além disso, quando eventualmente publica este modelo num serviço web, os títulos ajudam a identificar as colunas para o utilizador do serviço.  

Pode adicionar títulos de coluna utilizando o módulo [editar metadados.][edit-metadata]

Utiliza o módulo [editar metadados][edit-metadata] para alterar metadados associados a um conjunto de dados. Neste caso, utilize-o para fornecer nomes mais amigáveis para títulos de colunas. 

Para utilizar [os Metadados de Edição,][edit-metadata]primeiro especifica quais as colunas a modificar (neste caso, todas elas.) Em seguida, especifica a ação a realizar nessas colunas (neste caso, alterando as rubricas das colunas.)

1. Na paleta de módulos, escreva "metadados" na caixa **de pesquisa.** Os [Metadados de Edição][edit-metadata] aparecem na lista de módulos.

1. Clique e arraste o módulo [de Metadados editar][edit-metadata] para a tela e deixe-o abaixo do conjunto de dados que adicionou anteriormente.

1. Ligue o conjunto de dados aos [Metadados de Edição:][edit-metadata]clique na porta de saída do conjunto de dados (o pequeno círculo na parte inferior do conjunto de dados), arraste para a porta de entrada dos [Metadados][edit-metadata] de Edição (o pequeno círculo na parte superior do módulo) e, em seguida, liberte o botão do rato. O conjunto de dados e o módulo permanecem ligados mesmo que se mova na tela.
 
    A experiência deve agora ser mais ou menos assim:  

    ![Adicionar Metadados de Edição](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    O ponto de exclamação vermelho indica que ainda não definiu as propriedades deste módulo. Fará isto a seguir.

    > [!TIP]
    > Pode adicionar um comentário a um módulo, fazendo duplo clique no módulo e introduzindo o texto. Isto pode ajudá-lo a ver rapidamente o que o módulo está a fazer na sua experimentação. Neste caso, clique duas vezes no módulo [editar metadados][edit-metadata] e digite o comentário "Adicionar títulos de coluna". Clique em qualquer outro lugar na tela para fechar a caixa de texto. Para visualizar o comentário, clique na seta para baixo no módulo.
    > 
    > ![Editar módulo de metadados com comentário adicionado](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. [Selecione Editar Metadados][edit-metadata], e no painel **propriedades** à direita da tela, clique no **seletor de colunas de lançamento**.

1. No diálogo **de colunas Select,** selecione todas as linhas em **Colunas Disponíveis** e clique em > para movê-las para **Colunas Selecionadas**.
   O diálogo deve ser assim:

   ![Seletor de colunas com todas as colunas selecionadas](./media/tutorial-part1-credit-risk/select-columns.png)


1. Clique na marca de verificação **OK.**

1. De volta ao painel **de propriedades,** procure o parâmetro **dos nomes da nova coluna.** Neste campo, introduza uma lista de nomes para as 21 colunas no conjunto de dados, separadas por vírgulas e por ordem de coluna. Pode obter os nomes das colunas a partir da documentação do conjunto de dados no site da UCI, ou para conveniência pode copiar e colar a seguinte lista:  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   O painel de propriedades é assim:

   ![Propriedades para Editar Metadados](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > Se pretender verificar as posições da coluna, faça a experiência (clique em **RUN** abaixo da tela de experiência). Quando terminar de correr (uma marca de verificação verde aparece no [Editar Metadas),][edit-metadata]clique na porta de saída do módulo [Editar Metadas][edit-metadata] e selecione **Visualize**. Pode ver a saída de qualquer módulo da mesma forma para ver o progresso dos dados através da experiência.
   > 
   > 

### <a name="create-training-and-test-datasets"></a>Criar conjuntos de dados de treino e teste

Precisa de alguns dados para treinar o modelo e alguns para testá-lo.
Assim, no próximo passo da experiência, dividiu o conjunto de dados em dois conjuntos de dados separados: um para treinar o nosso modelo e outro para testá-lo.

Para isso, utilize o módulo [De Dados Divididos.][split]  

1. Encontre o módulo [de Dados Divididos,][split] arraste-o para a tela e conecte-o ao módulo [de metadados de edição.][edit-metadata]

1. Por predefinição, a relação de divisão é de 0,5 e o parâmetro **de divisão aleatório** é definido. Isto significa que metade dos dados aleatórios éprodução através de uma porta do módulo [de Dados Divididos,][split] e metade através da outra. Pode ajustar estes parâmetros, bem como o parâmetro **de sementes aleatórias,** para alterar a divisão entre os dados de treino e teste. Por exemplo, deixa-os como está.
   
   > [!TIP]
   > A **fração de propriedade de linhas no primeiro conjunto** de dados de saída determina a quantidade de dados que é a saída através da porta de saída *esquerda.* Por exemplo, se definir o rácio para 0,7, então 70% dos dados são de saída através da porta esquerda e 30% através da porta direita.  
   > 
   > 

1. Clique duas vezes no módulo [Dados Divididos][split] e introduza o comentário: "Dados de treino/teste dividem 50%". 

Pode utilizar as saídas do módulo [De Dados Divididos][split] como quiser, mas vamos escolher usar a saída esquerda como dados de treino e a saída certa como dados de teste.  

Tal como referido na [etapa anterior,](tutorial-part1-credit-risk.md#upload)o custo de classificação errada de um risco de crédito elevado, uma vez que baixo é cinco vezes superior ao custo de classificação errada de um baixo risco de crédito tão elevado. Para responder isto, gera um novo conjunto de dados que reflete esta função de custo. No novo conjunto de dados, cada exemplo de alto risco é replicado cinco vezes, enquanto cada exemplo de baixo risco não é replicado.   

Pode fazer esta replicação utilizando o código R:  

1. Encontre e arraste o módulo [executo R script][execute-r-script] para a tela de experiência. 

1. Ligue a porta de saída esquerda do módulo [De dados divididos][split] à primeira porta de entrada ("Dataset1") do módulo [executo R Script.][execute-r-script]

1. Clique duas vezes no módulo [Execut r Script][execute-r-script] e introduza o comentário: "Definir ajuste de custos".

1. No painel **propriedades,** elimine o texto predefinido no parâmetro **R Script** e introduza este script:
   
    ```r
    dataset1 <- maml.mapInputPort(1)
    data.set<-dataset1[dataset1[,21]==1,]
    pos<-dataset1[dataset1[,21]==2,]
    for (i in 1:5) data.set<-rbind(data.set,pos)
    maml.mapOutputPort("data.set")
    ```

    ![Script R no módulo executo R Script](./media/tutorial-part1-credit-risk/execute-r-script.png)

É necessário fazer esta mesma operação de replicação para cada saída do módulo [De Dados Divididos][split] para que os dados de treino e teste tenham o mesmo ajustamento de custo. A forma mais fácil de o fazer é duplicando o módulo [Execut R Script][execute-r-script] que acabou de fazer e ligando-o à outra porta de saída do módulo De [dados divididos.][split]

1. Clique com o botão direito no módulo [executar o script R][execute-r-script] e selecione **Copy**.

1. Clique com o botão direito na tela de experiência e **selecione Pasta**.

1. Arraste o novo módulo para a posição e, em seguida, ligue a porta de saída certa do módulo [Dados Divididos][split] à primeira porta de entrada deste novo módulo [execute R Script.][execute-r-script] 

1. Na parte inferior da tela, clique em **Executar**. 

> [!TIP]
> A cópia do módulo executo R Script contém o mesmo script que o módulo original. Quando copia e cola um módulo na tela, a cópia conserva todas as propriedades do original.  
> 
>

A nossa experiência agora é mais ou menos assim:

![Adicionar módulo split e scripts R](./media/tutorial-part1-credit-risk/experiment.png)

Para obter mais informações sobre a utilização de scripts R nas suas experiências, consulte [Extend your experiment with R](index.yml).


## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial completou estes passos: 
 
> [!div class="checklist"]
> * Criar um espaço de trabalho machine learning studio (clássico)
> * Faça o upload dos dados existentes para o espaço de trabalho
> * Criar uma experimentação

Está agora pronto para treinar e avaliar modelos para estes dados.

> [!div class="nextstepaction"]
> [Tutorial 2 - Treinar e avaliar modelos](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data