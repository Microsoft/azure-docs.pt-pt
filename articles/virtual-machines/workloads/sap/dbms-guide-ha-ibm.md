---
title: Configurar IBM Db2 HADR em máquinas virtuais Azure (VMs) Microsoft Docs
description: Estabelecer uma elevada disponibilidade de IBM Db2 LUW em máquinas virtuais Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2020
ms.author: juergent
ms.openlocfilehash: 7d453fba37e62e8528ae7b4ea86d1604973b84a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87052001"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Alta disponibilidade de IBM Db2 LUW em VMs Azure no SUSE Linux Enterprise Server com Pacemaker

O IBM Db2 para Linux, UNIX e Windows (LUW) na configuração de alta disponibilidade e recuperação de [desastres (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) consiste num nó que executa uma instância de base de dados primária e pelo menos um nó que executa uma instância de base de dados secundária. As alterações à instância da base de dados primária são replicadas numa instância de base de dados secundária sincronizada ou assíncronia, dependendo da sua configuração. 

Este artigo descreve como implantar e configurar as máquinas virtuais Azure (VMs), instalar a estrutura do cluster e instalar o IBM Db2 LUW com configuração HADR. 

O artigo não abrange como instalar e configurar a IBM Db2 LUW com instalação de software HADR ou SAP. Para ajudá-lo a realizar estas tarefas, fornecemos referências aos manuais de instalação SAP e IBM. Este artigo centra-se em peças específicas para o ambiente Azure. 

As versões IBM Db2 suportadas são 10.5 e posteriormente, como documentado na nota SAP [1928533].

Antes de iniciar uma instalação, consulte as seguintes notas e documentação SAP:

| Nota SAP | Descrição |
| --- | --- |
| [1928533] | Aplicações SAP no Azure: Produtos suportados e tipos de VM Azure |
| [2015553] | SAP on Azure: Pré-requisitos de apoio |
| [2178632] | Principais métricas de monitorização do SAP em Azure |
| [2191498] | SAP on Linux com Azure: Monitorização melhorada |
| [2243692] | Linux on Azure (IaaS) VM: Emissões de licença sap |
| [1984787] | SUSE LINUX Enterprise Server 12: Notas de instalação |
| [1999351] | Resolução de problemas melhorou a monitorização do Azure para o SAP |
| [2233094] | DB6: Aplicações SAP no Azure que utilizam o IBM Db2 para Linux, UNIX e Windows - informações adicionais |
| [1612105] | DB6: FAQ em Db2 com HADR |


| Documentação | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Tem todas as notas SAP necessárias para Linux |
| [Azure Virtual Machines planejamento e implementação para SAP no][planning-guide] guia Linux |
| [Azure Virtual Machines implantação para SAP em Linux][deployment-guide] (este artigo) |
| [Implementação do sistema de gestão de bases de dados de máquinas virtuais Azure (DBMS) para SAP no][dbms-guide] guia Linux |
| [Carga de trabalho sap na lista de verificação de planeamento e implantação da Azure][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server para aplicações SAP 12 GUIAs de boas práticas SP4][sles-for-sap-bp] |
| [Extensão de alta disponibilidade da empresa SUSE Linux 12 SP4][sles-ha-guide] |
| [Implementação em IBM DB2 do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM DB2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Descrição geral
Para obter uma elevada disponibilidade, a IBM Db2 LUW com HADR é instalada em pelo menos duas máquinas virtuais Azure, que são implantadas num [conjunto de disponibilidades Azure](../../windows/tutorial-availability-sets.md) ou em [zonas de disponibilidade Azure.](./sap-ha-availability-zones.md) 

Os gráficos a seguir exibem uma configuração de dois VMs de servidor de base de dados Azure. Ambos os servidores de base de dados Azure VMs têm o seu próprio armazenamento anexado e estão em funcionamento. Em HADR, uma das caixas de dados de uma das VMs do Azure tem o papel da primeira instância. Todos os clientes estão ligados a esta primeira instância. Todas as alterações nas transações de base de dados são persistiu localmente no registo de transações Db2. À medida que os registos de registo de transações são persistidos localmente, os registos são transferidos via TCP/IP para a caixa de dados no segundo servidor de base de dados, no servidor de espera ou na instância de standby. A instância de espera atualiza a base de dados local, transmitindo os registos de registos de transações transferidos. Desta forma, o servidor de espera é mantido sincronizado com o servidor primário.

HADR é apenas uma funcionalidade de replicação. Não tem deteção de avarias nem instalações de aquisição automática ou de falha. Uma aquisição ou transferência para o servidor de espera deve ser iniciada manualmente por um administrador de base de dados. Para obter uma deteção automática de aquisição e falha, pode utilizar a função de clustering Linux Pacemaker. O Pacemaker monitoriza as duas instâncias do servidor de bases de dados. Quando a instância do servidor de base de dados primária falha, o Pacemaker inicia uma aquisição *automática* de HADR pelo servidor de espera. O Pacemaker também garante que o endereço IP virtual é atribuído ao novo servidor primário.

![Visão geral de alta disponibilidade da IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Para que os servidores de aplicações SAP se conectem à base de dados primária, precisa de um nome de anfitrião virtual e de um endereço IP virtual. Em caso de falha, os servidores de aplicações SAP ligar-se-ão a novas situações de base de dados primárias. Num ambiente Azure, um [equilibficador de carga Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) é obrigado a usar um endereço IP virtual da forma que é necessária para HADR da IBM Db2. 

Para ajudá-lo a entender completamente como a IBM Db2 LUW com HADR e Pacemaker se enquadra numa configuração altamente disponível do sistema SAP, a imagem a seguir apresenta uma visão geral de uma configuração altamente disponível de um sistema SAP baseado na base de dados IBM Db2. Este artigo abrange apenas a IBM Db2, mas fornece referências a outros artigos sobre como configurar outros componentes de um sistema SAP.

![IBM DB2 alta disponibilidade visão geral do ambiente](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Visão geral de alto nível das etapas necessárias
Para implementar uma configuração IBM Db2, é necessário seguir estes passos:

  + Planeie o seu ambiente.
  + Desdobre os VMs.
  + Atualize sistemas de ficheiros SUSE Linux e configurar.
  + Instale e configuure o Pacemaker.
  + Instale [NFS altamente disponíveis][nfs-ha].
  + Instale [as ASCS/ERS num cluster separado.][ascs-ha]
  + Instale a base de dados IBM Db2 com opção Distribuída/Alta Disponibilidade (SWPM).
  + Instale e crie um nó e instância de base de dados secundárias e configuure o HADR.
  + Confirme que o HADR está a funcionar.
  + Aplique a configuração do Pacemaker para controlar o IBM Db2.
  + Configurar o balançador de carga Azure.
  + Instale servidores de aplicações primários e de diálogo.
  + Verifique e adapte a configuração dos servidores de aplicações SAP.
  + Realizar testes de failover e aquisição.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Plano Azure infraestrutura para hospedar IBM Db2 LUW com HADR

Complete o processo de planeamento antes de executar a implementação. O planeamento constrói as bases para a implantação de uma configuração de Db2 com HADR em Azure. Os elementos-chave que precisam de fazer parte do planeamento do IMB Db2 LUW (parte da base de dados do ambiente SAP) estão listados no quadro seguinte:

| Tópico | Breve descrição |
| --- | --- |
| Definir grupos de recursos Azure | Grupos de recursos onde implementa VM, VNet, Azure Load Balancer e outros recursos. Pode ser existente ou novo. |
| Rede virtual / Definição de sub-rede | Onde estão a ser implantados VMs para a IBM Db2 e para o Azure Load Balancer. Pode ser existente ou recém-criado. |
| Máquinas virtuais que hospedam IBM Db2 LUW | Tamanho VM, armazenamento, rede, endereço IP. |
| Nome de anfitrião virtual e IP virtual para base de dados IBM Db2| O ip virtual ou o nome do anfitrião que é usado para a ligação de servidores de aplicações SAP. **db-virt-hostname,** **db-virt-ip**. |
| Esgrima azul | Esgrima azul ou esgrima SBD (altamente recomendado). Método para evitar situações cerebrais divididas. |
| SBD VM | Tamanho da máquina virtual SBD, armazenamento, rede. |
| Balanceador de Carga do Azure | Utilização de Porta-sonda Básica ou Padrão (recomendada), porta-sonda para base de dados Db2 (nossa recomendação 62500) **porta-sonda**. |
| Resolução de nomes| Como funciona a resolução de nomes no ambiente. O serviço DNS é altamente recomendado. O ficheiro de anfitriões locais pode ser usado. |
    
Para obter mais informações sobre o Pacemaker Linux em Azure, consulte [Configurar o Pacemaker no SUSE Linux Enterprise Server em Azure](./high-availability-guide-suse-pacemaker.md).

## <a name="deployment-on-suse-linux"></a>Implantação em SUSE Linux

O agente de recursos da IBM Db2 LUW está incluído no SUSE Linux Enterprise Server para aplicações SAP. Para a configuração descrita neste documento, deve utilizar o SUSE Linux Server para aplicações SAP. O Azure Marketplace contém uma imagem para SUSE Enterprise Server para aplicações SAP 12 que pode utilizar para implementar novas máquinas virtuais Azure. Esteja atento aos vários modelos de suporte ou serviço que são oferecidos pela SUSE através do Azure Marketplace quando escolher uma imagem VM no Azure VM Marketplace. 

### <a name="hosts-dns-updates"></a>Anfitriões: Atualizações dns
Faça uma lista de todos os nomes dos anfitriões, incluindo nomes de anfitriões virtuais, e atualize os seus servidores DNS para permitir o endereço IP adequado para a resolução do nome do anfitrião. Se um servidor DNS não existir ou não puder atualizar e criar entradas DNS, tem de utilizar os ficheiros de anfitriões locais dos VM individuais que estão a participar neste cenário. Se estiver a utilizar as entradas de ficheiros de anfitrião, certifique-se de que as entradas são aplicadas a todos os VMs no ambiente do sistema SAP. No entanto, recomendamos que use o seu DNS que, idealmente, se estende até Azure


### <a name="manual-deployment"></a>Implementação manual

Certifique-se de que o sistema operativo selecionado é suportado pela IBM/SAP para a IBM Db2 LUW. A lista de versões de SO suportadas para versões Azure VMs e Db2 está disponível na nota SAP [1928533]. A lista de lançamentos de SO por versão Db2 individual está disponível na Matriz de Disponibilidade de Produto SAP. Recomendamos vivamente um mínimo de SLES 12 SP4 devido a melhorias de desempenho relacionadas com o Azure nestas ou posteriores versões SUSE Linux.

1. Criar ou selecionar um grupo de recursos.
1. Criar ou selecionar uma rede virtual e uma sub-rede.
1. Crie um conjunto de disponibilidade de Azure ou implemente uma zona de disponibilidade.
    + Para o conjunto de disponibilidade, desave os domínios de atualização máximo para 2.
1. Criar máquina virtual 1.
    + Utilize SLES para imagem SAP no Mercado Azure.
    + Selecione o conjunto de disponibilidade Azure que criou no passo 3, ou selecione A Zona de Disponibilidade.
1.  Criar máquina virtual 2.
    + Utilize SLES para imagem SAP no Mercado Azure.
    + Selecione a disponibilidade de Azure definida em criado no passo 3, ou selecione Availability Zone (não a mesma zona que no passo 3).
1. Adicione discos de dados aos VMs e, em seguida, verifique a recomendação de uma configuração do sistema de ficheiros no artigo [IBM Db2 Azure Virtual Machines DBMS implantação para carga de trabalho SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster Pacemaker
    
Para criar um cluster pacemaker básico para este servidor IBM Db2, consulte [Configurar o Pacemaker no SUSE Linux Enterprise Server em Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Instale o ambiente IBM Db2 LUW e SAP

Antes de iniciar a instalação de um ambiente SAP baseado na IBM Db2 LUW, reveja a seguinte documentação:

+ Documentação do Azure
+ Documentação DO SAP
+ Documentação IBM

As ligações a esta documentação são fornecidas na secção introdutória deste artigo.

Consulte os manuais de instalação SAP sobre a instalação de aplicações baseadas na NetWeaver na IBM Db2 LUW.

Pode encontrar os guias no portal AJUDA SAP utilizando o Guia [de Instalação SAP.][sap-instfind]

Pode reduzir o número de guias apresentados no portal definindo os seguintes filtros:

- Quero: "Instalar um novo sistema"
- Minha base de dados: "IBM Db2 para Linux, Unix e Windows"
- Filtros adicionais para versões SAP NetWeaver, configuração de pilha ou sistema operativo

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Dicas de instalação para configurar IBM Db2 LUW com HADR

Para configurar a primeira instância da base de dados IBM Db2 LUW:

- Utilize a opção de alta disponibilidade ou distribuição.
- Instale a instância SAP ASCS/ERS e Base de Dados.
- Faça uma cópia da base de dados recentemente instalada.


> [!IMPORTANT] 
> Escreva a "porta de comunicação da base de dados" definida durante a instalação. Deve ser o mesmo número de porta para ambas as situações de base de dados

Para configurar o servidor de base de dados Standby utilizando o procedimento de cópia homogénea do sistema SAP, execute estas etapas:

1. Selecione a opção **de cópia do Sistema** > instância de base de **dados**distribuída por sistemas  >  **Distributed**  >  **Database instance**alvo.
1. Como método de cópia, selecione **Homogeneous System** para que possa utilizar a cópia de segurança para restaurar uma cópia de segurança na instância do servidor de espera.
1. Quando chegar ao passo de saída para restaurar a base de dados para cópia homogénea do sistema, saia do instalador. Restaurar a base de dados a partir de uma cópia de segurança do hospedeiro principal. Todas as fases de instalação subsequentes já foram executadas no servidor de base de dados primário.
1. Configurar HADR para a IBM Db2.

   > [!NOTE]
   > Para a instalação e configuração específicas do Azure e do Pacemaker: Durante o procedimento de instalação através do SAP Software Provisioning Manager, existe uma questão explícita sobre a elevada disponibilidade para a IBM Db2 LUW:
   >+ Não selecione **IBM Db2 pureScale**.
   >+ Não selecione **Instalar automatização do sistema IBM Tivoli para formas multiplataformas.**
   >+ Não **selecione Ficheiros de configuração de clusters**.

   Quando utilizar um dispositivo SBD para o Pacemaker Linux, descreva os seguintes parâmetros Db2 HADR:
   + Duração da janela do permôm HADR (segundos) (HADR_PEER_WINDOW) = 300  
   + Valor de tempo limite HADR (HADR_TIMEOUT) = 60

   Quando utilizar um agente de esgrima Azure Pacemaker, desa um conjunto dos seguintes parâmetros:
   + Duração da janela do permôm HADR (segundos) (HADR_PEER_WINDOW) = 900  
   + Valor de tempo limite HADR (HADR_TIMEOUT) = 60

Recomendamos os parâmetros anteriores baseados em testes iniciais de failover/takeover. É obrigatório testar a funcionalidade adequada de failover e aquisição com estas definições de parâmetros. Como as configurações individuais podem variar, os parâmetros podem exigir ajuste. 

> [!IMPORTANT]
> Específico da IBM Db2 com configuração HADR com arranque normal: A instância de base de dados secundária ou de espera deve estar a funcionar antes de poder iniciar a primeira fase da base de dados.

Para fins de demonstração e os procedimentos descritos neste artigo, a base de dados SID é **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Verificação DO DB2 HADR da IBM
Depois de configurar o HADR e o estado é PEER e CONNECTED nos nos nóns primários e de espera, efetue a seguinte verificação:

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



## <a name="db2-pacemaker-configuration"></a>Configuração do Pacemaker Db2

Quando utilizar o Pacemaker para falha automática em caso de falha no nó, tem de configurar as suas instâncias Db2 e o Pacemaker em conformidade. Esta secção descreve este tipo de configuração.

Os seguintes itens são pré-fixados com:

- **[A]**: Aplicável a todos os nós
- **[1]**: Aplicável apenas ao nó 1 
- **[2]**: Aplicável apenas ao nó 2

**[A]** Pré-requisitos para a configuração do Pacemaker:
1. Desligue ambos os servidores de base de dados com db2 do utilizador \<sid> com db2stop.
1. Mude o ambiente do reservatório para o utilizador db2 \<sid> para */bin/ksh*. Recomendamos que utilize a ferramenta Yast. 


### <a name="pacemaker-configuration"></a>Configuração do pacemaker

> [!IMPORTANT]
> Testes recentes revelaram situações, em que o netcat deixa de responder aos pedidos devido a atrasos e à sua limitação de manuseamento de apenas uma ligação. O recurso netcat deixa de ouvir os pedidos do balançador Azure Load e o IP flutuante fica indisponível.  
> Para os aglomerados pacemaker existentes, recomendamos no passado a substituição do netcat por socat. Atualmente recomendamos a utilização de um agente de recursos azure-lb, que faz parte dos agentes de recursos do pacote, com os seguintes requisitos de versão pacote:
> - Para o SLES 12 SP4/SP5, a versão deve ser pelo menos agentes de recursos-4.3.018.a7fb5035-3.30.1.  
> - Para sLES 15/15 SP1, a versão deve ser pelo menos agentes de recursos-4.3.0184.6ee15eb2-4.13.1.  
>
> Note que a mudança requer breves tempos de inatividade.  
> Para os clusters pacemaker existentes, se a configuração já foi alterada para usar socat como descrito no [Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128), não existe qualquer requisito para mudar imediatamente para o agente de recursos azure-lb.

**[1]** Configuração do Pacemaker específica da IBM Db2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** Criar recursos IBM Db2:
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
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** Iniciar os recursos da IBM Db2:
* Coloque o Pacemaker fora do modo de manutenção.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** Certifique-se de que o estado do cluster está bem e que todos os recursos são iniciados. Não é importante qual nó os recursos estão a funcionar.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nóns configurados
# <a name="5-resources-configured"></a>5 recursos configurados

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Lista completa de recursos:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd (stonith:external/sbd): Iniciado azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Grupo de Recursos: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (ocf::heartbeat:IPaddr2): Iniciado azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat:azure-lb): Iniciado azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Conjunto mestre/escravo: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Mestres: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Escravos: [ azibmdb01 ]
</pre>

> [!IMPORTANT]
> Tem de gerir a instância Db2 agrupada do Pacemaker utilizando ferramentas Pacemaker. Se utilizar comandos db2 como db2stop, o Pacemaker deteta a ação como uma falha de recursos. Se estiver a fazer manutenção, pode colocar os nós ou recursos no modo de manutenção. O Pacemaker suspende os recursos de monitorização, e pode usar comandos normais da administração db2.


### <a name="configure-azure-load-balancer"></a>Configurar o Balanceador de Carga do Azure
Para configurar o Balançador de Carga Azure, recomendamos que utilize o [Azure Standard Load Balancer SKU](../../../load-balancer/load-balancer-overview.md) e, em seguida, faça o seguinte;

> [!NOTE]
> O Balancer de Carga Padrão SKU tem restrições que acedem a endereços IP públicos a partir dos nós por baixo do Balanceador de Carga. O artigo [Conectividade de ponto final público para máquinas virtuais usando O Balanceador de Carga Padrão Azure em cenários de alta disponibilidade SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md) está descrevendo formas de permitir que esses nóns acedam a endereços IP públicos

1. Criar um pool IP frontal:

   a. No portal Azure, abra o Balançador de Carga Azure, selecione **o pool IP frontend**e, em seguida, selecione **Add**.

   b. Introduza o nome do novo pool IP frontal (por exemplo, **ligação Db2**).

   c. Desaponda a **Estática** e introduza o endereço IP **Virtual-IP** definido no início. **Static**

   d. Selecione **OK**.

   e. Após a criação do novo pool IP frontal, note o endereço IP do pool.

1. Criar uma piscina traseira:

   a. No portal Azure, abra o Balançador de Carga Azure, selecione **piscinas de backend**e, em seguida, selecione **Add**.

   b. Insira o nome da nova piscina traseira (por exemplo, **Db2-backend**).

   c. **Selecione Adicionar uma máquina virtual**.

   d. Selecione o conjunto de disponibilidade ou as máquinas virtuais que hospedam a base de dados IBM Db2 criada no passo anterior.

   e. Selecione as máquinas virtuais do cluster IBM Db2.

   f. Selecione **OK**.

1. Criar uma sonda de saúde:

   a. No portal Azure, abra o Balançador de Carga Azure, selecione **sondas de saúde**e selecione **Add**.

   b. Insira o nome da nova sonda de saúde (por exemplo, **Db2-hp).**

   c. Selecione **TCP** como protocolo e porta **62500**. Mantenha o valor **de intervalo** definido para **5**, e mantenha o valor **limiar insalubre** definido para **2**.

   d. Selecione **OK**.

1. Criar as regras de equilíbrio de carga:

   a. No portal Azure, abra o Balançador de Carga Azure, selecione **regras de equilíbrio de carga**e, em seguida, selecione **Adicionar**.

   b. Introduza o nome da nova regra do Balanceador de Carga (por exemplo, **Db2-SID**).

   c. Selecione o endereço IP frontal, o pool traseiro e a sonda de saúde que criou anteriormente (por exemplo, **db2-frontend**).

   d. Mantenha o **Protocolo** definido para **TCP,** e introduza a *porta de comunicação da base de dados portuária*.

   e. Aumente o **tempo de 30** minutos.

   f. Certifique-se de que ativa o **IP flutuante**.

   exemplo, Selecione **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Fazer alterações nos perfis SAP para utilizar IP virtual para ligação
Para se ligar à primeira instância da configuração HADR, a camada de aplicação SAP precisa de utilizar o endereço IP virtual que definiu e configura para o Balançador de Carga Azure. São necessárias as seguintes alterações:

/sapmnt/ \<SID> /perfil/DEFAULT. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/ \<SID> /global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instale servidores de aplicações primárias e de diálogo

Quando instalar servidores de aplicações primários e de diálogo contra uma configuração Db2 HADR, utilize o nome de anfitrião virtual que escolheu para a configuração. 

Se efetuou a instalação antes de criar a configuração Db2 HADR, faça as alterações descritas na secção anterior e da seguinte forma para as pilhas SAP Java.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP+Java ou Java stack sistemas JDBC VERIFICAÇÃO DE URL

Utilize a ferramenta J2EE Config para verificar ou atualizar o URL JDBC. Como a ferramenta J2EE Config é uma ferramenta gráfica, é necessário instalar o servidor X:
 
1. Inscreva-se no servidor de aplicação primária da instância J2EE e execute:   `sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. No quadro esquerdo, escolha **a loja de segurança.**
1. No quadro direito, escolha a chave jdbc/pool/ \<SAPSID> /url.
1. Mude o nome do anfitrião no URL JDBC para o nome de anfitrião virtual.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Selecione **Adicionar**.
1. Para guardar as alterações, selecione o ícone do disco na parte superior esquerda.
1. Feche a ferramenta de configuração.
1. Reinicie a instância de Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configure o arquivo de log para a configuração hadr
Para configurar o arquivo de registo db2 para a configuração do HADR, recomendamos que configuure tanto a base de dados primária como a base de dados de espera para ter capacidade automática de recuperação de registos a partir de todos os locais de arquivo de registo. Tanto a base de dados primária como a base de dados de espera devem ser capazes de recuperar ficheiros de arquivo de registo de todos os locais de arquivo de registos aos quais qualquer uma das instâncias da base de dados pode arquivar ficheiros de registo. 

O arquivo de registos é realizado apenas pela base de dados primária. Se alterar as funções HADR dos servidores de base de dados ou se ocorrer uma falha, a nova base de dados primária é responsável pelo arquivamento de registos. Se tiver configurado vários locais de registo, os seus registos podem ser arquivados duas vezes. No caso de uma captura local ou remota, também poderá ter de copiar manualmente os registos arquivados do antigo servidor primário para a localização de registo ativo do novo servidor primário.

Recomendamos configurar uma partilha comum de NFS onde os registos são escritos de ambos os nós. A quota da NFS tem de estar altamente disponível. 

Você pode usar ações NFS altamente disponíveis para transportes ou um diretório de perfil. Para obter mais informações, consulte:

- [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server][nfs-ha] 
- [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server com Ficheiros Azure NetApp para aplicações SAP](./high-availability-guide-suse-netapp-files.md)
- [Ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-introduction.md) (para criar ações NFS)


## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta secção descreve como pode testar a sua configuração Db2 HADR. *Cada teste pressupõe que você está iniciado como raiz* de utilizador e a primária IBM Db2 está funcionando na máquina virtual *azibmdb01.*

O estado inicial de todos os casos de teste é explicado aqui: (crm_mon -r ou estado de crm)

- **crm status** é um instantâneo do estado de Pacemaker no tempo de execução 
- **crm_mon -r** é saída contínua do estado do Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

O estado original de um sistema SAP é documentado na Transação DBACOCKPIT > Configuração > Visão Geral, como mostra a seguinte imagem:

![DBACockpit - Pré-Migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Aquisição de teste da IBM Db2


> [!IMPORTANT] 
> Antes de iniciar o teste, certifique-se de que:
> * O Pacemaker não tem nenhuma ação falhada (estado de CRM).
> * Não existem restrições de localização (restos de teste de migração)
> * A sincronização DA IBM Db2 HADR está a funcionar. Verifique com o utilizador db2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrar o nó que está a executar a base de dados Db2 primária executando o seguinte comando:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Após a migração ser feita, a produção do estado de CRM parece:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

O estado original de um sistema SAP é documentado na Transação DBACOCKPIT > Configuração > Visão Geral, como mostra a seguinte imagem:

![DBACockpit - Pós Migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

A migração de recursos com "migração de recursos crm" cria restrições de localização. As restrições de localização devem ser eliminadas. Se as restrições de localização não forem eliminadas, o recurso não pode falhar ou pode experimentar aquisições indesejadas. 

Migrar o recurso de volta para *azibmdb01* e limpar as restrições de localização
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **migração de recursos \<res_name> \<host> crm:** Cria restrições de localização e pode causar problemas com a aquisição
- **crm recursos \<res_name> claros **: Limpa as restrições de localização
- **limpeza \<res_name> de recursos CRM **: Limpa todos os erros do recurso

### <a name="test-the-fencing-agent"></a>Teste o agente de esgrima

Neste caso, testamos esgrima SBD, o que recomendamos que faça quando utilizar o SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

O nó de *cluster azibmdb01* deve ser reiniciado. O papel de HADR primário da IBM Db2 vai ser transferido para *azibmdb02*. Quando *o azibmdb01* estiver novamente online, a instância Db2 vai mover-se no papel de uma instância de base de dados secundária. 

Se o serviço Pacemaker não começar automaticamente na antiga primária reiniciada, certifique-se de que o inicia manualmente com:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Teste uma aquisição manual

Pode testar uma aquisição manual interrompendo o serviço Pacemaker no nó *azibmdb01:*
<pre><code>service pacemaker stop</code></pre>

estado em *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Após o failover, pode recomeçar o serviço em *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Deses mate o processo Db2 no nó que executa a base de dados primária HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

A instância Db2 vai falhar, e pacemaker vai reportar o seguinte estado:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

O Pacemaker reiniciará a instância da base de dados primária Db2 no mesmo nó, ou falhará no nó que está a executar a instância da base de dados secundária e é reportado um erro.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Desista o processo Db2 no nó que executa a instância da base de dados secundária

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

O nó entra em falha declarada e erro reportado
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

A instância Db2 é reiniciada no papel secundário que tinha atribuído anteriormente.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Parar dB via db2stop force no nó que executa a instância de base de dados primária HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Como utilizador db2 \<sid> executar comando db2stop force:
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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

A instância da base de dados secundária Db2 HADR foi promovida para o papel principal
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Crash VM com reinício no nó que executa a instância de base de dados primária HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

O Pacemaker promoverá o caso secundário para o papel de primeira instância. A antiga instância primária passará para o papel secundário após o VM e todos os serviços são totalmente restaurados após o reboot de VM:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Despenhe o VM que executa a primeira área de dados HADR com "stop"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Nesse caso, o Pacemaker vai detetar que o nó que está a executar a primeira área de dados não está a responder.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

O próximo passo é verificar se há uma situação *cerebral dividida.* Depois do nó sobrevivente ter determinado que o nó que correu pela última vez na primeira área de dados está em baixo, uma falha de recursos é executada.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


Em caso de "paragem" do nó, o nó falhado tem de ser reiniciado através de ferramentas de Gestão Azure (no portal Azure, PowerShell ou no Azure CLI). Depois do nó falhado estar novamente on-line, inicia a instância Db2 no papel secundário.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Passos seguintes
- [Arquitetura e cenários de alta disponibilidade para SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
- [Configurar o Pacemaker no SUSE Linux Enterprise Server em Azure](./high-availability-guide-suse-pacemaker.md)

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
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
