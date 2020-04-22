---
title: Aceder a lojas de dados e partilhas de ficheiros com autenticação windows
description: Saiba como configurar o catálogo SSIS na Base de Dados Azure SQL e na Integração Azure-SSIS Runtime na Azure Data Factory para executar pacotes que acedam a lojas de dados e partilhem ficheiros com autenticação windows.
ms.date: 3/22/2018
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 0c4cdc3481fb58efd8eaa4cd83e1d6167f203a4e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760189"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Access data stores and file shares with Windows authentication from SSIS packages in Azure (Aceder a arquivos de dados e a partilhas de ficheiros com a autenticação do Windows a partir de pacotes do SSIS no Azure)

Pode utilizar a autenticação do Windows para aceder a lojas de dados, tais como Servidores SQL, partilhas de ficheiros, Ficheiros Azure, etc. a partir de pacotes SSIS que funcionam no seu Runtime de Integração Azure-SSIS (IR) na Azure Data Factory (ADF). As suas lojas de dados podem estar no local, hospedadas em Máquinas Virtuais Azure (VMs), ou em funcionamento em Azure como serviços geridos. Se estiverem no local, tem de se juntar ao seu IR Azure-SSIS a uma Rede Virtual (Rede Virtual Microsoft Azure) ligada à sua rede no local, consulte [Join Azure-SSIS IR a uma Rede Virtual Microsoft Azure](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Existem quatro métodos para aceder a lojas de dados com autenticação Windows a partir de pacotes SSIS em execução no seu IR Azure-SSIS:

| Método de ligação | Âmbito eficaz | Passo de configuração | Método de acesso em pacotes | Número de conjuntos de credenciais e recursos conectados | Tipo de recursos conectados | 
|---|---|---|---|---|---|
| Criação de um contexto de execução ao nível da atividade | Por executar atividade do pacote SSIS | Configure a propriedade de **autenticação do Windows** para configurar um contexto de "Execução/Execução como" ao executar pacotes SSIS como executar atividades do Pacote SSIS em pipelines ADF.<br/><br/> Para mais informações, consulte configurar a atividade do [Pacote SSIS executar](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). | Aceda diretamente aos recursos em pacotes via caminho da CNU, `\\YourFileShareServerName\YourFolderName` por exemplo, se utilizar ações de ficheiros ou Ficheiros Azure:`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Suporte apenas um conjunto de credenciais para todos os recursos conectados | - Ações de arquivo nas instalações/VMs Azure<br/><br/> - Ficheiros Azure, ver Use uma partilha de [ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - Servidores SQL nas instalações/VMs Azure com autenticação windows<br/><br/> - Outros recursos com autenticação windows |
| Criação de um contexto de execução ao nível do catálogo | Per Azure-SSIS IR, mas é ultrapassado ao criar um contexto de execução ao nível da atividade (ver acima) | Execute o procedimento `catalog.set_execution_credential` armazenado sSISDB para configurar um contexto "Execução/Execução como" .<br/><br/> Para mais informações, consulte o resto deste artigo abaixo. | Aceda diretamente aos recursos em pacotes via caminho da CNU, `\\YourFileShareServerName\YourFolderName` por exemplo, se utilizar ações de ficheiros ou Ficheiros Azure:`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Suporte apenas um conjunto de credenciais para todos os recursos conectados | - Ações de arquivo nas instalações/VMs Azure<br/><br/> - Ficheiros Azure, ver Use uma partilha de [ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - Servidores SQL nas instalações/VMs Azure com autenticação windows<br/><br/> - Outros recursos com autenticação windows |
| Credenciais persistentes via `cmdkey` comando | Per Azure-SSIS IR, mas é ultrapassado ao criar um contexto de execução de nível de atividade/catálogo (ver acima) | Execute `cmdkey` o comando num script`main.cmd`de configuração personalizado () ao fornecer o seu IR Azure-SSIS, `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword` `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`por exemplo, se utilizar ações de ficheiros ou Ficheiros Azure: ou .<br/><br/> Para mais informações, consulte [Customize configuração para O IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). | Aceda diretamente aos recursos em pacotes via caminho da CNU, `\\YourFileShareServerName\YourFolderName` por exemplo, se utilizar ações de ficheiros ou Ficheiros Azure:`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Apoiar múltiplos conjuntos de credenciais para diferentes recursos conectados | - Ações de arquivo nas instalações/VMs Azure<br/><br/> - Ficheiros Azure, ver Use uma partilha de [ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - Servidores SQL nas instalações/VMs Azure com autenticação windows<br/><br/> - Outros recursos com autenticação windows |
| Unidades de montagem no tempo de execução do pacote (não persistente) | Por pacote | Execute `net use` o comando na Executar Tarefa de Processo que é adicionado no início do fluxo de controlo nos seus pacotes, por exemplo,`net use D: \\YourFileShareServerName\YourFolderName` | Aceder a partilhas de ficheiros através de unidades mapeadas | Suporte várias unidades para diferentes partilhas de ficheiros | - Ações de arquivo nas instalações/VMs Azure<br/><br/> - Ficheiros Azure, ver Use uma partilha de [ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) |
|||||||

> [!WARNING]
> Caso não utilize nenhum dos métodos acima referidos para aceder a lojas de dados com autenticação Windows, os seus pacotes que dependem da autenticação do Windows não podem aceder aos mesmos e falhar no tempo de execução. 

O resto deste artigo descreve como configurar o catálogo SSIS (SSISDB) hospedado no servidor de base de dados Azure SQL/Managed Instance para executar pacotes no Ir Azure-SSIS que utilizam a autenticação do Windows para aceder a lojas de dados. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Só pode usar um conjunto de credenciais

Quando utiliza a autenticação do Windows num pacote SSIS, só pode utilizar um conjunto de credenciais. As credenciais de domínio que fornece quando segue os passos deste artigo aplicam-se a todas as execuções de pacotes - interativos ou programados - no seu IR Azure-SSIS até que as altere ou remova. Se o seu pacote tiver de se ligar a várias lojas de dados com diferentes conjuntos de credenciais, deverá considerar os métodos alternativos acima referidos.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Fornecer credenciais de domínio para autenticação do Windows

Para fornecer credenciais de domínio que permitem que os pacotes utilizem a autenticação do Windows para aceder a lojas de dados nas instalações, faça as seguintes coisas:

1. Com o SQL Server Management Studio (SSMS) ou outra ferramenta, ligue-se ao servidor de base de dados Azure SQL/Instância Gerida que acolhe o SSISDB. Para mais informações, consulte [Connect to SSISDB in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como base de dados atual, abra uma janela de consulta.

3. Executar o seguinte procedimento armazenado e fornecer as credenciais de domínio apropriadas:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Execute os seus pacotes SSIS. Os pacotes utilizam as credenciais que forneceu para aceder a lojas de dados nas instalações com autenticação windows.

### <a name="view-domain-credentials"></a>Ver credenciais de domínio

Para ver as credenciais de domínio ativos, faça as seguintes coisas:

1. Com SSMS ou outra ferramenta, ligue-se ao servidor de base de dados Azure SQL/Instância Gerida que acolhe o SSISDB. Para mais informações, consulte [Connect to SSISDB in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como base de dados atual, abra uma janela de consulta.

3. Executar o seguinte procedimento armazenado e verificar a saída:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Credenciais claras de domínio
Para limpar e remover as credenciais que forneceu como descrito neste artigo, faça as seguintes coisas:

1. Com SSMS ou outra ferramenta, ligue-se ao servidor de base de dados Azure SQL/Instância Gerida que acolhe o SSISDB. Para mais informações, consulte [Connect to SSISDB in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como base de dados atual, abra uma janela de consulta.

3. Executar o seguinte procedimento armazenado:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Ligue-se a um Servidor SQL nas instalações

Para verificar se pode ligar-se a um Servidor SQL nas instalações, faça as seguintes coisas:

1. Para executar este teste, encontre um computador não-unido.

2. No computador não-unido, execute o seguinte comando para iniciar SSMS com as credenciais de domínio que pretende utilizar:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. A partir de SSMS, verifique se pode ligar-se ao Servidor SQL nas instalações.

### <a name="prerequisites"></a>Pré-requisitos

Para aceder a um Servidor SQL nas instalações a partir de pacotes em funcionamento em Azure, faça as seguintes coisas:

1.  No SQL Server Configuration Manager, ative o protocolo TCP/IP.

2. Permitir o acesso através da firewall do Windows. Para mais informações, consulte [configurar a firewall do Windows para aceder ao Servidor SQL](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Junte-se ao seu IR Azure-SSIS a uma Rede Virtual Microsoft Azure que está ligada ao Servidor SQL nas instalações.  Para mais informações, consulte [Join Azure-SSIS IR para uma rede virtual Microsoft Azure](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

4. Utilize o procedimento `catalog.set_execution_credential` armazenado sSISDB para fornecer credenciais conforme descrito neste artigo.

## <a name="connect-to-a-file-share-on-premises"></a>Ligar a uma parte de arquivo nas instalações

Para verificar se pode ligar-se a uma parte de ficheiro seletiva nas instalações, faça as seguintes coisas:

1. Para executar este teste, encontre um computador não-unido.

2. No computador não-unido, execute os seguintes comandos. Estes comandos abrem uma janela de comando com as credenciais de domínio que pretende utilizar e, em seguida, testa a conectividade com a partilha de ficheiros nas instalações, obtendo uma listagem de diretório.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Verifique se a listagem de diretórios é devolvida para a parte do ficheiro nas instalações.

### <a name="prerequisites"></a>Pré-requisitos

Para aceder a uma partilha de ficheiros nas instalações de pacotes em funcionamento em Azure, faça as seguintes coisas:

1. Permitir o acesso através da firewall do Windows.

2. Junte-se ao seu IR Azure-SSIS a uma Rede Virtual Microsoft Azure que está ligada à partilha de ficheiros nas instalações.  Para mais informações, consulte [Join Azure-SSIS IR para uma rede virtual Microsoft Azure](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

3. Utilize o procedimento `catalog.set_execution_credential` armazenado sSISDB para fornecer credenciais conforme descrito neste artigo.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Ligue-se a uma parte de ficheiro no Azure VM

Para aceder a uma partilha de ficheiros no Azure VM a partir de pacotes em funcionamento em Azure, faça as seguintes coisas:

1. Com SSMS ou outra ferramenta, ligue-se ao servidor de base de dados Azure SQL/Instância Gerida que acolhe o SSISDB. Para mais informações, consulte [Connect to SSISDB in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como base de dados atual, abra uma janela de consulta.

3. Executar o seguinte procedimento armazenado e fornecer as credenciais de domínio apropriadas:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Ligue-se a uma parte de ficheiro em Ficheiros Azure

Para mais informações sobre ficheiros Azure, consulte [Ficheiros Azure](https://azure.microsoft.com/services/storage/files/).

Para aceder a uma participação de ficheiros em Ficheiros Azure a partir de pacotes em funcionamento em Azure, faça as seguintes coisas:

1. Com SSMS ou outra ferramenta, ligue-se ao servidor de base de dados Azure SQL/Instância Gerida que acolhe o SSISDB. Para mais informações, consulte [Connect to SSISDB in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como base de dados atual, abra uma janela de consulta.

3. Executar o seguinte procedimento armazenado e fornecer as credenciais de domínio apropriadas:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Passos seguintes

- Coloque os seus pacotes. Para mais informações, consulte [Implementar um projeto SSIS para Azure com SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Executem os vossos pacotes. Para mais informações, consulte [os pacotes Run SSIS em Azure com SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Agende os seus pacotes. Para mais informações, consulte os [pacotes Schedule SSIS em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).