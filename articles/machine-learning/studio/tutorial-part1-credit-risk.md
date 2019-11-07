---
title: 'Tutorial 1: prever o risco de crédito'
titleSuffix: ML Studio (classic) Azure
description: Um tutorial detalhado que mostra como criar uma solução de análise preditiva para avaliação de risco de crédito na versão clássica do Azure Machine Learning Studio. Este tutorial é a parte um de uma série de tutoriais de três partes.  Ele mostra como criar um espaço de trabalho, carregar dados e criar um experimento.
keywords: risco de crédito, solução de análise preditiva, avaliação de riscos
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 78092bd4ad4da896cd2fd36768f181764335ee85
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619189"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio-classic"></a>Tutorial 1: prever o risco de crédito-Azure Machine Learning Studio (clássico)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Neste tutorial, você tem uma visão detalhada do processo de desenvolvimento de uma solução de análise preditiva. Você desenvolve um modelo simples no Machine Learning Studio (clássico).  Em seguida, você implanta o modelo como um serviço Web Azure Machine Learning.  Esse modelo implantado pode fazer previsões usando novos dados. Este tutorial é **a parte um de uma série de tutoriais de três partes**.

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação de risco de crédito é um problema complexo, mas este tutorial vai simplificar um pouco. Você o usará como um exemplo de como é possível criar uma solução de análise preditiva usando Microsoft Azure Machine Learning Studio (clássico). Você usará a versão clássica do Azure Machine Learning Studio e um serviço Web Machine Learning para essa solução.  

Neste tutorial de três partes, você começa com dados de risco de crédito disponíveis publicamente.  Em seguida, você desenvolve e treina um modelo de previsão.  Por fim, você implanta o modelo como um serviço Web.

Nesta parte do tutorial, você: 
 
> [!div class="checklist"]
> * Criar um espaço de trabalho Machine Learning Studio (clássico)
> * Carregar os dados existentes
> * Criar uma experimentação

