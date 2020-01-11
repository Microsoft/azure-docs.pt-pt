---
title: Importar ou exportar um banco de dados SQL
description: Importe ou exporte um banco de dados SQL do Azure sem permitir que os serviços do Azure acessem o servidor.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 9f694f3f0ec740d0a4e8dc4e6bf8845c408802c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897848"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Importar ou exportar um banco de dados SQL do Azure sem permitir que os serviços do Azure acessem o servidor

Este artigo mostra como importar ou exportar um banco de dados SQL do Azure quando *permitir que os serviços do Azure* sejam definidos como *off* no SQL Server do Azure. O fluxo de trabalho usa uma máquina virtual do Azure para executar o SqlPackage para executar a operação de importação ou exportação.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Criar a máquina virtual do Azure

Crie uma máquina virtual do Azure selecionando o botão **implantar no Azure** .

Este modelo permite que você implante uma máquina virtual simples do Windows usando algumas opções diferentes para a versão do Windows, usando a versão mais recente com patches. Isso implantará uma VM de tamanho a2 no local do grupo de recursos e retornará o nome de domínio totalmente qualificado da VM.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Para obter mais informações, consulte [implantação muito simples de uma VM do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).


## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

As etapas a seguir mostram como se conectar à sua máquina virtual usando uma conexão de área de trabalho remota.

1. Após a conclusão da implementação, vá para o recurso de máquina virtual.

    ![VM](./media/import-export-from-vm/vm.png)  

2. Selecione **Ligar**.

   É apresentado um formulário de ficheiro (ficheiro. rdp) do protocolo de ambiente de trabalho remoto com o IP público endereço e porta número para a máquina virtual.

   ![Formulário RDP](./media/import-export-from-vm/rdp.png)  

3. Selecione **transferir ficheiro RDP**.

   > [!NOTE]
   > Também pode utilizar o SSH para ligar à sua VM.

4. Fechar o **ligar à máquina virtual** formulário.
5. Para ligar à sua VM, abra o ficheiro RDP transferido.
6. Quando lhe for pedido, selecione **Connect**. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) a partir da Mac App Store.

7. Introduza o nome de utilizador e palavra-passe que especificou ao criar a máquina virtual, em seguida, escolha **OK**.

8. Poderá receber um aviso de certificado durante o processo de início de sessão. Escolher **Sim** ou **continuar** para prosseguir com a ligação.



## <a name="install-sqlpackage"></a>Instalar SqlPackage

