---
title: 'Tutorial 1: Prever risco do crédito'
titleSuffix: Azure Machine Learning Studio
description: Um tutorial detalhado que mostra como criar uma solução de Análise Preditiva para avaliação de riscos de crédito no Azure Machine Learning Studio. Este tutorial é a primeira parte de uma série de tutoriais de três partes.  Ele mostra como criar uma área de trabalho, carregar dados e criar uma experimentação.
keywords: risco de crédito, solução de análise preditiva, avaliação de riscos
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: f69b3f2c8de4cf137583ad7a33e8edfe31373096
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904512"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio"></a>Tutorial 1: Prever o risco de crédito - Azure Machine Learning Studio

Neste tutorial, demorar mais aprofundadamente o processo de desenvolvimento de uma solução de Análise Preditiva. Desenvolver um modelo simples no Machine Learning Studio.  Em seguida, implementar o modelo como um serviço web do Azure Machine Learning.  Este modelo implementado pode efetuar predições utilizando novos dados. Este tutorial é **primeira parte de uma série de tutoriais de três partes**.

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

Avaliação de riscos de crédito é um problema complexo, mas este tutorial irá simplificá-lo um pouco. Irá utilizá-lo como um exemplo de como pode criar uma solução de Análise Preditiva com o Microsoft Azure Machine Learning Studio. Irá utilizar o Azure Machine Learning Studio e um serviço web do Machine Learning para esta solução.  

Neste tutorial de três partes, começar com dados de risco de crédito publicamente disponíveis.  Em seguida, desenvolver e formar um modelo preditivo.  Por fim implementa o modelo como um serviço web.

Nesta parte do tutorial: 
 
> [!div class="checklist"]
> * Criar uma área de trabalho do Machine Learning Studio
> * Carregar os dados existentes
> * Criar uma experimentação

