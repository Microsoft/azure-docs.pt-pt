---
title: Importar ou exportar uma base de dados SQL
description: Importar ou exportar uma base de dados Azure SQL sem permitir que os serviços do Azure acedam ao servidor.
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
ms.openlocfilehash: 61a85b2554bbd69541b3081f72525d2b7deed625
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80529239"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Importar ou exportar uma base de dados Azure SQL sem permitir que os serviços do Azure acedam ao servidor

Este artigo mostra-lhe como importar ou exportar uma base de dados Azure SQL quando a *Allow Azure Services* está definida para *off* no servidor Azure SQL. O fluxo de trabalho utiliza uma máquina virtual Azure para executar o SqlPackage para realizar a operação de importação ou exportação.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Criar a máquina virtual Azure

Crie uma máquina virtual Azure selecionando o botão **Deploy para Azure.**

Este modelo permite-lhe implementar uma máquina virtual simples do Windows utilizando algumas opções diferentes para a versão Windows, utilizando a versão mais recente corrigida. Isto irá implantar um VM de tamanho A2 na localização do grupo de recursos e devolver o nome de domínio totalmente qualificado do VM.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Para mais informações, consulte a [implementação muito simples de um VM windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).


## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

Os seguintes passos mostram-lhe como se conectar à sua máquina virtual utilizando uma ligação remota de ambiente de trabalho.

1. Após a conclusão da implementação, vá para o recurso de máquina virtual.

    ![VM](./media/import-export-from-vm/vm.png)  

2. Selecione **Ligar**.

   Um formulário de protocolo de ambiente de trabalho remoto (ficheiro.rdp) aparece com o endereço IP público e o número de porta para a máquina virtual.

   ![Formulário RDP](./media/import-export-from-vm/rdp.png)  

3. Selecione **Download RDP File**.

   > [!NOTE]
   > Também pode utilizar o SSH para se ligar ao seu VM.

4. Feche o **Formulário de Ligação à forma** da máquina virtual.
5. Para ligar à sua VM, abra o ficheiro RDP transferido.
6. Quando solicitado, selecione **Connect**. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) a partir da Mac App Store.

7. Introduza o nome de utilizador e a palavra-passe especificado ao criar a máquina virtual e, em seguida, escolha **OK**.

8. Poderá receber um aviso de certificado durante o processo de início de sessão. Escolha **Sim** ou **Continue** a prosseguir com a ligação.



## <a name="install-sqlpackage"></a>Instalar O Pacote Sql