[Baixe e instale a versão mais recente do SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Para obter informações adicionais, consulte [SqlPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Criar uma regra de firewall para permitir que a VM acesse o banco de dados

Adicione o endereço IP público da máquina virtual ao firewall do servidor do banco de dados SQL.

As etapas a seguir criam uma regra de firewall de IP no nível de servidor para o endereço IP público de sua máquina virtual e habilita a conectividade da máquina virtual.

1. Selecione **bancos** de dados SQL no menu à esquerda e, em seguida, selecione o seu banco de dados na página **SQL databases** . A página Visão geral do seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **servername.Database.Windows.net**) e fornece opções para configuração adicional.

2. Copie esse nome de servidor totalmente qualificado para usar ao conectar-se ao servidor e a seus bancos de dados.

   ![nome do servidor](./media/sql-database-get-started-portal/server-name.png)

3. Selecione **definir Firewall do servidor** na barra de ferramentas. A página **configurações de firewall** do servidor de banco de dados é aberta.

   ![regra de firewall de IP de nível de servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Escolha **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP público da máquina virtual a uma nova regra de firewall de IP no nível de servidor. Uma regra de firewall de IP no nível de servidor pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

5. Selecione **Guardar**. Uma regra de firewall de IP no nível de servidor é criada para o endereço IP público da sua máquina virtual abrindo a porta 1433 no servidor do banco de dados SQL.

6. Feche a página **configurações de firewall** .



## <a name="export-a-database-using-sqlpackage"></a>Exportar um banco de dados usando SqlPackage

Para exportar um banco de dados SQL usando o utilitário de linha de comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) , consulte [Exportar parâmetros e propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). O utilitário SqlPackage é fornecido com as versões mais recentes do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e do [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt), ou você pode baixar a versão mais recente do [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Recomendamos o uso do utilitário SqlPackage para escala e desempenho na maioria dos ambientes de produção. Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Este exemplo mostra como exportar um banco de dados usando SqlPackage. exe com Active Directory autenticação universal. Substituir por valores específicos do seu ambiente.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Importar um banco de dados usando SqlPackage

Para importar um banco de dados SQL Server usando o utilitário de linha de comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) , consulte [importar parâmetros e propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). O SqlPackage tem as últimas [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). Você também pode baixar a versão mais recente do [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Para escala e desempenho, é recomendável usar SqlPackage na maioria dos ambientes de produção em vez de usar o portal do Azure. Para obter um blog SQL Server equipe de consultoria ao cliente sobre a migração usando arquivos `BACPAC`, consulte [migrando do SQL Server para o banco de dados SQL do Azure usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

O comando SqlPackage a seguir importa o banco de dados **AdventureWorks2017** do armazenamento local para um servidor de banco de dados SQL do Azure. Ele cria uma nova base de dados chamado **myMigratedDatabase** com um **Premium** escalão de serviço e um **P6** objetivo de serviço. Altere estes valores conforme adequado para o seu ambiente.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Para se conectar a um servidor de banco de dados SQL que gerencia um banco de dados individual por trás de um firewall corporativo, o firewall deve ter a porta 1433 aberta. Para se conectar a uma instância gerenciada, você deve ter uma [conexão ponto a site](sql-database-managed-instance-configure-p2s.md) ou uma conexão de rota expressa.

Este exemplo mostra como importar uma base de dados com SqlPackage com o Active Directory Universal Authentication.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Considerações de desempenho

As velocidades de exportação variam devido a muitos fatores (por exemplo, forma de dados), portanto, é impossível prever qual velocidade deve ser esperada. O SqlPackage pode levar um tempo considerável, especialmente para bancos de dados grandes.

Para obter o melhor desempenho, você pode experimentar as seguintes estratégias:

1. Verifique se nenhuma outra carga de trabalho está em execução no banco de dados. Criar uma cópia antes da exportação pode ser a melhor solução para garantir que nenhuma outra carga de trabalho esteja em execução.
2. Aumente o objetivo de nível de serviço do banco de dados (SLO) para lidar melhor com a carga de trabalho de exportação (principalmente e/s de leitura). Se o banco de dados estiver atualmente GP_Gen5_4, talvez uma camada de Comercialmente Crítico ajudaria com a carga de trabalho de leitura.
3. Verifique se há índices clusterizados particularmente para tabelas grandes. 
4. As máquinas virtuais (VMs) devem estar na mesma região que o banco de dados para ajudar a evitar restrições de rede.
5. As VMs devem ter SSD com tamanho adequado para gerar artefatos temporários antes de carregar para o armazenamento de BLOBs.
6. As VMs devem ter a configuração de núcleo e memória adequada para o banco de dados específico.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Armazene o importado ou exportado. Arquivo BACPAC

Dos. O arquivo BACPAC pode ser armazenado em [BLOBs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)ou [arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction). 

Para obter o melhor desempenho, use os arquivos do Azure. O SqlPackage opera com o sistema de arquivos para que ele possa acessar os arquivos do Azure diretamente.

Para reduzir o custo, use BLOBs do Azure, que custam menos do que um compartilhamento de arquivos premium do Azure. No entanto, será necessário copiar o [. Arquivo BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) entre o blob e o sistema de arquivos local antes da operação de importação ou exportação. Como resultado, o processo levará mais tempo.

Para carregar ou baixar. Arquivos BACPAC, consulte [transferir dados com o armazenamento de BLOBs e AzCopy](../storage/common/storage-use-azcopy-blobs.md)e [transferir dados com o AzCopy e o armazenamento de arquivos](../storage/common/storage-use-azcopy-files.md).

Dependendo do seu ambiente, talvez seja necessário configurar as [redes virtuais e os firewalls de armazenamento do Azure](../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber como ligar e consultar uma base de dados importados do SQL, veja [início rápido: Azure SQL Database: Utilize o SQL Server Management Studio para ligar e consultar dados](sql-database-connect-query-ssms.md).
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Para uma discussão sobre o inteiro do SQL Server da base de dados do processo de migração, incluindo recomendações de desempenho, consulte [migração de base de dados do SQL Server para a base de dados do Azure SQL](sql-database-single-database-migrate.md).
- Para saber como gerir e partilhar as chaves de armazenamento e acesso partilhado assinaturas de forma segura, consulte [guia de segurança de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