Em seguida, pode utilizar esta experiência para [formar modelos na parte 2](tutorial-part2-credit-risk-train.md) e, em seguida [implementá-las na parte 3](tutorial-part3-credit-risk-deploy.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que já utilizou o Machine Learning Studio pelo menos uma vez antes e que tem algumas noções básicas sobre conceitos de machine learning. Porém, não parte do princípio de que é um especialista.

Se nunca tiver utilizado **Azure Machine Learning Studio** antes, convém começar com o guia de introdução [criar seu primeiro ciência de dados de experimentação no Azure Machine Learning Studio](create-experiment.md). O guia de introdução orienta-o Machine Learning Studio pela primeira vez. Mostra-lhe as noções básicas de como arrastar e largar módulos na experimentação, ligá-los entre si, executar a experimentação e analisar os resultados.


> [!TIP] 
> Pode encontrar uma cópia de trabalho da experimentação que desenvolver neste tutorial no [Galeria de IA do Azure](https://gallery.azure.ai). Aceda a **[Tutorial - prever crédito risco](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** e clique em **abrir no Studio** para transferir uma cópia da experimentação para a área de trabalho do Machine Learning Studio.
> 


## <a name="create-a-machine-learning-studio-workspace"></a>Criar uma área de trabalho do Machine Learning Studio

Para utilizar o Machine Learning Studio, tem de ter uma área de trabalho do Microsoft Azure Machine Learning Studio. Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.  

Para criar uma área de trabalho, consulte [criar e partilhar uma área de trabalho do Azure Machine Learning Studio](create-workspace.md).

Depois de sua área de trabalho é criada, abra o Machine Learning Studio ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Se tiver mais do que uma área de trabalho, pode selecionar a área de trabalho na barra de ferramentas no canto superior direito da janela.

![Selecione a área de trabalho do Studio](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Se for o proprietário da área de trabalho, pode partilhar as experimentações que está a trabalhar por convidando os outros usuários para a área de trabalho. Pode fazê-lo no Machine Learning Studio sobre o **definições** página. Apenas terá da conta Microsoft ou contas institucionais para cada utilizador.
> 
> No **definições** página, clique em **utilizadores**, em seguida, clique em **CONVIDAR utilizadores mais** na parte inferior da janela.
> 

## <a name="upload"></a>Carregar os dados existentes

Para desenvolver um modelo preditivo para o risco de crédito, terá de dados que pode usar para preparar e, em seguida, testar o modelo. Neste tutorial, vai utilizar o "UCI Statlog (dados de crédito alemães) conjunto de dados" do repositório de UC Irvine Machine Learning. Pode encontrá-lo aqui:  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Usará o arquivo chamado **german.data**. Transferir este ficheiro para o disco rígido local.  

O **german.data** conjunto de dados contém linhas de 20 variáveis para 1000 candidatos nos últimos de crédito. Estas variáveis de 20 representam o conjunto do conjunto de dados de recursos (a *vetor de funcionalidade*), que fornece características de identificação para cada candidato de crédito. Uma coluna adicional em cada linha representa o risco de crédito calculado o candidato, com 700 candidatos identificados como um risco de crédito baixa e 300 como um risco elevado.

O site UCI fornece uma descrição dos atributos do vetor de funcionalidade para estes dados. Estes dados incluem informações financeiras, o histórico de crédito, o estado de emprego e informações pessoais. Para cada candidato, uma classificação binária foi determinado que indica se são uma baixa ou alta risco de crédito. 

Usará esses dados para preparar um modelo de Análise Preditiva. Quando tiver terminado, o modelo deve ser capaz de aceitar um vetor de funcionalidade para um novo indivíduo e prever se ele é um risco de crédito baixa ou alta.  

Aqui está uma mudança interessante.

A descrição do conjunto de dados no site da UCI menciona o que custa se misclassify o risco de crédito de uma pessoa.
Se o modelo prevê um risco elevado crédito para alguém que é, na verdade, um risco de crédito baixa, o modelo fez uma misclassification.

Mas o inverso misclassification é cinco vezes mais caro para a instituição financeira: se o modelo prevê um risco de crédito baixa para alguém que é, na verdade, um risco elevado crédito.

Por isso, deseja preparar o seu modelo, para que o custo deste último tipo de misclassification é cinco vezes maior do que misclassifying a outra forma.

É uma forma simples de fazê-lo ao treinar o modelo na sua experimentação ao duplicar (cinco vezes) para essas entradas que representam a alguém com um risco elevado crédito. 

Em seguida, se o modelo misclassifies alguém como um risco de crédito baixa quando eles são, na verdade, um risco elevado, o modelo faz essa mesma misclassification cinco vezes, uma vez para cada duplicado. Isso aumentará o custo deste erro nos resultados de treinamento.


### <a name="convert-the-dataset-format"></a>Converter o formato de conjunto de dados

O conjunto de dados original usa um formato separados em branco. O Machine Learning Studio funciona melhor com um ficheiro de valores separados por vírgulas (CSV), portanto, converterá o conjunto de dados, substituindo espaços com vírgulas.  

Existem várias formas de converter esses dados. Uma forma é utilizando o seguinte comando Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Outra forma é através do comando de po Unix:  

    sed 's/ /,/g' german.data > german.csv  

Em ambos os casos, criou uma versão separada por vírgulas dos dados num arquivo chamado **german.csv** que pode utilizar na sua experimentação.

### <a name="upload-the-dataset-to-machine-learning-studio"></a>Carregar o conjunto de dados para Machine Learning Studio

Depois dos dados foi convertidos em formato CSV, tem de carregá-lo para Machine Learning Studio. 

1. Abrir a home page do Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Clique no menu ![Menu](./media/tutorial-part1-credit-risk/menu.png) no canto superior esquerdo da janela, clique **do Azure Machine Learning**, selecione **Studio**e iniciar sessão.

3. Clique em **+ novo** na parte inferior da janela.

4. Selecione **conjunto de dados**.

5. Selecione **do ficheiro LOCAL**.

    ![Adicionar um conjunto de dados a partir de um ficheiro local](./media/tutorial-part1-credit-risk/add-dataset.png)

6. Na **carregar um novo conjunto de dados** caixa de diálogo, clique em Procurar e localizar o **german.csv** ficheiro que criou.

7. Introduza um nome para o conjunto de dados. Para este tutorial, chamá-lo de "Dados de cartão de crédito de alemão de UCI".

8. Para o tipo de dados, selecione **arquivo CSV genérico com nenhum cabeçalho (. nh.csv)**.

9. Adicione uma descrição, se desejar.

10. Clique nas **OK** marca de verificação.  

    ![Carregar o conjunto de dados](./media/tutorial-part1-credit-risk/upload-dataset.png)

Isso carrega os dados num módulo de conjunto de dados que pode usar numa experimentação.

Pode gerir conjuntos de dados que carregar para o Studio clicando a **conjuntos de dados** separador à esquerda da janela do Studio.

![Gerir conjuntos de dados](./media/tutorial-part1-credit-risk/dataset-list.png)

Para obter mais informações sobre a importação de outros tipos de dados para uma experimentação, consulte [importar os dados de treinamento para o Azure Machine Learning Studio](import-data.md).

## <a name="create-an-experiment"></a>Criar uma experimentação

Neste tutorial, a próxima etapa é criar uma experimentação no Machine Learning Studio, que utiliza o conjunto de dados que carregou.  

1. No Studio, clique em **+ novo** na parte inferior da janela.
1. Selecione **experimentação**e, em seguida, selecione "Experimentação em branco". 

    ![Criar uma nova experimentação](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Selecione o nome predefinido da experimentação na parte superior da tela e renomeá-lo para algo significativo.

    ![Mudar o nome de experimentação](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > É uma boa prática para preencher **resumo** e **Descrição** para a experimentação no **propriedades** painel. Estas propriedades dão-lhe a oportunidade de se documentar a experimentação, para que qualquer pessoa que olha para ela mais tarde será compreender suas metas e metodologia.
   > 
   > ![Propriedades de experimentação](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. Na paleta do módulo para a esquerda da tela de experimentação, expanda **conjuntos de dados guardado**.
1. Localize o conjunto de dados que criou em **conjuntos de dados de meu** e arraste-o para a tela. Também pode encontrar o conjunto de dados ao introduzir o nome na **pesquisa** caixa acima a paleta.  

    ![Adicionar o conjunto de dados para a experimentação](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Preparar os dados

Pode ver as primeiras 100 linhas dos dados e algumas informações estatísticas para todo o conjunto de dados: Clique na porta de saída do conjunto de dados (o pequeno círculo na parte inferior) e selecione **Visualize**.  

Uma vez que o ficheiro de dados não vinha com cabeçalhos de coluna, Studio forneceu cabeçalhos genéricos (Col1, Col2, *etc.*). Boa cabeçalhos não são essenciais para a criação de um modelo, mas eles tornam mais fácil trabalhar com os dados na experimentação. Além disso, quando publica, eventualmente, esse modelo num serviço da web, os títulos ajudam a identificar as colunas para o utilizador do serviço.  

Pode adicionar cabeçalhos de coluna com o [Editar metadados] [ edit-metadata] módulo.

Utilizar o [Editar metadados] [ edit-metadata] módulo para alterar os metadados associados um conjunto de dados. Neste caso, usá-lo para fornecer nomes amigáveis mais para títulos de coluna. 

Para utilizar [Editar metadados][edit-metadata], primeiro tem de especificar as colunas que pretende modificar (no caso, todos eles.) Em seguida, especificar a ação a ser executada nessas colunas (no caso, alterar os cabeçalhos de coluna.)

1. Na paleta do módulo, escreva "metadados" a **pesquisa** caixa. O [Editar metadados] [ edit-metadata] aparece na lista de módulos.

1. Clique e arraste a [Editar metadados] [ edit-metadata] módulo para a tela e largue-o abaixo o conjunto de dados que adicionou anteriormente.

1. Ligar o conjunto de dados para o [Editar metadados][edit-metadata]: clique na porta de saída do conjunto de dados (o pequeno círculo na parte inferior do conjunto de dados), arraste para a porta de entrada de [Editar metadados] [ edit-metadata] (o pequeno círculo na parte superior do módulo), em seguida, largue o botão do mouse. O módulo e o conjunto de dados permanecem conectados, mesmo se move qualquer um na tela.
 
    A experimentação deverá agora ser semelhante ao seguinte:  

    ![Adicionar metadados de edição](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    O ponto de exclamação vermelho indica que ainda não o defina as propriedades para este módulo ainda. Fazer de seguida.

    > [!TIP]
    > Pode adicionar um comentário a um módulo, fazendo duplo clique no módulo e introduzindo o texto. Isto pode ajudá-lo a ver rapidamente o que o módulo está a fazer na sua experimentação. Neste caso, faça duplo clique nas [Editar metadados] [ edit-metadata] módulo e escreva os comentário "Adicionar cabeçalhos de coluna". Clique em qualquer outra coisa na tela para fechar a caixa de texto. Para apresentar o comentário, clique na seta para baixo no módulo.
    > 
    > ![Editar o módulo de metadados com o comentário adicionado](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. Selecione [Editar metadados][edit-metadata]e, no **propriedades** painel à direita da tela, clique em **iniciar Seletor de colunas**.

1. Na **selecionar colunas** caixa de diálogo, selecione todas as linhas na **colunas disponíveis** e clique em > para movê-los para **Selected Columns**.
   A caixa de diálogo deve ter este aspeto:

   ![Seletor de colunas com todas as colunas selecionadas](./media/tutorial-part1-credit-risk/select-columns.png)


1. Clique nas **OK** marca de verificação.

1. De volta a **propriedades** painel, procure o **novos nomes das colunas** parâmetro. Neste campo, introduza uma lista de nomes para a 21 de colunas no conjunto de dados, separados por vírgulas e a ordem da coluna. Pode obter os nomes de colunas na documentação do conjunto de dados no site da UCI, ou para sua comodidade pode copiar e colar a lista seguinte:  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   O painel de propriedades tem esta aparência:

   ![Propriedades de metadados de edição](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > Se pretender verificar os cabeçalhos de coluna, execute a experimentação (clique em **executar** abaixo a tela da experimentação). Quando ele for concluída, em execução (aparece uma marca de verificação verde [Editar metadados][edit-metadata]), clique na porta de saída do [Editar metadados] [ edit-metadata] módulo, e selecione **Visualize**. Pode ver a saída de qualquer módulo da mesma forma para ver o progresso dos dados através da experimentação.
   > 
   > 

### <a name="create-training-and-test-datasets"></a>A criação de treinamento e teste de conjuntos de dados

Precisa de alguns dados para preparar o modelo e alguns para testá-lo.
Para que o passo seguinte da experimentação, dividir o conjunto de dados em dois conjuntos de dados separados: um para o nosso modelo e outra para testá-lo de treinamento.

Para tal, utilize o [Split Data] [ split] módulo.  

1. Encontrar o [Split Data] [ split] módulo, arraste-o para a tela e ligá-la para o [Editar metadados] [ edit-metadata] módulo.

1. Por predefinição, a proporção de divisão é 0,5 e o **Randomized split** parâmetro estiver definido. Isso significa que um meio aleatório dos dados é a saída por meio de uma porta do [Split Data] [ split] módulo e metade através de outro. Pode ajustar esses parâmetros, bem como o **seed aleatório** parâmetro, para alterar a divisão entre preparação e teste de dados. Neste exemplo, deixá-los como-é.
   
   > [!TIP]
   > A propriedade **fração de linhas no primeiro conjunto de dados de saída** determina quanto dos dados de saída através da *esquerdo* porta de saída. Por exemplo, se definir a proporção de 0,7, em seguida, 70% dos dados é saída pela porta esquerda e 30% por meio da porta à direita.  
   > 
   > 

1. Clique duas vezes o [Split Data] [ split] módulo e introduza o comentário, "dados de treinamento/teste dividir 50%". 

Pode utilizar as saídas da [Split Data] [ split] módulo no entanto, como, mas vamos optar por utilizar a saída à esquerda, como teste de dados de saída de dados de treinamento e à direita.  

Conforme mencionado na [passo anterior](tutorial-part1-credit-risk.md#upload), o custo de misclassifying um risco elevado crédito como baixa é cinco vezes maior do que o custo de misclassifying um risco de crédito baixa como elevado. Para justificar isso, gerar um novo conjunto de dados reflete essa função de custo. No novo conjunto de dados, cada exemplo de alto risco é replicado cinco vezes, enquanto cada exemplo de baixo risco não é replicado.   

Pode fazer essa replicação utilizam código R:  

1. Localize e arraste a [executar Script R] [ execute-r-script] módulo para a tela de experimentação. 

1. Ligue a porta de saída à esquerda do [Split Data] [ split] módulo para a primeira porta de entrada ("Dataset1") da [executar Script do R] [ execute-r-script] módulo.

1. Faça duplo clique o [executar Script R] [ execute-r-script] módulo e introduza o comentário "Conjunto de ajuste de custo".

1. Na **propriedades** painel, elimine o texto predefinido no **R Script** parâmetro e introduza este script:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Script do R no módulo executar Script R](./media/tutorial-part1-credit-risk/execute-r-script.png)

Precisa fazer essa mesma operação de replicação para cada resultado do [Split Data] [ split] módulo para que os dados de treinamento e testes tenham o mesma ajuste de custo. A maneira mais fácil de fazê-lo é ao duplicar o [executar Script R] [ execute-r-script] módulo que acabou de criar e ligar a outra porta de saída a [Split Data] [ split] módulo.

1. Com o botão direito a [executar Script R] [ execute-r-script] módulo e selecione **cópia**.

1. A tela de experimentação com o botão direito e selecione **colar**.

1. Arraste o novo módulo para a posição pretendida e, em seguida, ligue a porta de saída de direito do [Split Data] [ split] módulo para a primeira porta de entrada deste novo [executar Script do R] [ execute-r-script] módulo. 

1. Na parte inferior da tela, clique em **executar**. 

> [!TIP]
> A cópia do módulo executar Script R contém o mesmo script como o módulo original. Quando copiar e colar um módulo na tela, a cópia retém todas as propriedades do original.  
> 
>

Nosso experimentação agora terá um aspeto semelhante ao seguinte:

![Adicionar o módulo de divisão e R scripts](./media/tutorial-part1-credit-risk/experiment.png)

Para obter mais informações sobre como utilizar os R scripts no suas experimentações, consulte [expandir a sua experiência com R](extend-your-experiment-with-r.md).


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, concluiu estes passos: 
 
> [!div class="checklist"]
> * Criar uma área de trabalho do Machine Learning Studio
> * Carregar dados existentes para a área de trabalho
> * Criar uma experimentação

Agora está pronto para preparar e avaliar modelos para estes dados.

> [!div class="nextstepaction"]
> [Tutorial 2 - preparar e avaliar modelos](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/