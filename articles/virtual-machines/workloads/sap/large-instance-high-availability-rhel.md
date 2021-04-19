---
title: Azure Large Instances alta disponibilidade para SAP em RHEL
description: Saiba como automatizar uma base de dados SAP HANA falha usando um cluster Pacemaker em Red Hat Enterprise Linux.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: dc27fd67a3801815464ecd37fea567c02dee6e49
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719048"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>Azure Large Instances alta disponibilidade para SAP em RHEL

> [!NOTE]
> Este artigo contém referências ao termo *blacklist*, um termo que a Microsoft já não utiliza. Quando este termo for removido do software, vamos removê-lo deste artigo.

Neste artigo, aprende-se a configurar o cluster Pacemaker em RHEL 7.6 para automatizar uma base de dados SAP HANA. Você precisa ter uma boa compreensão de Linux, SAP HANA e Pacemaker para completar os passos neste guia.

A tabela seguinte inclui os nomes de anfitrião que são usados ao longo deste artigo. Os blocos de código no artigo mostram os comandos que precisam de ser executados, bem como a saída desses comandos. Preste muita atenção ao nó referenciado em cada comando.

| Tipo | Nome do anfitrião | Nó|
|-------|-------------|------|
|Anfitrião primário|`sollabdsm35`|nó 1|
|Anfitrião secundário|`sollabdsm36`|nó 2|

## <a name="configure-your-pacemaker-cluster"></a>Configure o seu cluster Pacemaker


Antes de começar a configurar o cluster, crie a troca de chaves SSH para estabelecer a confiança entre nós.

1. Utilize os seguintes comandos para criar idênticos `/etc/hosts` em ambos os nós.

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    0.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    0.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    0.20.251.150 sollabdsm36-st

    10.20.251.151 sollabdsm35-st

    

    10.20.252.151 sollabdsm36-back

    10.20.252.150 sollabdsm35-back

    

    10.20.253.151 sollabdsm36-node

    10.20.253.150 sollabdsm35-node

    ```

2.  Criar e trocar as chaves SSH.
    1. Gerem chaves de ssh.

       ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
       ```
    2. Copie as chaves dos outros anfitriões para ssh sem palavras-passe.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  Desative o selinux em ambos os nós.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. Reinicie os servidores e, em seguida, utilize o seguinte comando para verificar o estado do selinux.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. Configure NTP (Protocolo de Tempo de Rede). Os fusos horários e horários de ambos os nós de cluster devem coincidir. Utilize o seguinte comando para abrir `chrony.conf` e verificar o conteúdo do ficheiro.
    1. Os seguintes conteúdos devem ser adicionados ao ficheiro config. Altere os valores reais de acordo com o seu ambiente.
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. Ativar o serviço de crono de crony. 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. Atualizar o Sistema
    1. Em primeiro lugar, instale as últimas atualizações no sistema antes de começar a instalar o dispositivo SBD.
    1. Se não pretender uma atualização completa do sistema, mesmo que seja recomendado, atualize no mínimo os seguintes pacotes.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```
 

7. Instale os repositórios SAP HANA e RHEL-HA.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. Instale as ferramentas Pacemaker, SBD, OpenIPMI, ipmitools e fencing_sbd em todos os nós.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitools
    ```

  ## <a name="configure-watchdog"></a>Configure Cão de Guarda

Nesta secção, aprende-se a configurar o Watchdog. Esta secção utiliza os mesmos dois anfitriões `sollabdsm35` `sollabdsm36` e, referenciado no início deste artigo.

