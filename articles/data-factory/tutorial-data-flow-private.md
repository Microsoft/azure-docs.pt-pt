---
title: Transformar dados com uma Azure Data Factory gerido fluxo de dados de mapeamento de rede virtual
description: Este tutorial fornece instruções passo a passo para a utilização da Azure Data Factory para transformar dados com fluxos de dados de mapeamento.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/19/2019
ms.openlocfilehash: d752b747a0156bcef587f81ee421c55a6de81e17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079477"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Transforme os dados de forma segura utilizando o fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

Neste tutorial, você usará a interface de utilizador da Data Factory (UI) para criar um pipeline que copia e transforma dados *de uma fonte de armazenamento de dados do Lago de Dados Gen2 para um lavatório De Armazenamento de Dados Gen2 (ambos permitindo o acesso a apenas redes selecionadas)* usando o fluxo de dados de mapeamento na Rede Virtual Gerida pela Data [Factory.](managed-virtual-network-private-endpoint.md) Pode expandir-se no padrão de configuração neste tutorial quando transforma dados utilizando o fluxo de dados de mapeamento.

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
>
> * Criar uma fábrica de dados.
> * Criar um oleoduto com uma atividade de fluxo de dados.
> * Construa um fluxo de dados de mapeamento com quatro transformações.
> * Testar a execução do pipeline.
> * Monitorize uma atividade de fluxo de dados.

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura Azure**. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Azure**. Utiliza o Armazenamento do Lago de Dados como *fonte* e *lava* datas. Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) para seguir os passos para criar uma. *Certifique-se de que a conta de armazenamento permite o acesso apenas a partir de redes selecionadas.* 

O ficheiro que vamos transformar neste tutorial é moviesDB.csv, que pode ser encontrado neste [site de conteúdo do GitHub.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Para recuperar o ficheiro do GitHub, copie o conteúdo para um editor de texto à sua escolha para o guardar localmente como ficheiro .csv. Para fazer o upload do ficheiro para a sua conta de armazenamento, consulte [as bolhas de upload com o portal Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Os exemplos referenciarão um contentor denominado **dados de amostra.**

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Neste passo, cria-se uma fábrica de dados e abre-se a UI da Data Factory para criar um oleoduto na fábrica de dados.

1. Abra o Microsoft Edge ou o Google Chrome. Atualmente, apenas os navegadores Web Microsoft Edge e Google Chrome suportam o UI da Data Factory.
1. No menu esquerdo, **selecione Criar uma**Fábrica de  >  **Analytics**  >  **Dados**de Análise de Recursos .
1. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

   O nome da fábrica de dados tem de ser *globalmente exclusivo*. Se receber uma mensagem de erro sobre o valor do nome, insira um nome diferente para a fábrica de dados (por exemplo, o seu nomeADFTutorialDataFactory). Para obter as regras de nomenclatura dos artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).

1. Selecione a **subscrição** do Azure na qual pretende criar a fábrica de dados.
1. Em **Grupo de Recursos**, efetue um destes passos:

    * Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.
    * Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md). 
1. Em **Versão**, selecione **V2**.
1. Em **Localização**, selecione uma localização para a fábrica de dados. Apenas os locais suportados aparecem na lista de suspensos. As lojas de dados (por exemplo, Azure Storage e Azure SQL Database) e os cálculos (por exemplo, Azure HDInsight) utilizados pela fábrica de dados podem estar noutras regiões.

1. Selecione **Criar**.
1. Depois de concluída a criação, vê o aviso no centro de Notificações. Selecione **Ir para o recurso** para ir à página data **Factory.**
1. Selecione **Criar e Monitorizar** para iniciar a IU do Data Factory num separador à parte.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Criar um Azure IR na Rede Virtual Gerida pela Fábrica de Dados
Neste passo, cria-se um Azure IR e ativa a Rede Virtual Gerida pela Data Factory.

