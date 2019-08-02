---
title: Fazer backup de bancos de dados SQL Server no Azure | Microsoft Docs
description: Este tutorial explica como fazer backup de SQL Server no Azure. O artigo também explica SQL Server recuperação.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: cddb540eb0d6892426c4857b152ab6caa746f6da
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639812"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Sobre a Cópia de Segurança do SQL Server em VMs do Azure

SQL Server bancos de dados são cargas de trabalho críticas que exigem um RPO (objetivo de ponto de recuperação) baixo e retenção de longo prazo. Você pode fazer backup de bancos de dados SQL Server em execução em VMs do Azure usando o [backup do Azure](backup-overview.md).

## <a name="backup-process"></a>Processo de backup

Essa solução aproveita as APIs nativas do SQL para fazer backups de seus bancos de dados SQL.

* Depois de especificar o SQL Server VM que você deseja proteger e consultar os bancos de dados nele, o serviço de backup do Azure instalará uma extensão de backup de carga de trabalho na VM `AzureBackupWindowsWorkload`pela extensão de nome  .
* Essa extensão consiste em um coordenador e um plug-in do SQL. Embora o coordenador seja responsável por disparar fluxos de trabalho para várias operações, como configurar backup, backup e restauração, o plug-in é responsável pelo fluxo de dados real.
* Para poder descobrir bancos de dados nessa VM, o backup do Azure cria a conta `NT SERVICE\AzureWLBackupPluginSvc`. Essa conta é usada para backup e restauração e requer permissões de sysadmin do SQL. O backup do Azure aproveita `NT AUTHORITY\SYSTEM`a conta para a pesquisa/consulta de banco de dados, portanto, essa conta precisa ser um logon público no SQL. Se você não criou a VM SQL Server do Azure Marketplace, você pode receber um erro **UserErrorSQLNoSysadminMembership**. Se isso ocorrer, [siga estas instruções](backup-azure-sql-database.md).
* Depois de disparar configurar a proteção nos bancos de dados selecionados, o serviço de backup configura o coordenador com os agendamentos de backup e outros detalhes da política, que a extensão armazena em cache localmente na VM 
* No horário agendado, o coordenador se comunica com o plug-in e começa a transmitir os dados de backup do SQL Server usando o VDI.  
* O plug-in envia os dados diretamente para o cofre dos serviços de recuperação, eliminando assim a necessidade de um local de preparo. Os dados são criptografados e armazenados pelo serviço de backup do Azure nas contas de armazenamento.
* Quando a transferência de dados é concluída, o coordenador confirma a confirmação com o serviço de backup.

  ![Arquitetura de backup do SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Antes de começar

Antes de começar, verifique o seguinte:

1. Verifique se você tem uma instância SQL Server em execução no Azure. Você pode [criar rapidamente uma instância de SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) no Marketplace.
2. Examine a [consideração de recursos](#feature-consideration-and-limitations) e o [suporte ao cenário](#scenario-support).
3. [Examine as perguntas comuns](faq-backup-sql-server.md) sobre esse cenário.

## <a name="scenario-support"></a>Suporte a cenários

**Suporte** | **Detalhes**
--- | ---
**Implantações com suporte** | As VMs do Azure do SQL Marketplace e do não Marketplace (SQL Server instaladas manualmente) têm suporte.
**Áreas geográficas com suporte** | Sudeste da Austrália (ASE), Austrália oriental (AE) <br> Sul do Brasil (BRS)<br> Canadá central (CNC), leste do Canadá (CE)<br> Ásia Oriental do Sul (SEA), Ásia Oriental (EA) <br> Leste dos EUA (EUS), leste dos EUA 2 (EUS2), Oeste EUA Central (WCUS), oeste dos EUA (WUS); Oeste dos EUA 2 (WUS 2) EUA Central do Norte (NCUS) EUA Central (CUS) EUA Central do Sul (SCUS) <br> Índia central (INC.), sul da Índia (INS) <br> Leste do Japão (JPE), oeste do Japão (JPW) <br> Coreia central (KRC), sul da Coreia (KRS) <br> Europa Setentrional (NE), Europa Ocidental <br> Sul do Reino Unido (UKS), Oeste do Reino Unido (UKW)
**Sistemas operativos suportados** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Atualmente, não há suporte para o Linux.
**Versões SQL Server com suporte** | SQL Server 2017, conforme detalhado [aqui](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 e SPS, conforme detalhado [aqui](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014 SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Versões do .NET com suporte** | .NET Framework 4.5.2 e acima instalados na VM

### <a name="support-for-sql-server-2008-and-sql-server-2008-r2"></a>Suporte para SQL Server 2008 e SQL Server 2008 R2

O backup do Azure anunciou recentemente o suporte para [servidores de EOS SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-2008-eos-extend-support) -SQL Server 2008 e SQL Server 2008 R2. Atualmente, a solução está em versão prévia para SQL Server de EOS e dá suporte à seguinte configuração:

1. SQL Server 2008 e SQL Server 2008 R2 em execução no Windows 2008 R2 SP1
2. .NET Framework 4.5.2 e acima precisam ser instalados na VM
3. Não há suporte para backup para bancos de dados FCI e espelhados

Os usuários não serão cobrados por esse recurso até o momento em que ele estiver disponível. Todas as outras [Considerações sobre recursos e limitações](#feature-consideration-and-limitations) também se aplicam a essas versões. Indique os [pré-requisitos](backup-sql-server-database-azure-vms.md#prerequisites) antes de configurar a proteção no SQL servers 2008 e 2008 R2, que incluem a definição da [chave do registro](backup-sql-server-database-azure-vms.md#add-registry-key-to-enable-registration) (essa etapa não será necessária quando o recurso estiver disponível).


## <a name="feature-consideration-and-limitations"></a>Considerações e limitações de recursos

- SQL Server Backup pode ser configurado no portal do Azure ou no **PowerShell**. Não há suporte para a CLI.
- Há suporte para a solução em ambos os [](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) tipos de implantações-Azure Resource Manager VMs e VMs clássicas.
- A VM em execução SQL Server requer conectividade com a Internet para acessar os endereços IP públicos do Azure.
- Não há suporte para SQL Server **FCI (instância de cluster de failover)** e SQL Server instância de cluster de failover AlwaysOn.
- Não há suporte para operações de backup e restauração para bancos de dados espelho e instantâneos de banco de dados.
- Usar mais de uma solução de backup para fazer backup da instância SQL Server autônoma ou do grupo de disponibilidade AlwaysOn do SQL pode levar a uma falha de backup; Evite fazer isso.
- Fazer backup de dois nós de um grupo de disponibilidade individualmente com soluções iguais ou diferentes também pode levar a uma falha de backup.
- O backup do Azure dá suporte apenas a tipos de backup completos completos e somente cópia para bancos de dados **somente leitura**
- Bancos de dados com grande número de arquivos não podem ser protegidos. O número máximo de arquivos com suporte é **~ 1000**.  
- Você pode fazer backup de até **~ 2000** SQL Server bancos de dados em um cofre. Você pode criar vários cofres caso tenha um número maior de bancos de dados.
- Você pode configurar o backup para até **50** bancos de dados em um só lugar; Essa restrição ajuda a otimizar os carregamentos de backup.
- Damos suporte a bancos de dados de até **2TB** de tamanho; para tamanhos maiores que isso, problemas de desempenho podem surgir.
- Para ter uma noção de como muitos bancos de dados podem ser protegidos por servidor, precisamos considerar fatores como largura de banda, tamanho da VM, frequência de backup, tamanho do banco de dados, etc. [Baixe](http://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) o Resource Planner que fornece o número aproximado de bancos de dados que você pode ter por servidor com base nos recursos da VM e na política de backup.
- No caso de grupos de disponibilidade, os backups são obtidos dos diferentes nós com base em alguns fatores. O comportamento de backup de um grupo de disponibilidade é resumido abaixo.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Fazer backup do comportamento em caso de grupos de disponibilidade AlwaysOn

É recomendável que o backup seja configurado em apenas um nó de um AG. O backup sempre deve ser configurado na mesma região que o nó primário. Em outras palavras, você sempre precisa que o nó primário esteja presente na região em que você está configurando o backup. Se todos os nós do AG estiverem na mesma região em que o backup está configurado, não haverá nenhuma preocupação.

**Para AG entre regiões**
- Independentemente da preferência de backup, os backups não acontecerão a partir dos nós que não estão na mesma região em que o backup está configurado. Isso ocorre porque não há suporte para os backups entre regiões. Se você tiver apenas 2 nós e o nó secundário estiver na outra região; Nesse caso, os backups continuarão a acontecer a partir do nó primário (a menos que a preferência de backup seja ' somente secundária ').
- Se ocorrer um failover em uma região diferente daquela em que o backup está configurado, os backups falhariam nos nós na região que passou por failover.

Dependendo dos tipos de preferência de backup e backups (completo/diferencial/log/cópia somente completa), os backups são obtidos de um nó específico (primário/secundário).

- **Preferência de backup: Primary**

**Tipo de backup** | **Node**
    --- | ---
    Completo | Primário
    Diferencial | Primário
    registo |  Primário
    Copiar somente completo |  Primário

- **Preferência de backup: Somente secundário**

**Tipo de backup** | **Node**
--- | ---
Completo | Primário
Diferencial | Primário
registo |  Secundário
Copiar somente completo |  Secundário

- **Preferência de backup: Secundário**

**Tipo de backup** | **Node**
--- | ---
Completo | Primário
Diferencial | Primário
registo |  Secundário
Copiar somente completo |  Secundário

- **Nenhuma preferência de backup**

**Tipo de backup** | **Node**
--- | ---
Completo | Primário
Diferencial | Primário
registo |  Secundário
Copiar somente completo |  Secundário

## <a name="set-vm-permissions"></a>Definir permissões de VM

  Quando você executa a descoberta em um SQL Server, o backup do Azure faz o seguinte:

* Adiciona a extensão AzureBackupWindowsWorkload.
* Cria uma conta NT SERVICE\AzureWLBackupPluginSvc para descobrir bancos de dados na máquina virtual. Essa conta é usada para backup e restauração e requer permissões de sysadmin do SQL.
* Descobre os bancos de dados que estão em execução em uma VM, o backup do Azure usa a conta NT AUTHORITY\SYSTEM. Essa conta deve ser uma entrada pública no SQL.

Se você não criou a VM SQL Server no Azure Marketplace ou se estiver no SQL 2008 e 2008 R2, você poderá receber um erro **UserErrorSQLNoSysadminMembership** .

Para conceder permissões no caso do **SQL 2008** e **2008 R2** em execução no Windows 2008 R2, consulte [aqui](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

Para todas as outras versões, corrija as permissões com as seguintes etapas:

  1. Use uma conta com SQL Server permissões sysadmin para entrar no SQL Server Management Studio (SSMS). A menos que você precise de permissões especiais, a autenticação do Windows deve funcionar.
  2. Na SQL Server, abra a pasta **segurança/logons** .

      ![Abra a pasta segurança/logons para ver as contas](./media/backup-azure-sql-database/security-login-list.png)

  3. Clique com o botão direito do mouse na pasta logons e selecione **novo logon**. Em **logon-novo**, selecione **Pesquisar**.

      ![Na caixa de diálogo logon – novo, selecione Pesquisar](./media/backup-azure-sql-database/new-login-search.png)

  4. A conta de serviço virtual do Windows **NT SERVICE\AzureWLBackupPluginSvc** foi criada durante o registro da máquina virtual e a fase de descoberta do SQL. Insira o nome da conta, conforme mostrado em **Inserir o nome do objeto a ser selecionado**. Selecione **verificar nomes** para resolver o nome. Clique em **OK**.

      ![Selecione verificar nomes para resolver o nome de serviço desconhecido](./media/backup-azure-sql-database/check-name.png)

  5. Em **funções de servidor**, verifique se a função **sysadmin** está selecionada. Clique em **OK**. As permissões necessárias agora devem existir.

      ![Certifique-se de que a função de servidor sysadmin esteja selecionada](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Agora, associe o banco de dados ao cofre dos serviços de recuperação. Na portal do Azure, na lista **servidores protegidos** , clique com o botão direito do mouse no servidor que está em um estado de erro > redescobrir **bancos**de os.

      ![Verifique se o servidor tem as permissões apropriadas](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Verifique o andamento na área **notificações** . Quando os bancos de dados selecionados são encontrados, uma mensagem de êxito é exibida.

      ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Se o SQL Server tiver várias instâncias do SQL Server instaladas, você deverá adicionar a permissão sysadmin para a conta **NT Service\AzureWLBackupPluginSvc** a todas as instâncias do SQL.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Conceder permissões de sysadmin do SQL para SQL 2008 e SQL 2008 R2

Adicione logons **NT AUTHORITY\SYSTEM** e **NT Service\AzureWLBackupPluginSvc** à instância de SQL Server:

1. Acesse a instância de SQL Server no Pesquisador de objetos.
2. Navegue até Security-> logons
3. Clique com o botão direito do mouse nos logons e clique em *novo logon...*

    ![Novo logon usando o SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Vá para a guia geral e digite **NT AUTHORITY\SYSTEM** como o nome de logon.

    ![nome de logon do SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Vá para *funções de servidor* e escolha funções *públicas* e *sysadmin* .

    ![escolhendo funções no SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Vá para *status*. *Conceda* a permissão para se conectar ao mecanismo de banco de dados e fazer logon como *habilitado*.

    ![Conceder permissões no SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Clique em OK.
8. Repita a mesma sequência de etapas (1-7 acima) para adicionar o logon do NT Service\AzureWLBackupPluginSvc à instância do SQL Server. Se o logon já existir, verifique se ele tem a função de servidor sysadmin e, sob status, ele concede a permissão para se conectar ao mecanismo de banco de dados e fazer logon como habilitado.
9. Depois de conceder a permissão, redescubra os **bancos** de os no Portal: Carga **->** de trabalho **->** de infraestrutura de backup do cofre na VM do Azure:

    ![Redescobrir bancos de os no portal do Azure](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Como alternativa, você pode automatizar a concessão de permissões executando os seguintes comandos do PowerShell no modo admin. O nome da instância é definido como MSSQLSERVER por padrão. Altere o argumento de nome da instância no script, se necessário:

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

* [Saiba mais sobre como](backup-sql-server-database-azure-vms.md) fazer backup de bancos de dados do SQL Server.
* [Saiba mais sobre como](restore-sql-database-azure-vm.md) restaurar bancos de dados de SQL Server de backup.
* [Saiba mais sobre como](manage-monitor-sql-database-backup.md) gerenciar bancos de dados de SQL Server de backup.
