---
title: Agendar trabalhos de U-SQL no Lago de Dados Azure
description: Saiba como usar os Serviços de Integração do Servidor SQL para agendar trabalhos U-SQL com script sinline ou a partir de ficheiros de consulta U-SQL.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71672900"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Agendar trabalhos U-SQL utilizando serviços de integração de servidores SQL (SSIS)

Neste documento, aprende-se a orquestrar e a criar empregos U-SQL utilizando o Serviço de Integração de Servidores SQL (SSIS). 

## <a name="prerequisites"></a>Pré-requisitos

O Pack de [Recursos Azure para serviços](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) de integração fornece a [tarefa Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) e o [Azure Data Lake Analytics Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) que ajuda a ligar ao serviço Azure Data Lake Analytics. Para utilizar esta tarefa, certifique-se de que instala:

- [Descarregue e instale ferramentas de dados do Servidor SQL (SSDT) para Estúdio Visual](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Instalar pacote de funcionalidades Azure para serviços de integração (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Tarefa de Análise do Lago de Dados Azure

A tarefa Azure Data Lake Analytics permite que os utilizadores enviem empregos U-SQL para a conta Azure Data Lake Analytics. 

[Saiba como configurar a tarefa Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Tarefa de análise de lago de dados azure no SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Pode obter o script U-SQL de diferentes lugares utilizando funções e tarefas incorporadas do SSIS, abaixo os cenários mostram como pode configurar os scripts U-SQL para diferentes casos de utilizadores.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Cenário 1-Use script inline call tvfs e procs armazenados

No Azure Data Lake Analytics Task Editor, configure **o SourceType** como **DirectInput**, e coloque as declarações U-SQL no **USQLStatement**.

Para uma fácil manutenção e gestão de códigos, basta colocar o script U-SQL curto como scripts inline, por exemplo, pode chamar as funções de tabela valorizadas existentes e procedimentos armazenados nas suas bases de dados U-SQL. 

![Editar script U-SQL inline na tarefa SSIS](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Artigo relacionado: [Como passar parâmetro para procedimentos armazenados](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Cenário 2-Use ficheiros U-SQL na Loja de Lagos De Dados Azure

Também pode utilizar ficheiros U-SQL na Loja do Lago De Dados Azure utilizando a Tarefa do Sistema de Ficheiros da Loja de **Ficheiros do Lago de Dados Azure** no Pack de Funcionalidades Azure. Esta abordagem permite-lhe utilizar os scripts armazenados na nuvem.

Siga abaixo os passos para estabelecer a ligação entre a Tarefa do Sistema de Ficheiros da Loja de Dados do Lago De dados Azure e a Tarefa de Análise do Lago de Dados Azure.

### <a name="set-task-control-flow"></a>Definir fluxo de controlo de tarefas

Na vista de design de pacoteSSIs, adicione uma tarefa de sistema de **ficheiros**de loja de dados Azure Data, um **recipiente Foreach Loop** e uma tarefa de análise de lago de dados **Azure** no recipiente Foreach Loop. A Tarefa do Sistema de Ficheiros da Loja do Lago de Dados Azure ajuda a transferir ficheiros U-SQL na sua conta ADLS para uma pasta temporária. O Recipiente Foreach Loop e a Tarefa de Análise do Lago de Dados Azure ajudam a submeter todos os ficheiros U-SQL sob a pasta temporária para a conta Azure Data Lake Analytics como um trabalho U-SQL.

![Utilize ficheiros U-SQL na Loja de Lagos De Dados Azure](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Configure Tarefa do sistema de ficheiros da loja de dados do lago de dados do Azure

1. Definir **operação** para **CopyFromADLS**.
2. Configurar **o AzureDataLakeConnection,** saiba mais sobre [o Azure Data Lake Store Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Definir **AzureDataLakeDirectory**. Aponte para a pasta que armazena os seus scripts U-SQL. Utilize um caminho relativo relativo relativo à pasta raiz da conta Azure Data Lake Store.
4. Desloque **o Destino** a uma pasta que caches os scripts U-SQL descarregados. Este caminho de pasta será utilizado no Recipiente Foreach Loop para submissão de trabalho U-SQL. 

![Configure Tarefa do sistema de ficheiros da loja de dados do lago de dados do Azure](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

Saiba mais sobre a tarefa do sistema de ficheiros da Loja de Dados [Azure.](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017)

### <a name="configure-foreach-loop-container"></a>Configure recipiente de loop foreach

1. Na página **de Recolha,** delineie **o Enumerador** para **Foreach File Enumerar**.

2. Desloque a **pasta** sob o grupo de **configuração enumerador** para a pasta temporária que inclui os scripts U-SQL descarregados.

3. Defino **ficheiros** sob `*.usql` **a configuração do Enumerador** de `.usql`modo a que o recipiente de loop apanhe apenas os ficheiros que terminam com .

    ![Configure recipiente de loop foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Na página **de Mapeamentos Variáveis,** adicione uma variável definida pelo utilizador para obter o nome de ficheiro para cada ficheiro U-SQL. Desloque o **Índice** para 0 para obter o nome do ficheiro. Neste exemplo, defina `User::FileName`uma variável chamada . Esta variável será usada para obter dinamicamente a ligação de ficheiros de script U-SQL e definir o nome de trabalho U-SQL em Azure Data Lake Analytics Task.

    ![Configure o recipiente Foreach Loop para obter o nome do ficheiro](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Configure Tarefa de análise de data lake 

1. Definir **SourceType** para **FileConnection**.

2. Detete **a Ligação** de Ficheiros à ligação de ficheiros que aponta para os objetos de ficheiro devolvidos do Recipiente Foreach Loop.
    
    Para criar esta ligação de ficheiros:

   1. Escolha ** \<nova ligação...>** na definição de FileConnection.
   2. Detete o **tipo de utilização** para o **ficheiro existente**e detete te o **Ficheiro** para o caminho de ficheiro de qualquer ficheiro existente.

       ![Configure recipiente de loop foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. Na visualização **do Gestor de Ligação,** clique na ligação de ficheiro criada agora mesmo, e escolha **Propriedades**.

   4. Na janela **Propriedades,** expanda **Expressões**e coloque **o ConnectionString** na variável `@[User::FileName]`definida no Recipiente Foreach Loop, por exemplo, .

       ![Configure recipiente de loop foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Detete **o AzureDataLakeAnalyticsConnection** para a conta Azure Data Lake Analytics para a a que pretende submeter postos de trabalho. Saiba mais sobre [o Azure Data Lake Analytics Connection Manager.](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017)

4. Definir outras configurações de trabalho. [Saiba mais.](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017)

5. Use **Expressões** para definir dinamicamente o nome de trabalho U-SQL:

    1. Na página **Expressions,** adicione um novo par de valor-chave de expressão para **JobName**.
    2. Defino o valor do Nome de Trabalho para a `@[User::FileName]`variável definida no Recipiente Foreach Loop, por exemplo, .
    
        ![Configure expressão SSIS para nome de trabalho U-SQL](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Cenário 3-Use Ficheiros U-SQL no Armazenamento de Blob Azure

Pode utilizar ficheiros U-SQL no Armazenamento De Blob Azure utilizando a Tarefa de **Descarregamento de Azure Blob** no Pack de Funcionalidades Azure. Esta abordagem permite-lhe utilizar os scripts na nuvem.

Os passos são semelhantes ao [Cenário 2: Utilize ficheiros U-SQL na Loja do Lago de Dados Azure](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Altere a tarefa do sistema de ficheiros da Loja de Dados Azure para a Tarefa de Descarregamento de Blob Azure. Saiba mais sobre a Tarefa de [Descarregamento de Azure Blob](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

O fluxo de controlo é como abaixo.

![Utilize ficheiros U-SQL na Loja de Lagos De Dados Azure](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Cenário 4-Use ficheiros U-SQL na máquina local

Além de utilizar ficheiros U-SQL armazenados na nuvem, também pode utilizar ficheiros na sua máquina local ou ficheiros implantados com os seus pacotes SSIS.

1. Clique direito gestores de **ligação** no projeto SSIS e escolha **New Connection Manager**.

2. Selecione o tipo **de ficheiro** e clique em **Adicionar...**.

3. Detete o **tipo de utilização** para o **ficheiro existente**e detete te o **ficheiro** no ficheiro da máquina local.

    ![Adicionar conexão de ficheiros ao ficheiro local](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Adicione a tarefa de análise do Lago de **Dados Azure** e:
    1. Definir **SourceType** para **FileConnection**.
    2. Definir **a Ligação** de Ficheiros à Ligação de Ficheiros criada agora mesmo.

5. Termine outras configurações para a Tarefa de Análise do Lago de Dados Azure.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Cenário 5-Use U-SQL declaração na variável SSIS

Em alguns casos, poderá ser necessário gerar dinamicamente as declarações da U-SQL. Pode utilizar a **Variável SSIS** com **expressão SSIS** e outras tarefas SSIS, como a Script Task, para ajudá-lo a gerar a declaração U-SQL de forma dinâmica.

1. Abra a janela da ferramenta Variáveis através do menu de alto nível **sis > Variáveis.**

2. Adicione uma Variável SSIS e detete o valor diretamente ou use a **Expressão** para gerar o valor.

3. Adicione a tarefa de análise do Lago de **Dados Azure** e:
    1. Definir **SourceType** para **Variável**.
    2. Definir **FonteVariável** para a Variável SSIS criada agora mesmo.

4. Termine outras configurações para a Tarefa de Análise do Lago de Dados Azure.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Cenário 6-Pass parâmetros para script U-SQL

Em alguns casos, pode querer definir dinamicamente o valor variável U-SQL no script U-SQL. A funcionalidade de mapeamento de **parâmetros** no Azure Data Lake Analytics Task ajuda com este cenário. Existem geralmente dois casos típicos de utilizador:

- Detete as variáveis do caminho do ficheiro de entrada e de saída dinamicamente com base na data e hora atuais.
- Defina o parâmetro para os procedimentos armazenados.

[Saiba mais sobre como definir parâmetros para o script U-SQL](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Passos seguintes

- [Executar pacotes do SSIS no Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Pacote de funcionalidades Azure para serviços de integração (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Agendar empregos U-SQL usando a Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)
