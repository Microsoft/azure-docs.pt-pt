---
title: Configurar o IBM Db2 HADR em máquinas virtuais do Azure (VMs) | Documentos da Microsoft
description: Estabelece uma elevada disponibilidade do IBM Db2 LUW em máquinas virtuais do Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: a74dd1a932cac41081786f76938a5b35de62d878
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689702"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Elevada disponibilidade da IBM Db2 LUW em VMs do Azure no SUSE Linux Enterprise Server com Pacemaker

IBM Db2 para Linux, UNIX e Windows (LUW) no [elevada disponibilidade e após desastre (HADR) a configuração da recuperação](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) consiste num nó que execute uma instância de base de dados primária e, pelo menos, um nó que execute uma instância de base de dados secundária. Alterações para a instância de base de dados primária são replicadas para uma instância de base de dados secundária forma síncrona ou assíncrona, consoante a configuração. 

Este artigo descreve como implementar e configurar as máquinas de virtuais (VMs) do Azure, instalar o framework de cluster e instalar o LUW de Db2 da IBM com configuração HADR. 

O artigo não abrange como instalar e configurar o IBM Db2 LUW com instalação de software HADR ou SAP. Para ajudar a realizar estas tarefas, fornecemos referências aos manuais de instalação SAP e IBM. Este artigo se concentra em partes que são específicas do ambiente do Azure. 

As versões suportadas do IBM Db2 são 10.5 e posterior, conforme documentado na nota SAP [1928533].

Antes de iniciar uma instalação, consulte a documentação e notas de SAP seguintes:

| Nota SAP | Descrição |
| --- | --- |
| [1928533] | Aplicações de SAP no Azure: Produtos suportados e tipos de VM do Azure |
| [2015553] | SAP no Azure: Pré-requisitos de suporte |
| [2178632] | Chave de métricas de monitorização para o SAP no Azure |
| [2191498] | SAP no Linux com o Azure: Melhorada a monitorização |
| [2243692] | Linux no Azure (IaaS) VM: Problemas de licença do SAP |
| [1984787] | SUSE LINUX Enterprise Server 12: Observações de instalação |
| [1999351] | Resolução de problemas avançado do Azure de monitorização para SAP |
| [2233094] | DB6: Aplicações de SAP no Azure que utilizam o IBM Db2 para Linux, UNIX e Windows - informações adicionais |
| [1612105] | DB6: FAQ sobre Db2 com HADR |