1. Certifique-se de que o cão de guarda daemon não está a funcionar em nenhum sistema.
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. O cão de guarda Linux predefinido, que será instalado durante a instalação, é o cão de guarda iTCO que não é suportado por sistemas UCS e HPE SDFlex. Portanto, este cão de guarda deve ser desativado.
    1. O cão de guarda errado é instalado e carregado no sistema:
       ```
   
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. Descarregar o condutor errado do ambiente:
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. Para se certificar de que o condutor não está carregado durante a próxima bota do sistema, o condutor deve estar bloqueado. Para bloquear os módulos iTCO, adicione o seguinte ao final do `50-blacklist.conf` ficheiro:
       ```
   
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. Copie o ficheiro para o anfitrião secundário.
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. Teste se o serviço ipmi foi iniciado. É importante que o temporizador do IPMI não esteja a funcionar. A gestão do temporizador será feita a partir do serviço de pacemaker SBD.
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. Por predefinição, o dispositivo necessário é /dev/watchdog não será criado.

    ```
    No watchdog device was created

    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. Configure o cão de guarda IPMI.

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. Copie o ficheiro config do cão de guarda para o secundário.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  Ativar e iniciar o serviço ipmi.
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     Agora o serviço IPMI é iniciado e o dispositivo /dev/watchdog é criado – Mas o temporizador ainda está parado. Mais tarde, o SBD irá gerir o reset do cão de guarda e ativa o temporizador IPMI.
7.  Verifique se existe o /dev/watchdog, mas não está a ser utilizado.
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>Configuração SBD
Nesta secção, aprende-se a configurar o SBD. Esta secção utiliza os mesmos dois anfitriões `sollabdsm35` `sollabdsm36` e, referenciado no início deste artigo.

1.  Certifique-se de que o disco iSCSI ou FC está visível em ambos os nós. Este exemplo utiliza um dispositivo SBD baseado em FC. Para obter mais informações sobre a esgrima SBD, consulte a [documentação de referência.](http://www.linux-ha.org/wiki/SBD_Fencing)
2.  O LUN-ID deve ser idêntico em todos os nós.
  
3.  Verifique o estado de vários caminhos para o dispositivo sbd.
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  Criando os discos SBD e configurando a esgrima primitiva do cluster. Este passo deve ser executado no primeiro nó.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  Copie o config SBD para o nó2.
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  Verifique se o disco SBD é visível de ambos os nós.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  Adicione o dispositivo SBD no ficheiro config SBD.

    ```
    \# SBD_DEVICE specifies the devices to use for exchanging sbd messages

    \# and to monitor. If specifying more than one path, use ";" as

    \# separator.

    \#

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    \## Type: yesno
     Default: yes
     \# Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>Inicialização do cluster
Nesta secção, inicializa-se o aglomerado. Esta secção utiliza os mesmos dois anfitriões `sollabdsm35` `sollabdsm36` e, referenciado no início deste artigo.

1.  Configurar a palavra-passe do utilizador do cluster (todos os nós).
    ```
    passwd hacluster
    ```
2.  Inicie o PCS em todos os sistemas.
    ```
    systemctl enable pcsd
    ```
  

3.  Pare a firewall e desative-a (todos os nós).
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  Inicie o serviço pcsd.
    ```
    systemctl start pcsd
    ```
  
  

5.  Executar a autenticação do cluster apenas a partir do nó1.

    ```
    pcs cluster auth sollabdsm35 sollabdsm36



        Username: hacluster

            Password:

            sollabdsm35.localdomain: Authorized

            sollabdsm36.localdomain: Authorized

     ``` 

6.  Criar o cluster.
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  Verifique o estado do cluster.

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. Se um nó não estiver a juntar-se ao cluster, verifique se a firewall ainda está em funcionamento.

  

9. Criar e ativar o dispositivo SBD
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  

10. Parar o cluster reiniciar os serviços de cluster (em todos os nós).

    ```
    pcs cluster stop --all
    ```


