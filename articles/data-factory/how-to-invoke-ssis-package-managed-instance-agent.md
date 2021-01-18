---
title: Executar pacotes SSIS usando O Agente de Instância Gerida Azure SQL
description: Aprenda a executar pacotes SSIS utilizando o Agente de Instância Gerido Azure SQL.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: 5f8b18375c517ab5b620b2d6d897f5133b14705d
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556465"
---
# <a name="run-ssis-packages-by-using-azure-sql-managed-instance-agent"></a>Executar pacotes SSIS utilizando o agente de instância gerido Azure SQL

Este artigo descreve como executar um pacote sql server integration services (SSIS) usando o Agente de Instância Gerido Azure SQL. Esta funcionalidade fornece comportamentos semelhantes aos de quando programa pacotes SSIS utilizando o SqL Server Agent no seu ambiente no local.

Com esta funcionalidade, pode executar pacotes SSIS que são armazenados em SSISDB numa SQL Managed Instance, um sistema de ficheiros como a Azure Files ou uma loja de pacotes de integração Azure-SSIS.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, [descarregue](/sql/ssms/download-sql-server-management-studio-ssms) e instale o mais recente SQL Server Management Studio (SSMS). Detalhes de suporte da versão como abaixo:

- Para executar pacotes em SSISDB ou sistema de ficheiros, instale a versão SSMS 18.5 ou superior.
- Para executar pacotes na loja de pacotes, instale a versão SSMS 18.6 ou superior.

Também precisa de [providenciar um tempo de integração Azure-SSIS](./tutorial-deploy-ssis-packages-azure.md) na Azure Data Factory. Utiliza um SQL Managed Instance como um servidor de ponto final.

## <a name="run-an-ssis-package-in-ssisdb"></a>Executar um pacote SSIS em SSISDB

Neste procedimento, utiliza o SqL Managed Instance Agent para invocar um pacote SSIS que está armazenado no SSISDB.

