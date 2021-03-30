---
title: Agendar trabalhos U-SQL do Azure Data Lake Analytics utilizando SSIS
description: Saiba como utilizar os Serviços de Integração de Servidores SQL para agendar trabalhos U-SQL com script inline ou a partir de ficheiros de consulta U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/17/2018
ms.openlocfilehash: b080b433f5af49e970faba02003fb68e21a08365
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221456"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Agendar empregos U-SQL utilizando serviços de integração de servidores SQL (SSIS)

Neste documento, aprende a orquestrar e criar empregos U-SQL utilizando o SQL Server Integration Service (SSIS). 

## <a name="prerequisites"></a>Pré-requisitos

[O Azure Feature Pack for Integration Services](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#scenario-managing-data-in-the-cloud) fornece a tarefa [Azure Data Lake Analytics](/sql/integration-services/control-flow/azure-data-lake-analytics-task) e o [Azure Data Lake Analytics Connection Manager](/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager) que ajuda a ligar-se ao serviço Azure Data Lake Analytics. Para utilizar esta tarefa, certifique-se de que instala:

- [Descarregue e instale ferramentas de dados do sql server (SSDT) para o Estúdio Visual](/sql/ssdt/download-sql-server-data-tools-ssdt)
- [Instalar pacote de recursos Azure para serviços de integração (SSIS)](/sql/integration-services/azure-feature-pack-for-integration-services-ssis)

## <a name="azure-data-lake-analytics-task"></a>Tarefa Azure Data Lake Analytics

A tarefa Azure Data Lake Analytics permite que os utilizadores submetam empregos U-SQL para a conta Azure Data Lake Analytics. 

[Saiba como configurar a tarefa Azure Data Lake Analytics](/sql/integration-services/control-flow/azure-data-lake-analytics-task).

![Tarefa Azure Data Lake Analytics em SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Você pode obter o script U-SQL de diferentes lugares usando funções e tarefas incorporadas SSIS, abaixo os cenários mostram como pode configurar as scripts U-SQL para diferentes casos de utilizador.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Cenário 1-Use script inline call tvfs e procs armazenados

No Azure Data Lake Analytics Task Editor, configurar **o SourceType** como **DirectInput,** e colocar as declarações U-SQL no **USQLStatement**.

Para uma fácil manutenção e gestão de códigos, apenas coloque o script U-SQL curto como scripts em linha, por exemplo, pode chamar funções de valor de tabela existentes e procedimentos armazenados nas suas bases de dados U-SQL. 

![Editar o script U-SQL em linha na tarefa SSIS](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Artigo relacionado: [Como passar o parâmetro para os procedimentos armazenados](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Cenário 2-Use U-SQL ficheiros em Azure Data Lake Store

Também pode utilizar ficheiros U-SQL na Azure Data Lake Store utilizando **a tarefa do sistema de ficheiros Azure Data Lake Store** em Azure Feature Pack. Esta abordagem permite-lhe utilizar os scripts armazenados na nuvem.

Siga abaixo os passos para configurar a ligação entre a tarefa do sistema de ficheiros Azure Data Lake Store e a Azure Data Lake Analytics Task.

### <a name="set-task-control-flow"></a>Definir fluxo de controlo de tarefas

Na vista de design de pacoteS SSIS, adicione uma **tarefa do sistema de ficheiros Azure Data Lake Store,** um recipiente **foreach Loop** e uma tarefa de análise do Lago de **Dados Azure** no recipiente Foreach Loop. A tarefa do sistema de ficheiros Azure Data Lake Store ajuda a descarregar ficheiros U-SQL na sua conta ADLS para uma pasta temporária. O Foreach Loop Container e a Azure Data Lake Analytics Task ajudam a enviar todos os ficheiros U-SQL sob a pasta temporária para a conta Azure Data Lake Analytics como um trabalho U-SQL.

![Diagrama que mostra uma tarefa do sistema de ficheiros Azure Data Lake Store sendo adicionada a um recipiente foreach Loop.](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Configure Azure Data Lake Store File System Task

1. Definir **Operação** para **CopyFromADLS**.
2. Configurar **a AzureDataLakeConnection,** saiba mais sobre [o Azure Data Lake Store Connection Manager](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).
3. Definir **AzureDataLakeDirectory**. Aponte para a pasta que armazena os seus scripts U-SQL. Utilize um caminho relativo que seja relativo à pasta raiz da conta Azure Data Lake Store.
4. Desfaça **o Destino** para uma pasta que cache os scripts U-SQL descarregados. Este caminho de pasta será utilizado no Foreach Loop Container para submissão de trabalho U-SQL. 

![Configure Azure Data Lake Store File System Task](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Saiba mais sobre a tarefa do sistema de ficheiros Azure Data Lake Store.](/sql/integration-services/control-flow/azure-data-lake-store-file-system-task)

### <a name="configure-foreach-loop-container"></a>Recipiente de loop foreach configurado

1. Na página **de recolha,** desaveir **o Enumerador** **para O Enumerador de Ficheiros Foreach**.

2. **Desfira a pasta** no grupo **de configuração** do Enumerador para a pasta temporária que inclui os scripts U-SQL descarregados.

3. Desaperte **os ficheiros** na **configuração do Enumerador** para `*.usql` que o recipiente de loop apenas apanhe os ficheiros que terminam com `.usql` .

    ![Screenshot que mostra o Foreach Loop Editor com "Collection" selecionado e as secções de configuração enumerador e enumerador em destaque.](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Na página **De Mapeamentos Variáveis,** adicione uma variável definida pelo utilizador para obter o nome do ficheiro para cada ficheiro U-SQL. Desave o **Índice** para 0 para obter o nome do ficheiro. Neste exemplo, defina uma variável chamada `User::FileName` . Esta variável será usada para obter dinamicamente a ligação de ficheiros de script U-SQL e definir o nome de trabalho U-SQL em Azure Data Lake Analytics Task.

    ![Configure o recipiente foreach loop para obter o nome do ficheiro](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Configure Azure Data Lake Analytics Task 

1. Definir **Tipôm de Origem** para **FileConnection**.

2. **Desconhete o FileConnection** na ligação de ficheiro que aponta para os objetos de ficheiro devolvidos do Foreach Loop Container.
    
    Para criar esta ligação de ficheiro:

   1. Escolha **\<New Connection...>** na definição de 'FileConnection'.
   2. Desave **o tipo de utilização** para **o ficheiro existente** e desacorda o **Ficheiro** para qualquer trajetória de ficheiro existente.

       ![Screenshot que mostra o Diretor de Ligação de Ficheiros Com "Ficheiro existente" selecionado para "Tipo de Utilização".](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. Na **visualização de Gestores de Conexão,** clique com o botão direito na ligação de ficheiros criada agora e escolha **Propriedades**.

   4. Na janela **Propriedades,** expanda **as Expressões** e coloque **o ConnectionString** na variável definida no Foreach Loop Container, por exemplo, `@[User::FileName]` .

       ![Recipiente de loop foreach configurado](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Desconfiem **da AzureDataLakeAnalyticsConnection** na conta Azure Data Lake Analytics para a quais pretende submeter empregos. Saiba mais sobre [o Azure Data Lake Analytics Connection Manager](/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager).

4. Desa estaleia outras configurações de trabalho. [Saiba Mais](/sql/integration-services/control-flow/azure-data-lake-analytics-task).

5. Use **expressões** para definir dinamicamente o nome de trabalho U-SQL:

    1. Na página **Expressions,** adicione um novo par de valores-chave de expressão para **o Nome de Emprego**.
    2. Deite o valor do Nome de Emprego à variável definida no Foreach Loop Container, por exemplo, `@[User::FileName]` .
    
        ![Configure expressão SSIS para nome de emprego U-SQL](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Cenário 3-Use U-SQL ficheiros em Azure Blob Storage

Pode utilizar ficheiros U-SQL no Azure Blob Storage utilizando **a tarefa de descarregamento Azure Blob** no Azure Feature Pack. Esta abordagem permite-lhe utilizar os scripts na nuvem.

Os passos são semelhantes ao [Cenário 2: Use ficheiros U-SQL na Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Altere a tarefa do sistema de ficheiros Azure Data Lake Store para Azure Blob Download Task. [Saiba mais sobre a tarefa de download da Azure Blob](/sql/integration-services/control-flow/azure-blob-download-task).

O fluxo de controlo é como abaixo.

![Use ficheiros U-SQL na Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Cenário 4-Use U-SQL ficheiros na máquina local

Além de utilizar ficheiros U-SQL armazenados na nuvem, também pode utilizar ficheiros na sua máquina local ou ficheiros implantados com os seus pacotes SSIS.

1. Clique com o botão direito **Gestores** de Conexão no projeto SSIS e escolha **Novo Gestor de Conexão.**

2. Selecione o tipo **de ficheiro** e clique **em Adicionar...**.

3. Desave **o tipo de utilização** para **o ficheiro existente** e desave o **Ficheiro** para o ficheiro na máquina local.

    ![Adicionar ligação de ficheiro ao arquivo local](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Adicione **a tarefa Azure Data Lake Analytics** e:
    1. Definir **Tipôm de Origem** para **FileConnection**.
    2. **Desconhecê-lo** para a Ligação de Ficheiros criado há pouco.

5. Termine outras configurações para Azure Data Lake Analytics Task.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Declaração U-SQL de 5 utilizações em variável SSIS

Em alguns casos, poderá ter de gerar dinamicamente as declarações U-SQL. Pode utilizar **a Variável SSIS** com **expressão SSIS** e outras tarefas SSIS, como a Script Task, para ajudá-lo a gerar a declaração U-SQL dinamicamente.

1. Abrir a janela da ferramenta de variáveis através do menu de **> variáveis** de nível superior.

2. Adicione uma Variável SSIS e desacione o valor diretamente ou use **a Expressão** para gerar o valor.

3. Adicione **a tarefa Azure Data Lake Analytics** e:
    1. Definir **Tipôm de Origem** para **Variável**.
    2. Desata **a FonteVariável** à Variável SSIS criada há pouco.

4. Termine outras configurações para Azure Data Lake Analytics Task.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Parâmetros de 6-Pass para script U-SQL

Em alguns casos, pode querer definir dinamicamente o valor variável U-SQL no script U-SQL. A funcionalidade **de mapeamento de parâmetros** na Azure Data Lake Analytics Task ajuda neste cenário. Existem geralmente dois casos típicos de utilizador:

- Desacorda as variáveis do caminho do ficheiro de entrada e saída dinamicamente com base na data e hora atuais.
- Desa estale o parâmetro para os procedimentos armazenados.

[Saiba mais sobre como definir parâmetros para o script U-SQL](/sql/integration-services/control-flow/azure-data-lake-analytics-task#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Passos seguintes

- [Executar pacotes do SSIS no Azure](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)
- [Pacote de Recursos Azure para Serviços de Integração (SSIS)](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#scenario-managing-data-in-the-cloud)
- [Agendar empregos U-SQL usando a Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md)