11. Reinicie os serviços de cluster (em todos os nós).

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. O Corosync tem de iniciar o serviço SBD.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. Reinicie o cluster (se não for iniciado automaticamente a partir do pcsd).

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Ativar as definições de Stonith.
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. Verifique o novo estado do cluster com agora um recurso.
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. Agora o temporizador IPMI tem de funcionar e o dispositivo /dev/watchdog deve ser aberto por sbd.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm351 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. Verifique o estado do SBD.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. Teste a esgrima SBD batendo o núcleo.

    * Aciona o Acidente de Kernel.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * O segundo teste a ser executado é vedar um nó utilizando comandos PCS.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. Para o resto do agrupamento SAP HANA pode desativar o STONITH definindo:

   * conjunto de propriedade pcs `stonith-enabled=false`
   * Este parâmetro deve ser definido para uma utilização produtiva. Se este parâmetro não for definido como verdadeiro, o cluster não será suportado.
   * conjunto de propriedade pcs `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>Integração hana no cluster

Nesta secção, integra-se a HANA no aglomerado. Esta secção utiliza os mesmos dois anfitriões `sollabdsm35` `sollabdsm36` e, referenciado no início deste artigo.

Existem duas opções para integrar a HANA. A primeira opção é uma solução otimizada de custos onde pode utilizar o sistema secundário para executar o sistema QAS. Não recomendamos este método porque não deixa nenhum sistema para testar atualizações no software de cluster, sistema operativo ou HANA, e as atualizações de configuração podem levar a um tempo de inatividade não planeado do sistema prd. Além disso, se o sistema prd precisar de ser ativado no sistema secundário, o QAS deve ser desligado no nó secundário. A segunda opção é instalar o sistema QAS num cluster e utilizar um segundo cluster para o PRD. Esta opção também permite testar todos os componentes antes de serem colocados em produção. Este artigo mostra-lhe como configurar a segunda opção.


* Este processo é construído da descrição do RHEL na página:

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>Passos a seguir para configurar o HSR

1.  Estas são as ações a executar no nó1 (primário).
    1. Certifique-se de que o modo de registo da base de dados está definido para normal.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p SAPhana10 -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. A replicação do sistema SAP HANA só funcionará depois de ter sido realizada a cópia de segurança inicial. O seguinte comando cria uma cópia de segurança inicial no `/tmp/` diretório. Selecione um sistema de ficheiros de cópia de segurança adequado para a base de dados. 
       ```
       * hdbsql -i 00 -u system -p SAPhana10 "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```
    

    3. Faça cópia de segurança de todos os contentores da base de dados desta base de dados.
       ```
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"
   
       
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. Ativar o processo HSR no sistema de origem.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. Verifique o estado do sistema primário.
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. Estas são as ações a executar no nó2 (secundário).
     1. Pare a base de dados.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. Apenas para SAP HANA2.0, copie o sistema SAP HANA `PKI SSFS_HR2.KEY` e `SSFS_HR2.DAT` os ficheiros do nó primário ao nó secundário.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. Ativar secundário como local de replicação.
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. Inicie a base de dados.
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. Verifique o estado da base de dados.
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. Também é possível obter mais informações sobre o estado de replicação:
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>Descrição do modo de replicação do registo

Para obter mais informações sobre o modo de replicação do registo, consulte a [documentação oficial](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/c039a1a5b8824ecfa754b55e0caffc01.html)do SAP .
  

#### <a name="network-setup-for-hana-system-replication"></a>Configuração da rede para replicação do sistema HANA


Para garantir que o tráfego de replicação está a utilizar o VLAN certo para a replicação, deve ser configurado corretamente no `global.ini` . Se saltar este passo, HANA usará o Access VLAN para a replicação, que pode não serdesejável.


Os exemplos a seguir mostram a configuração de resolução do nome do anfitrião para a replicação do sistema num site secundário. Podem ser identificadas três redes distintas:

* Rede pública com endereços no intervalo de 10.0.1.*

* Rede de comunicação interna SAP HANA entre anfitriões em cada local: 192.168.1.*

* Rede dedicada à replicação do sistema: 10.5.1.*

No primeiro exemplo, o `[system_replication_communication]listeninterface` parâmetro foi definido para e `.global` apenas os anfitriões do local de replicação vizinho são especificados.

No exemplo seguinte, o `[system_replication_communication]listeninterface` parâmetro foi definido e todos os `.internal` anfitriões de ambos os sites são especificados.

  

### <a name="source-sap-ag-sap-hana-hrs-networking"></a>Rede DE REDE SOURCE SAP AG SAP HANA HRS

  

Para a replicação do sistema, não é necessário editar o ficheiro, os `/etc/hosts` nomes de anfitriões internos ('virtual') devem ser mapeados para endereços IP no `global.ini` ficheiro para criar uma rede dedicada para replicação do sistema. A sintaxe para isto é a seguinte:

global.ini

[system_replication_hostname_resolution]

<ip-address_site>=<name_site> interno-anfitrião


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>Configure SAP HANA em um cluster pacemaker
Nesta secção, você aprende a configurar SAP HANA em um cluster pacemaker. Esta secção utiliza os mesmos dois anfitriões `sollabdsm35` `sollabdsm36` e, referenciado no início deste artigo.

Certifique-se de que cumpriu os seguintes pré-requisitos:  

* O cluster pacemaker é configurado de acordo com a documentação e tem esgrima adequada e de trabalho

* A startup SAP HANA no arranque é desativada em todos os nós do cluster, uma vez que o início e paragem serão geridos pelo cluster

* A replicação e aquisição do sistema SAP HANA utilizando ferramentas do SAP estão a funcionar corretamente entre os nós de cluster

* SAP HANA contém conta de monitorização que pode ser usada pelo cluster a partir de ambos os nós de cluster

* Ambos os nós são subscritos em canais 'Alta disponibilidade' e 'RHEL for SAP HANA' (RHEL 6,RHEL 7)

  

* Em geral, execute todos os comandos do PCS apenas a partir do nó, uma vez que o CIB será automaticamente atualizado a partir da concha do pcs.

* [Mais informações sobre a política do quórum](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>Passos para configurar 
1. Configurar pcs.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  Configuure o corosync.
    ```
    https://access.redhat.com/solutions/1293523 --> quorum information RHEL7

    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

