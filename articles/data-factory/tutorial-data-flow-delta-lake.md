---
title: Delta lake ETL com fluxos de dados
description: Este tutorial fornece instruções passo a passo para usar fluxos de dados para transformar e analisar dados em lago delta
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/14/2021
ms.openlocfilehash: a0fb3488aec6761764e30b4cf8556b71e5edda15
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515415"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Transforme dados no lago delta usando fluxos de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você usará a tela de fluxo de dados para criar fluxos de dados que lhe permitem analisar e transformar dados em Azure Data Lake Storage (ADLS) Gen2 e armazená-los em Delta Lake.

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura Azure**. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Azure**. Utiliza o armazenamento ADLS como *fonte* e *lava* datas. Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) para seguir os passos para criar uma.

O ficheiro que estamos a transformar neste tutorial é MoviesDB.csv, que pode ser encontrado [aqui.](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv) Para recuperar o ficheiro do GitHub, copie o conteúdo para um editor de texto à sua escolha para guardar localmente como um ficheiro .csv. Para fazer o upload do ficheiro para a sua conta de armazenamento, consulte [as bolhas de upload com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Os exemplos serão referentes a um contentor chamado "dados de amostra".

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Neste passo, cria-se uma fábrica de dados e abre-se o Data Factory UX para criar um pipeline na fábrica de dados.

1. Abra **o Microsoft Edge** ou o Google **Chrome**. Atualmente, a Data Factory UI é suportada apenas nos navegadores Web Microsoft Edge e Google Chrome.
1. No menu à esquerda, **selecione Criar uma** Fábrica de  >  Dados de **Integração de**  >   Recursos
1. Na nova página de **fábrica de dados,** em **Nome,** insira **ADFTutorialDataFactory**
1. Selecione a **subscrição** do Azure na qual pretende criar a fábrica de dados.
1. Em **Grupo de Recursos**, efetue um destes passos:

    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    b. Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md). 
1. Em **Versão**, selecione **V2**.
1. Em **Localização**, selecione uma localização para a fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. As lojas de dados (por exemplo, Azure Storage e SQL Database) e os cálculos (por exemplo, Azure HDInsight) utilizados pela fábrica de dados podem estar noutras regiões.
1. Selecione **Criar**.
1. Depois de concluída a criação, vê o aviso no Centro de Notificações. Selecione **Ir para o recurso** para navegar para a página de fábrica de dados.
1. Selecione **Criar e Monitorizar** para iniciar a IU do Data Factory num separador à parte.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Criar um oleoduto com uma atividade de fluxo de dados

Neste passo, irá criar um oleoduto que contém uma atividade de fluxo de dados.

1. Na página **Vamos começar**, selecione **Criar pipeline**.

   ![Criar pipeline](./media/doc-common-process/get-started-page.png)

1. Na guia **geral** para o gasoduto, insira **deltaLake** para **o nome** do oleoduto.
1. No painel **de atividades,** expanda o **acordeão Move and Transform.** Arraste e deixe cair a atividade do Fluxo de **Dados** do painel para a tela do gasoduto.

    ![Screenshot que mostra a tela do gasoduto onde pode deixar cair a atividade do Data Flow.](media/tutorial-data-flow/activity1.png)
1. No pop-up do Fluxo de **Dados adicionando,** selecione **Criar novo fluxo de dados** e, em seguida, nomear o seu fluxo de dados **DeltaLake**. Clique em Terminar quando terminar.

    ![Screenshot que mostra onde nomeia o fluxo de dados quando cria um novo fluxo de dados.](media/tutorial-data-flow/activity2.png)
