---
title: Instale o IBM Db2 HADR em máquinas virtuais Azure (VMs) no RHEL ! Microsoft Docs
description: Estabeleça uma elevada disponibilidade de IBM Db2 LUW em máquinas virtuais Azure (VMs) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.openlocfilehash: c6a230f6abeab45c56aab2db40b8b1defcc06d90
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598702"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105
[2694118]: https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Elevada disponibilidade do IBM DB2 LUW nas VMs do Azure no Red Hat Enterprise Linux Server

Ibm Db2 para Linux, UNIX e Windows (LUW) na configuração de [alta disponibilidade e recuperação de desastres (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) consiste num nó que executa uma instância de base de dados primária e pelo menos um nó que executa uma instância de base de dados secundária. As alterações na instância de base de dados primária são replicadas para uma instância de base de dados secundária sincronizada ou assíncrona, dependendo da sua configuração. 

Este artigo descreve como implementar e configurar as máquinas virtuais Azure (VMs), instalar a estrutura de cluster e instalar o IBM Db2 LUW com configuração HADR. 

O artigo não cobre como instalar e configurar o IBM Db2 LUW com instalação de software HADR ou SAP. Para ajudá-lo a realizar estas tarefas, fornecemos referências aos manuais de instalação SAP e IBM. Este artigo centra-se em peças específicas do ambiente Azure. 

As versões IBM Db2 suportadas são 10.5 e posteriormente, conforme documentado na nota SAP [1928533].

Antes de iniciar uma instalação, consulte as seguintes notas e documentação do SAP:

| Nota SAP | Descrição |
| --- | --- |
| [1928533] | Aplicações SAP em Azure: Produtos suportados e tipos de VM Azure |
| [2015553] | SAP em Azure: Apoiar pré-requisitos |
| [2178632] | Principais métricas de monitorização para SAP em Azure |
| [2191498] | SAP em Linux com Azure: Monitorização melhorada |
| [2243692] | Linux em Azure (IaaS) VM: Problemas de licença SAP |
| [2002167] | Red Hat Enterprise Linux 7.x: Instalação e upgrade |
| [2694118] | Red Hat Enterprise Linux HA Add-On em Azure |
| [1999351] | Resolução de problemas reforçou a monitorização do Azure para o SAP |
| [2233094] | DB6: Aplicações SAP no Azure que utilizam OIBM Db2 para Linux, UNIX e Windows - informações adicionais |
| [1612105] | DB6: FAQ em Db2 com HADR |


| Documentação | 
| --- |
| [Comunidade SAP Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Tem todas as notas SAP necessárias para Linux |
| [Planeamento e implementação de Máquinas Virtuais Azure para SAP no][planning-guide] guia Linux |
| [Implantação de Máquinas Virtuais Azure para SAP em Linux][deployment-guide] (este artigo) |
| Implantação do sistema de gestão de bases de [dados Azure Virtual Machines (DBMS) para SAP no][dbms-guide] guia Linux |
| [Carga de trabalho sap na lista de verificação de planeamento e implementação do Azure][azr-sap-plancheck] |
| [Visão geral do add-on de alta disponibilidade para red hat enterprise Linux 7][rhel-ha-addon] |
| [Administração add-on de alta disponibilidade][rhel-ha-admin] |
| [Referência addon de alta disponibilidade][rhel-ha-ref] |
| [Políticas de suporte para clusters de alta disponibilidade RHEL - Máquinas Virtuais Microsoft Azure como Membros do Cluster][rhel-azr-supp]
| [Instalação e Configuração de um Cluster de Alta Disponibilidade do Red Hat Enterprise Linux 7.4 (e mais tarde) no Microsoft Azure][rhel-azr-inst]
| [Ibm Db2 Azure Virtual Machines DBMS implementação para carga de trabalho SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [Política de Suporte para Clusters de Alta Disponibilidade RHEL - Gestão do IBM Db2 para Linux, Unix e Windows em um Cluster][rhel-db2-supp]



## <a name="overview"></a>Descrição geral
Para alcançar uma elevada disponibilidade, o IBM Db2 LUW com HADR está instalado em pelo menos duas máquinas virtuais Azure, que são implantadas num conjunto de [disponibilidade Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ou em zonas de [disponibilidade Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 

Os gráficos seguintes exibem uma configuração de dois VMs do servidor de base de dados Azure. Ambos os VMs do servidor de base de dados Azure têm o seu próprio armazenamento anexado e estão a funcionar. Em HADR, uma instância de base de dados num dos VMs Azure tem o papel da instância primária. Todos os clientes estão ligados à instância primária. Todas as alterações nas transações de base de dados são permanelogas localmente no registo de transações db2. À medida que os registos de registo de transações são percecionados localmente, os registos são transferidos via TCP/IP para a instância de base de dados no segundo servidor de base de dados, no servidor de espera ou na instância de espera. A instância de espera atualiza a base de dados local, relançando os registos de registos de transações transferidos. Desta forma, o servidor de espera é mantido em sincronização com o servidor primário.

HADR é apenas uma funcionalidade de replicação. Não possui deteção de falhas nem instalações de aquisição automática ou falha. Uma aquisição ou transferência para o servidor de espera deve ser iniciada manualmente por um administrador de base de dados. Para obter uma deteção automática de aquisição e falha, pode utilizar a função de clustering Linux Pacemaker. Pacemaker monitoriza as duas instâncias do servidor de base de dados. Quando a instância principal do servidor de base de dados falha, o Pacemaker inicia uma aquisição *automática* de HADR pelo servidor de espera. O Pacemaker também garante que o endereço IP virtual é atribuído ao novo servidor primário.

![Visão geral de alta disponibilidade da IBM Db2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Para que os servidores de aplicações SAP se conectem à base de dados primária, precisa de um nome de anfitrião virtual e de um endereço IP virtual. Em caso de falha, os servidores de aplicações SAP ligar-se-ão a novas instâncias de base de dados primárias. Num ambiente Azure, é necessário um equilibrador de [carga Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) para utilizar um endereço IP virtual da forma que é necessário para hadr da IBM Db2. 

Para ajudá-lo a entender como o IBM Db2 LUW com HADR e Pacemaker se enquadra numa configuração altamente disponível do sistema SAP, a imagem seguinte apresenta uma visão geral de uma configuração altamente disponível de um sistema SAP baseado na base de dados IBM Db2. Este artigo abrange apenas o IBM Db2, mas fornece referências a outros artigos sobre como configurar outros componentes de um sistema SAP.

![Visão geral de ambiente de alta disponibilidade IBM DB2](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Visão geral de alto nível dos passos necessários
Para implementar uma configuração IBM Db2, é necessário seguir estes passos:

  + Planeie o seu ambiente.
  + Implante os VMs.
  + Atualizar o RHEL Linux e configurar sistemas de ficheiros.
  + Instale e configure pacemaker.
  + Configuração [do cluster glusterfs][glusterfs] ou [Ficheiros Azure NetApp][anf-rhel]
  + Instale [ASCS/ERS num cluster separado][ascs-ha-rhel].
  + Instale a base de dados IBM Db2 com opção Distributed/High Availability (SWPM).
  + Instale e crie um nó de base de dados secundário e instância, e configure HADR.
  + Confirme que hadr está a trabalhar.
  + Aplique a configuração Pacemaker para controlar o IBM Db2.
  + Configure o equilíbrio de carga Azure.
  + Instale servidores de aplicações primários e de diálogo.
  + Verifique e adapte a configuração dos servidores de aplicações SAP.
  + Realizar testes de failover e aquisição.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Plano Azure infraestrutura para hospedar IBM Db2 LUW com HADR

Complete o processo de planeamento antes de executar a implementação. O planeamento constrói a base para a implementação de uma configuração de Db2 com HADR em Azure. Os elementos-chave que precisam de fazer parte do planeamento do IMB Db2 LUW (parte da base de dados do ambiente SAP) estão listados na tabela seguinte:

| Tópico | Descrição curta |
| --- | --- |
| Definir grupos de recursos Azure | Grupos de recursos onde implementa VM, VNet, Azure Load Balancer e outros recursos. Pode ser existente ou novo. |
| Definição de rede virtual / Subnet | Onde estão a ser implantados VMs para A IBM Db2 e azure Load Balancer. Pode ser existente ou recém-criado. |
| Máquinas virtuais que acolhem IBM Db2 LUW | Tamanho VM, armazenamento, rede, endereço IP. |
| Nome de anfitrião virtual e IP virtual para base de dados IBM Db2| O nome virtual IP ou anfitrião que é usado para a ligação dos servidores de aplicações SAP. **db-virt-hostname**, **db-virt-ip**. |
| Esgrima azul | O método para evitar situações cerebrais divididas é evitado. |
| Azure Load Balancer | Utilização de Base básica ou standard (recomendada), porta de sonda para base de dados Db2 (a nossa recomendação 62500) **porta de sonda**. |
| Resolução de nomes| Como a resolução de nomes funciona no ambiente. O serviço DNS é altamente recomendado. O arquivo de anfitriões locais pode ser usado. |
    
Para mais informações sobre o Pacemaker Linux em Azure, consulte [a configuração do Pacemaker no Red Hat Enterprise Linux em Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Implantação em Red Hat Enterprise Linux

O agente de recursos da IBM Db2 LUW está incluído no Red Hat Enterprise Linux Server HA Addon. Para a configuração descrita neste documento, você deve usar Red Hat Enterprise Linux para SAP. O Azure Marketplace contém uma imagem para Red Hat Enterprise Linux 7.4 para SAP ou superior que pode usar para implantar novas máquinas virtuais Azure. Esteja atento aos vários modelos de suporte ou serviço que são oferecidos pela Red Hat através do Azure Marketplace quando escolher uma imagem VM no Azure VM Marketplace.

### <a name="hosts-dns-updates"></a>Anfitriões: Atualizações do DNS
Faça uma lista de todos os nomes de anfitriões, incluindo nomes de anfitriões virtuais, e atualize os seus servidores DNS para ativar o endereço IP adequado para a resolução de nomes de anfitrião. Se um servidor DNS não existir ou não conseguir atualizar e criar entradas de DNS, precisa de utilizar os ficheiros anfitriões locais dos VMs individuais que estão a participar neste cenário. Se estiver a utilizar as entradas de ficheiros do anfitrião, certifique-se de que as entradas são aplicadas a todos os VMs no ambiente do sistema SAP. No entanto, recomendamos que use o seu DNS que, idealmente, se estende até Azure


### <a name="manual-deployment"></a>Implantação manual

Certifique-se de que o SISTEMA selecionado é suportado pela IBM/SAP para a IBM Db2 LUW. A lista de versões de OS suportadas para lançamentos de VMs E Db2 está disponível na nota SAP [1928533]. A lista de lançamentos de SO por versão individual do Db2 está disponível na Matriz de Disponibilidade de Produto SAP. Recomendamos vivamente um mínimo de Red Hat Enterprise Linux 7.4 para SAP devido a melhorias de desempenho relacionadas com o Azure nesta ou posterior red hat enterprise linux.

1. Criar ou selecionar um grupo de recursos.
1. Criar ou selecionar uma rede virtual e uma sub-rede.
1. Crie um conjunto de disponibilidade azure ou implemente uma zona de disponibilidade.
    + Para o conjunto de disponibilidade, detete os domínios máximode atualização para 2.
1. Criar a Máquina Virtual 1.
    + Use o Red Hat Enterprise Linux para a imagem SAP no Mercado Azure.
    + Selecione o conjunto de disponibilidade do Azure que criou no passo 3 ou selecione Zona de Disponibilidade.
1.  Criar a Máquina Virtual 2.
    + Use o Red Hat Enterprise Linux para a imagem SAP no Mercado Azure.
    + Selecione o conjunto de disponibilidade azure criado no passo 3, ou selecione Zona de Disponibilidade (não é a mesma zona que no passo 3).
1. Adicione discos de dados aos VMs e, em seguida, verifique a recomendação de um sistema de ficheiros configurado no artigo [IBM Db2 Azure Virtual Machines DBMS implementação para carga de trabalho SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster Pacemaker
    
Para criar um cluster pacemaker básico para este servidor IBM Db2, consulte [a configuração do Pacemaker no Red Hat Enterprise Linux em Azure][rhel-pcs-azr]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Instale o ambiente IBM Db2 LUW e SAP

Antes de iniciar a instalação de um ambiente SAP baseado no IBM Db2 LUW, reveja a seguinte documentação:

+ Documentação azure
+ Documentação SAP
+ Documentação IBM

Os links para esta documentação são fornecidos na secção introdutória deste artigo.

Verifique os manuais de instalação SAP sobre a instalação de aplicações baseadas em NetWeaver no IBM Db2 LUW.
Pode encontrar as guias no portal de ajuda SAP utilizando o Localizador de Guias de [Instalação SAP][sap-instfind].

Pode reduzir o número de guias apresentados no portal, definindo os seguintes filtros:
- Quero: "Instalar um novo sistema"
- My Database: "IBM Db2 for Linux, Unix e Windows"
- Filtros adicionais para versões SAP NetWeaver, configuração de pilhas ou sistema operativo

#### <a name="red-hat-firewall-rules"></a>Regras de firewall do Chapéu Vermelho
A Red Hat Enterprise Linux tem firewall ativada por defeito. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Dicas de instalação para a configuração do IBM Db2 LUW com HADR

Para configurar a principal instância de base de dados IBM Db2 LUW:

- Utilize a alta disponibilidade ou opção distribuída.
- Instale a instância SAP ASCS/ERS e a Base de Dados.
- Pegue uma cópia de segurança da base de dados recém-instalada.

> [!IMPORTANT] 
> Escreva a "porta de comunicação da base de dados" que está definida durante a instalação. Deve ser o mesmo número de porta para ambas as instâncias da base de dados.
>![sAP SWPM Definição de Definição de Definição de Porto](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Definições IBM Db2 HADR para Azure

   Quando utilizar um agente de esgrima Azure Pacemaker, defina os seguintes parâmetros:

   - Duração da janela dos pares HADR (segundos) (HADR_PEER_WINDOW) = 240  
   - Valor de tempo de tempo (HADR_TIMEOUT) = 45

Recomendamos os parâmetros anteriores baseados em testes iniciais de failover/takeover. É obrigatório que teste a funcionalidade adequada de failover e aquisição com estas definições de parâmetro. Como as configurações individuais podem variar, os parâmetros podem necessitar de regulação. 

> [!NOTE]
> Específico para o IBM Db2 com configuração HADR com arranque normal: A instância de base de dados secundária ou de espera deve estar em funcionamento antes de poder iniciar a instância de base de dados primária.

   
> [!NOTE]
> Para a instalação e configuração específicas do Azure e do Pacemaker: Durante o procedimento de instalação através do SAP Software Provisioning Manager, existe uma questão explícita sobre a elevada disponibilidade para o IBM Db2 LUW:
>+ Não selecione **IBM Db2 pureScale**.
>+ Não selecione Instalar a Automatização do **Sistema IBM Tivoli para multiplataformas**.
>+ Não selecione Geração de ficheiros de **configuração de cluster**.
>![Opções SAP SWPM - DB2 HA](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Para configurar o servidor de base de dados Standby utilizando o procedimento de cópia homogénea do sistema SAP, execute estas etapas:

1. Selecione a opção **de cópia do Sistema** > **Sistemas-alvo** > instância de base de **dados**de > **distribuídas** .
1. Como método de cópia, **selecione Sistema Homogéneo** para que possa utilizar a cópia de segurança para restaurar uma cópia de segurança na instância do servidor de espera.
1. Quando chegar à etapa de saída para restaurar a base de dados para cópia homogénea do sistema, saia do instalador. Restaure a base de dados de uma cópia de segurança do hospedeiro principal. Todas as fases de instalação subsequentes já foram executadas no servidor de base de dados primário.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Regras de firewall do Chapéu Vermelho para DB2 HADR
Adicione regras de firewall para permitir o tráfego para DB2 e entre DB2 para HADR funcionar:
+ Porta de comunicação de base de dados. Se utilizar divisórias, adicione também as portas.
+ Porta HADR (valor do parâmetro DB2 HADR_LOCAL_SVC)
+ Porto de sonda Azure
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>Ibm Db2 HADR check
Para efeitos de demonstração e os procedimentos descritos neste artigo, a base de dados SID é **ID2**.

Depois de configurar hadr e o estado é PEER e CONNECTED nos nós primários e de espera, execute a seguinte verificação:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Configuração db2 Pacemaker

Quando utilizar o Pacemaker para uma falha automática em caso de falha no nó, tem de configurar os seus casos Db2 e Pacemaker em conformidade. Esta secção descreve este tipo de configuração.

Os seguintes itens são pré-fixados com:

- **[A]** : Aplicável a todos os nós
- **[1]** : Aplicável apenas ao nó 1 
- **[2]** : Aplicável apenas ao nó 2

**[A]** Pré-requisito para a configuração do Pacemaker:
1. Desligue ambos os servidores de base de dados com o utilizador db2\<sid> com db2stop.
1. Altere o ambiente da concha para db2\<sid> utilizador para */bin/ksh:*
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Configuração do pacemaker

**[1]** Configuração do Pacemaker específico IBM Db2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** Criar recursos IBM Db2:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** Iniciar os recursos IBM Db2:
* Coloque pacemaker fora do modo de manutenção.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** Certifique-se de que o estado do cluster está ok e que todos os recursos são iniciados. Não é importante que nó dos recursos estejam a funcionar.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [az-idb01 az-idb02 ]

Lista completa de recursos:

 rsc_st_azure (stonith:fence_azure_arm): Iniciado az-idb01 Master/Slave set: Db2_HADR_ID2-master [Db2_HADR_ID2] Masters: [ az-idb01] Escravos: [ az-idb02] Grupo de recursos: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2): Iniciado az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Iniciado az-idb01

Estatuto da Daemon: corosync: pacemaker ativo/incapacitado: pcsd ativo/incapacitado: ativo/ativado
</pre>

> [!IMPORTANT]
> Deve gerir a instância Db2 agrupada do Pacemaker utilizando ferramentas Pacemaker. Se utilizar comandos db2 como db2stop, o Pacemaker deteta a ação como uma falha de recurso. Se estiver a realizar a manutenção, pode colocar os nós ou recursos no modo de manutenção. Pacemaker suspende os recursos de monitorização, e pode então usar comandos normais de administração db2.


### <a name="configure-azure-load-balancer"></a>Configurar o Balanceador de Carga do Azure
Para configurar o Equilíbrio de Carga Azure, recomendamos que utilize o [Balancer De carga Padrão Azure SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) e, em seguida, faça o seguinte;

> [!NOTE]
> O Standard Load Balancer SKU tem restrições de acesso a endereços IP públicos a partir dos nós por baixo do Balancer de Carga. O artigo [Conectividade de ponto final público para máquinas virtuais usando O Equilíbrio de Carga Padrão Azure em cenários de alta disponibilidade SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) está descrevendo formas de permitir que esses nódos acedam a endereços IP públicos



1. Crie uma piscina IP frontal:

   a. No portal Azure, abra o Balancer de Carga Azure, selecione **pool IP frontend,** e depois selecione **Adicionar**.

   b. Introduza o nome da nova piscina IP frontal (por exemplo, **ligação Db2).**

   c. Desloque a **Atribuição** à **Estática**e introduza o endereço IP **Virtual-IP** definido no início.

   d. Selecione **OK**.

   e. Depois de criado o novo pool IP frontal, note o endereço IP do pool.

1. Crie uma piscina de back-end:

   a. No portal Azure, abra o Balancer de Carga Azure, selecione **piscinas de backend,** e depois selecione **Adicionar**.

   b. Introduza o nome da nova piscina traseira (por exemplo, **Db2-backend).**

   c. **Selecione Adicionar uma máquina virtual**.

   d. Selecione o conjunto de disponibilidade ou as máquinas virtuais que hospedam base de dados IBM Db2 criada na etapa anterior.

   e. Selecione as máquinas virtuais do cluster IBM Db2.

   f. Selecione **OK**.

1. Criar uma sonda de saúde:

   a. No portal Azure, abra o Equilíbrio de Carga Azure, selecione sondas de **saúde,** e selecione **Adicionar**.

   b. Introduza o nome da nova sonda de saúde (por exemplo, **Db2-hp).**

   c. Selecione **TCP** como protocolo e porta **62500**. Mantenha o valor **de intervalo** definido para **5**, e mantenha o valor limiar **não saudável** definido para **2**.

   d. Selecione **OK**.

1. Criar as regras de equilíbrio de carga:

   a. No portal Azure, abra o Equilíbrio de Carga Azure, selecione regras de equilíbrio de **carga,** e, em seguida, selecione **Adicionar**.

   b. Introduza o nome da nova regra do Balancer de Carga (por exemplo, **Db2-SID**).

   c. Selecione o endereço IP frontal, o pool de back-end e a sonda de saúde que criou anteriormente (por exemplo, **db2-frontend**).

   d. Mantenha o **Protocolo** definido para **tCP,** e introduza a porta de *comunicação*da porta base de dados .

   e. Aumente o **tempo de inatividade** para 30 minutos.

   f. Certifique-se de **ativar**o IP flutuante .

   g. Selecione **OK**.

**[A]** Adicionar regra de firewall para porta de sonda:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Faça alterações nos perfis SAP para utilizar IP virtual para ligação
Para se ligar à instância primária da configuração HADR, a camada de aplicação SAP precisa de utilizar o endereço IP virtual que definiu e configurado para o Equilíbrio de Carga Azure. São necessárias as seguintes alterações:

/sapmnt/\<SID>/profile/DEFAULT. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalar servidores de aplicações primárias e de diálogo

Quando instalar servidores de aplicações primárias e de diálogo contra uma configuração DB2 HADR, utilize o nome de anfitrião virtual que escolheu para a configuração. 

Se realizou a instalação antes de criar a configuração DB2 HADR, faça as alterações conforme descrito na secção anterior e da seguinte forma para as pilhas SAP Java.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP+Java ou java stack sistemas JDBC URL check

Utilize a ferramenta J2EE Config para verificar ou atualizar o URL JDBC. Como a ferramenta J2EE Config é uma ferramenta gráfica, é necessário instalar o servidor X:
 
1. Inscreva-se no servidor de aplicação principal da instância J2EE e execute:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. No quadro esquerdo, escolha uma loja de **segurança.**
1. No quadro certo, escolha a chave `jdbc/pool/\<SAPSID>/url`.
1. Mude o nome do anfitrião no URL JDBC para o nome de anfitrião virtual.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Selecione **Adicionar**.
1. Para guardar as alterações, selecione o ícone do disco na parte superior esquerda.
1. Feche a ferramenta de configuração.
1. Reinicie a instância de Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configure arquivamento de registo para configuração HADR
Para configurar o arquivo de registo Db2 para a configuração HADR, recomendamos que configure tanto a base de dados primária como a base de dados de espera para ter capacidade automática de recuperação de registos de todos os locais de arquivo de registo. Tanto a base de dados primária como a base de dados de espera devem ser capazes de recuperar ficheiros de arquivo de registo de todos os locais de arquivo de registo para os quais qualquer uma das instâncias da base de dados pode arquivar ficheiros de registo. 

O arquivo de registo é realizado apenas pela base de dados primária. Se alterar as funções HADR dos servidores de base de dados ou se ocorrer uma falha, a nova base de dados primária é responsável pelo arquivamento de registos. Se tiver configurado vários locais de arquivo de registos, os seus registos podem ser arquivados duas vezes. No caso de uma atualização local ou remota, poderá também ter de copiar manualmente os registos arquivados do antigo servidor primário para a localização ativa do registo do novo servidor primário.

Recomendamos configurar uma partilha de NFS comum ou GlusterFS, onde os registos são escritos de ambos os nós. A quota nFS ou GlusterFS tem de estar altamente disponível. 

Pode utilizar ações NFS ou GlusterFS altamente disponíveis para transportes ou diretório de perfil. Para obter mais informações, consulte:

- [GlusterFS nas VMs do Azure no Red Hat Enterprise Linux para o SAP NetWeaver][glusterfs] 
- [Alta disponibilidade para SAP NetWeaver em VMs Azure em Red Hat Enterprise Linux com Ficheiros Azure NetApp para Aplicações SAP][anf-rhel]
- [Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (para criar ações da NFS)

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta secção descreve como pode testar a sua configuração DB2 HADR. Todos os testes assumem que as primárias da IBM Db2 estão a funcionar na máquina virtual *az-idb01.* Deve utilizar-se o utilizador com privilégios sudo ou raiz (não recomendado).

O estado inicial de todos os casos de teste é explicado aqui: (estado de crm_mon -r ou pcs)

- **o estado do pcs** é um instantâneo do estado do Pacemaker no momento da execução 
- **crm_mon -r** é uma saída contínua do estado do Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

O estado original num sistema SAP está documentado em Transaction DBACOCKPIT > Configuração > Visão Geral, como mostra a seguinte imagem:

![DBACockpit - Pré-Migração](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Aquisição de teste da IBM Db2


> [!IMPORTANT] 
> Antes de iniciar o teste, certifique-se de que:
> * O Pacemaker não tem nenhuma ação falhada (estado do PCS).
> * Não existem restrições de localização (restos de teste de migração)
> * A sincronização IBM Db2 HADR está a funcionar. Consulte o utilizador db2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrar o nó que está a executar a base de dados primária do Db2 executando o seguinte comando:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Após a migração, a saída do estado do CRM parece:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

O estado original num sistema SAP está documentado em Transaction DBACOCKPIT > Configuração > Visão Geral, como mostra a seguinte imagem:

![DBACockpit - Pós Migração](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

A migração de recursos com "pcs resource move" cria restrições de localização. Os constrangimentos de localização neste caso estão a impedir a execução da instância IBM Db2 no az-idb01. Se as restrições de localização não forem eliminadas, o recurso não poderá voltar atrás.

Remova a restrição de localização e o nó de espera será iniciado em az-idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
E alterações no estado do cluster para:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit - Restrição de localização removida](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Migrar o recurso de volta para *az-idb01* e limpar os constrangimentos de localização
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **pcs recursos movem-se \<res_name> <host>:** Cria constrangimentos de localização e pode causar problemas com a aquisição
- **pcs recurso claro \<res_name>** : Limpa os constrangimentos de localização
- **pcs limpeza de recursos \<res_name>** : Elimina todos os erros do recurso

### <a name="test-a-manual-takeover"></a>Testar uma aquisição manual

Pode testar uma aquisição manual, interrompendo o serviço Pacemaker no nó *az-idb01:*
<pre><code>systemctl stop pacemaker</code></pre>

estado em *az-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

Após a falha, pode recomeçar o serviço no *az-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Mate o processo Db2 no nó que gere a base de dados primária HADR

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

A instância db2 vai falhar, e pacemaker moverá o nó mestre e reportará o seguinte estado:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

O Pacemaker reiniciará a instância de base de dados primária db2 no mesmo nó, ou falhará no nó que está a executar a instância de base de dados secundária e um erro é relatado.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Mate o processo Db2 no nó que executa a instância de base de dados secundária

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

O nó entra em declarado falhado e erro reportado
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

A instância Db2 é reiniciada no papel secundário que tinha atribuído anteriormente.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Pare DB através da força db2stop no nó que executa a instância de base de dados primária HADR

Como utilizador db2\<sid> execute a força de comando db2stop:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Falha detetada:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

A instância de base de dados secundária DB2 HADR foi promovida para o papel principal.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Crash the VM que gere a instância de base de dados primária HADR com "stop"

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

Neste caso, o Pacemaker vai detetar que o nó que está a executar a base de dados primária não está a responder.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

O próximo passo é verificar se há uma situação *cerebral dividida.* Depois do nó sobrevivente ter determinado que o nó que correu pela última vez a base de dados primária está em baixo, uma falha de recursos é executado.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


Em caso de pânico, o nó falhado será restared pelo agente de esgrima. Depois do nó falhado estar de volta on-line, você deve começar o cluster pacemaker por
<pre><code>sudo pcs cluster start</code></pre> inicia a instância db2 no papel secundário.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Passos seguintes
- [Arquitetura de alta disponibilidade e cenários para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configuração do Pacemaker no Red Hat Enterprise Linux em Azure][rhel-pcs-azr]