1.  Criar recurso SAPHanaTopology clonado.
    ```
    pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 InstanceNumber=00 --clone clone-max=2 clone-node-max=1 interleave=true
    SAPHanaTopology resource is gathering status and configuration of SAP
    HANA System Replication on each node. SAPHanaTopology requires
    following attributes to be configured.



        Attribute Name Description

        SID SAP System Identifier (SID) of SAP HANA installation. Must be
    same for all nodes.

    InstanceNumber 2-digit SAP Instance identifier.
    pcs resource show SAPHanaTopology_HR2_00-clone

    Clone: SAPHanaTopology_HR2_00-clone

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true

        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat
    type=SAPHanaTopology)

        Attributes: InstanceNumber=00 SID=HR2

        Operations: monitor interval=60 timeout=60
    (SAPHanaTopology_HR2_00-monitor-interval-60)

        start interval=0s timeout=180
    (SAPHanaTopology_HR2_00-start-interval-0s)

        stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)

    ```

3.  Criar recurso SAPHana primário/secundário.

    ```
    SAPHana resource is responsible for starting, stopping and relocating the SAP HANA database. This resource must be run as a Primary/    Secondary cluster resource. The resource has the following attributes.

    

    Attribute Name Required? Default value Description

    SID Yes None SAP System Identifier (SID) of SAP HANA installation. Must be same for all nodes.

    InstanceNumber Yes none 2-digit SAP Instance identifier.

    PREFER_SITE_TAKEOVER

    no yes Should cluster prefer to switchover to secondary instance instead of restarting primary locally? ("no": Do prefer restart locally;   "yes": Do prefer takeover to remote site)

    AUTOMATED_REGISTER no false Should the former SAP HANA primary be registered as secondary after takeover and DUPLICATE_PRIMARY_TIMEOUT?     ("false": no, manual intervention will be needed; "true": yes, the former primary will be registered by resource agent as secondary)

    DUPLICATE_PRIMARY_TIMEOUT no 7200 Time difference (in seconds) needed between primary time stamps, if a dual-primary situation occurs. If   the time difference is less than the time gap, then the cluster holds one or both instances in a "WAITING" status. This is to give an   admin a chance to react on a failover. A failed former primary will be registered after the time difference is passed. After this   registration to the new primary all data will be overwritten by the system replication.
    ```
  

5.  Crie o recurso HANA.
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200   AUTOMATED_REGISTER=true primary notify=true clone-max=2 clone-node-max=1 interleave=true

    pcs resource show SAPHana_HR2_00-primary



    Primary: SAPHana_HR2_00-primary

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true

        Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)

        Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200
    InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2

        Operations: demote interval=0s timeout=320
    (SAPHana_HR2_00-demote-interval-0s)

        monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)

        monitor interval=121 role=Secondary timeout=60
    (SAPHana_HR2_00-monitor-

        interval-121)

        monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-

        interval-119)

        promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)

        start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)

        stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  Crie o recurso de endereço IP virtual.

    ```
    Cluster will contain Virtual IP address in order to reach the Primary instance of SAP HANA. Below is example command to create IPaddr2  resource with IP 10.7.0.84/24

    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  Criar constrangimentos.

    ```
    For correct operation we need to ensure that SAPHanaTopology resources are started before starting the SAPHana resources and also that  the virtual IP address is present on the node where the Primary resource of SAPHana is running. To achieve this, the following 2    constraints need to be created.

    pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
    pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
    ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>Testar o movimento manual do recurso SAPHana para outro nó

#### <a name="sap-hana-takeover-by-cluster"></a>(Aquisição SAP Hana por cluster)


Para testar a deslocação do recurso SAPHana de um nó para outro, utilize o comando abaixo. Note que a opção `--primary` não deve ser utilizada quando executa o seguinte comando devido ao funcionamento interno do recurso SAPHana.
```pcs resource move SAPHana_HR2_00-primary```

Após cada utilização do recurso do pcs mover a invocação do comando, o cluster cria restrições de localização para conseguir o movimento do recurso. Estes constrangimentos devem ser eliminados para permitir a sua caduência automática no futuro.
Para removê-los pode utilizar o comando que se segue.
```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* Faça login na HANA como verificação.

  * hospedeiro despromovido:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * Anfitrião promovido:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

Com `AUTOMATED_REGISTER=false` opção, não é possível alternar para trás e para a frente.

Se esta opção for definida como falsa, deve voltar a registar o nó:

  
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```
  

Agora o nó2, que foi o principal, atua como anfitrião secundário.

Considere definir esta opção de verdade para automatizar o registo do hospedeiro despromovido.

  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true

pcs cluster node clear node1
```