[Descarregue e instale a versão mais recente do SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Para obter informações adicionais, consulte [SqlPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Criar uma regra de firewall para permitir o acesso do VM à base de dados

Adicione o endereço IP público da máquina virtual à firewall do servidor sQL Database.

Os seguintes passos criam uma regra de firewall IP ao nível do servidor para o endereço IP público da sua máquina virtual e permite a conectividade a partir da máquina virtual.

1. Selecione bases de **dados SQL** a partir do menu à esquerda e, em seguida, selecione a sua base de dados na página de bases de **dados SQL.** A página de visão geral da sua base de dados abre-se, mostrando-lhe o nome do servidor totalmente qualificado (como **servername.database.windows.net)** e fornece opções para uma configuração posterior.

2. Copie este nome de servidor totalmente qualificado para utilizar quando estiver ligado ao seu servidor e às suas bases de dados.

   ![nome do servidor](./media/sql-database-get-started-portal/server-name.png)

3. Selecione **Definir a firewall** do servidor na barra de ferramentas. A página de **definições de Firewall** para o servidor de base de dados abre.

   ![regra de firewall IP de nível servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Escolha **adicionar IP** do cliente na barra de ferramentas para adicionar o endereço IP público da sua máquina virtual a uma nova regra de firewall IP de nível de servidor. Uma regra de firewall IP de nível de servidor pode abrir a porta 1433 para um único endereço IP ou uma gama de endereços IP.

5. Selecione **Guardar**. Uma regra de firewall IP de nível de servidor é criada para a porta de abertura de endereço IP da sua máquina virtual 1433 no servidor base de dados SQL.

6. Feche a página de definições da **Firewall.**



## <a name="export-a-database-using-sqlpackage"></a>Exportar uma base de dados usando o SqlPackage

Para exportar uma base de dados SQL utilizando o utilitário de linha de comando [SqlPackage,](https://docs.microsoft.com/sql/tools/sqlpackage) consulte [os parâmetros e propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)da exportação . O utilitário SqlPackage envia com as versões mais recentes do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e das Ferramentas de Dados do [Servidor SQL,](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)ou pode descarregar a versão mais recente do [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Recomendamos a utilização do utilitário SqlPackage para escala e desempenho na maioria dos ambientes de produção. Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Este exemplo mostra como exportar uma base de dados usando SqlPackage.exe com Autenticação Universal de Diretório Ativo. Substitua-se por valores específicos do seu ambiente.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Importar uma base de dados usando o SqlPackage

Para importar uma base de dados do SQL Server utilizando o utilitário da linha de comando [SqlPackage,](https://docs.microsoft.com/sql/tools/sqlpackage) consulte [os parâmetros e propriedades de importação](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). O SqlPackage tem o mais recente Estúdio de Gestão de [Servidores SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e ferramentas de [dados do Servidor SQL.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) Também pode descarregar a versão mais recente do [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Para escala e desempenho, recomendamos a utilização do SqlPackage na maioria dos ambientes de produção em vez de utilizar o portal Azure. Para um blog da Equipa de Aconselhamento ao `BACPAC` Cliente do SQL Server sobre a migração usando ficheiros, consulte [a migração do SQL Server para a Base de Dados SQL azure utilizando ficheiros BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

O seguinte comando SqlPackage importa a base de dados **AdventureWorks2017** do armazenamento local para um servidor de base de dados Azure SQL. Cria uma nova base de dados chamada **myMigratedDatabase** com um nível de serviço **Premium** e um Objetivo de Serviço **P6.** Altere estes valores conforme apropriado para o seu ambiente.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Para se ligar a um servidor de base de dados SQL que gere uma única base de dados por detrás de uma firewall corporativa, a firewall deve ter a porta 1433 aberta. Para se ligar a uma instância gerida, deve ter uma [ligação ponto-a-local](sql-database-managed-instance-configure-p2s.md) ou uma ligação de rota expressa.

Este exemplo mostra como importar uma base de dados usando o SqlPackage com autenticação universal de diretório ativo.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Considerações de desempenho

As velocidades de exportação variam devido a muitos fatores (por exemplo, forma de dados) pelo que é impossível prever qual a velocidade que deve ser esperada. O SqlPackage pode demorar bastante tempo, nomeadamente para grandes bases de dados.

Para obter o melhor desempenho pode experimentar as seguintes estratégias:

1. Certifique-se de que nenhuma outra carga de trabalho está a funcionar na base de dados. Criar uma cópia antes da exportação pode ser a melhor solução para garantir que nenhuma outra carga de trabalho esteja em funcionamento.
2. Aumentar o objetivo de nível de serviço de base de dados (SLO) para melhor lidar com a carga de trabalho de exportação (ler principalmente Em/O). Se a base de dados for atualmente GP_Gen5_4, talvez um nível Business Critical ajude a ler a carga de trabalho.
3. Certifique-se de que existem índices agrupados particularmente para grandes tabelas. 
4. As máquinas virtuais (VMs) devem estar na mesma região que a base de dados para ajudar a evitar constrangimentos de rede.
5. Os VMs devem ter SSD com tamanho adequado para gerar artefactos temporários antes de enviar para o armazenamento de bolhas.
6. Os VMs devem ter uma configuração adequada do núcleo e da memória para a base de dados específica.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Armazenar os importados ou exportados. Ficheiro BACPAC

O. O ficheiro BACPAC pode ser armazenado em [Blobs Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview), ou [Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction). 

Para obter o melhor desempenho, utilize ficheiros Azure. O SqlPackage opera com o sistema de ficheiros para que possa aceder diretamente aos Ficheiros Azure.

Para reduzir os custos, utilize o Azure Blobs, que custa menos do que uma quota de ficheiros Azure premium. No entanto, será necessário copiar o [. Ficheiro BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) entre a bolha e o sistema de ficheiros local antes da operação de importação ou exportação. Como resultado, o processo demorará mais tempo.

Para fazer upload ou download. Os ficheiros BACPAC, consulte [os dados de transferência com armazenamento AzCopy e Blob,](../storage/common/storage-use-azcopy-blobs.md)e [transfira dados com AzCopy e armazenamento de ficheiros.](../storage/common/storage-use-azcopy-files.md)

Dependendo do seu ambiente, poderá ser necessário [configurar firewalls de armazenamento Azure e redes virtuais.](../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Passos seguintes

- Para aprender a ligar e consultar uma base de dados SQL importada, consulte [Quickstart: Azure SQL Database: Use SQL Server Management Studio para ligar e consultar dados](sql-database-connect-query-ssms.md).
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Para uma discussão de todo o processo de migração da base de dados SQL Server, incluindo recomendações de desempenho, consulte a migração da base de [dados Do Servidor SQL para a Base de Dados Azure SQL](sql-database-single-database-migrate.md).
- Para saber gerir e partilhar chaves de armazenamento e assinaturas de acesso partilhadas de forma segura, consulte o Guia de Segurança do [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
