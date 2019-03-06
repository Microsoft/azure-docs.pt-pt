---
title: Configurar a recuperação após desastre para SQL Server com o SQL Server e o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como configurar a recuperação após desastre para SQL Server usando o SQL Server e o Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 2d141a330b6b6c5fc102dd70ae1d6b3b01283d19
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444876"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Configurar a recuperação após desastre para SQL Server 

Este artigo descreve como proteger o SQL Server back-end de um aplicativo usando uma combinação de continuidade de negócio do SQL Server e tecnologias de (BCDR) de recuperação após desastre, e [do Azure Site Recovery](site-recovery-overview.md).

Antes de começar, certifique-se de que compreende capacidades de recuperação de desastres do SQL Server, incluindo o clustering de ativação pós-falha, grupos de disponibilidade Always On, espelhamento de banco de dados e o envio de log.


## <a name="sql-server-deployments"></a>Implementações do SQL Server

Muitas cargas de trabalho utilizam o SQL Server como uma base e pode ser integrado com aplicações como o SharePoint, Dynamics e SAP, para implementar os serviços de dados.  SQL Server pode ser implementado de várias formas:

* **Servidor de SQL autónoma**: SQL Server e todos os bancos de dados estão alojados num único computador (físico ou virtual). Quando virtualizado, clustering de host é usado para alta disponibilidade local. Ao nível do convidado elevada disponibilidade não está implementada.
* **SQL Server Clustering de ativação pós-falha instâncias (sempre em FCI)**: Dois ou mais nós executar o SQL Server instanciados com discos partilhados estão configurados num cluster de ativação pós-falha do Windows. Se um nó estiver desativado, o cluster pode failover do SQL Server para outra instância. Esta configuração é normalmente utilizada para implementar elevada disponibilidade num site primário. Esta implementação não protege contra falha ou indisponibilidade na camada de armazenamento partilhado. Um disco partilhado pode ser implementado através de iSCSI, canal de Fibra ou vhdx partilhado.
* **SQL grupos de disponibilidade Always On**: Dois ou mais nós são configuradas no partilhado nada de cluster, com bancos de dados do SQL Server configurados num grupo de disponibilidade, com a replicação síncrona e failover automático.

 Este artigo utiliza as seguintes nativas SQL após desastre tecnologias de recuperação para recuperar bases de dados para um site remoto:

* SQL grupos de Disponibilidade AlwaysOn, para fornecer para recuperação após desastre para SQL Server 2012 ou 2014 Enterprise Edition.
* SQL espelhamento do banco de dados no modo de alta segurança, para o SQL Server Standard edition (qualquer versão) ou para o SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Suporte do Site Recovery

### <a name="supported-scenarios"></a>Cenários suportados
Recuperação de sites pode proteger o SQL Server conforme resumido na tabela.

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim
**Azure**|ND| Sim

### <a name="supported-sql-server-versions"></a>Versões suportadas do SQL Server
Estas versões do SQL Server são suportados para os cenários suportados:

* SQL Server 2016 Enterprise e Standard
* SQL Server 2014 Enterprise e Standard
* SQL Server 2012 Enterprise e Standard
* SQL Server 2008 R2 Enterprise and Standard

### <a name="supported-sql-server-integration"></a>Integração do SQL Server suportada

Recuperação de sites pode ser integrada com as tecnologias de SQL Server BCDR nativas resumidas na tabela, para fornecer uma solução de recuperação após desastre.

**Funcionalidade** | **Detalhes** | **SQL Server** |
--- | --- | ---
**Grupo de disponibilidade Always On** | Executam várias instâncias autônomas do SQL Server num cluster de ativação pós-falha com vários nós.<br/><br/>Bases de dados podem ser agrupados em grupos de ativação pós-falha que podem ser copiados (espelhado) em instâncias do SQL Server, de modo que não é necessário nenhum armazenamento compartilhado.<br/><br/>Fornece a recuperação após desastre entre um site primário e um ou mais sites secundários. Dois nós podem ser configurados num partilhado nada cluster com bancos de dados do SQL Server configurado num grupo de disponibilidade com a replicação síncrona e failover automático. | SQL Server 2016, o SQL Server 2014 e SQL Server 2012 Enterprise edition
**(Sempre em FCI) de clustering de ativação pós-falha** | SQL Server tira partido de cluster para elevada disponibilidade de cargas de trabalho no local do SQL Server de failover do Windows.<br/><br/>Nós a executar instâncias do SQL Server com discos partilhados estão configurados num cluster de ativação pós-falha. Se uma instância está inativo executa o cluster de failover para outro.<br/><br/>O cluster não protege contra falha ou falhas no armazenamento partilhado. O disco partilhado pode ser implementado com o iSCSI, canal de fibra, ou partilhado VHDXs. | Edições do SQL Server Enterprise<br/><br/>SQL Server Standard edition (limitado a apenas dois nós)
**Base de dados de espelhamento (modo de alta segurança)** | Protege uma base de dados para uma única cópia secundária. Disponível em ambos os alta segurança (síncrona) e modos de replicação (assíncrona) de alto desempenho. Não precisa de um cluster de ativação pós-falha. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise todas as edições
**Servidor do SQL autónomo** | O SQL Server e base de dados estão alojados num único servidor (físico ou virtual). Clustering de Host é utilizado para elevada disponibilidade se o servidor virtual. Elevada disponibilidade não ao nível do convidado. | Edição Enterprise ou Standard

