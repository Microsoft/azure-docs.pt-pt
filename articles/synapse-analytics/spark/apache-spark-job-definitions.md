---
title: 'Tutorial - Apache Spark para Azure Synapse Analytics: Definição de trabalho de Apache Spark para Synapse'
description: Tutorial - Use o Azure Synapse Analytics para criar definições de trabalho spark, e submeta-as a uma Apache Spark para piscina Azure Synapse Analytics.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 5fc9dffaa73d195c842381b6682a00e9834c0fe7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587940"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Tutorial: Use Azure Synapse Analytics para criar definições de trabalho apache Spark para piscinas de faíscas synapse

Este tutorial demonstra como usar o Azure Synapse Analytics para criar definições de trabalho spark, e, em seguida, submetê-los a uma piscina Synapse Spark. Pode utilizar o plug-in de algumas formas:

* Desenvolva e submeta uma definição de trabalho spark em uma piscina Synapse Spark.
* Consulte os detalhes do trabalho após a submissão.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> * Desenvolva e submeta uma definição de trabalho spark em uma piscina Synapse Spark.
> * Consulte os detalhes do trabalho após a submissão.

## <a name="prerequisites"></a>Pré-requisitos

* Um espaço de trabalho Azure Synapse Analytics. Para obter instruções, consulte Criar um espaço de [trabalho Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).

## <a name="get-started"></a>Introdução

Antes de submeter uma definição de trabalho spark, você precisa ser o Proprietário de Dados Blob de Armazenamento do sistema de ficheiros ADLS Gen2 com o quais pretende trabalhar. Se não estiver, tem de adicionar a permissão manualmente.

### <a name="scenario-1-add-permission"></a>Cenário 1: Adicionar permissão

