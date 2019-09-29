---
title: Agendar Azure Data Lake Analytics trabalhos U-SQL usando o SSIS
description: Saiba como usar SQL Server Integration Services para agendar trabalhos U-SQL com script embutido ou arquivos de consulta do U-SQL.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 0650fcc5023ac57b193fa23b0dedf65113fd64e6
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672900"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Agendar trabalhos U-SQL usando SQL Server Integration Services (SSIS)

Neste documento, você aprende a orquestrar e criar trabalhos do U-SQL usando o SSIS (serviço de integração do SQL Server). 

## <a name="prerequisites"></a>Pré-requisitos

O [Feature Pack do Azure para Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) fornece a [tarefa de Azure data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) e o Gerenciador de [conexões Azure data Lake Analytics](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) que ajuda a conectar-se ao Azure data Lake Analytics Service. Para usar essa tarefa, certifique-se de instalar:

- [Baixar e instalar o SQL Server Data Tools (SSDT) para Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Instalar o Azure Feature Pack para Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Azure Data Lake Analytics tarefa

A tarefa Azure Data Lake Analytics permite que os usuários enviem trabalhos U-SQL para a conta de Azure Data Lake Analytics. 

[Saiba como configurar Azure data Lake Analytics tarefa](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Azure Data Lake Analytics tarefa no SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Você pode obter o script U-SQL de diferentes locais usando funções e tarefas internas do SSIS, os cenários a seguir mostram como você pode configurar os scripts U-SQL para diferentes casos de usuário.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Cenário 1-usar chamada de script embutido TVFs e procs armazenados

No editor da tarefa Azure Data Lake Analytics, configure **SourceType** como **DirectInput**e coloque as instruções U-SQL em **USQLStatement**.

Para facilitar a manutenção e o gerenciamento de código, coloque apenas o script U-SQL curto como scripts embutidos, por exemplo, você pode chamar funções com valor de tabela existentes e procedimentos armazenados em seus bancos de dados U-SQL. 

![Editar script U-SQL embutido na tarefa do SSIS](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Artigo relacionado: [Como passar o parâmetro para procedimentos armazenados](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Cenário 2 – usar arquivos U-SQL no Azure Data Lake Store

Você também pode usar arquivos U-SQL no Azure Data Lake Store usando a **tarefa sistema de arquivos Azure data Lake Store** no Feature Pack do Azure. Essa abordagem permite que você use os scripts armazenados na nuvem.

Siga as etapas abaixo para configurar a conexão entre Azure Data Lake Store tarefa do sistema de arquivos e Azure Data Lake Analytics tarefa.

### <a name="set-task-control-flow"></a>Definir fluxo de controle de tarefa

No modo de design de pacote SSIS, adicione uma **tarefa do sistema de arquivos Azure data Lake Store**, um **contêiner Loop Foreach** e uma **tarefa de Azure data Lake Analytics** no contêiner Loop Foreach. A Azure Data Lake Store tarefa do sistema de arquivos ajuda a baixar arquivos U-SQL em sua conta do ADLS para uma pasta temporária. O contêiner Loop Foreach e a tarefa Azure Data Lake Analytics ajudam a enviar cada arquivo U-SQL na pasta temporária para a conta de Azure Data Lake Analytics como um trabalho do U-SQL.

![Usar arquivos U-SQL no Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Configurar Azure Data Lake Store tarefa do sistema de arquivos

1. Defina a **operação** como **CopyFromADLS**.
2. Configure o **AzureDataLakeConnection**, saiba mais sobre [Azure data Lake Store Gerenciador de conexões](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Defina **AzureDataLakeDirectory**. Aponte para a pasta que armazena seus scripts U-SQL. Use o caminho relativo relativo à pasta raiz da conta de Azure Data Lake Store.
4. Defina **destino** como uma pasta que armazena em cache os scripts U-SQL baixados. Esse caminho de pasta será usado no contêiner Loop Foreach para envio de trabalhos U-SQL. 

![Configurar Azure Data Lake Store tarefa do sistema de arquivos](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Saiba mais sobre Azure data Lake Store tarefa do sistema de arquivos](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Configurar contêiner de loop foreach

1. Na página **coleção** , defina **enumerador** como **enumerador de arquivo foreach**.

2. Defina a **pasta** em grupo de **configuração do enumerador** para a pasta temporária que inclui os scripts U-SQL baixados.

3. Defina **arquivos** sob a **configuração do enumerador** como `*.usql` para que o contêiner de loop Capture apenas os arquivos que terminam com `.usql`.

    ![Configurar contêiner de loop foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Na página **mapeamentos de variáveis** , adicione uma variável definida pelo usuário para obter o nome do arquivo para cada arquivo U-SQL. Defina o **índice** como 0 para obter o nome do arquivo. Neste exemplo, defina uma variável chamada `User::FileName`. Essa variável será usada para obter dinamicamente a conexão do arquivo de script U-SQL e definir o nome do trabalho U-SQL na tarefa Azure Data Lake Analytics.

    ![Configurar o contêiner Loop Foreach para obter o nome do arquivo](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Configurar Azure Data Lake Analytics tarefa 

1. Defina **SourceType** como **FileConnection**.

2. Defina **FileConnection** como a conexão de arquivo que aponta para os objetos de arquivo retornados do contêiner Loop Foreach.
    
    Para criar essa conexão de arquivo:

   1. Escolha **a conexão \<New... >** na configuração de FileConnection.
   2. Defina o **tipo de uso** como **arquivo existente**e defina o **arquivo** como qualquer caminho de arquivo do arquivo existente.

       ![Configurar contêiner de loop foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. Na exibição **gerenciadores de conexões** , clique com o botão direito do mouse na conexão de arquivo criada apenas agora e escolha **Propriedades**.

   4. Na janela **Propriedades** , expanda **expressões**e defina **ConnectionString** para a variável definida no contêiner Loop Foreach, por exemplo, `@[User::FileName]`.

       ![Configurar contêiner de loop foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Defina **AzureDataLakeAnalyticsConnection** como a conta de Azure data Lake Analytics para a qual você deseja enviar os trabalhos. Saiba mais sobre [Azure data Lake Analytics Gerenciador de conexões](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Defina outras configurações de trabalho. [Saiba mais](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. Use **expressões** para definir dinamicamente o nome do trabalho do U-SQL:

    1. Na página **expressões** , adicione uma nova expressão de par chave-valor para **JobName**.
    2. Defina o valor de JobName para a variável definida no contêiner Loop Foreach, por exemplo, `@[User::FileName]`.
    
        ![Configurar a expressão SSIS para o nome do trabalho do U-SQL](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Cenário 3 – usar arquivos U-SQL no armazenamento de BLOBs do Azure

Você pode usar arquivos U-SQL no armazenamento de BLOBs do Azure usando a **tarefa de download de blob do Azure** no Feature Pack do Azure. Essa abordagem permite que você use os scripts na nuvem.

As etapas são semelhantes com [Scenario 2: Use arquivos U-SQL no Azure Data Lake Store @ no__t-0. Altere a tarefa do sistema de arquivos Azure Data Lake Store para a tarefa de download de blob do Azure. [Saiba mais sobre a tarefa de download de blob do Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

O fluxo de controle é como a seguir.

![Usar arquivos U-SQL no Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Cenário 4-usar arquivos U-SQL no computador local

Além de usar arquivos U-SQL armazenados na nuvem, você também pode usar arquivos em seu computador local ou arquivos implantados com seus pacotes SSIS.

1. Clique com o botão direito do mouse em **gerenciadores de conexões** no projeto do SSIS e escolha **novo Gerenciador de conexões**.

2. Selecione o tipo de **arquivo** e clique em **Adicionar...** .

3. Defina o **tipo de uso** como **arquivo existente**e defina o **arquivo** para o arquivo no computador local.

    ![Adicionar conexão de arquivo ao arquivo local](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Adicionar **Azure data Lake Analytics** tarefa e:
    1. Defina **SourceType** como **FileConnection**.
    2. Defina **FileConnection** para a conexão de arquivo criada apenas agora.

5. Conclua outras configurações para Azure Data Lake Analytics tarefa.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Cenário 5 – usar a instrução U-SQL na variável SSIS

Em alguns casos, talvez seja necessário gerar dinamicamente as instruções U-SQL. Você pode usar a **variável do SSIS** com a **expressão SSIS** e outras tarefas do SSIS, como a tarefa Script, para ajudá-lo a gerar a instrução U-SQL dinamicamente.

1. Abrir janela de ferramentas de variáveis por meio do > menu de nível superior de **variáveis do SSIS** .

2. Adicione uma variável do SSIS e defina o valor diretamente ou use a **expressão** para gerar o valor.

3. Adicionar **Azure data Lake Analytics tarefa** e:
    1. Defina **SourceType** como **variável**.
    2. Defina **SourceVariable** para a variável do SSIS criada apenas agora.

4. Conclua outras configurações para Azure Data Lake Analytics tarefa.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Cenário 6-passar parâmetros para o script U-SQL

Em alguns casos, talvez você queira definir dinamicamente o valor da variável U-SQL no script U-SQL. O recurso de **mapeamento de parâmetros** no Azure data Lake Analytics tarefa ajuda com esse cenário. Geralmente, há dois casos de usuário típicos:

- Defina as variáveis de caminho do arquivo de entrada e saída dinamicamente com base na data e hora atuais.
- Defina o parâmetro para procedimentos armazenados.

[Saiba mais sobre como definir parâmetros para o script U-SQL](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Passos seguintes

- [Executar pacotes do SSIS no Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Feature Pack do Azure para Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Agendar trabalhos U-SQL usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)