## <a name="deployment-recommendations"></a>Recomendações de implementação

A tabela seguinte resume as nossas recomendações para a integração de tecnologias BCDR do SQL Server com o Site Recovery.

| **Versão** | **Edição** | **Implementação** | **Local para a no local** | **No local para o Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2012, 2014 ou 2016 |Enterprise |Instância de cluster de ativação pós-falha |Grupos de disponibilidade Always On |Grupos de disponibilidade Always On |
|| Enterprise |Always On grupos de disponibilidade para elevada disponibilidade |Grupos de disponibilidade Always On |Grupos de disponibilidade Always On | |
|| Standard |Instância de cluster de ativação pós-falha (FCI) |Replicação do site Recovery com espelhamento local |Replicação do site Recovery com espelhamento local | |
|| Enterprise ou Standard |Autónomo |Replicação do site Recovery |Replicação do site Recovery | |
| SQL Server 2008 R2 ou 2008 |Enterprise ou Standard |Instância de cluster de ativação pós-falha (FCI) |Replicação do site Recovery com espelhamento local |Replicação do site Recovery com espelhamento local |
|| Enterprise ou Standard |Autónomo |Replicação do site Recovery |Replicação do site Recovery | |
| SQL Server (qualquer versão) |Enterprise ou Standard |Instância de cluster de ativação pós-falha - aplicação de DTC |Replicação do site Recovery |Não suportado |

## <a name="deployment-prerequisites"></a>Pré-requisitos da implementação

* Uma implementação no local do SQL Server, executar uma versão suportada do SQL Server. Normalmente, também precisa do Active Directory para o SQL server.
* Os requisitos para o cenário de que pretende implementar. Saiba mais sobre os requisitos de suporte para [replicação para o Azure](site-recovery-support-matrix-to-azure.md) e [no local](site-recovery-support-matrix.md), e [pré-requisitos de implementação](site-recovery-prereq.md).

## <a name="set-up-active-directory"></a>Configurar o Active Directory

Configure o Active Directory, no site secundário de recuperação, para o SQL Server a ser executado corretamente.

* **Pequenas empresas**— com um pequeno número de aplicativos e o controlador de domínio único para o site no local, se pretender efetuar a ativação pós-falha de todo o site, recomendamos que utilize replicação do Site Recovery para replicar o controlador de domínio para o secundário Centro de dados, ou para o Azure.
* **Médias a grandes empresas**— se tiver um grande número de aplicativos, uma floresta do Active Directory, e que pretende efetuar a ativação por aplicação ou carga de trabalho, recomendamos que configura um controlador de domínio adicional no Centro de dados secundário ou no Azure. Se estiver a utilizar grupos de disponibilidade Always On para recuperar para um site remoto, recomendamos que definir outro controlador de domínio adicionais no site secundário ou no Azure, para utilizar para a instância do SQL Server recuperada.

As instruções neste artigo presumem que um controlador de domínio está disponível na localização secundária. [Leia mais](site-recovery-active-directory.md) sobre como proteger o Active Directory com o Site Recovery.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Integrar com o SQL Server Always On para a replicação para o Azure

Eis o que precisa fazer:

