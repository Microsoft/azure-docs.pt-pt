---
title: Cópia de segurança de bases de dados do SQL Server para o Azure | Documentos da Microsoft
description: Este tutorial explica como criar cópias de segurança do SQL Server para o Azure. O artigo também explica a recuperação do SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: raynew
ms.openlocfilehash: 2a6319565aa05f34ce31a14c5fc57e591248f4ee
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399698"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Sobre a Cópia de Segurança do SQL Server em VMs do Azure

Bases de dados do SQL Server são cargas de trabalho críticas que exigem um objetivo de ponto de recuperação (RPO) e a retenção de longa duração. Pode fazer backup de bancos de dados do SQL Server em execução em VMs do Azure utilizando [Azure Backup](backup-overview.md).

## <a name="backup-process"></a>Processo de cópia de segurança

Esta solução tira partido das APIs de nativas de SQL para efetuar cópias de segurança das bases de dados SQL.

* Depois de especificar a VM do SQL Server que pretende proteger e consultar para as bases de dados no mesmo, o serviço de cópia de segurança do Azure irá instalar uma extensão de cópia de segurança da carga de trabalho na VM com o nome `AzureBackupWindowsWorkload`  extensão.
* Esta extensão é constituído por um coordenador e um plug-in do SQL. Embora o coordenador é responsável por acionar fluxos de trabalho para várias operações como configurar a cópia de segurança, cópia de segurança e restauro, o plug-in é responsável por fluxo de dados real.
* Para poder detetar as bases de dados nesta VM, o Azure Backup cria a conta `NT SERVICE\AzureWLBackupPluginSvc`. Esta conta é utilizada para cópia de segurança e restauro e necessita de permissões de administrador do sistema do SQL. Tira partido de cópia de segurança do Azure a `NT AUTHORITY\SYSTEM` de conta para deteção/consulta de base de dados, para que esta conta tem de ser um início de sessão público no SQL. Se não criar a VM do SQL Server no Azure Marketplace, poderá receber um erro **UserErrorSQLNoSysadminMembership**. Se isto ocorrer [siga estas instruções](backup-azure-sql-database.md).
* Assim que o acionador de configurar a proteção em bases de dados selecionadas, o serviço de cópia de segurança configura o coordenador com as agendas de cópia de segurança e outros detalhes da política, que a extensão coloca em cache localmente na VM 
* Na hora agendada, o coordenador de comunica com o plug-in e ele começa a transmissão em fluxo os dados de cópia de segurança do SQL server a utilizar a VDI.  
* O plug-in envia os dados diretamente para o Cofre de serviços de recuperação, eliminando a necessidade de uma localização de transição. Os dados são encriptados e armazenados pelo serviço de cópia de segurança do Azure em contas de armazenamento.
* Quando a transferência de dados estiver concluída, o coordenador confirma a consolidação com o serviço de cópia de segurança.

  ![Arquitetura de cópia de segurança SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Antes de começar

Antes de começar, verifique se o abaixo:

1. Certifique-se de que tem uma instância do SQL Server em execução no Azure. Pode [rapidamente a criar uma instância do SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) no marketplace.
2. Reveja os [consideração de funcionalidades](#feature-consideration-and-limitations) e [suporte a cenários](#scenario-support).
3. [Reveja as perguntas mais comuns](faq-backup-sql-server.md) sobre este cenário.

## <a name="scenario-support"></a>Suporte a cenários

**Suporte** | **Detalhes**
--- | ---
**Implementações suportadas** | As VMs do Azure do SQL Marketplace e externas (SQL Server instalado manualmente) são suportadas VMs.
**Áreas geográficas suportadas** | Sudeste da Austrália (ASE), leste da Austrália (AE) <br> Sul do Brasil (BRS)<br> Canada Central (CNC), Canada East (CE)<br> South East Asia (SEA), East Asia (EA) <br> EUA Leste (EUS), este dos E.U.A. 2 (EUS2), EUA Centro-Oeste (WCUS), E.U.A. oeste (WUS); E.U.A. oeste 2 (WUS 2) Norte dos E.U.A. (NCUS) dos EUA Central (CUS) Centro-Sul dos E.U.A. (SCUS) <br> India Central (INC), India South (INS) <br> Leste do Japão (JPE), oeste do Japão (JPW) <br> Coreia Central (KRC), Sul da Coreia (KRS) <br> Europa do Norte (m), Europa Ocidental <br> Sul do Reino Unido (UKS), do Reino Unido oeste (UKW)
**Sistemas operativos suportados** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux não é atualmente suportado.
**Versões suportadas do SQL Server** | SQL Server 2017; SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Versões suportadas do .NET** | .NET framework 4.5.2 e superior instalado na VM

## <a name="feature-consideration-and-limitations"></a>Funcionalidade considerações e limitações

- Cópia de segurança do SQL Server pode ser configurada no portal do Azure ou **PowerShell**. Não suportamos a CLI.
- A solução é suportada em ambos os tipos de [implementações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) -VMs do Azure Resource Manager e as VMs clássicas.
- VM com o SQL Server necessita de conectividade de internet para acessar os endereços IP públicos do Azure.
- SQL Server **instância de Cluster de ativação pós-falha (FCI)** e SQL Server sempre na instância de Cluster de ativação pós-falha não são suportadas.
- Criar cópias de segurança e as operações de restauro de bases de dados de espelhamento e instantâneos de base de dados não são suportadas.
- Utilizar mais do que um soluções de cópia de segurança para criar cópias de segurança do SQL Server autónomo instância ou SQL sempre no grupo de disponibilidade pode levar a falhas de cópia de segurança; Evite impedidos de o fazer.
- Fazer backup de dois nós de um grupo de disponibilidade individualmente com as soluções idêntica ou diferentes, também pode levar a falhas de cópia de segurança.
- Tipos de cópia de segurança completa apenas de cópia para e do Azure Backup suporta apenas completa **só de leitura** bases de dados
- Não não possível proteger bases de dados com grande número de ficheiros. É o número máximo de ficheiros suportado **~ 1000**.  
- Pode fazer até **~ 2000** bases de dados do SQL Server num cofre. Pode criar vários cofres no caso de ter um maior número de bases de dados.
- Pode configurar a cópia de segurança para até **50** bases de dados em um ir; esta restrição ajuda a otimizar cargas de cópia de segurança.
- Damos suporte a bancos de dados até **2TB** tamanho; para tamanhos maiores do que isso, podem surgir problemas de desempenho.
- Para ter uma idéia de como o número de bases de dados podem ser protegidos por servidor, é necessário considerar fatores como a largura de banda, tamanho da VM, frequência de cópia de segurança, tamanho da base de dados, etc. Estamos a trabalhar num Planeador de implementações que pode ajudar a calcular que esses números em o proprietário. Podemos estarão publicando-lo em breve.
- Em caso de grupos de disponibilidade, os backups são feitos de diferentes nós com base em alguns fatores. O comportamento de cópia de segurança de um grupo de disponibilidade é resumido abaixo.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Criar cópias de segurança comportamento em caso de sempre em grupos de disponibilidade

Recomenda-se que a cópia de segurança está configurada em apenas um nó de um AG. Cópia de segurança sempre deve ser configurada na mesma região que o nó principal. Em outras palavras, sempre tem o nó primário esteja presente na região em que estiver a configurar a cópia de segurança. Se todos os nós do AG estão na mesma região em que a cópia de segurança está configurada, não há qualquer preocupação.

**Para AG entre regiões**
- Independentemente da preferência de cópia de segurança, as cópias de segurança não ocorrerá de nós que não estão na mesma região em que a cópia de segurança está configurada. Isto acontece porque as cópias de segurança entre regiões não são suportadas. Se tiver apenas 2 nós e o nó secundário está em outra região. Neste caso, as cópias de segurança continuarão a ser efetuadas a partir do nó principal (a menos que seja de sua preferência de cópia de segurança 'apenas para o secundário').
- Se ocorrer uma ativação pós-falha para uma região diferente em que a cópia de segurança está configurada, as cópias de segurança falha em nós na região com ativação pós-falha.

Consoante a preferência de cópia de segurança e os tipos de cópias de segurança (completa/diferencial/registo/apenas de cópia completos), as cópias de segurança são extraídas de um determinado nó (primária/secundária).

- **Preferência de cópia de segurança: Primário**

**Tipo de cópia de segurança** | **Node**
    --- | ---
    Completo | Primária
    Diferencial | Primária
    Registar |  Primária
    Apenas de cópia completa |  Primária

- **Preferência de cópia de segurança: Apenas secundária**

**Tipo de cópia de segurança** | **Node**
--- | ---
Completo | Primária
Diferencial | Primária
Registar |  Secundária
Apenas de cópia completa |  Secundária

- **Preferência de cópia de segurança: Secundário**

**Tipo de cópia de segurança** | **Node**
--- | ---
Completo | Primária
Diferencial | Primária
Registar |  Secundária
Apenas de cópia completa |  Secundária

- **Sem preferência de cópia de segurança**

**Tipo de cópia de segurança** | **Node**
--- | ---
Completo | Primária
Diferencial | Primária
Registar |  Secundária
Apenas de cópia completa |  Secundária

## <a name="fix-sql-sysadmin-permissions"></a>Corrigir permissões de administrador do sistema do SQL

  Se precisa corrigir as permissões devido **UserErrorSQLNoSysadminMembership** erro, execute os passos abaixo:

  1. Utilize uma conta com permissões de administrador do sistema do SQL Server para iniciar sessão para o SQL Server Management Studio (SSMS). A menos que precise permissões especiais, autenticação do Windows deverá funcionar.
  2. No SQL Server, abra a **inícios de sessão/segurança** pasta.

      ![Abra a pasta de segurança/inícios de sessão para ver as contas](./media/backup-azure-sql-database/security-login-list.png)

  3. Com o botão direito a **inícios de sessão** pasta e selecione **novo início de sessão**. Na **início de sessão - novo**, selecione **pesquisa**.

      ![No início de sessão - nova caixa de diálogo, selecione Search](./media/backup-azure-sql-database/new-login-search.png)

  4. Conta de serviço do Windows virtual **NT SERVICE\AzureWLBackupPluginSvc** foi criado durante o registo de máquina virtual e a fase de deteção SQL. Introduza o nome da conta, conforme mostrado na **introduza o nome de objeto a selecionar**. Selecione **verificar nomes** para resolver o nome. Clique em **OK**.

      ![Selecione verificar nomes para resolver o nome do serviço desconhecido](./media/backup-azure-sql-database/check-name.png)

  5. Na **funções de servidor**, certifique-se a **sysadmin** função está selecionada. Clique em **OK**. As permissões necessárias agora devem existir.

      ![Certifique-se de que a função de servidor sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Agora, associe a base de dados com o Cofre dos serviços de recuperação. No portal do Azure, no **servidores protegidos** lista, clique com o botão direito do servidor que está em estado de erro > **Redeteção de DBs**.

      ![Verifique se que o servidor tem as permissões adequadas](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Verificar o progresso **notificações** área. Quando são encontrados as bases de dados selecionadas, é apresentada uma mensagem de êxito.

      ![Mensagem de êxito da implementação](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Se o SQL Server tem várias instâncias do SQL Server instalada, tem de adicionar permissão de administrador do sistema para **NT Service\AzureWLBackupPluginSvc** de conta para todas as instâncias SQL.

## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre](backup-sql-server-database-azure-vms.md) backup bancos de dados do SQL Server.
- [Saiba mais sobre](restore-sql-database-azure-vm.md) restaurar cópia de segurança de bases de dados do SQL Server.
- [Saiba mais sobre](manage-monitor-sql-database-backup.md) gerir cópia de segurança de bases de dados do SQL Server.
