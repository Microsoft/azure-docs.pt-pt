---
title: Melhores práticas para escrever para ficheiros para data lake com fluxos de dados
description: Este tutorial fornece as melhores práticas para escrever para ficheiros para data lake com fluxos de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/01/2021
ms.openlocfilehash: 8010f3f95c9358714b659df5821a375bd8488ad8
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582319"
---
# <a name="best-practices-for-writing-to-files-to-data-lake-with-data-flows"></a>Melhores práticas para escrever para ficheiros para data lake com fluxos de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, você aprenderá as melhores práticas que podem ser aplicadas ao escrever ficheiros para ADLS Gen2 ou Azure Blob Storage usando fluxos de dados. Você precisará de acesso a uma conta de armazenamento Azure Blob ou Azure Data Lake Store Gen2 para ler um ficheiro parquet e, em seguida, armazenar os resultados em pastas.

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura Azure**. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Azure**. Utiliza o armazenamento ADLS como *fonte* e *lava* datas. Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) para seguir os passos para criar uma.

Os passos neste tutorial vão assumir que você tem 

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Neste passo, cria-se uma fábrica de dados e abre-se o Data Factory UX para criar um pipeline na fábrica de dados.

1. Abra **o Microsoft Edge** ou o Google **Chrome**. Atualmente, a Data Factory UI é suportada apenas nos navegadores Web Microsoft Edge e Google Chrome.
1. No menu à esquerda, **selecione Criar uma** Fábrica de  >  Dados de **Integração de**  >   Recursos
1. Na nova página de **fábrica de dados,** em **Nome,** insira **ADFTutorialDataFactory**
1. Selecione a **subscrição** do Azure na qual pretende criar a fábrica de dados.
1. Em **Grupo de Recursos**, efetue um destes passos:

    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.
    
    b. **Selecione Criar novo** e insira o nome de um grupo de recursos. Para conhecer os grupos de recursos, consulte [utilize grupos de recursos para gerir os seus recursos Azure](../azure-resource-manager/management/overview.md).
    
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
1. Na barra superior da fábrica, deslize o **depurador de fluxo de dados.** O modo Debug permite testes interativos da lógica de transformação contra um cluster de Faíscas ao vivo. Os clusters Data Flow demoram 5 a 7 minutos a aquecer e os utilizadores são recomendados a ligar primeiro o depurg se planeiam fazer o desenvolvimento do Fluxo de Dados. Para mais informações, consulte [o Modo Debug](concepts-data-flow-debug-mode.md).

    ![Atividade do fluxo de dados](media/tutorial-data-flow/dataflow1.png)
1. No painel **de atividades,** expanda o **acordeão Move and Transform.** Arraste e deixe cair a atividade do Fluxo de **Dados** do painel para a tela do gasoduto.

    ![Screenshot que mostra a tela do gasoduto onde pode deixar cair a atividade do Data Flow.](media/tutorial-data-flow/activity1.png)
