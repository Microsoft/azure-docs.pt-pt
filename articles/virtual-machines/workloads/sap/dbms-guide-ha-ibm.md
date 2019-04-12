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
ms.openlocfilehash: d47c64fb8b148e39eab09a33c3241ac7dd3ea537
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502132"
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

IBM Db2 LUW (Linux, Unix e Windows) no [configuração HADR](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) consistem num nó que execute uma instância de base de dados primária e, pelo menos, um nó que execute uma instância de base de dados secundária. Replicar as alterações para a instância de base de dados primária obter instância da base de dados secundária forma síncrona ou assíncrona, depende de sua configuração. 

Este artigo descreve como implementar e configurar as máquinas virtuais, instalar o framework de cluster, instale e configure IBM Db2 LUW na configuração de HADR. O artigo não explicam como instalar e configurar o IBM Db2 LUW na instalação de software HADR ou SAP. Referências a SAP e IBM manuais de instalação são fornecidas para alcançar essas tarefas. Foco está em partes que são específicas para o ambiente do Azure. 

Versões suportadas do IBM Db2 são 10.5 e superior, conforme documentado no SAP note #[1928533].

Leia as seguintes notas SAP e documentação primeiro antes de atingir uma instalação:

| Nota SAP | Descrição |
| --- | --- |
| [1928533] | Aplicações de SAP no Azure: Produtos suportados e tipos de VM do Azure |
| [2015553] | SAP no Microsoft Azure: Pré-requisitos de suporte |
| [2178632] | Chave de métricas de monitorização para o SAP no Microsoft Azure |
| [2191498] | SAP no Linux com o Azure: Melhorada a monitorização |
| [2243692] | Linux no Microsoft Azure (IaaS) VM: Problemas de licença do SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Observações de instalação |
| [1999351] |Resolução de problemas avançada a monitorização do Azure para SAP |
| [2233094] |DB6: Aplicações SAP no Azure com o IBM Db2 para Linux, UNIX e Windows - informações adicionais |
| [1612105] |DB6: FAQ sobre recuperação de desastres de elevada disponibilidade para Db2 (HADR) |