1. Na barra superior da tela do gasoduto, deslize o **depurador de fluxo de dados.** O modo Debug permite testes interativos da lógica de transformação contra um cluster de Faíscas ao vivo. Os clusters Data Flow demoram 5 a 7 minutos a aquecer e os utilizadores são recomendados a ligar primeiro o depurg se planeiam fazer o desenvolvimento do Fluxo de Dados. Para mais informações, consulte [o Modo Debug](concepts-data-flow-debug-mode.md).

    ![Atividade do fluxo de dados](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas&quot;></a>Construir lógica de transformação na tela do fluxo de dados

Irá gerar dois fluxos de dados neste tutorial. O fluxo de dados do punho é uma fonte simples para afundar para gerar um novo Delta Lake a partir dos filmes ficheiro CSV de cima. Por último, irá criar este design de fluxo abaixo para atualizar dados em Delta Lake.

![Fluxo final](media/data-flow/data-flow-tutorial-6.png &quot;Fluxo final")

### <a name="tutorial-objectives"></a>Objetivos tutoriais

1. Pegue a fonte de conjunto de dados MoviesCSV de cima, forme um novo Delta Lake a partir dele
1. Construa a lógica para atualizar as audiências para filmes de 1988 para '1'
1. Apagar todos os filmes de 1950
1. Insira novos filmes para 2021 duplicando os filmes de 1960

### <a name="start-from-a-blank-data-flow-canvas"></a>Comece a partir de uma tela de fluxo de dados em branco

1. Clique na transformação da fonte
1. Clique em novo ao lado do conjunto de dados no painel inferior 1 Crie um novo Serviço Ligado para ADLS Gen2
1. Escolha Texto Delimitado para o tipo de conjunto de dados
1. Nomeie o conjunto de dados "MoviesCSV" 
1. Aponte para o ficheiro MoviesCSV que carregou para o armazenamento acima
1. Desafirá-lo para ser delimitado e incluir cabeçalho na primeira linha 
1. Vá ao separador de projeção de origem e clique em "Detetar tipos de dados"
1. Uma vez que tenha o seu conjunto de projeção, você pode continuar 
1. Adicione uma transformação de pia
1. Delta é um tipo de conjunto de dados inline. Terá de apontar para a sua conta de armazenamento ADLS Gen2.
   
   ![Conjunto de dados inline](media/data-flow/data-flow-tutorial-5.png "Conjunto de dados inline")

1. Escolha um nome de pasta no seu recipiente de armazenamento onde gostaria que a ADF criasse o Lago Delta
1. Volte ao designer de gasodutos e clique em Debug para executar o pipeline em modo depurrão apenas com esta atividade de fluxo de dados na tela. Isto gerará o seu novo Delta Lake na ADLS Gen2.
1. A partir de Recursos de Fábrica, clique em novo fluxo de dados > 
1. Volte a utilizar o MoviesCSV como fonte e clique em "Detetar tipos de dados" novamente
1. Adicione uma transformação de filtro à sua transformação de origem no gráfico
1. Só permita filas de filmes que correspondam aos três anos com que vai trabalhar, que serão 1950, 1988 e 1960.
1. Atualizar as classificações para cada filme de 1988 para '1' adicionando agora uma transformação de coluna derivada à sua transformação de filtro
1. Nessa mesma coluna derivada, crie filmes para 2021, tirando um ano existente e alterando o ano para 2021. Vamos escolher 1960.
1. Isto é o que as suas três colunas derivadas serão

   ![Coluna derivada](media/data-flow/data-flow-tutorial-2.png "Coluna derivada")
   
1. ```Update, insert, delete, and upsert``` as políticas são criadas na transformação alter Row. Adicione uma transformação de linha de alter após a sua coluna derivada.
1. As tuas políticas de alter linha devem ser assim.

   ![Alterar linha](media/data-flow/data-flow-tutorial-3.png "Alterar linha")
   
1. Agora que definiu a política adequada para cada tipo de linha de alteração, verifique se as regras de atualização adequadas foram definidas na transformação do lavatório

   ![Sink](media/data-flow/data-flow-tutorial-4.png "Sink")
   
1. Aqui estamos a usar a pia do Delta Lake para o seu lago de dados ADLS Gen2 e permitindo inserções, atualizações, eliminações. 
1. Note que as Colunas-Chave são uma chave composta composta pela coluna-chave primária do Filme e coluna do ano. Isto porque criámos filmes falsos para 2021 duplicando as linhas de 1960. Isto evita colisões quando se olha para cima das linhas existentes, proporcionando singularidade.

### <a name="download-completed-sample"></a>Descarregar amostra concluída
[Aqui está uma solução de amostra para o gasoduto Delta com um fluxo de dados para linhas de atualização/eliminação no lago:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a linguagem de expressão do [fluxo de dados.](data-flow-expression-functions.md)
