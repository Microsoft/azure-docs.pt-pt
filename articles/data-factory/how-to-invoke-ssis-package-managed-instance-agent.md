---
title: Executar pacotes SSIS utilizando o Agente de Instância gerido pela Base de Dados Azure SQL
description: Aprenda a executar pacotes SSIS utilizando o Agente de Instância gerido pela Base de Dados SQL Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: fcbfeb5ab3a3a80fdb8f7e355f290451d4afe804
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144809"
---
# <a name="run-ssis-packages-by-using-azure-sql-database-managed-instance-agent"></a>Executar pacotes SSIS utilizando o Agente de Instância gerido pela Base de Dados Azure SQL
Este artigo descreve como executar um pacote de Serviços de Integração de Servidores SQL (SSIS) utilizando o Agente de Instância gerido pela Base de Dados Azure SQL. Esta funcionalidade fornece comportamentos semelhantes ao programar pacotes SSIS utilizando o Agente servidor SQL no seu ambiente no local.

Com esta funcionalidade, pode executar pacotes SSIS que são armazenados no SSISDB numa instância gerida pela Azure SQL Database ou num sistema de ficheiros como o Azure Files.

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar esta funcionalidade, [descarregue](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) e instale a versão mais recente do SQL Server Management Studio (SSMS), que é a versão 18.5.

Também precisa de fornecer um tempo de execução de [integração Azure-SSIS](tutorial-create-azure-ssis-runtime-portal.md) na Azure Data Factory. Utiliza uma base de dados Azure SQL gerida como um servidor de ponto final. 

## <a name="run-an-ssis-package-in-ssisdb"></a>Executar um pacote SSIS no SSISDB
Neste procedimento, utiliza o Agente de Instância gerido pela Base de Dados Azure SQL para invocar um pacote SSIS que está armazenado no SSISDB.

1. Na versão mais recente do SSMS, ligue-se a uma instância gerida pela Base de Dados Azure SQL.
1. Criar um novo emprego de agente e um novo passo de trabalho. Em **SQL Server Agent,** clique à direita na pasta **Jobs** e, em seguida, selecione **New Job**.

   ![Seleções para criar um novo trabalho de agente](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Na página **New Job Step,** selecione **SQL Server Integration Services Package** como o tipo.

   ![Seleções para criar um novo passo de trabalho do SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. No separador **Pacote,** selecione **SSIS Catalog** como o tipo de origem do pacote.
1. Como o SSISDB está numa instância gerida pela Base de Dados Azure SQL, não precisa de especificar a autenticação.
1. Especifique um pacote SSIS do SSISDB.

   ![Separador de pacote com seleções para o tipo de origem do pacote](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. No separador **Configuração,** pode:
  
   - Especifique os valores dos parâmetros nos **parâmetros**.
   - Sobrepor valores ao abrigo **dos Gestores**de Ligação .
   - Sobrepor-se à propriedade e escolha o nível de exploração **em Advanced**.

   ![Separador de configuração com seleções para o tipo de origem do pacote](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. Selecione **OK** para salvar a configuração de trabalho do agente.
1. Inicie o trabalho de agente para executar o pacote SSIS.


## <a name="run-an-ssis-package-in-the-file-system"></a>Executar um pacote SSIS no sistema de ficheiros
Neste procedimento, utiliza o Agente de Instância gerido pela Base de Dados Azure SQL para executar um pacote SSIS que está armazenado no sistema de ficheiros.

1. Na versão mais recente do SSMS, ligue-se a uma instância gerida pela Base de Dados Azure SQL.
1. Criar um novo emprego de agente e um novo passo de trabalho. Em **SQL Server Agent,** clique à direita na pasta **Jobs** e, em seguida, selecione **New Job**.

   ![Seleções para criar um novo trabalho de agente](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Na página **New Job Step,** selecione **SQL Server Integration Services Package** como o tipo.

   ![Seleções para criar um novo passo de trabalho do SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. No separador **Pacote:**

   1. Para **obter uma fonte de pacote,** selecione sistema de **ficheiros**.
   
   1. Para **o tipo de origem de ficheiros:**   

      - Se o seu pacote for enviado para Ficheiros Azure, selecione **a partilha de ficheiros Azure**.

        ![Opções para o tipo de fonte de ficheiro](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)
      
        O caminho do pacote é ** \\ <storage account name>\< \<.file.core.windows.net nome de partilha de ficheiros>nome de pacote>.dtsx**.
      
        No âmbito da credencial de acesso ao **ficheiro Pacote,** introduza o nome da conta de ficheiro Sintetizador e a chave de conta azure para aceder ao ficheiro Azure. O domínio é definido como **Azure**.

      - Se o seu pacote for enviado para uma partilha de rede, selecione **a partilha da Rede**.
      
        O caminho do pacote é o caminho unc do seu arquivo pacote com a sua extensão .dtsx.
      
        Introduza o domínio, nome de utilizador e senha correspondentes para aceder ao ficheiro pacote de partilha de rede.
   1. Se o ficheiro do pacote estiver encriptado com uma palavra-passe, selecione **a palavra-passe de Encriptação** e introduza a palavra-passe.
1. No separador **Configurações,** introduza o caminho do ficheiro de configuração se precisar de um ficheiro de configuração para executar o pacote SSIS.
1. No separador **opções de Execução,** pode escolher se utiliza a **autenticação do Windows** ou o **tempo de execução de 32 bits** para executar o pacote SSIS.
1. No separador **Logado,** pode escolher a via de registo e a credencial de acesso ao registo correspondente para armazenar os ficheiros de registo. Por predefinição, o caminho de registo é o mesmo que o caminho da pasta do pacote, e a credencial de acesso ao registo é a mesma que a credencial de acesso ao pacote.
1. No separador **de valores set,** pode entrar no caminho da propriedade e valorizar para anular as propriedades do pacote.
 
   Por exemplo, para sobrepor o valor da variável do utilizador, insira o seu caminho no seguinte formato: **<variable name>\Package.Variables[Utilizador:: ]. Valor**.
1. Selecione **OK** para salvar a configuração de trabalho do agente.
1. Inicie o trabalho de agente para executar o pacote SSIS.


## <a name="cancel-ssis-package-execution"></a>Cancelar execução de pacote SSIS
Para cancelar a execução do pacote de um trabalho de Agente de Instância gerido pela Base de Dados Azure SQL, tome as seguintes medidas em vez de parar diretamente o trabalho do agente:

1. Encontre o seu agente SQL **JobId** da **msdb.dbo.sysjobs**.
1. Encontre o **execução** SSIS correspondente com base no ID de trabalho, utilizando esta consulta:
   ```sql
   select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
1. Clique no catálogo SSISDB e, em seguida, selecione **Operações Ativas**.

   !["Operações Ativas" no menu de atalho para o catálogo SSISDB](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. Parar a operação correspondente com base no **execuçãoId**.

## <a name="next-steps"></a>Passos seguintes
Também pode agendar pacotes SSIS utilizando a Azure Data Factory. Para obter instruções passo a passo, consulte o gatilho do [evento Azure Data Factory](how-to-create-event-trigger.md). 