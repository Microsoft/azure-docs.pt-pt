---
title: Importar ou exportar uma Base de Dados Azure SQL sem permitir que os serviços da Azure acedam ao servidor.
description: Importar ou exportar uma Base de Dados Azure SQL sem permitir que os serviços da Azure acedam ao servidor.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 3a02876234d43df2e98a3a4e60453fc3f1f74ef6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724174"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Importar ou exportar uma Base de Dados Azure SQL sem permitir que os serviços da Azure acedam ao servidor
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo mostra-lhe como importar ou exportar uma Base de Dados Azure SQL quando *o Allow Azure Services* está definido para *OFF* no servidor. O fluxo de trabalho utiliza uma máquina virtual Azure para executar a SqlPackage para realizar a operação de importação ou exportação.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Criar a máquina virtual Azure

Crie uma máquina virtual Azure selecionando o botão **Implementar para Azul.**

Este modelo permite-lhe implementar uma simples máquina virtual do Windows utilizando algumas opções diferentes para a versão Windows, utilizando a versão mais recente corrigida. Isto irá implantar um VM tamanho A2 na localização do grupo de recursos e devolver o nome de domínio totalmente qualificado do VM.
<br><br>

[![Imagem mostrando um botão marcado "Implementar para Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

Para obter mais informações, consulte [a implementação muito simples de um VM do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

Os seguintes passos mostram-lhe como ligar-se à sua máquina virtual utilizando uma ligação remota de ambiente de trabalho.

1. Após a conclusão da implementação, vá para o recurso de máquina virtual.

   ![A screenshot mostra uma página de visão geral da máquina virtual com um botão Connect.](./media/database-import-export-azure-services-off/vm.png)  

2. Selecione **Ligar**.

   Um formulário de protocolo de ambiente de trabalho remoto (.ficheiro rdp) aparece com o endereço IP público e o número de porta para a máquina virtual.

   ![Formulário RDP](./media/database-import-export-azure-services-off/rdp.png)  

3. Selecione **Transferir Ficheiro RDP**.

   > [!NOTE]
   > Também pode utilizar o SSH para ligar ao seu VM.

4. Feche o **Formulário de Máquina Virtual Connect.**
5. Para ligar à sua VM, abra o ficheiro RDP transferido.
6. Quando solicitado, selecione **Connect**. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) a partir da Mac App Store.

7. Introduza o nome de utilizador e a palavra-passe especificado ao criar a máquina virtual e, em seguida, escolha **OK**.

8. Poderá receber um aviso de certificado durante o processo de início de sessão. Escolha **Sim** ou **Continue** a proceder com a ligação.

## <a name="install-sqlpackage"></a>Instalar SqlPackage

[Descarregue e instale a versão mais recente do SqlPackage.](/sql/tools/sqlpackage-download)

Para mais informações, consulte [SqlPackage.exe](/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Criar uma regra de firewall para permitir o acesso vm à base de dados

Adicione o endereço IP público da máquina virtual à firewall do servidor.

Os passos seguintes criam uma regra de firewall IP de nível de servidor para o endereço IP público da sua máquina virtual e permite a conectividade a partir da máquina virtual.

1. Selecione **bases de dados SQL** a partir do menu à esquerda e, em seguida, selecione a sua base de dados na página **de bases de dados SQL.** A página geral da sua base de dados abre, mostrando-lhe o nome do servidor totalmente qualificado (como **servername.database.windows.net)** e fornece opções para uma configuração posterior.

2. Copie este nome de servidor totalmente qualificado para utilizar ao ligar-se ao seu servidor e às suas bases de dados.

   ![nome do servidor](./media/database-import-export-azure-services-off/server-name.png)

3. Selecione **Definir firewall** do servidor na barra de ferramentas. A página **de definições de Firewall** para o servidor abre.

   ![regra de firewall IP de nível de servidor](./media/database-import-export-azure-services-off/server-firewall-rule.png)

4. Escolha adicionar o IP do **cliente** na barra de ferramentas para adicionar o endereço IP público da sua máquina virtual a uma nova regra de firewall IP de nível de servidor. Uma regra de firewall IP de nível de servidor pode abrir a porta 1433 para um único endereço IP ou uma gama de endereços IP.

5. Selecione **Guardar**. Uma regra de firewall IP de nível de servidor é criada para o endereço IP público da sua máquina virtual, abrindo a porta 1433 no servidor.

6. Feche a página **de definições de Firewall.**

## <a name="export-a-database-using-sqlpackage"></a>Exportar uma base de dados usando SqlPackage

Para exportar uma Base de Dados Azure SQL utilizando o utilitário da linha de comando [SqlPackage,](/sql/tools/sqlpackage) consulte [os parâmetros e propriedades da Exportação](/sql/tools/sqlpackage#export-parameters-and-properties). Os navios utilitários SqlPackage com as versões mais recentes do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e [sql Server Data Tools,](/sql/ssdt/download-sql-server-data-tools-ssdt)ou pode descarregar a versão mais recente do [SqlPackage](/sql/tools/sqlpackage-download).

Recomendamos a utilização do utilitário SqlPackage para escala e desempenho na maioria dos ambientes de produção. Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Este exemplo mostra como exportar uma base de dados utilizando SqlPackage.exe com Autenticação Universal do Diretório Ativo. Substitua por valores específicos do seu ambiente.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-a-database-using-sqlpackage"></a>Importar uma base de dados usando SqlPackage

Para importar uma base de dados SQL Server utilizando o utilitário de linha de comando [SqlPackage,](/sql/tools/sqlpackage) consulte [parâmetros e propriedades de importação](/sql/tools/sqlpackage#import-parameters-and-properties). A SqlPackage tem o mais recente [SqL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e [as Ferramentas de Dados do Servidor SQL](/sql/ssdt/download-sql-server-data-tools-ssdt). Também pode baixar a versão mais recente do [SqlPackage.](/sql/tools/sqlpackage-download)

Para escala e desempenho, recomendamos a utilização do SqlPackage na maioria dos ambientes de produção em vez de utilizar o portal Azure. Para um blog da SqL Server Customer Advisory Team sobre migração através `BACPAC` de ficheiros, consulte [migrar do SQL Server para a Azure SQL Database utilizando ficheiros BACPAC](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

O seguinte comando SqlPackage importa a base de dados **AdventureWorks2017** do armazenamento local para uma Base de Dados Azure SQL. Cria uma nova base de dados chamada **myMigratedDatabase** com um nível de serviço **Premium** e um Objetivo de Serviço **P6.** Altere estes valores conforme apropriado para o seu ambiente.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Para ligar à base de dados tAzure SQL por detrás de uma firewall corporativa, a firewall deve ter a porta 1433 aberta.

Este exemplo mostra como importar uma base de dados usando SqlPackage com Autenticação Universal do Diretório Ativo.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Considerações de desempenho

As velocidades de exportação variam devido a muitos fatores (por exemplo, forma de dados) pelo que é impossível prever qual a velocidade que deve ser esperada. A SqlPackage pode demorar muito tempo, especialmente para grandes bases de dados.

Para obter o melhor desempenho pode experimentar as seguintes estratégias:

1. Certifique-se de que não há outra carga de trabalho na base de dados. Criar uma cópia antes da exportação pode ser a melhor solução para garantir que não estão a funcionar outras cargas de trabalho.
2. Aumentar o objetivo do nível do serviço de base de dados (SLO) para melhor lidar com a carga de trabalho de exportação (ler principalmente I/O). Se a base de dados está atualmente GP_Gen5_4, talvez um nível Business Critical ajude na leitura da carga de trabalho.
3. Certifique-se de que existem índices agrupados especialmente para grandes tabelas.
4. As máquinas virtuais (VMs) devem estar na mesma região que a base de dados para ajudar a evitar constrangimentos de rede.
5. Os VMs devem ter SSD com tamanho adequado para gerar artefactos temporários antes de carregar para o armazenamento de bolhas.
6. Os VMs devem ter uma configuração de núcleo e memória adequada para a base de dados específica.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Armazenar os importados ou exportados. Arquivo BACPAC

O . O ficheiro BACPAC pode ser armazenado em [Azure Blobs,](../../storage/blobs/storage-blobs-overview.md)ou [Ficheiros Azure](../../storage/files/storage-files-introduction.md).

Para obter o melhor desempenho, utilize ficheiros Azure. A SqlPackage opera com o sistema de ficheiros para que possa aceder diretamente aos Ficheiros Azure.

Para reduzir o custo, utilize a Azure Blobs, que custa menos do que uma parte premium do ficheiro Azure. No entanto, vai exigir que copie o [. Ficheiro BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) entre a bolha e o sistema de ficheiros local antes da operação de importação ou exportação. Como resultado, o processo vai demorar mais tempo.

Para carregar ou descarregar . Ficheiros BACPAC, ver [dados de transferência com armazenamento AzCopy e Blob,](../../storage/common/storage-use-azcopy-v10.md#transfer-data)e [transferir dados com AzCopy e armazenamento de ficheiros](../../storage/common/storage-use-azcopy-files.md).

Dependendo do seu ambiente, poderá necessitar de [configurar firewalls de armazenamento Azure e redes virtuais.](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Próximos passos

- Para aprender a ligar e consultar uma base de dados SQL importada, consulte [Quickstart: Azure SQL Database: Use SQL Server Management Studio para ligar e consultar dados](connect-query-ssms.md).
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Para uma discussão de todo o processo de migração da base de dados do SQL Server, incluindo recomendações de desempenho, consulte [a migração da base de dados do SQL Server para a Base de Dados Azure SQL](migrate-to-database-from-sql-server.md).
- Para aprender a gerir e partilhar as chaves de armazenamento e as assinaturas de acesso partilhadas de forma segura, consulte o [Guia de Segurança do Armazenamento Azure](../../storage/blobs/security-recommendations.md).
