---
title: Aceder a lojas de dados e partilhas de ficheiros com autenticação do Windows
description: Saiba como configurar o catálogo SSIS na Base de Dados Azure SQL e no Runtime de Integração Azure-SSIS na Azure Data Factory para executar pacotes que acedam a lojas de dados e partilhas de ficheiros com a autenticação do Windows.
ms.date: 10/27/2020
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: jburchel
ms.openlocfilehash: 8cb92407954ab5376850e57b0ee91d934480b025
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786894"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Access data stores and file shares with Windows authentication from SSIS packages in Azure (Aceder a arquivos de dados e a partilhas de ficheiros com a autenticação do Windows a partir de pacotes do SSIS no Azure)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pode utilizar a autenticação do Windows para aceder a lojas de dados, tais como SQL Servers, partilhas de ficheiros, Ficheiros Azure, etc. a partir de pacotes SSIS em execução no seu Tempo de Execução de Integração Azure-SSIS (IR) na Azure Data Factory (ADF). As suas lojas de dados podem estar no local, hospedadas em Azure Virtual Machines (VMs), ou em funcionamento em Azure como serviços geridos. Se estiverem no local, tem de se juntar ao seu Azure-SSIS IR a uma Rede Virtual (Microsoft Azure Virtual Network) ligada à sua rede de instalações, consulte [Join Azure-SSIS IR a uma Rede Virtual Microsoft Azure](./join-azure-ssis-integration-runtime-virtual-network.md). Existem quatro métodos para aceder às lojas de dados com a autenticação do Windows a partir de pacotes SSIS em execução no seu Azure-SSIS IR:

| Método de ligação | Âmbito eficaz | Passo de configuração | Método de acesso em pacotes | Número de conjuntos de credenciais e recursos conectados | Tipo de recursos conectados | 
|---|---|---|---|---|---|
| Criação de um contexto de execução ao nível da atividade | Per Execute atividade do pacote SSIS | Configure a propriedade **de autenticação** do Windows para configurar um contexto "Execução/Execução como" ao executar pacotes SSIS como executar atividades do Pacote SSIS em oleodutos ADF.<br/><br/> Para obter mais informações, consulte [a atividade do Pacote SSIS Configure Execute](./how-to-invoke-ssis-package-ssis-activity.md). | Aceder diretamente aos recursos em pacotes, por exemplo, utilizar o caminho da UNC para aceder a partilhas de ficheiros ou ficheiros Azure: `\\YourFileShareServerName\YourFolderName``\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Suporte apenas um conjunto de credenciais para todos os recursos conectados | - Ações de arquivo nas instalações/Azure VMs<br/><br/> - Ficheiros Azure, ver [Utilizar uma partilha de ficheiros Azure](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> - SQL Servers nas instalações/VMs Azure com autenticação do Windows<br/><br/> - Outros recursos com autenticação do Windows |
| Criação de um contexto de execução ao nível do catálogo | Per Azure-SSIS IR, mas é ultrapassado ao estabelecer um contexto de execução ao nível da atividade (ver acima) | Execute o procedimento de armazenação SSISDB `catalog.set_execution_credential` para configurar um contexto "Execução/Execução como".<br/><br/> Para mais informações, consulte o resto deste artigo abaixo. | Aceder diretamente aos recursos em pacotes, por exemplo, utilizar o caminho da UNC para aceder a partilhas de ficheiros ou ficheiros Azure: `\\YourFileShareServerName\YourFolderName``\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Suporte apenas um conjunto de credenciais para todos os recursos conectados | - Ações de arquivo nas instalações/Azure VMs<br/><br/> - Ficheiros Azure, ver [Utilizar uma partilha de ficheiros Azure](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> - SQL Servers nas instalações/VMs Azure com autenticação do Windows<br/><br/> - Outros recursos com autenticação do Windows |
| Credenciais persistentes via `cmdkey` comando | Per Azure-SSIS IR, mas é ultrapassado ao criar um contexto de execução de nível de atividade/catálogo (ver acima) | Execute `cmdkey` o comando num script de configuração personalizado `main.cmd` () ao forreir o seu Azure-SSIS IR, por exemplo, se utilizar ações de ficheiros, Ficheiros Azure ou SQL Server:<br/><br/> `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword`,<br/><br/> `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey` ou <br/><br/> `cmdkey /add:YourSQLServerFullyQualifiedDomainNameOrIPAddress:YorSQLServerPort /user:YourDomainName\YourUsername /pass:YourPassword`.<br/><br/> Para obter mais informações, consulte [a configuração de Personalização para Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md). | Aceder diretamente aos recursos em pacotes, por exemplo, utilizar o caminho da UNC para aceder a partilhas de ficheiros ou ficheiros Azure: `\\YourFileShareServerName\YourFolderName``\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Suporte conjuntos de credenciais múltiplos para diferentes recursos conectados | - Ações de arquivo nas instalações/Azure VMs<br/><br/> - Ficheiros Azure, ver [Utilizar uma partilha de ficheiros Azure](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> - SQL Servers nas instalações/VMs Azure com autenticação do Windows<br/><br/> - Outros recursos com autenticação do Windows |
| Unidades de montagem no tempo de execução do pacote (não persistente) | Por pacote | Executar `net use` o comando na Tarefa de Processo de Execução que é adicionado no início do fluxo de controlo nas suas embalagens, por exemplo, `net use D: \\YourFileShareServerName\YourFolderName` | Aceder a partilhas de ficheiros através de unidades mapeadas | Suporte várias unidades para diferentes ações de ficheiros | - Ações de arquivo nas instalações/Azure VMs<br/><br/> - Ficheiros Azure, ver [Utilizar uma partilha de ficheiros Azure](../storage/files/storage-how-to-use-files-windows.md) |
|||||||

> [!WARNING]
> Se não utilizar nenhum dos métodos acima referidos para aceder a lojas de dados com autenticação do Windows, as suas embalagens que dependem da autenticação do Windows não podem aceder aos mesmos e falhar no tempo de execução. 

O resto deste artigo descreve como configurar o catálogo SSIS (SSISDB) alojado na SQL Database/SQL Managed Instance para executar pacotes no Azure-SSIS IR que utilizam a autenticação do Windows para aceder às lojas de dados. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Só pode usar um conjunto de credenciais

Quando utilizar a autenticação do Windows num pacote SSIS, só pode utilizar um conjunto de credenciais. As credenciais de domínio que fornece quando segue os passos deste artigo aplicam-se a todas as execuções de pacotes - interativos ou agendadas - no seu Azure-SSIS IR até que altere ou remova. Se o seu pacote tiver de se ligar a várias lojas de dados com diferentes conjuntos de credenciais, deve considerar os métodos alternativos acima.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Fornecer credenciais de domínio para a autenticação do Windows

Para fornecer credenciais de domínio que permitem que os pacotes utilizem a autenticação do Windows para aceder a lojas de dados no local, faça as seguintes coisas:

1. Com o SQL Server Management Studio (SSMS) ou outra ferramenta, conecte-se à SQL Database/SQL Managed Instance que acolhe o SSISDB. Para obter mais informações, consulte [Connect to SSISDB em Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como base de dados atual, abra uma janela de consulta.

3. Executar o seguinte procedimento armazenado e fornecer as credenciais de domínio apropriadas:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Execute os seus pacotes SSIS. Os pacotes utilizam as credenciais que forneceu para aceder a lojas de dados nas instalações com autenticação do Windows.

### <a name="view-domain-credentials"></a>Ver credenciais de domínio

Para ver as credenciais de domínio ativas, faça as seguintes coisas:

1. Com SSMS ou outra ferramenta, ligue-se à SQL Database/SQL Managed Instance que acolhe o SSISDB. Para obter mais informações, consulte [Connect to SSISDB em Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como base de dados atual, abra uma janela de consulta.

3. Executar o seguinte procedimento armazenado e verificar a saída:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Credenciais de domínio claras
Para limpar e remover as credenciais que forneceu como descrito neste artigo, faça as seguintes coisas:

1. Com SSMS ou outra ferramenta, ligue-se à SQL Database/SQL Managed Instance que acolhe o SSISDB. Para obter mais informações, consulte [Connect to SSISDB em Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como base de dados atual, abra uma janela de consulta.

3. Executar o seguinte procedimento armazenado:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Ligue-se a um Servidor SQL nas instalações

Para verificar se pode ligar-se a um SQL Server nas instalações, faça as seguintes coisas:

1. Para realizar este teste, encontre um computador não-de-domínio.

2. No computador não ligado a domínios, executar o seguinte comando para iniciar SSMS com as credenciais de domínio que pretende utilizar:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. A partir de SSMS, verifique se pode ligar-se ao SQL Server nas instalações.

### <a name="prerequisites"></a>Pré-requisitos

Para aceder a um SQL Server nas instalações a partir de pacotes em execução em Azure, faça as seguintes coisas:

1.  No Gestor de Configuração do Servidor SQL, ative o protocolo TCP/IP.

2. Permitir o acesso através da firewall do Windows. Para obter mais informações, consulte [a firewall do Windows para aceder ao SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Junte-se ao seu Azure-SSIS IR a uma Rede Virtual Microsoft Azure que esteja ligada ao SQL Server nas instalações.  Para obter mais informações, consulte [Join Azure-SSIS IR para uma Rede Virtual Microsoft Azure](./join-azure-ssis-integration-runtime-virtual-network.md).

4. Utilize o procedimento armazenado do SSISDB `catalog.set_execution_credential` para fornecer credenciais como descrito neste artigo.

## <a name="connect-to-a-file-share-on-premises"></a>Conecte-se a uma partilha de ficheiros no local

Para verificar se pode ligar-se a uma partilha de ficheiros no local, faça as seguintes coisas:

1. Para realizar este teste, encontre um computador não-de-domínio.

2. No computador não ligado ao domínio, execute os seguintes comandos. Estes comandos abrem uma janela de pedido de comando com as credenciais de domínio que pretende utilizar e, em seguida, testar a conectividade com a partilha de ficheiros nas instalações, obtenda uma listagem de diretório.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Verifique se a listagem de diretórios é devolvida para a partilha de ficheiros no local.

### <a name="prerequisites"></a>Pré-requisitos

Para aceder a uma partilha de ficheiros nas instalações de pacotes em execução em Azure, faça as seguintes coisas:

1. Permitir o acesso através da firewall do Windows.

2. Junte-se ao seu Azure-SSIS IR a uma Rede Virtual Microsoft Azure que esteja ligada à partilha de ficheiros nas instalações.  Para obter mais informações, consulte [Join Azure-SSIS IR para uma Rede Virtual Microsoft Azure](./join-azure-ssis-integration-runtime-virtual-network.md).

3. Utilize o procedimento armazenado do SSISDB `catalog.set_execution_credential` para fornecer credenciais como descrito neste artigo.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Ligue-se a uma partilha de ficheiros na Azure VM

Para aceder a uma partilha de ficheiros na Azure VM a partir de pacotes em execução em Azure, faça as seguintes coisas:

1. Com SSMS ou outra ferramenta, ligue-se à SQL Database/SQL Managed Instance que acolhe o SSISDB. Para obter mais informações, consulte [Connect to SSISDB em Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como base de dados atual, abra uma janela de consulta.

3. Executar o seguinte procedimento armazenado e fornecer as credenciais de domínio apropriadas:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Conecte-se a uma partilha de ficheiros em Ficheiros Azure

Para obter mais informações sobre ficheiros Azure, consulte [os Ficheiros Azure](https://azure.microsoft.com/services/storage/files/).

Para aceder a uma partilha de ficheiros em Ficheiros Azure a partir de pacotes em execução em Azure, faça as seguintes coisas:

1. Com SSMS ou outra ferramenta, ligue-se à SQL Database/SQL Managed Instance que acolhe o SSISDB. Para obter mais informações, consulte [Connect to SSISDB em Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como base de dados atual, abra uma janela de consulta.

3. Executar o seguinte procedimento armazenado e fornecer as credenciais de domínio apropriadas:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Passos seguintes

- Desloquem os vossos pacotes. Para mais informações, consulte [implementar um projeto SSIS para Azure com SSMS.](/sql/integration-services/ssis-quickstart-deploy-ssms)
- Coloquem os seus pacotes. Para mais informações, consulte [os pacotes Run SSIS em Azure com SSMS](/sql/integration-services/ssis-quickstart-run-ssms).
- Agende os seus pacotes. Para mais informações, consulte [os pacotes Schedule SSIS em Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).
