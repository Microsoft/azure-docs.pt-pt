---
title: Fazer o back bases de dados do SQL Server para o Azure
description: Este artigo explica como fazer o back up SQL Server para Azure. O artigo também explica a recuperação do SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 510d9637031928e31abaa5f82a5bf58c6ef44719
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316851"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Sobre a Cópia de Segurança do SQL Server em VMs do Azure

[O Azure Backup](backup-overview.md) oferece uma solução especializada em stream para apoiar o SQL Server em funcionamento em VMs Azure. Esta solução alinha-se com os benefícios da Azure Backup de backup de infraestruturas zero, retenção a longo prazo e gestão central. Além disso, fornece as seguintes vantagens especificamente para o SQL Server:

1. Backups de trabalho conscientes que suportam todos os tipos de backup - completo, diferencial e log
2. RPO de 15 minutos (objetivo ponto de recuperação) com backups frequentes de registo
3. Recuperação pontual até um segundo
4. Backup e restauro do nível de base de dados individual

Para ver o backup e restaurar os cenários que apoiamos hoje, consulte a [matriz de suporte.](sql-support-matrix.md#scenario-support)

## <a name="backup-process"></a>Processo de cópia de segurança

Esta solução aproveita as APIs nativas do SQL para obter cópias de segurança das suas bases de dados SQL.

* Assim que especificar o SQL Server VM que pretende proteger e consultar as bases de dados, o serviço Azure Backup instalará uma extensão de backup de carga de trabalho na extensão do VM pela extensão do `AzureBackupWindowsWorkload` nome.
* Esta extensão é constituída por um coordenador e um plugin SQL. Enquanto o coordenador é responsável por desencadear fluxos de trabalho para várias operações como configurar backup, backup e restauro, o plugin é responsável pelo fluxo real de dados.
* Para ser capaz de descobrir bases de dados neste VM, o Azure Backup cria a conta `NT SERVICE\AzureWLBackupPluginSvc` . Esta conta é usada para cópia de segurança e restauro e requer permissões de sysadmin SQL. A `NT SERVICE\AzureWLBackupPluginSvc` conta é uma Conta de Serviço [Virtual,](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)pelo que não requer qualquer gestão de senha. O Azure Backup utiliza a `NT AUTHORITY\SYSTEM` conta para a descoberta/inquérito da base de dados, pelo que esta conta tem de ser um login público no SQL. Se não criou a VM do SQL Server a partir do Azure Marketplace, pode receber um erro **UserErrorSQLNoSysadminMembership**. Se ocorrer essa situação, [siga estas instruções](#set-vm-permissions).
* Assim que acionar a proteção de configuração nas bases de dados selecionadas, o serviço de cópia de segurança configura o coordenador com os horários de backup e outros detalhes da política, que a extensão cache localmente no VM.
* Na hora programada, o coordenador comunica com o plugin e começa a transmitir os dados de backup do servidor SQL utilizando O VDI.  
* O plugin envia os dados diretamente para o cofre dos Serviços de Recuperação, eliminando assim a necessidade de um local de paragem. Os dados são encriptados e armazenados pelo serviço Azure Backup em contas de armazenamento.
* Quando a transferência de dados estiver concluída, o coordenador confirma o compromisso com o serviço de cópia de segurança.

  ![Arquitetura de backup SQL](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Antes de começar

Antes de começar, verifique os seguintes requisitos:

1. Certifique-se de que tem uma instância SQL Server a decorrer em Azure. Pode [criar rapidamente uma instância SQL Server](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) no mercado.
2. Reveja as [considerações](sql-support-matrix.md#feature-considerations-and-limitations) de recurso e [o suporte ao cenário](sql-support-matrix.md#scenario-support).
3. [Reveja questões comuns](faq-backup-sql-server.md) sobre este cenário.

## <a name="set-vm-permissions"></a>Definir permissões da VM

  Quando executa a descoberta num servidor SQL, o Azure Backup faz o seguinte:

* Adiciona a extensão AzureBackupWindowsWorkload.
* Cria uma conta NT SERVICE\AzureWLBackupPluginSvc para descobrir bases de dados na máquina virtual. Esta conta é usada para uma cópia de segurança e restauro e requer permissões de sysadmin SQL.
* Descobre bases de dados que estão a funcionar num VM, a Azure Backup utiliza a conta NT AUTHORITY\SYSTEM. Esta conta deve ser uma súmia pública no SQL.

Se não criou o SQL Server VM no Azure Marketplace ou se estiver no SQL 2008 ou 2008 R2, poderá receber um erro **de nomeação userErrorSQLNoSysadminMembership.**

Para obter permissões no caso do **SQL 2008** e **2008 R2** em execução no Windows 2008 R2, consulte [aqui](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

Para todas as outras versões, corrija permissões com os seguintes passos:

  1. Utilize uma conta com permissões sysadmin do SQL Server para iniciar sinscrição no SQL Server Management Studio (SSMS). A menos que precise de permissões especiais, a autenticação do Windows deve funcionar.
  2. No SQL Server, abra a pasta **Segurança/Logins.**

      ![Abra a pasta Segurança/Logins para ver contas](./media/backup-azure-sql-database/security-login-list.png)

  3. Clique com o botão direito na pasta **'Logins'** e selecione **Novo Início de Sessão**. Em **Login - Novo,** selecione **Search**.

      ![No Início de Sessão - Nova caixa de diálogo, selecione Pesquisar](./media/backup-azure-sql-database/new-login-search.png)

  4. A conta de serviço virtual windows **NT SERVICE\AzureWLBackupPluginSvc** foi criada durante a fase de registo de máquinas virtuais e fase de descoberta do SQL. Introduza o nome da conta como mostrado no **Insira o nome do objeto para selecionar**. Selecione **Verificar Nomes** para resolver o nome. Selecione **OK**.

      ![Selecione Verificar Nomes para resolver o nome de serviço desconhecido](./media/backup-azure-sql-database/check-name.png)

  5. Em **Funções de Servidor,** certifique-se de que a função **sysadmin** está selecionada. Selecione **OK**. As permissões necessárias devem agora existir.

      ![Certifique-se de que a função do servidor sysadmin está selecionada](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Agora associe a base de dados com o cofre dos Serviços de Recuperação. No portal Azure, na lista de **Servidores Protegidos,** clique com o botão direito no servidor que está em estado de erro > **Redescobrir DBs**.

      ![Verifique se o servidor tem permissões apropriadas](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Verifique os progressos na área de **Notificações.** Quando as bases de dados selecionadas são encontradas, aparece uma mensagem de sucesso.

      ![Mensagem de implementação com êxito](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Se o seu SQL Server tiver várias instâncias de SQL Server instaladas, então deve adicionar permissão sysadmin para **conta NT Service\AzureWLBackupPluginSvc** a todas as instâncias SQL.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Dê permissões de sysadmin SQL para SQL 2008 e SQL 2008 R2

Adicione **NT AUTHORITY\SYSTEM** e **NT Service\AzureWLBackupPluginSvc** para a sql Server Instance:

1. Vá à ocorrência do servidor SQL no explorador de objetos.
2. Navegue para Segurança -> Logins
3. Clique com o botão direito nos logins e selecione *Novo Login...*

    ![Novo Login utilizando SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Aceda ao separador Geral e insira **NT AUTHORITY\SYSTEM** como o Nome de Login.

    ![Nome de login para SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Vá a *Server Roles* e escolha funções *públicas* e *sysadmin.*

    ![Escolher papéis em SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Ir para *o Status*. *Conceda* a Permissão para ligar ao motor de base de dados e ao Login conforme *Ativado*.

    ![Concessão de permissões em SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Selecione OK.
8. Repita a mesma sequência de passos (1-7 acima) para adicionar o login NT Service\AzureWLBackupPluginSvc à instância do SQL Server. Se o login já existir, certifique-se de que tem a função de servidor sysadmin e em Status tem a Permissão de Ligar ao motor de base de dados e ao Login como Ativado.
9. Após a concessão de permissão, **redescubra os DBs** no portal: Carga de Trabalho da Infraestrutura de Backup do Cofre **->** **->** em Azure VM:

    ![Redescobrir DBs no portal Azure](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Em alternativa, pode automatizar dando as permissões executando os seguintes comandos PowerShell no modo de administração. O nome da instância é definido para MSSQLSERVER por padrão. Altere o argumento do nome da instância no script, se necessário:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Passos seguintes

* [Saiba como](backup-sql-server-database-azure-vms.md) fazer o backup das bases de dados do SQL Server.
* [Saiba como](restore-sql-database-azure-vm.md) restaurar as bases de dados do SQL Server.
* [Saiba mais sobre](manage-monitor-sql-database-backup.md) a gestão das bases de dados do SQL Server.