Você pode usar esse experimento para [treinar modelos na parte 2](tutorial-part2-credit-risk-train.md) e, em seguida, [implantá-los na parte 3](tutorial-part3-credit-risk-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você usou Machine Learning Studio (clássico) pelo menos uma vez antes, e que você tem alguma compreensão dos conceitos de aprendizado de máquina. Porém, não parte do princípio de que é um especialista.

Se você nunca usou **Azure Machine Learning Studio (clássico)** antes, talvez queira começar com o início rápido, [criar seu primeiro experimento de ciência de dados no Azure Machine Learning Studio (clássico)](create-experiment.md). O guia de início rápido orienta você pelo Machine Learning Studio (clássico) pela primeira vez. Mostra-lhe as noções básicas de como arrastar e largar módulos na experimentação, ligá-los entre si, executar a experimentação e analisar os resultados.


> [!TIP] 
> Você pode encontrar uma cópia de trabalho do experimento que você desenvolve neste tutorial no [Galeria de ia do Azure](https://gallery.azure.ai). Acesse **[tutorial-prever risco de crédito](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** e clique em **abrir no estúdio** para baixar uma cópia do experimento em seu espaço de trabalho Machine Learning Studio (clássico).
> 


## <a name="create-a-machine-learning-studio-classic-workspace"></a>Criar um espaço de trabalho Machine Learning Studio (clássico)

Para usar Machine Learning Studio (clássico), você precisa ter um espaço de trabalho Microsoft Azure Machine Learning Studio (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.  

Para criar um espaço de trabalho, consulte [criar e compartilhar um espaço de trabalho Azure Machine Learning Studio (clássico)](create-workspace.md).

Depois que o espaço de trabalho for criado, abra Machine Learning Studio (clássico) ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Se você tiver mais de um espaço de trabalho, poderá selecionar o espaço de trabalho na barra de ferramentas no canto superior direito da janela.

![Selecionar espaço de trabalho no estúdio (clássico)](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Se você for o proprietário do espaço de trabalho, poderá compartilhar os experimentos nos quais está trabalhando convidando outras pessoas para o espaço de trabalho. Você pode fazer isso em Machine Learning Studio (clássico) na página **configurações** . Você só precisa do conta Microsoft ou conta organizacional para cada usuário.
> 
> Na página **configurações** , clique em **usuários**e, em seguida, clique em **convidar mais usuários** na parte inferior da janela.
> 

## <a name="upload"></a>Carregar dados existentes

Para desenvolver um modelo de previsão para o risco de crédito, você precisa de dados que podem ser usados para treinar e testar o modelo. Para este tutorial, você usará o "conjunto de dados de Statlog de UCI (dados de crédito alemão)" do repositório de Machine Learning de UC Irvine. Você pode encontrá-lo aqui:  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Você usará o arquivo chamado **alemão. Data**. Baixe esse arquivo em seu disco rígido local.  

O conjunto de **dados alemão. Data** contém linhas de 20 variáveis para 1000 candidatos anteriores para crédito. Essas 20 variáveis representam o conjunto de recursos do DataSet (o *vetor de recurso*), que fornece características de identificação para cada candidato de crédito. Uma coluna adicional em cada linha representa o risco de crédito calculado do candidato, com 700 candidatos identificados como um risco de crédito baixo e 300 como um risco alto.

O site UCI fornece uma descrição dos atributos do vetor de recurso para esses dados. Esses dados incluem informações financeiras, histórico de crédito, status de emprego e informações pessoais. Para cada candidato, uma classificação binária foi dada indicando se eles são um risco de crédito baixo ou alto. 

Você usará esses dados para treinar um modelo de análise preditiva. Quando você terminar, seu modelo deverá ser capaz de aceitar um vetor de recurso para um novo indivíduo e prever se eles são um risco de crédito baixo ou alto.  

Aqui está um ponto interessante.

A descrição do conjunto de conteúdo no site do UCI menciona o que custará se você classificar incorretamente o risco de crédito de uma pessoa.
Se o modelo prevê um alto risco de crédito para alguém que, na verdade, é um risco de crédito baixo, o modelo fez uma classificação incorreta.

Mas a classificação incorreta inversa é cinco vezes mais dispendiosa para a instituição financeira: se o modelo prevê um baixo risco de crédito para alguém que, na verdade, é um risco de crédito alto.

Portanto, você deseja treinar seu modelo para que o custo desse último tipo de classificação incorreta seja cinco vezes maior do que a classificação incorreta da outra maneira.

Uma maneira simples de fazer isso ao treinar o modelo em seu experimento é duplicando (cinco vezes) as entradas que representam alguém com um risco de crédito alto. 

Em seguida, se o modelo classificar incorretamente alguém como um risco de crédito baixo quando for realmente um risco alto, o modelo fará essa mesma classificação incorretamente cinco vezes, uma vez para cada duplicata. Isso aumentará o custo desse erro nos resultados de treinamento.


### <a name="convert-the-dataset-format"></a>Converter o formato do conjunto de um

O conjunto de valores original usa um formato separado por espaços em branco. A versão clássica do Machine Learning Studio funciona melhor com um arquivo CSV (valores separados por vírgula), portanto, você converterá o conjunto de os espaços com vírgulas.  

Há várias maneiras de converter esses dados. Uma maneira é usar o seguinte comando do Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Outra maneira é usar o comando Unix sed:  

    sed 's/ /,/g' german.data > german.csv  

Em ambos os casos, você criou uma versão separada por vírgula dos dados em um arquivo chamado **alemão. csv** que você pode usar em seu experimento.

### <a name="upload-the-dataset-to-machine-learning-studio-classic"></a>Carregar o conjunto de Machine Learning Studio (clássico)

Depois que os dados tiverem sido convertidos no formato CSV, você precisará carregá-los na versão clássica do Machine Learning Studio. 

1. Abra o home page de Machine Learning Studio (clássico) ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Clique no menu ![menu](./media/tutorial-part1-credit-risk/menu.png) no canto superior esquerdo da janela, clique em **Azure Machine Learning**, selecione **estúdio**e entre.

3. Clique em **+ novo** na parte inferior da janela.

4. Selecione **DataSet**.

5. Selecione **do arquivo local**.

    ![Adicionar um conjunto de uma de um arquivo local](./media/tutorial-part1-credit-risk/add-dataset.png)

6. Na caixa de diálogo **carregar um novo conjunto de um** , clique em procurar e localize o arquivo **alemão. csv** que você criou.

7. Insira um nome para o conjunto de os. Para este tutorial, ligue para "dados de cartão de crédito alemão de UCI".

8. Para tipo de dados, selecione **arquivo CSV genérico sem cabeçalho (. NH. csv)** .

9. Adicione uma descrição, se desejar.

10. Clique na marca de seleção **OK** .  

    ![Carregar o conjunto de um](./media/tutorial-part1-credit-risk/upload-dataset.png)

Isso carrega os dados em um módulo DataSet que você pode usar em um experimento.

Você pode gerenciar conjuntos de os que você carregou no Studio (clássico) clicando na guia **conjuntos de valores** à esquerda da janela Studio (clássico).

![Gerenciar conjuntos de os](./media/tutorial-part1-credit-risk/dataset-list.png)

Para obter mais informações sobre como importar outros tipos de dados em um experimento, consulte [importar seus dados de treinamento para Azure Machine Learning Studio (clássico)](import-data.md).

## <a name="create-an-experiment"></a>Criar uma experimentação

A próxima etapa neste tutorial é criar um experimento na versão clássica do Machine Learning Studio que usa o conjunto de um que você carregou.  

1. No Studio (clássico), clique em **+ novo** na parte inferior da janela.
1. Selecione **experimento**e, em seguida, selecione "experimento em branco". 

    ![Criar uma nova experimentação](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Selecione o nome do experimento padrão na parte superior da tela e renomeie-o para algo significativo.

    ![Renomear experimento](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > É uma boa prática preencher o **Resumo** e a **Descrição** do experimento no painel **Propriedades** . Essas propriedades lhe dão a chance de documentar o teste para que qualquer pessoa que olha a ti posteriormente entenda suas metas e metodologia.
   > 
   > ![Propriedades do experimento](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. Na paleta de módulo à esquerda da tela do experimento, expanda **DataSets salvos**.
1. Localize o conjunto de um que você criou em **meus conjuntos de valores** e arraste-o para a tela. Você também pode encontrar o conjunto de os digitando o nome na caixa de **pesquisa** acima da paleta.  

    ![Adicionar o conjunto de um ao experimento](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Preparar os dados

Você pode exibir as primeiras 100 linhas dos dados e algumas informações estatísticas para todo o conjunto: clique na porta de saída do conjunto de dados (o círculo pequeno na parte inferior) e selecione **Visualizar**.  

Como o arquivo de dados não vem com cabeçalhos de coluna, a versão clássica do estúdio forneceu títulos genéricos (Col1, Col2, *etc.* ). Bons títulos não são essenciais para a criação de um modelo, mas facilitam o trabalho com os dados no experimento. Além disso, quando você eventualmente publica esse modelo em um serviço Web, os títulos ajudam a identificar as colunas para o usuário do serviço.  

Você pode adicionar títulos de coluna usando o módulo [Editar metadados][edit-metadata] .

Você usa o módulo [Editar metadados][edit-metadata] para alterar os metadados associados a um conjunto de uma. Nesse caso, você deve usá-lo para fornecer nomes mais amigáveis para títulos de coluna. 

Para usar [Editar metadados][edit-metadata], primeiro você especifica quais colunas modificar (nesse caso, todas elas). Em seguida, especifique a ação a ser executada nessas colunas (nesse caso, alterando os títulos de coluna).

1. Na paleta de módulos, digite "metadados" na caixa de **pesquisa** . A [edição de metadados][edit-metadata] é exibida na lista de módulos.

1. Clique e arraste o módulo [Editar metadados][edit-metadata] para a tela e solte-o abaixo do conjunto de um que você adicionou anteriormente.

1. Conectar o conjunto de dados aos [metadados de edição][edit-metadata]: clique na porta de saída do conjunto de dados (o círculo pequeno na parte inferior do conjunto), arraste para a porta de entrada de [Editar metadados][edit-metadata] (o círculo pequeno na parte superior do módulo) e solte o botão do mouse. O conjunto de módulos (DataSet) e o módulo permanecem conectados mesmo se você mover qualquer um deles na tela.
 
    O experimento agora deve ser semelhante a este:  

    ![Adicionando editar metadados](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    O ponto de exclamação vermelho indica que você ainda não definiu as propriedades deste módulo. Você fará isso em seguida.

    > [!TIP]
    > Pode adicionar um comentário a um módulo, fazendo duplo clique no módulo e introduzindo o texto. Isto pode ajudá-lo a ver rapidamente o que o módulo está a fazer na sua experimentação. Nesse caso, clique duas vezes no módulo [Editar metadados][edit-metadata] e digite o comentário "adicionar títulos de coluna". Clique em qualquer outro lugar na tela para fechar a caixa de texto. Para exibir o comentário, clique na seta para baixo no módulo.
    > 
    > ![Editar módulo de metadados com Comentário adicionado](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. Selecione [Editar metadados][edit-metadata]e, no painel **Propriedades** à direita da tela, clique em **Iniciar seletor de coluna**.

1. Na caixa de diálogo **selecionar colunas** , selecione todas as linhas em **colunas disponíveis** e clique em > para movê-las para as **colunas selecionadas**.
   A caixa de diálogo deve ter a seguinte aparência:

   ![Seletor de coluna com todas as colunas selecionadas](./media/tutorial-part1-credit-risk/select-columns.png)


1. Clique na marca de seleção **OK** .

1. De volta ao painel **Propriedades** , procure o parâmetro **novos nomes de coluna** . Nesse campo, insira uma lista de nomes para as 21 colunas no conjunto de linhas, separadas por vírgulas e na ordem das colunas. Você pode obter os nomes das colunas na documentação do conjunto de documentos no site do UCI, ou para sua conveniência, você pode copiar e colar a seguinte lista:  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   O painel Propriedades tem esta aparência:

   ![Propriedades para editar metadados](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > Se você quiser verificar os títulos de coluna, execute o experimento (clique em **executar** abaixo da tela do experimento). Ao concluir a execução (uma marca de seleção verde aparece em [Editar metadados][edit-metadata]), clique na porta de saída do módulo [Editar metadados][edit-metadata] e selecione **Visualizar**. Você pode exibir a saída de qualquer módulo da mesma maneira para exibir o progresso dos dados por meio do experimento.
   > 
   > 

### <a name="create-training-and-test-datasets"></a>Criar conjuntos de testes de treinamento e de teste

Você precisa de alguns dados para treinar o modelo e alguns para testá-lo.
Portanto, na próxima etapa do experimento, você divide o conjunto de um em dois conjuntos de valores separados: um para treinar nosso modelo e outro para testá-lo.

Para fazer isso, use o módulo [dividir dados][split] .  

1. Localize o módulo [dividir dados][split] , arraste-o para a tela e conecte-o ao módulo [Editar metadados][edit-metadata] .

1. Por padrão, a taxa de divisão é 0,5 e o parâmetro **Split aleatório** é definido. Isso significa que uma metade aleatória dos dados é impressa por meio de uma porta do módulo [dividir dados][split] e metade por meio do outro. Você pode ajustar esses parâmetros, bem como o parâmetro **semente aleatória** , para alterar a divisão entre os dados de treinamento e teste. Para este exemplo, você os deixa como está.
   
   > [!TIP]
   > A **fração de propriedade das linhas no primeiro conjunto** de dados de saída determina quanto dos dados são gerados por meio da porta de saída *à esquerda* . Por exemplo, se você definir a taxa como 0,7, 70% dos dados serão gerados por meio da porta esquerda e 30% por meio da porta à direita.  
   > 
   > 

1. Clique duas vezes no módulo [dividir dados][split] e insira o comentário "divisão de dados de treinamento/teste 50%". 

Você pode usar as saídas do módulo [dividir dados][split] , como desejar, mas vamos optar por usar a saída à esquerda como dados de treinamento e a saída à direita como dados de teste.  

Conforme mencionado na [etapa anterior](tutorial-part1-credit-risk.md#upload), o custo da classificação incorreta de um risco de crédito alto como baixo é cinco vezes maior do que o custo de classificar incorretamente um risco de crédito baixo como alto. Para considerar isso, você gera um novo conjunto de um DataSet que reflete essa função de custo. No novo conjunto de um, cada exemplo de alto risco é replicado cinco vezes, enquanto cada exemplo de baixo risco não é replicado.   

Você pode fazer essa replicação usando o código R:  

1. Localize e arraste o módulo [Executar script R][execute-r-script] para a tela do experimento. 

1. Conecte a porta de saída à esquerda do módulo [dividir dados][split] à primeira porta de entrada ("dataSet1") do módulo [Executar script R][execute-r-script] .

1. Clique duas vezes no módulo [Executar script R][execute-r-script] e insira o comentário "definir ajuste de custo".

1. No painel **Propriedades** , exclua o texto padrão no parâmetro de **script R** e insira este script:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Script r no módulo executar script R](./media/tutorial-part1-credit-risk/execute-r-script.png)

Você precisa fazer essa mesma operação de replicação para cada saída do módulo [dividir dados][split] para que os dados de treinamento e teste tenham o mesmo ajuste de custo. A maneira mais fácil de fazer isso é duplicando o módulo [Executar script R][execute-r-script] que você acabou de criar e conectando-o à outra porta de saída do módulo [dividir dados][split] .

1. Clique com o botão direito do mouse no módulo [Executar script R][execute-r-script] e selecione **copiar**.

1. Clique com o botão direito do mouse na tela do experimento e selecione **colar**.

1. Arraste o novo módulo para a posição e conecte a porta de saída à direita do módulo [dividir dados][split] à primeira porta de entrada desse novo módulo [Executar script R][execute-r-script] . 

1. Na parte inferior da tela, clique em **executar**. 

> [!TIP]
> A cópia do módulo executar script R contém o mesmo script do módulo original. Quando você copia e cola um módulo na tela, a cópia retém todas as propriedades do original.  
> 
>

Nosso experimento agora tem uma aparência semelhante a esta:

![Adicionando módulo de divisão e scripts R](./media/tutorial-part1-credit-risk/experiment.png)

Para obter mais informações sobre como usar scripts do R em seus experimentos, consulte [estender seu experimento com o R](extend-your-experiment-with-r.md).


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você concluiu estas etapas: 
 
> [!div class="checklist"]
> * Criar um espaço de trabalho Machine Learning Studio (clássico)
> * Carregar dados existentes no espaço de trabalho
> * Criar uma experimentação

Agora você está pronto para treinar e avaliar modelos para esses dados.

> [!div class="nextstepaction"]
> [Tutorial 2 – treinar e avaliar modelos](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