1. Abra o [Microsoft Azure,](https://ms.portal.azure.com)em seguida, abra a conta de Armazenamento.

2. Clique em **Recipientes**e, em seguida, crie um **sistema de ficheiros**. Este tutorial utiliza `sparkjob`.

    ![Clique no botão de submeter para submeter a definição de trabalho de faísca](./media/apache-spark-job-definitions/open-azure-container.png)

    ![A caixa de diálogo de submissão de faíscas](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Abra, clique no Controlo de `sparkjob` **Acesso (IAM)** e, em seguida, clique em **Adicionar** e selecione Adicionar a atribuição de **funções**.

    ![Clique no botão de submeter para submeter a definição de trabalho de faísca](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Clique no botão de submeter para submeter a definição de trabalho de faísca](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Clique em **atribuições de role,** insere o nome do utilizador e verifique a função do utilizador.

    ![Clique no botão de submeter para submeter a definição de trabalho de faísca](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Cenário 2: Preparar estrutura de pastas

Antes de submeter uma definição de trabalho spark, um trabalho que precisa fazer é enviar ficheiros para ADLS Gen2 e preparar a estrutura da pasta lá. Usamos o nó de armazenamento no Estúdio Synapse para armazenar ficheiros.

1. Open [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Clique em **Dados,** **selecione contas de Armazenamento**e faça upload dos ficheiros relevantes para o seu sistema de ficheiros ADLS Gen2. Apoiamos Scala, Java, .NET e Python. Este tutorial usa o exemplo na figura como demonstração, pode alterar a estrutura do projeto como desejar.

    ![Definir o valor da definição de trabalho de Faísca](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Criar uma definição de trabalho de Faísca

1. Abra a [Análise Synapse Azure,](https://web.azuresynapse.net/)e selecione **Desenvolver**.

2. Selecione **definições** de trabalho spark a partir do painel esquerdo.

3. Clique no nó de **Ações** à direita das "definições de trabalho spark".

     ![Criar nova definição de trabalho de faísca](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. Da lista de **ações** drop-down, selecione **new spark definição** de trabalho

     ![Criar nova definição de trabalho de faísca](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. Na janela de definição de trabalho new Spark, selecione o idioma e, em seguida, forneça as seguintes informações:  

   * Selecione **Idioma** como **Spark (Scala)**.

    |  Propriedade   | Descrição   |  
    | ----- | ----- |  
    |Nome da definição de emprego| Insira um nome para a sua definição de trabalho spark.  Este tutorial utiliza `job definition sample`. Este nome pode ser atualizado a qualquer momento até que seja publicado.|  
    |Arquivo de definição principal| O ficheiro principal usado para o trabalho. Selecione um ficheiro JAR a partir do seu armazenamento. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento. |
    |Nome principal da classe| O identificador totalmente qualificado ou a classe principal que está no ficheiro de definição principal.|
    |Argumentos de linha de comando| Argumentos opcionais para o trabalho.|
    |Ficheiros de referência| Ficheiros adicionais utilizados para referência no ficheiro de definição principal. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento.|
    |Piscina de faíscas| O trabalho será submetido à piscina de Faísca selecionada.|
    |Versão spark| Versão de Spark que a piscina Spark está funcionando.|
    |Executores| Número de executores a serem dados na piscina de Faísca especificada para o trabalho.|
    |Tamanho do executor| Número de núcleos e memória a utilizar para executores dado na piscina de Faísca especificada para o trabalho.|  
    |Tamanho do condutor| Número de núcleos e memória a utilizar para o condutor dado na piscina de Faísca especificada para o trabalho.|

    ![Definir o valor da definição de trabalho de Faísca](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Selecione **Idioma** como **PySpark (Python)**.

    |  Propriedade   | Descrição   |  
    | ----- | ----- |  
    |Nome da definição de emprego| Insira um nome para a sua definição de trabalho spark.  Este tutorial utiliza `job definition sample`. Este nome pode ser atualizado a qualquer momento até que seja publicado.|  
    |Arquivo de definição principal| O ficheiro principal usado para o trabalho. Selecione um ficheiro PY a partir do seu armazenamento. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento.|
    |Argumentos de linha de comando| Argumentos opcionais para o trabalho.|
    |Ficheiros de referência| Ficheiros adicionais utilizados para referência no ficheiro de definição principal. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento.|
    |Piscina de faíscas| O trabalho será submetido à piscina de Faísca selecionada.|
    |Versão spark| Versão de Spark que a piscina Spark está funcionando.|
    |Executores| Número de executores a serem dados na piscina de Faísca especificada para o trabalho.|
    |Tamanho do executor| Número de núcleos e memória a utilizar para executores dado na piscina de Faísca especificada para o trabalho.|  
    |Tamanho do condutor| Número de núcleos e memória a utilizar para o condutor dado na piscina de Faísca especificada para o trabalho.|

    ![Definir o valor da definição de trabalho de Faísca](./media/apache-spark-job-definitions/create-py-definition.png)

   * Selecione **Idioma** como **.NET Spark (C#/F#)**.

    |  Propriedade   | Descrição   |  
    | ----- | ----- |  
    |Nome da definição de emprego| Insira um nome para a sua definição de trabalho spark.  Este tutorial utiliza `job definition sample`. Este nome pode ser atualizado a qualquer momento até que seja publicado.|  
    |Arquivo de definição principal| O ficheiro principal usado para o trabalho. Selecione um ficheiro ZIP que contenha a sua aplicação .NET para a spark (isto é, o ficheiro executável principal, DLLs contendo funções definidas pelo utilizador e outros ficheiros necessários) do seu armazenamento. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento.|
    |Arquivo executável principal| O ficheiro principal executável no ficheiro ZIP de definição principal.|
    |Argumentos de linha de comando| Argumentos opcionais para o trabalho.|
    |Ficheiros de referência| Ficheiros adicionais necessários pelos nós do trabalhador para a execução da aplicação .NET para spark que não está incluído na definição principal ficheiro ZIP (isto é, frascos dependentes, dLLs de função definida pelo utilizador adicional e outros ficheiros config). Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento.|
    |Piscina de faíscas| O trabalho será submetido à piscina de Faísca selecionada.|
    |Versão spark| Versão de Spark que a piscina Spark está funcionando.|
    |Executores| Número de executores a serem dados na piscina de Faísca especificada para o trabalho.|
    |Tamanho do executor| Número de núcleos e memória a utilizar para executores dado na piscina de Faísca especificada para o trabalho.|  
    |Tamanho do condutor| Número de núcleos e memória a utilizar para o condutor dado na piscina de Faísca especificada para o trabalho.|

    ![Definir o valor da definição de trabalho de Faísca](./media/apache-spark-job-definitions/create-net-definition.png)

6. Selecione **Publicar** para salvar a definição de trabalho spark.

    ![Publicar definição de trabalho spark](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Submeter uma definição de trabalho de Faísca

Depois de criar uma definição de trabalho spark, você pode submetê-lo a uma piscina Synapse Spark. Certifique-se de ter passado por passos em **Get-started** antes de experimentar amostras nesta parte.

### <a name="scenario-1-submit-spark-job-definition"></a>Cenário 1: Submeter a definição de trabalho de Faísca

1. Abra uma janela de definição de trabalho de faísca clicando nela.

      ![Definição de trabalho de faísca aberta para submeter ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Clique em **submeter** o ícone para submeter o seu projeto ao Spark Pool selecionado. Pode clicar no separador URL de **monitorização spark** para ver o LogQuery da aplicação Spark.

    ![Clique no botão de submeter para submeter a definição de trabalho de faísca](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![A caixa de diálogo de submissão de faíscas](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Cenário 2: Ver o trabalho da Spark a correr o progresso

1. Clique no **Monitor**e, em seguida, selecione a opção **de aplicações Spark.** Pode encontrar o pedido de Spark submetido.

    ![Ver aplicação Spark](./media/apache-spark-job-definitions/view-spark-application.png)

2. Em seguida, clique na aplicação Spark, ecrãs de janela **sessão.** Pode ver o progresso da execução do trabalho a partir do **LogQuery**.

    ![Ver logquery de aplicação de faísca](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Cenário 3: Verificar ficheiro de saída

 1. Clique em **Dados**e, em seguida, selecione **contas de armazenamento**. Depois de uma corrida bem sucedida, você pode ir ao armazenamento ADLS Gen2 e verificar que as saídas são geradas.

    ![Ver ficheiro de saída](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Passos seguintes

Este tutorial demonstrou como usar o Azure Synapse Analytics para criar definições de trabalho spark, e, em seguida, submetê-los a uma piscina Synapse Spark. Em seguida, pode utilizar o Azure Synapse Analytics para criar conjuntos de dados power BI e gerir dados do Power BI. 

- [Ligar a dados no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Visualizar com o Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