1. Importar scripts para a sua conta de automatização do Azure. Contém os scripts para ativação pós-falha o grupo de disponibilidade SQL num [Resource Manager virtual machine](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) e uma [máquinas virtuais clássicas](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Adicione o ASR-SQL-FailoverAG como uma ação de pré-instalação do primeiro grupo de plano de recuperação.

1. Siga as instruções disponíveis no script para criar uma variável de automatização para fornecer o nome dos grupos de disponibilidade.

### <a name="steps-to-do-a-test-failover"></a>Passos para fazer uma ativação pós-falha de teste

SQL Always On não suportar nativamente a ativação pós-falha de teste. Por conseguinte, recomendamos o seguinte:

1. Configurar [Azure Backup](../backup/backup-azure-arm-vms.md) na máquina virtual que aloja a réplica do grupo de disponibilidade no Azure.

1. Antes de acionar a ativação pós-falha de teste do plano de recuperação, recupere a máquina virtual a partir da cópia de segurança criada no passo anterior.

    ![Restaurar a partir de cópia de segurança do Azure](./media/site-recovery-sql/restore-from-backup.png)

1. [Forçar um quórum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) na máquina virtual restaurada a partir de cópia de segurança.

1. Atualize o IP do ouvinte para um IP disponível na rede de ativação pós-falha de teste.

    ![Atualizar o serviço de escuta de IP](./media/site-recovery-sql/update-listener-ip.png)

1. Colocar o serviço de escuta online.

    ![Colocar o serviço de escuta Online](./media/site-recovery-sql/bring-listener-online.png)

1. Crie um balanceador de carga com um que IP criado no conjunto IP de front-end correspondente a cada serviço de escuta do grupo de disponibilidade e com a máquina virtual do SQL adicionado no conjunto de back-end.

     ![Criar Balanceador de carga – conjunto IP de front-end](./media/site-recovery-sql/create-load-balancer1.png)

    ![Criar Balanceador de carga - conjunto de back-end](./media/site-recovery-sql/create-load-balancer2.png)

1. Fazer uma ativação pós-falha de teste do plano de recuperação.

### <a name="steps-to-do-a-failover"></a>Passos para fazer uma ativação pós-falha

Depois de ter adicionado o script no plano de recuperação e validar o plano de recuperação por efetuar uma ativação pós-falha de teste, pode fazer a ativação pós-falha do plano de recuperação.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integrar com o SQL Server Always On para replicação para um site secundário no local

Se o SQL Server estiver a utilizar grupos de disponibilidade para elevada disponibilidade (ou um FCI), recomendamos que utilize grupos de disponibilidade no site de recuperação. Tenha em atenção que isto se aplica a aplicações que não utilizam a transações distribuídas.

1. [Configurar bases de dados](https://msdn.microsoft.com/library/hh213078.aspx) em grupos de disponibilidade.
1. Crie uma rede virtual no site secundário.
1. Configure uma ligação de VPN de site a site entre a rede virtual e o site primário.
1. Criar uma máquina virtual no site de recuperação e instalar o SQL Server no mesmo.
1. Estenda os existentes sempre em grupos de disponibilidade para a nova VM do SQL Server. Configure esta instância do SQL Server como uma cópia da réplica assíncrona.
1. Criar um serviço de escuta do grupo de disponibilidade ou atualizar o serviço de escuta existente para incluir a máquina virtual de réplica assíncrona.
1. Certifique-se de que o farm de aplicação está configurado utilizando o serviço de escuta. Se está configurada utilizando o nome do servidor de base de dados, atualizá-la para utilizar o serviço de escuta, pelo que não precisa reconfigurá-las após a ativação pós-falha.

Para aplicações que utilizam a transações distribuídas, é recomendável implementar a recuperação de Site com [replicação de site a site do servidor VMware/físico](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considerações do plano de recuperação
1. Adicione este script de exemplo para a biblioteca do VMM, nos sites primários e secundários.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Quando cria um plano de recuperação para a aplicação, adicione uma ação de pré-instalação para o passo de grupo-1 em script, que invoca o script para efetuar a ativação pós-falha de grupos de disponibilidade.

## <a name="protect-a-standalone-sql-server"></a>Proteger o SQL Server autónomo

Neste cenário, recomendamos que utilize replicação do Site Recovery para proteger a máquina do SQL Server. Os passos exatos dependem se o SQL Server é uma VM ou um servidor físico e se pretender replicar para o Azure ou um secundário no site local. Saiba mais sobre [cenários de recuperação de Site](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Proteger um cluster do SQL Server (standard edition e Windows Server 2008 R2)

Para um cluster a executar o SQL Server Standard edition ou o SQL Server 2008 R2, recomendamos que usar replicação do Site Recovery para proteger o SQL Server.

### <a name="on-premises-to-on-premises"></a>Local para local

* Se a aplicação utilizar transações distribuídas Recomendamos que implemente [Site Recovery com replicação SAN](site-recovery-vmm-san.md) para um ambiente de Hyper-V, ou [VMware/servidor físico para o VMware](site-recovery-vmware-to-vmware.md) para um ambiente de VMware.
* Para aplicativos de não DTC, use a abordagem acima para recuperar o cluster como um servidor autónomo, ao tirar partido de uma segurança elevada local espelho de DB.

### <a name="on-premises-to-azure"></a>Local para Azure

Recuperação de sites não fornece convidado suporte de cluster, ao replicar para o Azure. SQL Server também não oferece uma solução de recuperação após desastre de baixo custo para a edição Standard. Neste cenário, recomendamos que protegerem o cluster do SQL Server no local para o SQL Server autónomo e recuperá-la no Azure.

1. Configure uma instância do SQL Server autónomo adicionais no site no local.
1. Configure a instância para servir como um espelho das bases de dados que pretende proteger. Configure o espelhamento no modo de alta segurança.
1. Configurar a recuperação de Site no site no local, para ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware VMs/servidores físicos)](site-recovery-vmware-to-azure-classic.md).
1. Utilize a replicação do Site Recovery para replicar a nova instância do SQL Server para o Azure. Uma vez que é uma cópia de espelho de alta segurança, serão sincronizado com o cluster principal, mas vão ser replicado para o Azure com a replicação do Site Recovery.


![Cluster Standard](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considerações sobre a reativação pós-falha

Para clusters do SQL Server Standard, a reativação pós-falha após uma ativação pós-falha não planeada requer uma cópia de segurança do SQL server e o restauro, a partir da instância de espelhamento para o cluster original, com reestablishment do espelho.

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais](site-recovery-components.md) sobre a arquitetura do Site Recovery.