| Documentação | 
| --- |
| [Wiki de Comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Tem todas as notas de SAP necessária para Linux |
| [Máquinas de virtuais de planeamento e implementação para o SAP no Linux do Azure] [ planning-guide] guia |
| [Implementação de máquinas virtuais do Azure para SAP no Linux] [ deployment-guide] (Este artigo) |
| [Implantação de system(DBMS) de gerenciamento para o SAP no Linux da base de dados de máquinas virtuais do Azure] [ dbms-guide] guia |
| [Carga de trabalho SAP na lista de verificação de planejamento e implantação do Azure][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server para o SAP aplicativos 12 SP3 melhores práticas guias][sles-for-sap-bp] |
| [SUSE Linux Enterprise elevada disponibilidade extensão 12 SP3][sles-ha-guide] |
| [Implementação de DBMS de máquinas virtuais do Azure do IBM Db2 para a carga de trabalho do SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Descrição geral
Para assegurar elevada disponibilidade, IBM Db2 LUW com HADR está instalado, pelo menos, duas máquinas virtuais do Azure, que são implementadas num [conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ou na [Azure Availability Zones](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Os gráficos seguintes apresentam uma instalação do servidor de base de dados de duas VMs do Azure. Tanto o servidor de base de dados as VMs do Azure tem seu próprio armazenamento ligado e estão em execução. No HADR, uma instância de base de dados em uma das VMs do Azure com a função da instância primária. Todos os clientes estão ligados a esta instância primária. Todas as alterações em transações de base de dados são mantidas localmente no registo de transações de Db2. Como os registros de log de transação são mantidos localmente, os registos são transferidos através de TCP/IP para a instância de base de dados, no segundo servidor de base de dados, o servidor em espera ou instância em espera. A instância em espera atualiza a base de dados local, ao implementar para a frente a transação transferida registros de log. Dessa forma, o servidor em espera é mantido sincronizado com o servidor primário.

HADR é apenas uma funcionalidade de replicação. Ele tem sem deteção de falhas e sem instalações de obtenção de controlo ou da ativação pós-falha automática. Uma obtenção de controlo ou a transferência para o servidor em espera deve ser iniciada manualmente por um administrador de banco de dados. Para alcançar uma obtenção de controlo de automática e deteção de falhas, pode utilizar a funcionalidade de clustering Pacemaker de Linux. Pacemaker monitoriza as instâncias de servidor de base de dados de dois. Quando a instância de servidor de base de dados primária falha da parte, inicia Pacemaker uma *automática* obtenção de controlo de HADR pelo servidor em espera. Pacemaker também garante que o endereço IP virtual é atribuído para o novo servidor primário.

![Descrição geral de elevada disponibilidade do IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Ter SAP servidores de aplicações ligar à base de dados primária, tem um nome de anfitrião virtual e um endereço IP virtual. Em caso de uma ativação pós-falha, os servidores de aplicações SAP irão ligar à nova instância de base de dados primária. Num ambiente do Azure, um [Balanceador de carga do Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) é necessária para utilizar um endereço IP virtual da forma que é necessário para HADR do IBM Db2. 

Para ajudar a compreender como o IBM Db2 LUW com HADR e Pacemaker se encaixa numa configuração de sistema SAP elevada disponibilidade, a imagem seguinte apresenta uma visão geral de uma configuração de elevada disponibilidade de um sistema SAP com base na base de dados Db2 da IBM. Este artigo abrange apenas a IBM Db2, mas ele fornece referências para outros artigos sobre como configurar a outros componentes de um sistema SAP.

![Descrição geral do ambiente completo de elevada disponibilidade IBM DB2](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Descrição geral de alto nível das etapas necessárias
Para implementar uma configuração de IBM Db2, tem de seguir estes passos:

  + Planeje seu ambiente.
  + Implemente VMs.
  + Atualizar o SUSE Linux e configurar sistemas de ficheiros.
  + Instale e configure Pacemaker.
  + Instale [elevada disponibilidade NFS][nfs-ha].
  + Instale [ASCS/ERS num cluster separado][ascs-ha].
  + Instale a base de dados IBM Db2 com a opção de Distributed/de alta disponibilidade (SWPM).
  + Instalar e criar um nó de base de dados secundária e a instância e configurar HADR.
  + Certifique-se que HADR está a funcionar.
  + Aplica a configuração de Pacemaker ao controle IBM Db2.
  + Configure o Balanceador de carga do Azure.
  + Instalação principal e servidores de aplicações de caixa de diálogo.
  + Verifique e adaptar-se a configuração de servidores de aplicações SAP.
  + Efetue ativação pós-falha e testes de obtenção de controlo.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planear a infraestrutura do Azure para alojar o IBM Db2 LUW com HADR

Conclua o processo de planejamento antes de executar a implantação. Planejamento baseia-se a base para a implementação de uma configuração de Db2 com HADR no Azure. Elementos principais que têm de ser parte do planeamento de IMB Db2 LUW (parte da base de dados do ambiente de SAP) estão listados na tabela a seguir:

| Tópico | Breve descrição |
| --- | --- |
| Definir os grupos de recursos do Azure | Grupos de recursos onde implementar VM, o VNet, o Balanceador de carga do Azure e outros recursos. Pode ser a nova ou existente. |
| Rede virtual / definição de sub-rede | Onde estão a ser implementadas VMs para IBM Db2 e o Balanceador de carga do Azure. Pode ser criado recentemente ou existentes. |
| Máquinas virtuais com IBM Db2 LUW | Tamanho da VM, armazenamento, rede, endereço IP. |
| Nome de anfitrião virtual e de IP virtual para a base de dados IBM Db2| O virtual IP ou nome de anfitrião que é utilizado para ligação de servidores de aplicações SAP. **db-virt-hostname**, **db-virt-ip**. |
| A delimitação por barreiras do Azure | A delimitação por barreiras do Azure ou SBD a delimitação por barreiras (altamente recomendado). Método para evitar situações de cérebro de divisão é impedido. |
| SBD VM | Tamanho da máquina virtual SBD, armazenamento, rede. |
| Azure Load Balancer | Utilização do Basic ou Standard (recomendado), porta da base de dados Db2 (a nossa recomendação 62500) de sonda **porta de sonda**. |
| Resolução de nomes| Como a resolução de nomes funciona no ambiente. Serviço DNS é altamente recomendado. Arquivo local hosts pode ser utilizado. |
    
Para obter mais informações sobre Pacemaker do Linux no Azure, consulte [configurar Pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Implementação no SUSE Linux

O agente de recursos para o IBM Db2 LUW está incluído no SUSE Linux Enterprise Server para aplicações SAP. Para a configuração descrita neste documento, tem de utilizar o servidor do SUSE Linux para aplicações SAP. O Azure Marketplace contém uma imagem para o SUSE Enterprise Server para 12 de aplicações SAP que pode utilizar para implementar novas máquinas virtuais do Azure. Lembre-se de que os vários modelos de suporte ou serviço que são oferecidos pelo SUSE através do Azure Marketplace ao escolher uma imagem de VM no mercado VM do Azure. 

### <a name="hosts-dns-updates"></a>Hosts: Atualizações de DNS
Faça uma lista de todos os nomes de anfitrião, incluindo nomes de anfitrião virtual e atualizar seus servidores DNS para permitir o endereço IP adequado para a resolução de nome de anfitrião. Se um servidor DNS não existe ou não é possível atualizar e criação de entradas DNS, terá de utilizar os ficheiros do anfitrião local das VMs individuais que estão a participar neste cenário. Se estiver a utilizar entradas de ficheiros do anfitrião, certifique-se de que as entradas são aplicadas a todas as VMs no ambiente do sistema SAP. No entanto, recomendamos que utilize o seu DNS que, o ideal é que se estende para o Azure


### <a name="manual-deployment"></a>Implementação manual

Certifique-se de que o sistema operacional selecionado é suportado pela IBM/SAP para o IBM Db2 LUW. A lista de versões de SO suportadas VMs do Azure e versões de Db2 está disponível na nota SAP [1928533]. A lista de versões de SO por individual Db2 versão está disponível na matriz de disponibilidade do produto de SAP. É altamente recomendável um mínimo de SLES 12 SP3 devido aos melhoramentos de desempenho relacionados com o Azure nisso ou posterior versões do SUSE Linux.

1. Crie ou selecione um grupo de recursos.
1. Crie ou selecione uma rede virtual e uma sub-rede.
1. Criar um conjunto de disponibilidade do Azure ou implementar uma zona de disponibilidade.
    + Para o conjunto de disponibilidade, defina os domínios de atualização máxima para 2.
1. Crie Máquina Virtual 1.
    + Utilize o SLES para a imagem SAP no Azure Marketplace.
    + Selecione o conjunto de disponibilidade do Azure que criou no passo 3, ou zona de disponibilidade.
1.  Crie a Máquina Virtual 2.
    + Utilize o SLES para a imagem SAP no Azure Marketplace.
    + Selecione o conjunto de disponibilidade do Azure no criada no passo 3, ou selecione a zona de disponibilidade (não a mesma zona como no passo 3).
1. Adicionar discos de dados para as VMs e, em seguida, verifique a recomendação de uma configuração de sistema de ficheiros no artigo [implementação de DBMS de máquinas virtuais do Azure do IBM Db2 para a carga de trabalho do SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster Pacemaker
    
Para criar um cluster de Pacemaker básico para este servidor IBM Db2, veja [configurar Pacemaker no SUSE Linux Enterprise Server no Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Instalar o ambiente LUW de Db2 da IBM e SAP

Antes de iniciar a instalação de um ambiente SAP com base no IBM Db2 LUW, reveja a seguinte documentação:

+ Documentação do Azure
+ Documentação do SAP
+ Documentação da IBM

São fornecidas hiperligações para esta documentação na secção introdução deste artigo.

Verifique os manuais de instalação SAP sobre a instalação de aplicativos baseados em NetWeaver no IBM Db2 LUW.

Pode encontrar os guias no portal do SAP ajudar com o [localizador de guia de instalação SAP][sap-instfind].

Pode reduzir o número de guias apresentado no portal do definindo os seguintes filtros:

- Quero: "Instalar um novo sistema"
- Meu banco de dados: "IBM Db2 para Linux, Unix e Windows"
- Filtros adicionais para as versões SAP NetWeaver, configuração de pilha ou sistema operativo

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Dicas de instalação para a configuração IBM Db2 LUW com HADR

Para configurar a instância de base de dados primária IBM Db2 LUW:

- Utilize a opção distribuída ou de elevada disponibilidade.
- Instale a instância do SAP ASCS/ERS e base de dados.
- Faça uma cópia de segurança da base de dados recentemente instalado.


> [!IMPORTANT] 
> Anote a "porta de comunicação de base de dados" que é definida durante a instalação. Tem de ser o mesmo número de porta para ambas as instâncias de base de dados

Para configurar o servidor de base de dados do modo de espera ao utilizar o procedimento de cópia de sistema homogénea do SAP, execute estes passos:

1. Selecione o **cópia do sistema** opção > **sistemas de destino** > **distribuídas** > **instância de base de dados**.
1. Como um método de cópia, selecione **sistema homogénea** para que possa utilizar cópias de segurança para restaurar uma cópia de segurança na instância do servidor em espera.
1. Quando atingir o passo de saída para restaurar a base de dados para cópia de sistema homogénea, sair do instalador. Restaure a base de dados a partir de uma cópia de segurança do anfitrião principal. Todas as fases subsequentes instalação já foram executadas no servidor de base de dados primária.
1. Configure HADR para Db2 da IBM.

   > [!NOTE]
   > Para instalação e configuração específica para o Azure e Pacemaker: Durante o procedimento de instalação através do Gestor de aprovisionamento de Software SAP, existe uma questão explícita sobre a elevada disponibilidade para o IBM Db2 LUW:
   >+ Não selecione **IBM Db2 pureScale**.
   >+ Não selecione **instalar o IBM Tivoli System Automation para Multiplatforms**.
   >+ Não selecione **gerar arquivos de configuração de cluster**.

   Quando utilizar um dispositivo SBD para Linux Pacemaker, defina os seguintes parâmetros de Db2 HADR:
   + Duração da janela HADR ponto a ponto (segundos) (HADR_PEER_WINDOW) = 300  
   + O valor de tempo limite HADR (HADR_TIMEOUT) = 60

   Quando utiliza um agente de delimitação por barreiras Pacemaker do Azure, defina os seguintes parâmetros:
   + Duração da janela HADR ponto a ponto (segundos) (HADR_PEER_WINDOW) = 900  
   + O valor de tempo limite HADR (HADR_TIMEOUT) = 60

Recomendamos que os parâmetros anteriores, com base no teste inicial de obtenção de controlo/ativação pós-falha. É obrigatório que teste para a funcionalidade adequada de ativação pós-falha e a obtenção de controlo com estas definições de parâmetro. Como podem variar configurações individuais, os parâmetros podem necessitar de ajuste. 

> [!IMPORTANT]
> Específica ao IBM Db2 com configuração HADR com normal de inicialização: A instância de base de dados secundário ou em modo de espera deve estar em execução antes de iniciar a instância de base de dados primária.

Para fins de demonstração e os procedimentos descritos neste artigo, a base de dados for SID **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Verificação da IBM Db2 HADR
Depois de configurar HADR e o estado é o elemento de rede e ligado em nós principais e de reserva, execute a seguinte verificação:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Configuração de Pacemaker de Db2

Quando utiliza Pacemaker para ativação pós-falha em caso de falha de nó, terá de configurar suas instâncias de Db2 e Pacemaker em conformidade. Esta secção descreve este tipo de configuração.

Os seguintes itens são prefixados com ambos:

- **[A]**: Aplicável a todos os nós
- **[1]**: Aplicável apenas ao nó 1 
- **[2]**: Aplicável apenas ao nó 2

**[A]**  Pré-requisitos para a configuração de Pacemaker:
1. Encerre ambos os servidores de base de dados com o utilizador db2\<sid > com db2stop.
1. Altere o ambiente de shell para db2\<sid > usuário */bin/ksh*. Recomendamos que utilize a ferramenta de Yast. 


### <a name="pacemaker-configuration"></a>Configuração de pacemaker

**[1]**  Configuração Pacemaker do IBM Db2 HADR específicas:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  IBM Db2 criar recursos:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]**  Iniciar IBM Db2 recursos:
* Coloque Pacemaker do modo de manutenção.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Certifique-se de que o estado do cluster está OK, e se todos os recursos foram iniciados. Não é importante nó para que os recursos estão em execução.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nós configurados
# <a name="5-resources-configured"></a>5 recursos configurados

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Lista completa de recursos:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd    (stonith:external/sbd): Introdução azibmdb02
#  <a name="resource-group-gipdb2ptrptr"></a>Grupo de recursos: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Introdução azibmdb02
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat: nada):      Introdução azibmdb02
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>Conjunto de mestre/subordinado: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Formas mestres: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Subordinados: [azibmdb01]
</pre>

> [!IMPORTANT]
> Tem de gerir o Pacemaker instância em cluster de Db2 usando as ferramentas de Pacemaker. Se utilizar comandos de db2 como db2stop, Pacemaker Deteta a ação como uma falha de recurso. Se estiver a efetuar a manutenção, pode colocar os nós ou a recursos no modo de manutenção. Pacemaker suspende a monitorização de recursos e, em seguida, pode utilizar os comandos de administração de normal db2.


### <a name="configure-azure-load-balancer"></a>Configurar o Balanceador de Carga do Azure
Para configurar o Balanceador de carga do Azure, recomendamos que utilize o [SKU de Balanceador de carga Standard do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) e, em seguida, efetue o seguinte:

1. Crie um conjunto IP Front-end:

   a. No portal do Azure, abra o Balanceador de carga do Azure, selecione **conjunto IP de front-end**e, em seguida, selecione **Add**.

   b. Introduza o nome do novo conjunto IP Front-end (por exemplo, **Db2-connection**).

   c. Definir o **atribuição** para **estático**e introduza o endereço IP **Virtual IP** definidos no início.

   d. Selecione **OK**.

   e. Depois de criar o novo conjunto IP Front-end, tome nota do endereço IP do conjunto.

1. Crie um conjunto de back-end:

   a. No portal do Azure, abra o Balanceador de carga do Azure, selecione **conjuntos de back-end**e, em seguida, selecione **Add**.

   b. Introduza o nome do novo conjunto de back-end (por exemplo, **Db2-back-end**).

   c. Selecione **adicionar uma máquina virtual**.

   d. Selecione o conjunto de disponibilidade ou as máquinas virtuais que aloja a base de dados do IBM Db2 criada no passo anterior.

   e. Selecione as máquinas virtuais do cluster IBM Db2.

   f. Selecione **OK**.

1. Crie uma sonda de estado de funcionamento:

   a. No portal do Azure, abra o Balanceador de carga do Azure, selecione **sondas de estado de funcionamento**e selecione **Add**.

   b. Introduza o nome da sonda de estado de funcionamento novo (por exemplo, **Db2 hp**).

   c. Selecione **TCP** como o protocolo e porta **62500**. Manter o **intervalo** valor definido como **5**e manter o **limiar de mau estado de funcionamento** valor definido como **2**.

   d. Selecione **OK**.

1. Crie as regras de balanceamento de carga:

   a. No portal do Azure, abra o Balanceador de carga do Azure, selecione **regras de balanceamento de carga**e, em seguida, selecione **Add**.

   b. Introduza o nome da nova regra de Balanceador de carga (por exemplo, **Db2 SID**).

   c. Selecione o endereço IP Front-end, o conjunto de back-end e a sonda de estado de funcionamento que criou anteriormente (por exemplo, **Db2-front-end**).

   d. Manter o **protocolo** definida como **TCP**e introduza a porta *porta de comunicação de base de dados*.

   e. Aumentar a **tempo limite de inatividade** como 30 minutos.

   f. Certifique-se de que **ativar o IP flutuante**.

   g. Selecione **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Efetuar alterações a perfis SAP para utilizar o virtual IP para a ligação
Para ligar à instância principal da configuração HADR, o SAP camada de aplicativo tem de utilizar o endereço IP virtual que definidos e configurados para o Balanceador de carga do Azure. As seguintes alterações são necessárias:

/sapmnt/\<SID >/perfil/padrão. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalação principal e servidores de aplicações de caixa de diálogo

Quando instalar o principal e servidores de aplicações de caixa de diálogo em relação a uma configuração de Db2 HADR, utilize o anfitrião virtual dê o nome que escolheu para a configuração. 

Se efetuar a instalação antes que criou a configuração de Db2 HADR, faça as alterações, conforme descrito na secção anterior e, da seguinte forma para pilhas de Java de SAP.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Verifique de sistemas de pilha ABAP + Java ou Java URL de JDBC

Utilize a ferramenta de configuração de J2EE para verificar ou atualizar o URL do JDBC. Como a ferramenta de configuração do J2EE é uma ferramenta gráfica, tem de ter X servidor instalado:
 
1. Inicie sessão no servidor de aplicações principal da instância do J2EE e execute:
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1. No quadro do esquerda, escolha **store segurança**.
1. Na estrutura de direito, escolha o jdbc/conjunto chave / \<SAPSID>/url.
1. Altere o nome de anfitrião no JDBC URL para o nome de anfitrião virtual.
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1. Selecione **adicionar**.
1. Para guardar as alterações, selecione o ícone de disco no canto superior esquerdo.
1. Feche a ferramenta de configuração.
1. Reinicie a instância de Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configurar o arquivamento de registo para a configuração HADR
Para configurar o registo de Db2 arquivamento para a configuração HADR, recomendamos que configure a primária e a base de dados em modo de espera ter a capacidade de recuperação automática de registos de todos os locais de arquivo de log. A base de dados principal e de reserva tem de ser capaz de recuperar os ficheiros de arquivo de log de todos os locais de arquivo de log para que qualquer um da base de dados instâncias podem arquivar os ficheiros de registo. 

O arquivo de log é executada apenas pela base de dados primário. Se alterar as funções HADR dos servidores de base de dados ou se ocorrer uma falha, a nova base de dados principal é responsável por arquivo de log. Se tiver configurado a vários locais de arquivo de log, os registos podem ser arquivados duas vezes. Em caso de um catch-up local ou remoto, também poderá ter de copiar manualmente os registos arquivados a partir do servidor principal antigo para a localização do registo ativas do novo servidor primário.

Recomendamos configurar uma partilha NFS comuns em que os registos são escritos de ambos os nós. A partilha NFS tem de ser de elevada disponibilidade. 

Pode utilizar partilhas NFS de elevada disponibilidade existentes para transportes ou um diretório de perfil. Para obter mais informações, consulte:

- [Disponibilidade elevada para NFS nas VMs do Azure no SUSE Linux Enterprise Server][nfs-ha] 
- [Elevada disponibilidade para SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com o NetApp serviço ficheiros do Azure para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Os ficheiros do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (para criar partilhas NFS)


## <a name="test-the-cluster-setup"></a>Teste a configuração de cluster

Esta secção descreve como pode testar a configuração de Db2 HADR. *Cada teste parte do princípio de que estiver conectado como raiz do utilizador* e a primária IBM Db2 está em execução no *azibmdb01* máquina virtual.

O estado inicial para todos os casos de teste é explicado aqui: (crm_mon - r ou crm status)

- **status de CRM** é um instantâneo do Estado de Pacemaker no momento da execução 
- **crm_mon - r** é saída contínua do Estado de Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

O estado original num sistema SAP está documentado na transação DBACOCKPIT > configuração > Descrição geral, conforme mostrado na imagem seguinte:

![DBACockpit - migração de pré-instalação](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Obtenção de controlo de teste do IBM Db2


> [!IMPORTANT] 
> Antes de começar o teste, certifique-se de que:
> * Pacemaker não tem quaisquer ações falhadas (status de crm).
> * Não há nenhuma restrição de localização (sobras do teste de migração)
> * A sincronização do IBM Db2 HADR está a funcionar. Contacte o utilizador db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migre o nó que está a executar a base de dados de Db2 primário executando o seguinte comando:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Após a migração estiver concluída, a saída de status de crm é semelhante a:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

O estado original num sistema SAP está documentado na transação DBACOCKPIT > configuração > Descrição geral, conforme mostrado na imagem seguinte:

![DBACockpit - após migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Migração de recursos com "Migrar recursos de crm" cria as restrições de localização. Restrições de localização devem ser eliminadas. Se a restrições de localização não são eliminadas, o recurso não é possível efetuar a reativação pós-falha ou pode experimentar o takeovers indesejados. 

Migrar o recurso de volta ao *azibmdb01* e desmarque as restrições de localização
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **Migrar recursos de CRM \<res_name > <host>:** Cria as restrições de localização e pode causar problemas de obtenção de controlo
- **recursos de CRM claro \<res_name >**: Limpa a restrições de localização
- **limpeza de recursos de CRM \<res_name >**: Limpa todos os erros do recurso

### <a name="test-the-fencing-agent"></a>O agente de delimitação por barreiras de teste

Neste caso, podemos testar SBD delimitação por barreiras, que recomendamos que faz quando usar o SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Nó de cluster *azibmdb01* deve ser reiniciado. Vai ser movida para a função HADR primária IBM Db2 *azibmdb02*. Quando *azibmdb01* é novamente online, o Db2 instância irá mover a função de uma instância de base de dados secundária. 

Se o serviço de Pacemaker não for iniciado automaticamente no primeiro reinicializado principal, certifique-se de que começá-lo manualmente com:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testar uma obtenção de controlo manual

Pode testar uma obtenção de controlo manual, parando o serviço de Pacemaker no *azibmdb01* nó:
<pre><code>service pacemaker stop</code></pre>

Estado no *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Após a ativação pós-falha, pode iniciar o serviço novamente *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Interromper o processo de Db2 no nó que executa a base de dados primária HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

A instância de Db2 vai falhar e Pacemaker irão comunicar o estado a seguir:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker irá reiniciar a instância de base de dados primária Db2 no mesmo nó, ou ele irá efetuar a ativação pós-falha para o nó que está executando a instância de base de dados secundária e é comunicado um erro.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Interromper o processo de Db2 no nó que executa a instância de base de dados secundária

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

O nó obtém em falha declarado e comunicado um erro
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

A instância de Db2 obtém reiniciada na função secundária que tinha atribuído antes.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Parar DB via db2stop força no nó que executa a instância de base de dados primária HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Como utilizador db2\<sid > executar força db2stop de comando:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Falha detetada
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

A instância de base de dados secundária de Db2 HADR tem promovida para a função primária
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>VM entrar em pane ao reiniciar no nó que executa a instância de base de dados primária HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker irá promover a instância secundária para a função de instância primária. A instância principal antiga irá mudar para a função secundária após a VM e todos os serviços sejam totalmente restaurados depois de reiniciar a VM:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>A VM que executa a instância de base de dados primária HADR com "Parar" de falhas

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Nesse caso, Pacemaker irá detetar que o nó que está a executar a instância de base de dados primária não está a responder.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

a próxima etapa é verificar a existência de um *dividir cérebro* situação. Depois do nó operacional determinou que o nó que a última execução a instância de base de dados primária está desativado, é executada uma ativação pós-falha de recursos.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


Em caso de um "halting" do nó, o nó com falha tem de ser reiniciado através de ferramentas de gestão do Azure (no portal do Azure, PowerShell ou a CLI do Azure). Depois do nó com defeito esteja novamente online, este começa a instância de Db2 para a função secundária.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Passos Seguintes
- [Arquitetura de elevada disponibilidade e cenários para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configurar Pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

