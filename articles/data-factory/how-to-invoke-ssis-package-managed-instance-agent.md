---
title: Executar pacotes SSIS por Agente de Instância Gerido Azure SQL
description: Saiba como executar pacotes SSIS por Agente de Instância Gerido Azure SQL.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394725"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>Executar pacotes SSIS por Agente de Instância Gerido Azure SQL
Este artigo descreve como executar um pacote de Serviços de Integração de Servidores SQL (SSIS) utilizando o Agente de Instância Gerido Azure SQL. Esta funcionalidade fornece comportamentos semelhantes, tal como quando marca pacotes SSIS pelo SQL Server Agent no seu ambiente on-prem.

Com esta funcionalidade, pode executar pacotes SSIS que estão armazenados em SSISDB de Azure SQL Managed Instance ou File System, como Ficheiros Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar esta funcionalidade, descarregue e instale a versão mais recente do SSMS, que é a versão 18.5 ou posterior. Descarregue-o a partir [deste site](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

E é necessário fornecer um Tempo de Integração Azure-SSIS na Azure Data Factory, que utiliza o Azure SQL Managed Instance como servidor de ponto final. Se ainda não o tiver provisionado, forneça-o seguindo instruções no [tutorial](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>Executar pacotes SSIS em SSISDB por Agente de Instância Gerido Azure SQL
Neste passo, utiliza o Agente de Instância Gerido Azure SQL para invocar pacotes SSIS que são armazenados no SSISDB em 1º Lugar, em Caso Gerido por Azure SQL.
1. Na versão mais recente do SSMS, ligue-se à Instância Gerida Azure SQL.
2. Criar um novo Agente Job e um novo passo de trabalho.

![Novo Agente Job](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Na página **New Job Step,** escolha o tipo de pacote de pacote de serviços de integração do **Servidor SQL.**

![Novo passo de trabalho do SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. No separador **Pacote,** escolha o **Catálogo SSIS** como tipo de fonte de embalagem.
5. Como o SSISDB está na mesma Instância Gerida Azure SQL, não precisa especificar a autenticação.
6. Especifique um pacote SSIS do seu SSISDB.

![Tipo de Fonte de Pacote - Catálogo SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. No separador **Configurações,** pode especificar valores de **parâmetros,** sobrepor-se aos valores nos Gestores de **Ligação,** substituir **a Propriedade** e escolher o **nível de registo**.

![Tipo de fonte de pacote - Configuração do catálogo SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. Depois de terminar todas as configurações acima, clique em **OK** para salvar a configuração do Agente Job.
9. Inicie o Trabalho do Agente para executar o pacote SSIS.


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>Executar pacotes SSIS no sistema de ficheiros pelo agente de instância gerido pela Azure SQL
Neste passo, utiliza o Agente de Instância Gerido azure SQL para invocar pacotes SSIS que são armazenados no Sistema de Ficheiros para executar.
1. Na versão mais recente do SSMS, ligue-se à Instância Gerida Azure SQL.
2. Criar um novo Agente Job e um novo passo de trabalho.

   ![Novo Agente Job](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Na página **New Job Step,** escolha o tipo de pacote de pacote de serviços de integração do **Servidor SQL.**

   ![Novo passo de trabalho do SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. No separador **Pacote,** escolha o **sistema Ficheiro** como tipo de fonte de embalagem.

   ![Tipo de origem do pacote - Sistema de ficheiros](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. Se o seu pacote for enviado para o Ficheiro Azure, escolha a partilha de **ficheiros Azure** como tipo de fonte de ficheiro.
      - O caminho do pacote é ** \\ <storage account name>\< \<.file.core.windows.net nome de partilha de ficheiros>nome de pacote>.dtsx**
      - Digite o nome da conta de ficheiro Sintetizador e a chave de conta no pacote de **acesso** ao ficheiro Azure. O domínio é definido como **Azure**.
   2. Se o seu pacote for enviado para uma partilha de rede, escolha a **partilha da Rede** como tipo de fonte de ficheiro.
      - O caminho do pacote é o **caminho unc** do seu arquivo pacote com a sua extensão dtsx.
      - Digite no **domínio**correspondente, **nome de utilizador**e **palavra-passe** para aceder ao ficheiro pacote de partilha de rede.
   3. Se o ficheiro do pacote estiver encriptado com senha, selecione **a palavra-passe de encriptação** e escreva a palavra-passe.

 5. No separador **Configurações,** escreva o caminho do ficheiro de **configuração** se precisar de um ficheiro de configuração para executar o pacote SSIS.
 6. No separador **opções de Execução,** pode escolher se utiliza a **autenticação** do windows ou o **tempo de execução de 32 bits** para executar o pacote SSIS.
 7. No **separador Login,** pode escolher a **via de registo** e a credencial de acesso ao registo correspondente para armazenar os ficheiros de registo. Por predefinição, o caminho de registo será o mesmo que o caminho da pasta do pacote e a credencial de acesso ao registo será a mesma que a credencial de acesso ao pacote.
 8. No separador **De valores set,** pode escrever o Caminho e **Valor** **da Propriedade** para anular as propriedades do pacote.
 Por exemplo, para sobrepor o valor da variável do utilizador, insira o seu caminho no seguinte formato: **<variable name>\Package.Variables[Utilizador:: ]. Valor**.
 9. Depois de terminar todas as configurações acima, clique em **OK** para salvar a configuração do Agente Job.
 10. Inicie o Trabalho do Agente para executar o pacote SSIS.


 ## <a name="cancel-ssis-package-execution"></a>Cancelar execução de pacote SSIS
 Para cancelar a execução do pacote de um trabalho de Agente Gerido Azure SQL, deve seguir os passos abaixo em vez de parar diretamente o trabalho de agente.
 1. Encontre o seu agente SQL **JobId** da **msdb.dbo.sysjobs**.
 2. Encontre o correspondente **execução SSIS Com** base no ID de trabalho por consulta abaixo:
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. Selecione **Operações Ativas** no catálogo SSIS.

    ![Tipo de origem do pacote - Sistema de ficheiros](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. Parar o funcionamento correspondente com base no **execuçãoId**.

## <a name="next-steps"></a>Passos seguintes
 Também pode agendar pacotes SSIS utilizando a Azure Data Factory. Para obter instruções passo a passo, consulte o Gatilho do Evento da Fábrica de [Dados Azure](how-to-create-event-trigger.md). 