| Documentação | 
| --- |
| [WIKI de Comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas de SAP necessária para Linux |
| [Máquinas de virtuais de planeamento e implementação para o SAP no Linux do Azure] [ planning-guide] guia |
| [Implementação de máquinas virtuais do Azure para SAP no Linux] [ deployment-guide] (Este artigo) |
| [Implementação de DBMS de máquinas virtuais do Azure para SAP no Linux] [ dbms-guide] guia |
| [Carga de trabalho SAP na lista de verificação de planejamento e implantação do Azure][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server para o SAP aplicativos 12 SP3 melhores práticas guias][sles-for-sap-bp] |
| [SUSE Linux Enterprise elevada disponibilidade extensão 12 SP3][sles-ha-guide] |
| [Implementação de DBMS de máquinas virtuais do Azure do IBM Db2 para a carga de trabalho do SAP][dbms-db2] |
| [Recuperação de desastres do IBM Db2 elevada disponibilidade 11.1][db2-hadr-11.1] |
| [Recuperação de desastres do IBM Db2 elevada disponibilidade 10.5 de R][db2-hadr-10.5] |

## <a name="overview"></a>Descrição geral
Para assegurar elevada disponibilidade, IBM Db2 LUW com HADR está instalado, pelo menos, duas máquinas virtuais do Azure, que são implementadas num [conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ou na [Azure Availability Zones](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). O gráfico abaixo mostra uma configuração de servidor de base de dados duas VMs do Azure. Tanto o servidor de base de dados as VMs do Azure tem seu próprio armazenamento ligado e estão em execução. No HADR, uma instância de base de dados em uma das VMs do Azure com a função da instância primária. Todos os clientes estão ligados a esta instância primária. Todas as alterações em transações de base de dados são mantidas localmente no registo de transações de Db2. Como os registros de log de transação são mantidos localmente, os registos são transferidos através de TCP/IP para a instância de base de dados, no segundo servidor de base de dados, o servidor em espera ou instância em espera. A instância em espera atualiza a base de dados local, ao implementar para a frente a transação transferida registros de log. Portanto, o servidor em espera é mantido sincronizado com o servidor primário.

HADR é apenas uma funcionalidade de replicação. Ele tem sem deteção de falhas e sem instalações de obtenção de controlo ou da ativação pós-falha automática. Uma obtenção de controlo ou a transferência para o modo de espera deve ser iniciada manualmente por um administrador de banco de dados. Para alcançar uma obtenção de controlo de automática e deteção de falhas, pode utilizar a funcionalidade de clustering de Pacemaker de Linux. Pacemaker monitoriza as base de dados de dois servidores/instâncias. Quando a servidor/instância de base de dados primária falha da parte, inicia Pacemaker uma **automática** obtenção de controlo de HADR pelo servidor em espera e também garante que o endereço IP virtual é atribuído para o novo servidor primário.

![Descrição geral do IBM Db2 elevada disponibilidade](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Para SAP servidores de aplicações para ligar à base de dados primária precisam de um nome de anfitrião virtual e um endereço IP virtual. No evento de uma ativação pós-falha, os servidores de aplicações SAP irão ligar à nova instância de base de dados primária. Num ambiente do Azure, um [Balanceador de carga do Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) é necessária para utilizar um endereço IP virtual da maneira necessária para HADR do IBM Db2. 

Para compreender totalmente, como o IBM Db2 LUW com HADR e Pacemaker se encaixa numa configuração de sistema SAP elevada disponibilidade, a imagem seguinte apresenta uma visão geral de uma configuração de elevada disponibilidade de um sistema SAP com base na base de dados Db2 da IBM. Este artigo abrange apenas a IBM Db2 e faz referência a outros artigos sobre como configurar a outros componentes do sistema SAP.

![Descrição geral do ambiente IBM DB2 HA completo](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-steps-needed"></a>Visão geral dos passos necessários
Para poder implementar uma configuração de IBM Db2, estes passos têm de ser abordadas:

  + Plano de ambiente
  + Implementar VMs
  + Atualizar o SUSE Linux e configurar sistemas de ficheiros
  + Instalar e configurar Pacemaker
  + Instalar [NFS de elevada disponibilidade][nfs-ha]
  + Instalar [ASCS/ERS num cluster separado][ascs-ha] 
  + Instalar a base de dados IBM Db2 com a opção de Distributed/de alta disponibilidade (SWPM)
  + Instalação/criar o nó de base de dados secundária e a instância e configurar HADR
  + Confirme que HADR está a funcionar
  + Aplicar a configuração de Pacemaker ao controle IBM Db2
  + Configurar o Balanceador de Carga do Azure 
  + Instalar primário + diálogo servidores de aplicações
  + Configuração de verificação/adaptar para servidores de aplicações SAP
  + Execute uma ativação pós-falha / testes de obtenção de controlo



## <a name="planning-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planear a infraestrutura do Azure para alojar o IBM Db2 LUW com HADR

Percorra o planejamento da antes de executar a implantação. Trate de criar a base para a implementação de uma configuração de Db2 com HADR no Azure. Elementos principais que têm de ser parte do planeamento de IMB Db2 LUW (parte da base de dados do ambiente de SAP).

| Tópico | Breve descrição |
| --- | --- |
| Definir grupos de recursos do Azure | Grupos de recursos onde implementar VM, o VNet, o Balanceador de carga do Azure e outros recursos. Pode ser novo ou existente |
| Rede virtual / definição de sub-rede | Onde as VMs para o IBM Db2 e o Balanceador de carga do Azure estão a obter a serem implantados. Pode ser criado recentemente ou existente |
| Máquinas virtuais com IBM Db2 LUW | VM size, storage, networking, IP address |
| Nome de anfitrião virtual & IP virtual para a base de dados IBM Db2| Virtual IP/nome de anfitrião que é utilizada para ligação de servidores de aplicações SAP. **db-virt-hostname**, **db-virt-ip** |
| A delimitação por barreiras do Azure | A delimitação por barreiras do Azure ou SBD a delimitação por barreiras (altamente recomendado). Método para evitar situações de cérebro de divisão é evitado |
| SBD VM | Tamanho da Máquina Virtual de SBD, armazenamento, rede |
| Azure Load Balancer | Utilização do Basic ou Standard (recomendado), porta da base de dados Db2 (a nossa recomendação 62500) de sonda **porta de sonda** |
| Resolução de nomes| Como a resolução de nomes funciona no ambiente. Serviço DNS é altamente recomendado. Arquivo local hosts pode ser utilizado |
    
Obter mais detalhes sobre a utilização de Pacemaker do Linux no Azure podem ser encontrados nestes artigos:

- [Como configurar Pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)



## <a name="deployment-on-suse-linux"></a>Implementação no SUSE Linux

O agente de recursos para o IBM Db2 LUW está incluído no SUSE Linux Enterprise Server para aplicações SAP. Para a configuração descrita neste documento, é obrigatório que use o servidor do SUSE Linux para aplicações SAP. O Azure Marketplace contém uma imagem para o SUSE Enterprise Server para 12 de aplicações SAP que pode utilizar para implementar novas máquinas virtuais do Azure. Lembre-se de que os modelos de diferentes/serviço de suporte oferecido pelo SUSE pelas galerias do Azure, ao escolher a imagem de VM na Galeria VM do Azure. 

### <a name="hosts---dns-updates"></a>Anfitriões - atualizações DNS
Fazer uma lista de todos os nomes de anfitrião incluindo nomes de anfitrião virtual e atualize os servidores DNS para permitir o endereço IP adequado para a resolução de nome de anfitrião. No caso, um servidor DNS não existe ou não é possível atualizar e criar entradas DNS, precisa aproveitar os ficheiros de local hosts das VMs individuais que estão a participar neste cenário. No caso de entradas de ficheiros do anfitrião a utilizar, terá de certificar-se de que as entradas são aplicadas a todas as VMs no ambiente de sistema SAP. No entanto, recomenda-se utilizar o seu DNS que o ideal é que é expandido para o Azure


### <a name="manual-deployment"></a>Implementação manual

Certifique-se de que o sistema operacional selecionado é suportado pela IBM/SAP para o IBM Db2 LUW. A lista de versões de SO suportadas VMs do Azure e versões de Db2 está disponível na nota SAP [1928533]. A lista de versões de SO por versões de Db2 individuais está disponível na matriz de disponibilidade do produto de SAP. É altamente recomendável que um mínimo de SLES 12 SP3 devido a melhorias de desempenho relacionados do Azure neste ou posterior versões do SUSE Linux.

1. Criar/selecionar um grupo de recursos
2. Criar/selecionar uma rede virtual e sub-rede
3. Criar conjunto de disponibilidade do Azure ou implementar numa zona de disponibilidade
    + Disponibilidade definida - definir domínios de atualização máx. para dois
4. Crie Máquina Virtual 1.
    + Utilizar o SLES para a imagem SAP na galeria do Azure
    + Selecione o conjunto criado na zona de disponibilidade de 3 ou selecione do passo de disponibilidade do Azure
5.  Crie a Máquina Virtual 2.
    + Utilizar o SLES para a imagem SAP na galeria do Azure
    + Selecione o conjunto criado no passo 3 de disponibilidade do Azure. ou selecione a zona de disponibilidade – não a mesma zona como no passo 3.
6. Adicionar discos de dados para as VMs - Verifique a recomendação do programa de configuração de sistema de ficheiros no artigo [implementação de DBMS de máquinas virtuais do Azure do IBM Db2 para a carga de trabalho do SAP][dbms-db2]

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster Pacemaker
    
Siga os passos em [Pacemaker no SUSE Linux Enterprise Server no Azure a configurar][sles-pacemaker] para criar um cluster de Pacemaker básico para este servidor IBM Db2. 

## <a name="install-ibm-db2-luw-and-sap-environment"></a>Instalar o IBM Db2 LUW e o ambiente de SAP

Antes de iniciar a instalação de um ambiente SAP com base no IBM Db2 LUW, consulte (ligações fornecidas no início do artigo):

+ Documentação do Azure
+ Documentação do SAP
+ Documentação da IBM

Verifique o manual(s) de instalação do SAP sobre como instalar aplicações do NetWeaver com base no IBM Db2 LUW.

Pode obter indicações no SAP ajudar portal com o [localizador de guia de instalação de SAP][sap-instfind]

Pode filtrar a pesquisa para reduzir o número de guias disponíveis com a definir os filtros:

+ Quero: "Instalar um novo sistema"
+ Meu banco de dados: "IBM Db2 para Linux, Unix e Windows"
+ Filtros adicionais para as versões SAP Netweaver, configuração de pilha ou sistema operativo.

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Dicas de instalação para a configuração IBM Db2 LUW com HADR

Configure a instância de base de dados primária IBM Db2 LUW:

- Utilize a opção distribuída ou de elevada disponibilidade
- Instalar uma instância do SAP ASCS/ERS e base de dados
- Faça uma cópia de segurança da base de dados recentemente instalado


> [!IMPORTANT] 
> Anote a "porta de comunicação de base de dados" definido durante a instalação. Tem de ser o mesmo número de porta para ambas as instâncias de base de dados

Para configurar o servidor de base de dados do modo de espera utilizando o procedimento de cópia de sistema homogénea do SAP, execute estes passos:

  - Utilize a opção de sistema de cópia - sistemas - distribuídos - instância de base de dados de destino.
  - Como método de cópia, escolha Homogêneo de sistema de cópia, para que possa utilizar cópias de segurança para restaurar uma cópia de segurança no servidor/instância em espera
  - Quando atingir o passo de saída para restaurar a base de dados para cópia de sistema homogénea, sair do instalador. Restaure a base de dados a partir de uma cópia de segurança do anfitrião principal. Todas as fases subsequentes instalação já foram executadas no servidor de base de dados primária
- Configurar a HADR para IBM Db2

> [!NOTE]
> Instalação/configuração específica para o Azure e Pacemaker. Durante o procedimento de instalação através do Gestor de aprovisionamento de Software SAP, existe uma questão explícita de elevada disponibilidade para o IBM Db2 LUW:
>+ Não selecione pureScale IBM Db2
>+ Não selecione "instalar o IBM Tivoli sistema automatização para Multiplatforms
>+ Não selecione "Arquivos de configuração de cluster de gerar"

> [!NOTE]
>Quando utilizar um dispositivo SBD para Linux Pacemaker, defina os parâmetros de HADR de Db2
>+ Duração da janela HADR ponto a ponto (segundos) (HADR_PEER_WINDOW) = 300  
>+ O valor de tempo limite HADR (HADR_TIMEOUT) = 60

> [!NOTE]
>Com o agente de delimitação por barreiras de Pacemaker do Azure:
>+ Duração da janela HADR ponto a ponto (segundos) (HADR_PEER_WINDOW) = 900  
>+ O valor de tempo limite HADR (HADR_TIMEOUT) = 60

Parâmetros recomendados com base no teste inicial de obtenção de controlo/ativação pós-falha. É obrigatório para testar a funcionalidade adequada de obtenção de controlo e a ativação pós-falha com estas definições de parâmetro. Uma vez que as configurações individuais podem variar, esses parâmetros podem necessitar de ajuste. 

> [!IMPORTANT]
> Específicas para IBM Db2 na configuração de HADR com normal de inicialização - a instância de base de dados do modo de espera/secundário tem de estar em execução antes de poder iniciar a instância de base de dados primária.

Para fins de demonstração e os procedimentos documentados neste documento, a base de dados for SID **PTR**.

##### <a name="ibm-db2-hadr-check"></a>Verificação HADR IBM Db2
Uma vez que configurou HADR e de estado é o elemento de rede e ligado em nós principais e de reserva.

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

- **[A]**  - aplicáveis a todos os nós
- **[1]**  – apenas aplicável no nó 1 
- **[2]**  – apenas aplicável a nó 2.

<!-- Yast is a fixed term in Linux and not a spelling error -->

**[A]**  Pré-requisitos para a configuração de Pacemaker:
1. Encerre ambos os servidor de base de dados com o utilizador db2\<sid > com db2stop
2. Alterar o ambiente de shell para db2\<sid > usuário "/ bin/ksh" - recomendado que utilize a ferramenta de Yast 


### <a name="pacemaker-configuration"></a>Configuração de pacemaker:

**[1]**  Configuração Pacemaker específica do IBM Db2 HADR
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Recursos de criar o IBM Db2
<pre><code># Replace **bold strings** with your instance name db2sid, database SID and virtual IP address/Azure Load Balancer

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virutal IP - same as Azure Load Balancer IP
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

**[1]**  Recursos de iniciar o IBM Db2 - colocados Pacemaker do modo de manutenção
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante no nó que os recursos estão em execução.
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
> Gerir Pacemaker instância em cluster de Db2 deve ser feita por meio de ferramentas de Pacemaker. Utilizar comandos de db2 (como db2stop) será detectado pelo Pacemaker como falha de recurso. Em caso de manutenção, pode colocar os nós ou recursos para o modo de manutenção e Pacemaker irá suspender a monitorização de recursos e comandos de administração de normal db2 podem ser utilizados.


### <a name="configure-azure-load-balancer"></a>Configurar o Balanceador de Carga do Azure
É recomendado que utilize o [SKU de Balanceador de carga Standard do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. Configure o Balanceador de carga do Azure (através do portal do Azure). Primeiro, crie um conjunto IP de front-end:

   1. No portal do Azure, abra o Balanceador de carga do Azure, selecione **conjunto IP de front-end**e selecione **Add**.
   2. Introduza o nome do novo conjunto IP Front-end (por exemplo, **Db2-connection**).
   3. Definir o **atribuição** para **estático** e introduza o endereço IP **Virtual IP** definidos no início.
   4. Selecione **OK**.
   5. Depois de criar o novo conjunto IP Front-end, tome nota do endereço IP do conjunto.

2. Passo seguinte é criar um conjunto de back-end:

   1. No portal do Azure, abra o Balanceador de carga do Azure, selecione **conjuntos de back-end**e selecione **Add**.
   2. Introduza o nome do novo conjunto de back-end (por exemplo, **Db2-back-end**).
   3. Selecione **adicionar uma máquina virtual**.
   4. Selecione as máquinas virtuais/conjunto de disponibilidade que aloja a base de dados IBM Db2 criado na etapa 3.
   5. Selecione as máquinas virtuais do cluster IBM Db2.
   6. Selecione **OK**.

3. Terceiro passo é criar uma sonda de estado de funcionamento:

   1. No portal do Azure, abra o Balanceador de carga do Azure, selecione **sondas de estado de funcionamento**e selecione **Add**.
   2. Introduza o nome da sonda de estado de funcionamento novo (por exemplo, **Db2 hp**).
   3. Selecione **TCP** como o protocolo e porta **62500**. Manter o **intervalo** valor definido para 5 e o **limiar de mau estado de funcionamento** valor definido como 2.
   4. Selecione **OK**.

4. Crie as regras de balanceamento de carga:

   1. No portal do Azure, abra o Balanceador de carga do Azure, selecione **regras de balanceamento de carga**e selecione **Add**.
   2. Introduza o nome da nova regra de Balanceador de carga (por exemplo, **Db2 SID**).
   3. Selecione o endereço IP Front-end, o conjunto de back-end e a sonda de estado de funcionamento que criou anteriormente (por exemplo, **Db2-front-end**).
   4. Manter o **protocolo** definida como **TCP**e introduza a porta *porta de comunicação de base de dados*.
   5. Aumentar a **tempo limite de inatividade** como 30 minutos.
   6. Certifique-se de que **ativar o IP flutuante**.
   7. Selecione **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Efetuar alterações a perfis SAP para utilizar o virtual IP para a ligação
Camada de aplicação SAP tem de utilizar o endereço IP virtual definido e configurado para o Balanceador de carga do Azure ligar à instância principal da configuração HADR. As seguintes alterações são necessárias.

/sapmnt/\<SID >/perfil/padrão. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalação principal e o aplicativo de caixa de diálogo servidores

Quando instalar principal e servidores de aplicações de caixa de diálogo em relação a uma configuração de Db2 HADR, deve utilizar o nome de anfitrião virtual que escolheu para a configuração. 

No caso de efetuar a instalação antes de criar a configuração de Db2 HADR, terá de efetuar alterações, conforme descrito no parágrafo anterior e, da seguinte forma para pilhas de Java de SAP.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Verifique de sistemas de pilha ABAP + Java ou Java URL de JDBC

Utilize a ferramenta de configuração de J2EE para verificar ou atualizar o URL do JDBC. A ferramenta gráfica de é a ferramenta de configuração do J2EE, como resultado terá **X servidor** instalado:
 
1. Inicie sessão no servidor de aplicação principal da instância do J2EE e execute:
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
2. No quadro do esquerda, escolha o arquivo de segurança.
2. Na estrutura de direito, escolha o jdbc/conjuntochave/<SAPSID>/url.
2. Altere o nome de anfitrião no JDBC URL para o nome de anfitrião virtual <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
5. Escolha adicionar.
5. Para guardar as alterações, clique no ícone de disco no canto superior esquerdo.
5. Feche a ferramenta de configuração.
5. Reinicie a instância de Java.

## <a name="configuration-of-log-archiving-for-hadr-setup"></a>Configuração de arquivar os registos para a configuração de HADR
Para configurar o registo de Db2 arquivamento para a configuração HADR, recomendamos que configure a primária e a base de dados em modo de espera ter a capacidade de recuperação automática de registos de todos os locais de arquivo de log. A primária e a base de dados em modo de espera tem de ser capazes de recuperar os ficheiros de arquivo de log de todos os locais de arquivo de log para que qualquer um da base de dados instâncias podem arquivar os ficheiros de registo. 

O arquivo de log só é executada pela base de dados primário. Se alterar as funções HADR dos servidores de base de dados ou se ocorrer uma falha, a nova base de dados principal é responsável por arquivo de log. Se tiver definido locais de arquivo de log diferente, os registos podem ser arquivados duas vezes e, no caso de catch-up local ou remoto, poderá ter de copiar manualmente os registos arquivados a partir do servidor principal antigo para a localização do registo ativas do novo servidor primário.

Recomendamos configurar comuns partilha NFS, onde os registos são escritos de ambos os nós. NFS tem de ser de elevada disponibilidade. 

Pode utilizar o NFS de elevada disponibilidade existente, utilizado para transportes, diretório de perfil. Leitura:

- [Disponibilidade elevada para NFS nas VMs do Azure no SUSE Linux Enterprise Server][nfs-ha] 
- [Elevada disponibilidade para SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com o NetApp serviço ficheiros do Azure para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) sobre como utilizar [Azure NetApp ficheiros](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) para criar partilhas NFS


## <a name="test-the-cluster-setup"></a>Teste a configuração de cluster

Esta secção descreve como pode testar a configuração de Db2 HADR. **Cada teste parte do princípio de que estiver conectado como raiz do utilizador** e o IBM Db2 primária está em execução no **azibmdb01** máquina virtual.

O estado inicial para todos os casos de teste é explicado aqui: (crm_mon - r ou crm status)

- **status de CRM** é um Estado do instantâneo Pacemaker no momento da execução 
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

O estado original num sistema SAP está documentado na transação DBACOCKPIT--> configuração--> Descrição geral, como:

![DBACockpit - migração de pré-instalação](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Obtenção de controlo de teste do IBM Db2.


> [!IMPORTANT] 
> Antes de começar o teste, certifique-se de que Pacemaker não tem quaisquer ações falhadas (status de crm) e não há nenhuma restrição de localização (sobras do teste de migração) e a sincronização do IBM Db2 HADR está a funcionar. Verifique junto com o utilizador db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migre o nó a executar a base de dados de Db2 primário executando o seguinte comando:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Depois de fazer a migração, a saída de status de crm é semelhante a:
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

O estado original num sistema SAP está documentado na transação DBACOCKPIT--> configuração--> Descrição geral, como: ![DBACockpit - após migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Migração de recursos com "Migrar recursos de crm" cria as restrições de localização. Restrições de localização devem ser eliminadas. Se a restrições de localização não são eliminadas, em seguida, o recurso não é possível efetuar a reativação pós-falha ou pode experimentar o takeovers indesejados. 

Migrar recurso de volta ao **azibmdb01** e desmarque as restrições de localização
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>


- recursos de CRM migrar < res_name > <host> - cria as restrições de localização e pode causar problemas de obtenção de controlo
- limpar com recursos de CRM < res_name > - limpa as restrições de localização
- limpeza de recursos de CRM < res_name > - limpa todos os erros do recurso


### <a name="test-the-fencing-agent"></a>O agente de delimitação por barreiras de teste

Neste caso, podemos testar SBD delimitação por barreiras, o que é recomendada para utilização com SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Nó de cluster **azibmdb01** deve ser reiniciado. Função HADR primária IBM Db2 vai ser movida para **azibmdb02**. Quando **azibmdb01** é novamente online, o Db2 instância irá mover a função de uma instância de base de dados secundária. 

Para o caso em que o serviço de Pacemaker não for iniciado automaticamente no primeiro reinicializado principal, certifique-se para iniciá-lo manualmente com:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testar uma obtenção de controlo manual

Pode testar uma obtenção de controlo manual, parando o serviço de Pacemaker no **azibmdb01** nó:
<pre><code>service pacemaker stop</code></pre>

Estado no **azibmdb02**
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

Após a ativação pós-falha, pode iniciar o serviço novamente **azibmdb01**.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-db2-process-on-the-node-running-the-hadr-primary-database"></a>Finalizar processo Db2 no nó com a base de dados primária HADR

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

Pacemaker vai reiniciar a instância de base de dados primária Db2 no mesmo nó ou de ativação pós-falha para o nó a executar a base de dados secundário instanciado e é comunicado um erro.

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


### <a name="kill-db2-process-on-node-that-runs-the-secondary-database-instance"></a>Finalizar processo Db2 no nó que executa a instância de base de dados secundária

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

O get de instância de Db2 reiniciado para a função secundária que tinha atribuído antes de

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



### <a name="stop-db-via-db2stop-force-on-node-running-the-hadr-primary-database-instance"></a>Parar DB via db2stop força no nó a executar a instância de base de dados primária HADR

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


### <a name="crash-vm-with-restart-on-node-running-the-hadr-primary-database-instance"></a>VM de falha ao reiniciar no nó a executar a instância de base de dados primária HADR

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



### <a name="crash-the-vm-running-the-hadr-primary-database-instance-with-halt"></a>A VM a executar a instância de base de dados primária HADR com "Parar" de falhas

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Nesse caso, Pacemaker irá detetar que o nó a executar a instância de base de dados primária não está a responder.

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

a próxima etapa é verificar a existência de um **dividir cérebro** situação. Assim que o nó operacional estiver-se de que o nó, o que for executada pela última vez a instância de base de dados primária, está desativado, uma ativação pós-falha de recursos vai ser executado
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


No caso de "Parar" no nó, o nó com falha tem de ser reiniciado através de ferramentas de gestão do Azure (Portal, PowerShel, AzureCLI,...). O nó com falha irá iniciar a instância de Db2 para a função secundária, quando ele estiver novamente online.

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
Consulte esta documentação:

- [Arquitetura de elevada disponibilidade e cenários para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Como configurar Pacemaker no introduza do SUSE Linux
- pri
- Se o servidor no Azure] (https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