1. Na versão mais recente do SSMS, ligue-se a uma SQL Managed Instance.
1. Criar um novo emprego de agente e um novo passo de emprego. No **âmbito do SqL Server Agent,** clique à direita na pasta **Jobs** e, em seguida, selecione **New Job**.

   ![Seleções para criar um novo trabalho de agente](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Na página **New Job Step,** selecione **o Pacote de Serviços de Integração de Servidor SQL** como o tipo.

   ![Seleções para criar um novo passo de trabalho SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. No **separador Pacote,** selecione **o Catálogo SSIS** como localização do pacote.
1. Como o SSISDB está numa localização gerida do SQL, não precisa de especificar a autenticação.
1. Especifique um pacote SSIS do SSISDB.

   ![Separador de pacote com seleções para o tipo de fonte de pacote](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. No **separador Configuração,** pode:
  
   - Especifique os valores dos parâmetros nos **parâmetros**.
   - Sobrepõe-se aos valores em **"Connection Managers".**
   - Sobrepõe-se à propriedade e escolha o nível de registo em **Advanced**.

   ![Separador de configuração com seleções para o tipo de fonte de pacote](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. Selecione **OK** para guardar a configuração de trabalho do agente.
1. Inicie o trabalho de agente para executar o pacote SSIS.

## <a name="run-an-ssis-package-in-the-file-system"></a>Executar um pacote SSIS no sistema de ficheiros

Neste procedimento, utiliza o SqL Managed Instance Agent para executar um pacote SSIS que está armazenado no sistema de ficheiros.

1. Na versão mais recente do SSMS, ligue-se a uma SQL Managed Instance.
1. Criar um novo emprego de agente e um novo passo de emprego. No **âmbito do SqL Server Agent,** clique à direita na pasta **Jobs** e, em seguida, selecione **New Job**.

   ![Seleções para criar um novo trabalho de agente](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Na página **New Job Step,** selecione **o Pacote de Serviços de Integração de Servidor SQL** como o tipo.

   ![Seleções para criar um novo passo de trabalho SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. No **separador Pacote:**

   1. Para **a localização do pacote**, selecione o sistema de **ficheiros**.

   1. Para **o tipo de origem de ficheiro:**

      - Se o seu pacote for enviado para ficheiros Azure, selecione **a partilha de ficheiros Azure**.

        ![Opções para tipo de origem de ficheiros](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

        O caminho do pacote **`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`** é.

        In **Package file access credencial**, insira o nome da conta de ficheiro Azure e a chave de conta para aceder ao ficheiro Azure. O domínio é definido como **Azure.**

      - Se o seu pacote for enviado para uma partilha de rede, selecione **Network share**.

        O caminho do pacote é o caminho unc do seu ficheiro de pacote com a sua extensão .dtsx.

        Introduza o domínio correspondente, o nome de utilizador e a palavra-passe para aceder ao ficheiro do pacote de partilha de rede.
   1. Se o seu ficheiro de pacote estiver encriptado com uma palavra-passe, selecione **a palavra-passe de encriptação** e introduza a palavra-passe.
1. No separador **Configurações,** introduza o caminho do ficheiro de configuração se precisar de um ficheiro de configuração para executar o pacote SSIS.
   Se armazenar a sua configuração em Ficheiros Azure, o seu caminho de configuração será **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. No separador **Opções de Execução,** pode escolher se utiliza a **autenticação do Windows** ou **o tempo de execução de 32 bits** para executar o pacote SSIS.
1. No **separador Registar,** pode escolher a trajetória de registo e a credencial de acesso correspondente para armazenar os ficheiros de registo. 
   Por predefinição, a trajetória de registo é a mesma que a trajetória da pasta de embalagem, e a credencial de acesso ao registo é a mesma que a credencial de acesso ao pacote.
   Se armazenar os seus registos em Ficheiros Azure, o seu caminho de registo será **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. No **separador Valores Definidos,** pode introduzir o caminho e valor da propriedade para anular as propriedades do pacote.

   Por exemplo, para anular o valor da variável do utilizador, insira o seu caminho no seguinte formato: **`\Package.Variables[User::<variable name>].Value`** .
1. Selecione **OK** para guardar a configuração de trabalho do agente.
1. Inicie o trabalho de agente para executar o pacote SSIS.

## <a name="run-an-ssis-package-in-the-package-store"></a>Executar um pacote SSIS na loja de pacotes

Neste procedimento, utiliza o SqL Managed Instance Agent para executar um pacote SSIS que está armazenado na loja de pacotes Azure-SSIS IR.

1. Na versão mais recente do SSMS, ligue-se a uma SQL Managed Instance.
1. Criar um novo emprego de agente e um novo passo de emprego. No **âmbito do SqL Server Agent,** clique à direita na pasta **Jobs** e, em seguida, selecione **New Job**.

   ![Seleções para criar um novo trabalho de agente](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Na página **New Job Step,** selecione **o Pacote de Serviços de Integração de Servidor SQL** como o tipo.

   ![Seleções para criar um novo passo de trabalho SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. No **separador Pacote:**

   1. Para **a localização do pacote**, selecione Package **Store**.

   1. Para **o caminho do pacote:**

      O caminho do pacote **`<package store name>\<folder name>\<package name>`** é.

      ![Opções para tipo de loja de pacotes](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-package-store.png)

   1. Se o seu ficheiro de pacote estiver encriptado com uma palavra-passe, selecione **a palavra-passe de encriptação** e introduza a palavra-passe.
1. No separador **Configurações,** introduza o caminho do ficheiro de configuração se precisar de um ficheiro de configuração para executar o pacote SSIS.
   Se armazenar a sua configuração em Ficheiros Azure, o seu caminho de configuração será **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. No separador **Opções de Execução,** pode escolher se utiliza a **autenticação do Windows** ou **o tempo de execução de 32 bits** para executar o pacote SSIS.
1. No **separador Registar,** pode escolher a trajetória de registo e a credencial de acesso correspondente para armazenar os ficheiros de registo.
   Por predefinição, a trajetória de registo é a mesma que a trajetória da pasta de embalagem, e a credencial de acesso ao registo é a mesma que a credencial de acesso ao pacote.
   Se armazenar os seus registos em Ficheiros Azure, o seu caminho de registo será **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. No **separador Valores Definidos,** pode introduzir o caminho e valor da propriedade para anular as propriedades do pacote.

   Por exemplo, para anular o valor da variável do utilizador, insira o seu caminho no seguinte formato: **`\Package.Variables[User::<variable name>].Value`** .
1. Selecione **OK** para guardar a configuração de trabalho do agente.
1. Inicie o trabalho de agente para executar o pacote SSIS.

## <a name="cancel-ssis-package-execution"></a>Cancelar execução de pacote SSIS

Para cancelar a execução de pacotes a partir de um trabalho de Agente de Instância Gerida SQL, tome as seguintes medidas em vez de parar diretamente o trabalho de agente:

1. Encontre o seu trabalho de agente **SQLId** a partir de **msdb.dbo.sysempregos.**
1. Encontre a **execução** SSIS correspondente com base no ID do trabalho, utilizando esta consulta:
   ```sql
   select * from '{table for job execution}' where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
   Se os seus pacotes SSIS estiverem no SSISDB, use **ssisdb.internal.execution_parameter_values** como tabela para a execução de empregos. Se as suas embalagens SSIS estiverem no sistema de ficheiros, utilize **ssisdb.internal.execution_parameter_values_noncatalog**.
1. Clique com o botão direito no catálogo SSISDB e, em seguida, selecione **Ative Operations**.

   !["Operações Ativas" no menu de atalho para o catálogo SSISDB](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. Parar a operação correspondente com base na **execuçãoId**.

## <a name="next-steps"></a>Passos seguintes
Também pode agendar pacotes SSIS utilizando a Azure Data Factory. Para obter instruções passo a passo, consulte o gatilho do [evento Azure Data Factory](how-to-create-event-trigger.md).