1. No portal Data Factory, vá a **Manage,** e selecione **Novo** para criar um novo Azure IR.

   ![Screenshot que mostra a criação de um novo Azure IR.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Selecione a opção **Azure** IR.

   ![Screenshot que mostra um novo Azure IR.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. Na **configuração da rede virtual (Pré-visualização)**, selecione **Enable**.

   ![Screenshot que mostra permitir um novo Azure IR.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Selecione **Criar**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Criar um oleoduto com uma atividade de fluxo de dados

Neste passo, irá criar um oleoduto que contém uma atividade de fluxo de dados.

1. Na página **Vamos começar**, selecione **Criar pipeline**.

   ![Screenshot que mostra a criação de um oleoduto.](./media/doc-common-process/get-started-page.png)

1. No painel de propriedades para o oleoduto, **insira a TransformMovies** para obter o nome do gasoduto.
1. Na barra superior da fábrica, deslize o **depurador de fluxo de dados.** O modo Debug permite testes interativos da lógica de transformação contra um cluster de Faíscas ao vivo. Os aglomerados de fluxo de dados demoram 5 a 7 minutos a aquecer. Ligue o **fluxo de dados depurar** primeiro se planeia fazer o desenvolvimento do fluxo de dados. Para mais informações, consulte [o modo Debug.](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode)

    ![Screenshot que mostra o depurador de fluxo de dados.](media/tutorial-data-flow-private/dataflow-debug.png)
1. No painel **de atividades,** expanda **o Move and Transform.** Arraste a atividade do Fluxo de **Dados** do painel para a tela do gasoduto.

1. No pop-up do fluxo de **dados de adicionar,** selecione **Criar um novo fluxo de dados** e, em seguida, selecione Mapping Data **Flow**. Selecione **OK** quando terminar.

    ![Screenshot que mostra mapeamento do fluxo de dados.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Nomeie o seu fluxo de **dados TransformMovies** no painel de propriedades.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Construir lógica de transformação na tela do fluxo de dados

Depois de criar o fluxo de dados, será automaticamente enviado para a tela de fluxo de dados. Neste passo, você vai construir um fluxo de dados que leva o arquivo moviesDB.csv no Armazenamento de Data Lake e agrega a classificação média de comédias de 1910 a 2000. Em seguida, irá escrever este ficheiro de volta para o armazenamento de Data Lake.

### <a name="add-the-source-transformation"></a>Adicione a transformação da fonte

Neste passo, configuras o Data Lake Storage Gen2 como fonte.

1. Na tela de fluxo de dados, adicione uma fonte selecionando a caixa **Add Source.**

1. Diga o nome da sua fonte **MoviesDB**. Selecione **Novo** para criar um novo conjunto de dados de origem.

1. Selecione **Azure Data Lake Storage Gen2**e, em seguida, selecione **Continue**.

1. Selecione **DelimitedText**e, em seguida, **selecione Continue**.

1. Nomeie o seu conjunto de **dados MoviesDB**. Na entrega de serviços ligada, selecione **New**.

1. No ecrã de criação de serviços ligado, nomeie o seu serviço ligado ao Data Lake Storage Gen2 **ADLSGen2** e especifique o seu método de autenticação. Em seguida, insira as suas credenciais de ligação. Neste tutorial, estamos a usar a **chave conta** para ligar à nossa conta de armazenamento. 

1. Certifique-se de que ativa **a autoria interativa.** Pode levar um minuto para ser ativado.

    ![Screenshot que mostra a autoria interativa.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Selecione **Testar ligação**. Deve falhar porque a conta de armazenamento não permite o acesso à sua sem a criação e aprovação de um ponto final privado. Na mensagem de erro, deverá ver um link para criar um ponto final privado que pode seguir para criar um ponto final privado gerido. Uma alternativa é ir diretamente ao **separador Gerir** e seguir as instruções [desta secção](#create-a-managed-private-endpoint) para criar um ponto final privado gerido.

1. Mantenha a caixa de diálogo aberta e, em seguida, vá para a sua conta de armazenamento.

1. Siga as instruções [nesta secção](#approval-of-a-private-link-in-a-storage-account) para aprovar o link privado.

1. Volta para a caixa de diálogo. Selecione a **ligação de teste** novamente e selecione **Criar** para implementar o serviço ligado.

1. No ecrã de criação do conjunto de dados, insira onde o seu ficheiro está localizado no campo **do caminho do Ficheiro.** Neste tutorial, o ficheiro moviesDB.csv está localizado nos **dados**da amostra do recipiente. Como o ficheiro tem cabeçalhos, selecione a primeira linha como caixa de verificação **do cabeçalho.** Selecione **Da ligação/loja** para importar o esquema do cabeçalho diretamente do ficheiro armazenado. Selecione **OK** quando terminar.

    ![Screenshot que mostra o caminho da origem.](media/tutorial-data-flow-private/source-file-path.png)

1. Se o seu cluster de depuração tiver começado, vá ao **separador Desatenção** de Dados da transformação da fonte e selecione **Refresh** para obter uma imagem dos dados. Pode utilizar a pré-visualização de dados para verificar se a sua transformação está configurada corretamente.

    ![Screenshot que mostra o separador De pré-visualização de dados.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Criar um ponto final privado gerido

Se não usou a hiperligação quando testou a ligação anterior, siga o caminho. Agora precisa de criar um ponto final privado gerido que irá ligar ao serviço ligado que criou.

1. Vá ao **separador Gerir.**

   > [!NOTE]
   > O **separador Manage** pode não estar disponível para todas as instâncias da Data Factory. Se não o vir, pode aceder a pontos finais privados selecionando **o Ponto**  >  De Terminante Privado de**Ligações**  >  **de**Autor .

1. Aceda à secção **de pontos finais privados geridos.**
1. Selecione **+ Novos** **pontos finais privados geridos**.

    ![Screenshot que mostra o botão novo dos pontos finais privados geridos.](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Selecione o azulejo **Azure Data Lake Storage Gen2** da lista e selecione **Continue**.
1. Insira o nome da conta de armazenamento que criou.
1. Selecione **Criar**.
1. Após alguns segundos, deve ver que o link privado criado precisa de uma aprovação.
1. Selecione o ponto final privado que criou. Pode ver uma hiperligação que o levará a aprovar o ponto final privado ao nível da conta de armazenamento.

    ![Screenshot que mostra o painel de pontos finais privados Manage.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Aprovação de um link privado numa conta de armazenamento

1. Na conta de armazenamento, aceda às **ligações de ponto final privados** na secção **Definições.**

1. Selecione a caixa de verificação pelo ponto final privado que criou e selecione **Aprovar**.

    ![Screenshot que mostra o botão de aprovação do ponto final privado.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Adicione uma descrição e selecione **sim**.
1. Volte para a secção de **pontos finais privados geridos** do **separador Managed** na Data Factory.
1. Após cerca de um minuto, deverá ver a aprovação aparecer para o seu ponto final privado.

### <a name="add-the-filter-transformation"></a>Adicione a transformação do filtro

1. Ao lado do seu nó de origem na tela de fluxo de dados, selecione o ícone plus para adicionar uma nova transformação. A primeira transformação que vai adicionar é um **Filtro.**

    ![Screenshot que mostra a adição de um filtro.](media/tutorial-data-flow-private/add-filter.png)
1. Nomeie o seu filter transformation **FilterYears**. Selecione a caixa de expressão ao lado **do Filtro para** abrir o construtor de expressão. Aqui irá especificar a sua condição de filtragem.

    ![Screenshot que mostra FilterYears.](media/tutorial-data-flow-private/filter-years.png)
1. O construtor de expressão de fluxo de dados permite-lhe construir expressões interativas para usar em várias transformações. As expressões podem incluir funções incorporadas, colunas do esquema de entrada e parâmetros definidos pelo utilizador. Para obter mais informações sobre como construir expressões, consulte [o construtor de expressão de fluxo de dados.](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder)

    * Neste tutorial, quer filtrar filmes no género da comédia que saiu entre os anos de 1910 e 2000. Como o ano é atualmente uma corda, é preciso convertê-lo num inteiro usando a ```toInteger()``` função. Utilize os operadores maiores ou iguais (>=) e inferiores ou iguais aos operadores (<=) para comparar com os valores do ano literal de 1910 e 2000. União estas expressões juntamente com o operador e (&&). A expressão sai como:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Para descobrir quais filmes são comédias, você pode usar a ```rlike()``` função para encontrar o padrão 'Comédia' nos géneros das colunas. União a expressão rlike com a comparação do ano para obter:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Se tiver um cluster de depuração ativo, pode verificar a sua lógica selecionando **Refresh** para ver a saída da expressão em comparação com as entradas utilizadas. Há mais do que uma resposta certa sobre como pode realizar esta lógica usando a linguagem de expressão do fluxo de dados.

        ![Screenshot que mostra a expressão do filtro.](media/tutorial-data-flow-private/filter-expression.png)

    * **Selecione Save and finish** after you're finish with your expression.

1. Pegue uma **pré-visualização de dados** para verificar se o filtro está a funcionar corretamente.

    ![Screenshot que mostra a visualização de dados filtrada.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Adicione a transformação agregada

1. A próxima transformação que vai adicionar é uma transformação **agregada** sob **o modificador Schema.**

    ![Screenshot que mostra a adição do agregado.](media/tutorial-data-flow-private/add-aggregate.png)
1. Nomeie a sua transformação **agregada AggregateComedyRating**. No **Grupo por** aba, selecione o **ano** da caixa de entrega para agrupar as agregações até ao ano em que o filme saiu.

    ![Screenshot que mostra o grupo agregado.](media/tutorial-data-flow-private/group-by-year.png)
1. Vá ao **separador Agregados.** Na caixa de texto esquerda, nomeie a coluna agregada **AverageComedyRating**. Selecione a caixa de expressão certa para introduzir a expressão agregada através do construtor de expressão.

    ![Screenshot que mostra o nome da coluna agregada.](media/tutorial-data-flow-private/name-column.png)
1. Para obter a média de **classificação**de coluna, utilize a ```avg()``` função agregada. Como **a classificação** é uma corda e ```avg()``` requer uma entrada numérica, temos de converter o valor num número através da ```toInteger()``` função. Esta expressão parece:

    ```avg(toInteger(Rating))```

1. **Selecione Save e termine** depois de terminar.

    ![Screenshot que mostra salvar o agregado.](media/tutorial-data-flow-private/save-aggregate.png)
1. Aceda ao **separador Data Preview** para ver a saída de transformação. Note que apenas existem duas colunas, **ano** e **Média ComedyRating**.

### <a name="add-the-sink-transformation"></a>Adicione a transformação do lavatório

1. Em seguida, você quer adicionar uma transformação **de Sink** em **Destino**.

    ![Screenshot que mostra adicionar uma pia.](media/tutorial-data-flow-private/add-sink.png)
1. Diga o nome da **pia.** Selecione **Novo** para criar o conjunto de dados da pia.

    ![Screenshot que mostra a criação de uma pia.](media/tutorial-data-flow-private/create-sink.png)
1. Na nova página de **conjunto de dados,** selecione **Azure Data Lake Storage Gen2** e, em seguida, selecione **Continue**.

1. Na página de **formato Select,** selecione **DelimitedText** e, em seguida, selecione **Continue**.

1. Nomeie o seu conjunto de dados de pia **MoviesSink**. Para o serviço ligado, escolha o mesmo serviço ligado **ADLSGen2** que criou para a transformação de fontes. Introduza uma pasta de saída para escrever os seus dados para. Neste tutorial, estamos escrevendo para a **saída** da pasta nos **dados**da amostra do recipiente. A pasta não precisa de existir previamente e pode ser criada dinamicamente. Selecione a primeira linha como caixa de verificação **do cabeçalho** e selecione **Nenhum** para **o esquema de importação**. Selecione **OK**.

    ![Screenshot que mostra o caminho da pia.](media/tutorial-data-flow-private/sink-file-path.png)

Agora já terminou de construir o seu fluxo de dados. Está pronto para correr no seu oleoduto.

## <a name="run-and-monitor-the-data-flow"></a>Executar e monitorizar o fluxo de dados

Pode depurar um oleoduto antes de publicá-lo. Neste passo, desencadeia-se uma fuga de depurar o gasoduto de fluxo de dados. Embora a pré-visualização dos dados não escreva dados, uma corrida de depurar escreverá dados para o seu destino de pia.

1. Vai para a tela do oleoduto. Selecione **Debug** para desencadear uma corrida de depurador.

1. A depuração de gasodutos das atividades de fluxo de dados utiliza o cluster de depuração ativo, mas ainda demora pelo menos um minuto a ser inicializado. Pode acompanhar o progresso através do separador **Saída.** Depois de ter sido bem sucedido, selecione o ícone de óculos para obter detalhes de execução.

1. Na página de detalhes, pode ver o número de linhas e o tempo gasto em cada passo de transformação.

    ![Screenshot que mostra uma execução de monitorização.](media/tutorial-data-flow-private/run-details.png)
1. Selecione uma transformação para obter informações detalhadas sobre as colunas e a divisão dos dados.

Se seguiu corretamente este tutorial, deverá ter escrito 83 linhas e 2 colunas na sua pasta da pia. Pode verificar se os dados estão corretos verificando o armazenamento do seu blob.

## <a name="summary"></a>Resumo

Neste tutorial, você usou o UI da Data Factory para criar um pipeline que copia e transforma dados de uma fonte de Data Lake Storage Gen2 para um lavatório De Armazenamento de Dados Gen2 (ambos permitindo o acesso a apenas redes selecionadas) usando o fluxo de dados de mapeamento na Rede Virtual Gerida pela [Data Factory.](managed-virtual-network-private-endpoint.md)
