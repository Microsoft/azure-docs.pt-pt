---
title: Agendar tarefas de Data Lake Analytics U-SQL do Azure através do SSIS
description: Saiba como utilizar o SQL Server Integration Services para agendar tarefas de U-SQL.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 5393e202c7b5005552f164c9c6f55da92fc82572
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104442"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Agendar tarefas de U-SQL através do SQL Server Integration Services (SSIS)

Neste documento, saiba como orquestrar e criar tarefas de U-SQL com o SQL Server Integration Service (SSIS). 

## <a name="prerequisites"></a>Pré-requisitos

[Pacote de funcionalidades do Azure para serviços de integração](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) fornece o [tarefa do Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) e o [Gestor de ligações do Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) que ajuda a ligar ao Azure Data Lake Serviço de análise. Para utilizar esta tarefa, certifique-se de que instalar:

- [Transfira e instale o SQL Server Data Tools (SSDT) para Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Instalar o pacote de recursos do Azure para o Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Tarefa de análise do Azure Data Lake

A tarefa do Azure Data Lake Analytics permitir aos utilizadores enviar tarefas de U-SQL para a conta do Azure Data Lake Analytics. 

[Saiba como configurar a tarefa do Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Azure Data Lake Analytics tarefas no SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Pode obter o script de U-SQL a partir de locais diferentes através das funções incorporadas do SSIS e tarefas, abaixo cenários mostram como pode configurar os scripts U-SQL para casos de utilizador diferente.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Cenário 1-utilização inline script chamada tvfs e procs armazenado

No Azure Data Lake Analytics Editor de tarefas, configure **SourceType** como **DirectInput**e colocar as instruções de U-SQL para **USQLStatement**.

Para facilidade de manutenção e gerenciamento de código, apenas colocar o script de U-SQL curto como inline scripts, por exemplo, pode chamar funções de valor de tabela existente e procedimentos armazenados nas suas bases de U-SQL. 

![Editar script de U-SQL de inline em tarefas SSIS](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Artigo relacionado: [Como passar parâmetros para procedimentos armazenados](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Cenário 2-utilize U-arquivos SQL no Azure Data Lake Store

Também pode utilizar os ficheiros de U-SQL a Store do Azure Data Lake, utilizando **tarefa de sistema de ficheiros do Azure Data Lake Store** no pacote de funcionalidades do Azure. Esta abordagem permite-lhe utilizar os scripts armazenados na cloud.

Siga os passos para configurar a ligação entre a tarefa de sistema de ficheiros do Azure Data Lake Store e do Azure Data Lake Analytics tarefas abaixo.

### <a name="set-task-control-flow"></a>Fluxo de controlo do conjunto de tarefas

Na vista de estrutura de pacote do SSIS, adicione uma **tarefa de sistema de ficheiros do Azure Data Lake Store**, uma **contentor de Loop Foreach** e um **tarefa de análise do Azure Data Lake** no Foreach Loop Contentor. A tarefa de sistema de ficheiros do Azure Data Lake Store ajuda a transferir ficheiros de U-SQL na sua conta do ADLS para uma pasta temporária. O contentor de Loop Foreach e a tarefa de análise do Azure Data Lake ajudam a enviar de todos os ficheiros de U-SQL sob a pasta temporária para a conta do Azure Data Lake Analytics como uma tarefa U-SQL.

![Utilizar ficheiros de U-SQL no Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Configurar a tarefa de sistema de ficheiros do Azure Data Lake Store

1. Definir **operação** ao **CopyFromADLS**.
2. Configurar **AzureDataLakeConnection**, saiba mais sobre [Gestor de ligações do Azure Data Lake Store](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Set **AzureDataLakeDirectory**. Aponte para a pasta armazenar seus scripts U-SQL. Utilize o caminho relativo que é relativo à pasta de raiz da conta do Azure Data Lake Store.
4. Definir **destino** para uma pasta que armazena em cache os scripts baixados do U-SQL. Este caminho da pasta será utilizado no contentor de Loop Foreach para submissão de tarefas de U-SQL. 

![Configurar a tarefa de sistema de ficheiros do Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Saiba mais sobre a tarefa de sistema de ficheiros do Azure Data Lake Store](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Configurar o contentor de Foreach Loop

1. Na **recolha** página, defina **enumerador** para **enumerador de ficheiro de Foreach**.

2. Definir **pasta** sob **configuração de enumerador** grupo para a pasta temporária que inclui scripts U-SQL transferidos.

3. Definir **arquivos** sob **configuração de enumerador** para `*.usql` para que o contentor de loop só resolve os ficheiros que termina com `.usql`.

    ![Configurar o contentor de Foreach Loop](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Na **mapeamentos de variável** página, adicione uma variável definida pelo utilizador para obter o nome de ficheiro para cada ficheiro de U-SQL. Definir o **índice** como 0 para obter o nome de ficheiro. Neste exemplo, definir uma variável chamada `User::FileName`. Esta variável será utilizada para obter a ligação de ficheiro de script de U-SQL e definir o nome da tarefa de U-SQL no Azure Data Lake Analytics tarefas dinamicamente.

    ![Configurar o contentor de Loop Foreach para obter o nome de ficheiro](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Configurar tarefas do Azure Data Lake Analytics 

1. Definir **SourceType** ao **FileConnection**.

2. Definir **FileConnection** para o ficheiro de ligação que aponta para os objetos de arquivo devolvido do contentor de Loop Foreach.
    
    Para criar esta ligação do ficheiro:

   1. Escolher **<New Connection...>** na definição de FileConnection.
   2. Definir **tipo de utilização** para **ficheiro existente**e defina o **ficheiro** ao caminho de ficheiro de qualquer arquivo existente.

       ![Configurar o contentor de Foreach Loop](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. Na **gerenciadores de conexão** ver, com o botão direito a ligação de ficheiro agora mesmo a criou e escolha **propriedades**.

   4. Na **propriedades** janela, expanda **expressões**e defina **ConnectionString** para a variável definida num contentor do Loop Foreach, por exemplo, `@[User::FileName]`.

       ![Configurar o contentor de Foreach Loop](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Definir **AzureDataLakeAnalyticsConnection** para a conta do Azure Data Lake Analytics que pretende submeter tarefas para. Saiba mais sobre [o Gestor de ligações do Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Defina outras configurações de tarefa. [Saiba mais](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. Uso **expressões** para definir dinamicamente o nome da tarefa de U-SQL:

    1. Na **expressões** página, adicione um novo par de chave-valor de expressão para **JobName**.
    2. Defina o valor para JobName para a variável definida num contentor do Loop Foreach, por exemplo, `@[User::FileName]`.
    
        ![Configurar a expressão do SSIS para o nome da tarefa de U-SQL](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Cenário 3-utilize U-ficheiros SQL no armazenamento de Blobs do Azure

Pode utilizar os ficheiros de U-SQL no armazenamento de Blobs do Azure, utilizando **a tarefa de transferir o Blob do Azure** no pacote de funcionalidades do Azure. Esta abordagem permite-lhe utilizar os scripts na cloud.

Os passos são semelhantes com [cenário 2: Utilizar ficheiros de U-SQL no Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Altere a tarefa de sistema de ficheiros do Azure Data Lake Store para a tarefa de transferência de Blobs do Azure. [Saiba mais sobre a tarefa de transferir o Blob do Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

O fluxo de controle é como abaixo.

![Utilizar ficheiros de U-SQL no Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Cenário 4 utilize U-ficheiros SQL no computador local

Além de usar arquivos de U-SQL armazenados na cloud, também pode utilizar arquivos no seu computador local ou implementadas com seus pacotes do SSIS.

1. Com o botão direito **gerenciadores de conexão** no SSIS do projeto e escolha **novo Gerenciador de conexões**.

2. Selecione **arquivo** escreva e clique em **adicionar...** .

3. Definir **tipo de utilização** ao **ficheiro existente**e defina o **ficheiro** para o ficheiro no computador local.

    ![Adicionar ligação de ficheiro para o ficheiro local](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Adicione **do Azure Data Lake Analytics** tarefas e:
    1. Definir **SourceType** ao **FileConnection**.
    2. Definir **FileConnection** para a ligação do arquivo criado agora mesmo.

5. Outras configurações para o Azure Data Lake Analytics tarefa ser concluída.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Cenário 5 utilize U-instrução SQL na variável SSIS

Em alguns casos, terá de gerar dinamicamente as instruções de U-SQL. Pode usar **variável do SSIS** com **SSIS expressão** e outras tarefas SSIS, como a tarefa de Script, para o ajudar a gerar dinamicamente a instrução U-SQL.

1. Abrir janela de ferramentas de variáveis através de **SSIS > variáveis** menu de nível superior.

2. Adicionar uma variável SSIS e defina o valor diretamente ou usar **expressão** para gerar o valor.

3. Adicione **do Azure Data Lake Analytics tarefas** e:
    1. Definir **SourceType** ao **variável**.
    2. Definir **SourceVariable** para a variável SSIS criado agora mesmo.

4. Outras configurações para o Azure Data Lake Analytics tarefa ser concluída.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>6-transmita os parâmetros cenário para o script de U-SQL

Em alguns casos, pode querer definir dinamicamente o valor da variável de U-SQL no script de U-SQL. **Mapeamento de parâmetros** funcionalidade na ajuda da tarefa de análise do Azure Data Lake com este cenário. Normalmente, existem dois casos de usuário típico:

- Definir a entrada e saída de variáveis de caminho de ficheiro dinamicamente com base na data e hora atuais.
- Defina o parâmetro de procedimentos armazenados.

[Saiba mais sobre como definir parâmetros para o script de U-SQL](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Passos Seguintes

- [Executar pacotes do SSIS no Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Pacote de recursos do Azure para o Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Agendar tarefas de U-SQL com o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

