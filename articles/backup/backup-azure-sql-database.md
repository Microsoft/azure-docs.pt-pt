---
title: Back up SQL Server bases de dados para Azure
description: Este artigo explica como apoiar o SQL Server para o Azure. O artigo também explica a recuperação do SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 7305a75852deac466028e6278fca76626d8c1820
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297497"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Sobre a Cópia de Segurança do SQL Server em VMs do Azure

[O Azure Backup](backup-overview.md) oferece uma solução especializada e baseada em fluxo para apoiar o SQL Server em execução em VMs Azure. Esta solução alinha-se com os benefícios da Azure Backup de backup de infraestruturas zero, retenção a longo prazo e gestão central. Além disso, fornece as seguintes vantagens especificamente para o Servidor SQL:

1. Backups conscientes da carga de trabalho que suportam todos os tipos de backup - completo, diferencial e log
2. RPO de 15 min (objetivo do ponto de recuperação) com backups de registo frequentes
3. Recuperação pontual até um segundo
4. Backup e restauro de nível de base de dados individuais

Para ver os cenários de backup e restauro que apoiamos hoje, consulte a matriz de [suporte.](backup-azure-sql-database.md#scenario-support)

## <a name="backup-process"></a>Processo de backup

Esta solução aproveita as APIs nativas do SQL para retirar cópias de segurança das suas bases de dados SQL.

* Assim que especificar o VM do Servidor SQL que pretende proteger e consultar as bases de dados do mesmo, o serviço de backup Azure instalará uma extensão de backup de carga de trabalho no VM pelo nome `AzureBackupWindowsWorkload` extensão.
* Esta extensão é constituída por um coordenador e um plugin SQL. Embora o coordenador seja responsável por desencadear fluxos de trabalho para várias operações como configurar cópias de segurança, cópia de segurança e restauro, o plugin é responsável pelo fluxo real de dados.
* Para poder descobrir bases de dados neste VM, o Azure Backup cria a conta `NT SERVICE\AzureWLBackupPluginSvc`. Esta conta é usada para cópia de segurança e restauro e requer permissões de sysadmina SQL. A conta `NT SERVICE\AzureWLBackupPluginSvc` é uma Conta de [Serviço Virtual,](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)pelo que não requer qualquer gestão de senha. A Azure Backup aproveita a `NT AUTHORITY\SYSTEM` conta para a descoberta/investigação da base de dados, pelo que esta conta tem de ser um login público no SQL. Se não criou o VM do Servidor SQL a partir do Mercado Azure, poderá receber um erro **UserErrorSQLNoSysadminMembership**. Se isto ocorrer [siga estas instruções](#set-vm-permissions).
* Uma vez acionado a proteção configurada nas bases de dados selecionadas, o serviço de backup configura o coordenador com os horários de backup e outros detalhes da política, que a extensão caches localmente no VM.
* Na hora programada, o coordenador comunica com o plugin e começa a transmitir os dados de backup do servidor SQL utilizando o VDI.  
* O plugin envia os dados diretamente para o cofre dos serviços de recuperação, eliminando assim a necessidade de uma localização de encenação. Os dados são encriptados e armazenados pelo serviço de backup Azure em contas de armazenamento.
* Quando a transferência de dados estiver concluída, o coordenador confirma o compromisso com o serviço de backup.

  ![Arquitetura de backup SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Antes de começar

Antes de começar, verifique o seguinte:

1. Certifique-se de que tem uma instância sQL Server em funcionamento em Azure. Pode [criar rapidamente uma instância de SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) no mercado.
2. Reveja a [consideração](#feature-consideration-and-limitations) da funcionalidade e o [suporte ao cenário.](#scenario-support)
3. [Reveja as questões comuns](faq-backup-sql-server.md) sobre este cenário.

## <a name="scenario-support"></a>Suporte ao cenário

**Suporte** | **Detalhes**
--- | ---
**Implementações apoiadas** | Os VMs SQL Marketplace Azure e os VMs não-Marketplace (SQL Server instalados manualmente) são suportados.
**Geos apoiados** | Austrália Sudeste (ASE), Austrália Oriental (AE), Austrália Central (AC), Austrália Central 2 (AC) <br> Sul do Brasil (BRS)<br> Canadá Central (CNC), Canadá Leste (CE)<br> Sudeste Asiático (SEA), Leste asiático (EA) <br> LESTE DOS EUA (EUS), Leste dos EUA 2 (EUS2), West Central US (WCUS), West US (WUS); Oeste DOS EUA 2 (WUS 2) Centro Norte DOS EUA (NCUS) Centro DOS EUA (CUS) Centro Sul DOS EUA (SCUS) <br> Índia Central (INC), Índia Sul (INS), Índia Oeste <br> Japão Leste (JPE), Japão Oeste (JPW) <br> Coreia Central (KRC), Coreia do Sul (KRS) <br> Norte da Europa (NE), Europa Ocidental <br> Reino Unido Sul (Reino Unido), Reino Unido Oeste (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD Central, US DoD East <br> Alemanha Norte, Alemanha West Central <br> Suíça Norte, Suíça Oeste <br> França Central <br> China Leste, China Leste 2, China Norte, China Norte 2
**Sistemas operativos suportados** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux não é apoiado atualmente.
**Versões SQL Server suportadas** | SQL Server 2019, SQL Server 2017 conforme detalhado na página de ciclo de vida do [produto Search](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 e SPs conforme detalhado na página de ciclo de vida do produto [Search](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.
**Versões suportadas .NET** | .QUADRO LÍQUIDO 4.5.2 ou posteriormente instalado no VM

## <a name="feature-consideration-and-limitations"></a>Consideração e limitações de recurso

* A cópia de segurança do SQL Server pode ser configurada no portal Azure ou **powerShell**. Não apoiamos o CLI.
* A solução é suportada em ambos os tipos de [implementações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) - VMs do Gestor de Recursos Azure e VMs clássicos.
* VM executando SQL Server requer conectividade de internet para aceder a endereços IP públicos Azure.
* A Instância de Falha do Servidor SQL **(FCI)** não é suportada.
* As operações de back up e restauro para bases de dados de espelhos e fotografias de bases de dados não são suportadas.
* A utilização de mais de uma solução de backup para fazer backup a sua instância autónoma do SQL Server ou o Grupo SQL Always no grupo de disponibilidade pode levar a falhas de backup; abster-se de fazê-lo.
* Apoiar dois nós de um grupo de disponibilidade individualmente com soluções iguais ou diferentes, também pode levar a falhas de backup.
* O Azure Backup suporta apenas tipos de cópia completos e completos para bases de dados **apenas** de leitura
* Bases de dados com um grande número de ficheiros não podem ser protegidas. O número máximo de ficheiros suportados é **~1000**.  
* Pode fazer o back até **~2000** sQL Server bases de dados num cofre. Pode criar vários cofres caso tenha um maior número de bases de dados.
* Pode configurar backup para até **50** bases de dados de uma só vez; esta restrição ajuda a otimizar as cargas de backup.
* Apoiamos bases de dados até **2 TB** em tamanho; para tamanhos maiores do que os problemas de desempenho podem surgir.
* Para termos uma ideia de quantas bases de dados podem ser protegidas por servidor, precisamos de considerar fatores como largura de banda, tamanho VM, frequência de backup, tamanho da base de dados, etc. [Descarregue](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) o planejador de recursos que dê o número aproximado de bases de dados que pode ter por servidor com base nos recursos VM e na política de backup.
* Em caso de grupos de disponibilidade, os backups são retirados dos diferentes nós com base em alguns fatores. O comportamento de backup para um grupo de disponibilidade é resumido abaixo.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Back up comportamento em caso de Sempre em grupos de disponibilidade

Recomenda-se que a cópia de segurança esteja configurada apenas num nó de uma AG. A cópia de segurança deve ser sempre configurada na mesma região que o nó principal. Por outras palavras, precisa sempre do nó principal para estar presente na região em que está a configurar backup. Se todos os nós da AG estiverem na mesma região em que o backup está configurado, não há nenhuma preocupação.

#### <a name="for-cross-region-ag"></a>Para AG transversal

* Independentemente da preferência de backup, os backups não vão acontecer a partir dos nós que não estão na mesma região onde o backup está configurado. Isto porque os backups transfronteiriços não são apoiados. Se tiver apenas dois nós e o nó secundário estiver na outra região; neste caso, os backups continuarão a acontecer a partir do nó primário (a menos que a sua preferência de reserva seja "apenas secundária").
* Se uma falha acontecer a uma região diferente daquela em que o backup está configurado, os backups falhariam nos nós da região falhada.

Dependendo da preferência de reserva e dos tipos de backup (completo/diferencial/log/copy-only full), as cópias de segurança são retiradas de um nó particular (primário/secundário).

* **Preferência de cópia de segurança: Primária**

**Tipo de cópia de segurança** | **Node**
    --- | ---
    cheio | Principal
    Diferencial | Principal
    Registar |  Principal
    Cópia Apenas Cheia |  Principal

* **Preferência de backup: Secundário**

**Tipo de cópia de segurança** | **Node**
--- | ---
cheio | Principal
Diferencial | Principal
Registar |  Secundário
Cópia Apenas Cheia |  Secundário

* **Preferência de backup: Secundário**

**Tipo de cópia de segurança** | **Node**
--- | ---
cheio | Principal
Diferencial | Principal
Registar |  Secundário
Cópia Apenas Cheia |  Secundário

* **Sem preferência de backup**

**Tipo de cópia de segurança** | **Node**
--- | ---
cheio | Principal
Diferencial | Principal
Registar |  Secundário
Cópia Apenas Cheia |  Secundário

## <a name="set-vm-permissions"></a>Definir permissões VM

  Quando executa a descoberta num Servidor SQL, o Azure Backup faz o seguinte:

* Adiciona a extensão AzureBackupWindowsWorkload.
* Cria uma conta NT SERVICE\AzureWLBackupPluginSvc para descobrir bases de dados na máquina virtual. Esta conta é usada para uma cópia de segurança e restauro e requer permissões de sysadmina SQL.
* Descobre bases de dados que estão em execução num VM, o Azure Backup utiliza a conta NT AUTHORITY\SYSTEM. Esta conta deve ser um registo público na SQL.

Se não criou o VM do Servidor SQL no Mercado Azure ou se estiver no SQL 2008 e 2008 R2, poderá receber um erro de **UserErrorSQLNoSysadminMembership.**

Para dar permissões no caso de **SQL 2008** e **2008 R2** em execução no Windows 2008 R2, consulte [aqui](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

Para todas as outras versões, corrija permissões com os seguintes passos:

  1. Utilize uma conta com permissões de sisadmina do SQL Server para iniciar sessão no SQL Server Management Studio (SSMS). A menos que precise de permissões especiais, a autenticação do Windows deve funcionar.
  2. No Servidor SQL, abra a pasta **Security/Logins.**

      ![Abra a pasta Segurança/Logins para ver contas](./media/backup-azure-sql-database/security-login-list.png)

  3. Clique na pasta **Logins** e selecione **New Login**. Em **Login - Novo,** selecione **Procurar**.

      ![No Login - Nova caixa de diálogo, selecione Search](./media/backup-azure-sql-database/new-login-search.png)

  4. A conta de serviço virtual do Windows **NT SERVICE\AzureWLBackupPluginSvc** foi criada durante o registo de máquinas virtuais e a fase de descoberta do SQL. Introduza o nome da conta como mostrado no **nome do objeto para selecionar**. Selecione **Verificar Nomes** para resolver o nome. Clique em **OK**.

      ![Selecione Ver Nomes para resolver o nome de serviço desconhecido](./media/backup-azure-sql-database/check-name.png)

  5. Nas **funções do servidor,** certifique-se de que a função **de sisadmina** é selecionada. Clique em **OK**. As permissões necessárias devem agora existir.

      ![Certifique-se de que a função do servidor de sysadmina é selecionada](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Agora associe a base de dados ao cofre dos Serviços de Recuperação. No portal Azure, na lista de **Servidores Protegidos,** clique no servidor que está em estado de erro > **Redescubra dBs**.

      ![Verifique se o servidor tem permissões apropriadas](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Verifique o progresso na área de **Notificações.** Quando as bases de dados selecionadas são encontradas, aparece uma mensagem de sucesso.

      ![Mensagem de sucesso de implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Se o seu Servidor SQL tiver várias instâncias de SQL Server instaladas, então deve adicionar permissão de sysadmin para a conta **NT Service\AzureWLBackupPluginSvc** em todas as instâncias Do QQL.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Dar permissões de sysadmina SQL para SQL 2008 e SQL 2008 R2

Adicione os logins **NT AUTHORITY\SYSTEM** e **NT Service\AzureWLBackupPluginSvc** à instância do servidor SQL:

1. Vá à Instância do Servidor SQL no explorador de objetos.
2. Navegar para segurança -> Logins
3. Clique no login e clique em *New Login...*

    ![Novo Login usando SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Aceda ao separador Geral e **introduza o NT AUTHORITY\SYSTEM** como o Nome de Login.

    ![nome de login para SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Vá a *Funções de Servidor* e escolha funções *públicas* e *sysadmin.*

    ![escolha de papéis em SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Ir para o *Status.* *Conceda* a permissão para ligar ao motor de base de dados e ao Login como *Ativado*.

    ![Concessão de permissões em SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Clique em OK.
8. Repita a mesma sequência de passos (1-7 acima) para adicionar o login nt Service\AzureWLBackupPluginSvc à instância do Servidor SQL. Se o login já existir, certifique-se de que tem a função de servidor de sysadmin e em status tem a Permissão de ligar ao motor de base de dados e ao Login como Enabled.
9. Após a concessão de permissão, **redescubra dBs** no portal: Cofre **->** Infraestrutura de Backup **->** Carga de Trabalho em Azure VM:

    ![Redescubra dBs no portal Azure](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Em alternativa, pode automatizar dando as permissões executando os seguintes comandos PowerShell em modo de administração. O nome da instância é definido para MSSQLSERVER por padrão. Mude o argumento do nome da instância no script, se necessário:

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

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre](backup-sql-server-database-azure-vms.md) o backup das bases de dados do SQL Server.
* [Saiba restaurar](restore-sql-database-azure-vm.md) as bases de dados do SQL Server.
* [Saiba mais sobre](manage-monitor-sql-database-backup.md) a gestão das bases de dados do SQL Server.