1. No pop-up do Fluxo de **Dados adicionando,** selecione **Criar novo fluxo de dados** e, em seguida, nomear o seu fluxo de dados **DeltaLake**. Clique em Terminar quando terminar.

    ![Screenshot que mostra onde nomeia o fluxo de dados quando cria um novo fluxo de dados.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Construir lógica de transformação na tela do fluxo de dados

Você pegará quaisquer dados de origem (neste tutorial, usaremos uma fonte de arquivo Parquet) e usaremos uma transformação de pia para aterrar os dados em formato Parquet usando os mecanismos mais eficazes para o lago de dados ETL.

![Fluxo final](media/data-flow/parts-final.png "Fluxo final")

### <a name="tutorial-objectives"></a>Objetivos tutoriais

1. Escolha qualquer um dos seus conjuntos de dados de origem num novo fluxo de dados
1. Utilize fluxos de dados para dividir eficazmente o conjunto de dados do lavatório
1. Aterre os seus dados divididos em pastas do lago ADLS Gen2

### <a name="start-from-a-blank-data-flow-canvas"></a>Comece a partir de uma tela de fluxo de dados em branco

Em primeiro lugar, vamos configurar o ambiente de fluxo de dados para cada um dos mecanismos descritos abaixo para os dados de aterragem na ADLS Gen2

1. Clique na transformação da fonte.
1. Clique no novo botão ao lado do conjunto de dados no painel inferior.
1. Escolha um conjunto de dados ou crie um novo. Para esta demonstração, usaremos um conjunto de dados parquet chamado Dados de Utilizador.
1. Adicione uma transformação da Coluna Derivada. Vamos usar isto como forma de definir os nomes das pastas desejadas de forma dinâmica.
1. Adicione uma transformação de pia.
   
### <a name="hierarchical-folder-output"></a>Saída de pasta hierárquica

É muito comum usar valores únicos nos seus dados para criar hierarquias de pastas para dividir os seus dados no lago. Esta é uma forma muito ideal de organizar e processar dados no lago e em Spark (o motor de computação por trás dos fluxos de dados). No entanto, haverá um pequeno custo de desempenho para organizar a sua produção desta forma. Espere ver uma pequena diminuição do desempenho global do gasoduto utilizando este mecanismo na pia.

1. Volte para o designer de fluxo de dados e edite o fluxo de dados criado acima. Clique na transformação da pia.
1. Clique em Otimizar > definir a chave de > de divisão
1. Escolha as colunas que pretende utilizar para definir a sua estrutura de pasta hierárquica.
1. Note que o exemplo abaixo utiliza ano e mês como as colunas para o nome de pasta. Os resultados serão pastas do formulário ```releaseyear=1990/month=8``` .
1. Ao aceder às divisórias de dados numa fonte de fluxo de dados, irá apontar apenas para a pasta de nível superior acima ```releaseyear``` e utilizar um padrão wildcard para cada pasta subsequente, ex: ```**/**/*.parquet```
1. Para manipular os valores de dados, ou mesmo se precisar de gerar valores sintéticos para nomes de pastas, utilize a transformação da Coluna Derivada para criar os valores que pretende utilizar nos nomes das suas pastas.

![Divisórias chave](media/data-flow/key-parts.png "Divisórias chave")
   
### <a name="name-folder-as-data-values"></a>Pasta de nome como valores de dados

Uma técnica de pia ligeiramente melhor para os dados do lago utilizando a ADLS Gen2 que não oferece o mesmo benefício que a divisória chave/valor, é ```Name folder as column data``` . Enquanto o estilo de partição chave da estrutura hierárquica permitirá processar fatias de dados mais facilmente, esta técnica é uma estrutura de pasta achatada que pode escrever dados mais rapidamente.

1. Volte para o designer de fluxo de dados e edite o fluxo de dados criado acima. Clique na transformação da pia.
1. Clique em Otimizar > Definir partição > Utilize a partição atual.
1. Clique em Definições > Pasta nome como dados de coluna.
1. Escolha a coluna que pretende utilizar para gerar nomes de pastas.
1. Para manipular os valores de dados, ou mesmo se precisar de gerar valores sintéticos para nomes de pastas, utilize a transformação da Coluna Derivada para criar os valores que pretende utilizar nos nomes das suas pastas.

![Opção pasta](media/data-flow/folders.png "Pastas")

### <a name="name-file-as-data-values"></a>Ficheiro de nome como valores de dados

As técnicas listadas nos tutoriais acima são casos de bom uso para criar categorias de pastas no seu lago de dados. O esquema de nomeação de ficheiros predefinidos que está a ser utilizado por essas técnicas é utilizar o ID do executor de faíscas. Por vezes, pode desejar definir o nome do ficheiro de saída num sumidouro de texto de fluxo de dados. Esta técnica só é sugerida para utilização com ficheiros pequenos. O processo de fusão de ficheiros de partição num único ficheiro de saída é um processo de longa duração.

1. Volte para o designer de fluxo de dados e edite o fluxo de dados criado acima. Clique na transformação da pia.
1. Clique em Otimizar > Definir a partição > divisão única. É este requisito de partição única que cria um estrangulamento no processo de execução à medida que os ficheiros são fundidos. Esta opção só é recomendada para pequenos ficheiros.
1. Clique em Definições > Ficheiro Nome como dados de coluna.
1. Escolha a coluna que pretende utilizar para gerar nomes de ficheiros.
1. Para manipular os valores de dados, ou mesmo se precisar de gerar valores sintéticos para nomes de ficheiros, utilize a transformação da Coluna Derivada para criar os valores que pretende utilizar nos nomes dos seus ficheiros.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [os sumidouros de fluxo de dados.](data-flow-sink.md)
