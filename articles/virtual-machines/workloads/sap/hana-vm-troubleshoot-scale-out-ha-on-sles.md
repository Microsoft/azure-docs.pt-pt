---
title: SAP HANA escala HSR-Pacemaker com SLES em Azure VMs resolução de problemas Microsoft Docs
description: Guia para verificar e resolver problemas uma configuração complexa de alta disponibilidade SAP HANA com base na replicação do sistema SAP HANA (HSR) e Pacemaker em SLES 12 SP3 em execução em máquinas virtuais Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: fb90bfff72f41d8d7ccc34d3ad6dd0e9206bb88e
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566238"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Verifique e coloque problemas na configuração de alta disponibilidade da SAP HANA no SLES 12 SP3 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Este artigo ajuda-o a verificar a configuração do cluster Pacemaker para a escala SAP HANA que funciona em máquinas virtuais Azure (VMs). A configuração do cluster foi realizada em combinação com a Replicação do Sistema SAP HANA (HSR) e o pacote SUSE RPM SAPHanaSR-ScaleOut. Todos os testes foram feitos apenas em SUSE SLES 12 SP3. As secções do artigo abrangem diferentes áreas e incluem comandos de amostra e excertos de ficheiros config. Recomendamos estas amostras como um método para verificar e verificar toda a configuração do cluster.



## <a name="important-notes"></a>Notas importantes

Todos os testes para sap HANA scale-out em combinação com a Replicação do Sistema SAP HANA e Pacemaker foi feito apenas com SAP HANA 2.0. A versão do sistema operativo foi SUSE Linux Enterprise Server 12 SP3 para aplicações SAP. O mais recente pacote RPM, SAPHanaSR-ScaleOut da SUSE, foi usado para configurar o cluster Pacemaker.
A SUSE publicou uma [descrição detalhada desta configuração otimizada][sles-hana-scale-out-ha-paper]pelo desempenho.

Para tipos de máquinas virtuais suportados para escala SAP HANA, consulte o [diretório IaaS certificado sAP HANA][sap-hana-iaas-list].

Houve um problema técnico com a escala SAP HANA em combinação com várias subredes e vNICs e a criação de HSR. É obrigatório usar os mais recentes patches SAP HANA 2.0 onde este problema foi corrigido. As seguintes versões SAP HANA são suportadas: 

* rev2.00.024.04 ou superior 
* rev2.00.032 ou superior

Se precisar de apoio da SUSE, siga este [guia][suse-pacemaker-support-log-files]. Recolher todas as informações sobre o cluster de alta disponibilidade (HA) da SAP HANA, conforme descrito no artigo. O suporte da SUSE necessita desta informação para uma análise mais aprofundada.

Durante os testes internos, a configuração do cluster ficou confusa com uma paragem normal e graciosa de VM através do portal Azure. Por isso, recomendamos que teste um cluster falhapor outros métodos. Use métodos como forçar o pânico do núcleo, ou desligar as redes ou migrar o recurso **msl.** Consulte os detalhes nas seguintes secções. O pressuposto é que uma paralisação padrão acontece com intenção. O melhor exemplo de uma paragem intencional é a manutenção. Consulte os detalhes na [manutenção planeada.](#planned-maintenance)

Além disso, durante os testes internos, a configuração do cluster ficou confusa após uma aquisição manual do SAP HANA enquanto o cluster estava em modo de manutenção. Recomendamos que volte a ligá-lo manualmente antes de terminar o modo de manutenção do cluster. Outra opção é desencadear uma falha antes de colocar o cluster no modo de manutenção. Para mais informações, consulte a [manutenção planeada.](#planned-maintenance) A documentação da SUSE descreve como pode redefinir o cluster desta forma utilizando o comando **crm.** Mas a abordagem mencionada anteriormente foi robusta durante os testes internos e nunca mostrou quaisquer efeitos colaterais inesperados.

Quando utilizar o comando de **migração de crm,** certifique-se de limpar a configuração do cluster. Acrescenta constrangimentos de localização que pode não ter conhecimento. Estes constrangimentos afetam o comportamento do cluster. Veja mais detalhes na [manutenção planeada.](#planned-maintenance)



## <a name="test-system-description"></a>Descrição do sistema de teste

 Para a verificação e certificação de HA em escala SAP HANA, foi utilizada uma configuração. Consistia em dois sistemas com três nós SAP HANA cada: um mestre e dois trabalhadores. A tabela seguinte lista os nomes vm e endereços IP internos. Todas as amostras de verificação que se seguem foram feitas nestes VMs. Ao utilizar estes nomes vm e endereços IP nas amostras de comando, pode compreender melhor os comandos e as suas saídas:


| Tipo de nó | o nome da VM | Endereço IP |
| --- | --- | --- |
| Nó mestre no local 1 | hso-hana-vm-s1-0 | 10.0.0.30 |
| Nó operário 1 no local 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| Nó de trabalhador 2 no local 1 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Nó mestre no local 2 | hso-hana-vm-s2-0 | 10.0.0.40 |
| Nó operário 1 no local 2 | hso-hana-vm-s2-1 | 10.0.0.41 |
| Nó de trabalhador 2 no local 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Nó de fabricante maioritário | hso-hana-dm | 10.0.0.13 |
| Servidor de dispositivo SBD | hso-hana-sbd | 10.0.0.19 |
| | | |
| Servidor NFS 1 | hso-nfs-vm-0 | 10.0.0.15 |
| Servidor NFS 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Múltiplas subredes e vNICs

Seguindo as recomendações da rede SAP HANA, três subnets foram criadas dentro de uma rede virtual Azure. A escala SAP HANA no Azure tem de ser instalada em modo não partilhado. Isto significa que cada nó utiliza volumes de disco locais para **/hana/data** e **/hana/log**. Como os nós usam apenas volumes de disco locais, não é necessário definir uma subnet separada para armazenamento:

- 10.0.2.0/24 para comunicação interna da SAP HANA
- 10.0.1.0/24 para a replicação do sistema SAP HANA (HSR)
- 10.0.0.0.0/24 para tudo o resto

Para obter informações sobre a configuração do SAP HANA relacionada com a utilização de várias redes, consulte [o SAP HANA global.ini](#sap-hana-globalini).

Cada VM do cluster tem três vNICs que correspondem ao número de subredes. [Como criar uma máquina virtual Linux em Azure com vários cartões][azure-linux-multiple-nics] de interface de rede descreve um potencial problema de encaminhamento no Azure ao implementar um VM Linux. Este artigo de encaminhamento específico aplica-se apenas para utilização de múltiplos vNICs. O problema é resolvido pela SUSE por padrão no SLES 12 SP3. Para mais informações, consulte [Multi-NIC com cloud-netconfig em EC2 e Azure][suse-cloud-netconfig].


Para verificar se o SAP HANA está configurado corretamente para utilizar várias redes, execute os seguintes comandos. Primeiro verifique no nível de Os que os três endereços IP internos para as três subredes estão ativos. Se definiu as subredes com diferentes gamas de endereços IP, tem de adaptar os comandos:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

A saída da amostra que se segue é do segundo nó de trabalhador no local 2. Você pode ver três diferentes endereços IP internos de eth0, eth1 e eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Em seguida, verifique as portas SAP HANA para o servidor de nome e HSR. O SAP HANA deve ouvir as subredes correspondentes. Dependendo do número da instância SAP HANA, tem de adaptar os comandos. Para o sistema de teste, o número da instância era **00**. Existem diferentes formas de descobrir quais os portos utilizados. 

A seguinte declaração sQL devolve a instância ID, número de instância e outras informações:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Para encontrar os números de porta corretos, pode procurar, por exemplo, no Estúdio HANA sob **Configuração** ou através de uma declaração SQL:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Para encontrar todas as portas utilizadas na pilha de software SAP, incluindo SAP HANA, procure [portas TCP/IP de todos os produtos SAP][sap-list-port-numbers].

Dada a instância número **00** no sistema de teste SAP HANA 2.0, o número de porta do servidor de nome é **30001**. O número da porta para a comunicação de metadados HSR é **40002**. Uma opção é iniciar sessão com um nó de trabalhador e, em seguida, verificar os serviços de nó do mestre. Para este artigo, verificamos o nó de trabalhador 2 no local 2 tentando ligar-se ao nó principal no local 2.

Verifique a porta do servidor de nomes:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Para provar que a comunicação internode utiliza a sub-rede **10.0.2.0/24,** o resultado deve parecer a seguinte saída de amostra.
Só a ligação através da sub-rede **10.0.2.0/24** deve ter êxito:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Verifique agora a porta **HSR 40002:**

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Para provar que a comunicação HSR utiliza a sub-rede **10.0.1.0/24,** o resultado deve parecer a seguinte saída da amostra.
Só a ligação através da sub-rede **10.0.1.0/24** deve ter êxito:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosinso


O ficheiro de config **corosinso** tem de estar correto em todos os nós do cluster, incluindo o nó de fabricante maioritário. Se a adesão do cluster a um nó não funcionar como esperado, crie ou **copie /etc/corosync/corosync.conf** manualmente sobre todos os nós e reinicie o serviço. 

O conteúdo de **corosync.conf** do sistema de teste é um exemplo.

A primeira secção é **totem**, como descrito na [instalação do Cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation), passo 11. Pode ignorar o valor para **o mcastaddr.** Basta manter a entrada existente. As inscrições para **ficha** e **consenso** devem ser definidas de acordo com a documentação microsoft [Azure SAP HANA][sles-pacemaker-ha-guide].

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

A segunda secção, **a exploração madeireira,** não foi alterada dos defeitos dado:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

A terceira secção mostra a lista de **nodelistas.** Todos os nós do aglomerado têm que aparecer com o seu **nódeideide:**

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

Na última secção, **quórum,** é importante definir o valor para **expected_votes** corretamente. Deve ser o número de nós, incluindo o nó da maioria. E o valor para **two_node** tem de ser **0.** Não retire completamente a entrada. Basta definir o valor para **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Reiniciar o serviço via **systemctl:**

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>Dispositivo SBD

Como configurar um dispositivo SBD num VM Azure é descrito em [esgrima SBD](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing).

Primeiro, verifique o VM do servidor SBD se houver entradas ACL para cada nó do cluster. Executar o seguinte comando no VM do servidor SBD:


<pre><code>
targetcli ls
</code></pre>


No sistema de teste, a saída do comando parece a seguinte amostra. Os nomes da ACL como **iqn.2006-04.hso-db-0.local:hso-db-0** devem ser inscritos como os nomes de iniciador correspondentes nos VMs. Cada VM precisa de um diferente.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Em seguida, verifique se os nomes do iniciador em todos os VMs são diferentes e correspondem às entradas anteriormente mostradas. Este exemplo é do nó 1 do trabalhador no local 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

A saída parece a seguinte amostra:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Em seguida, verifique se a **descoberta** funciona corretamente. Executar o seguinte comando em cada nó de cluster utilizando o endereço IP do VM do servidor SBD:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

A saída deve parecer a seguinte amostra:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

O próximo ponto de prova é verificar se o nó vê o dispositivo SDB. Verifique em cada nó, incluindo o nó de fabricante maioritário:

<pre><code>
lsscsi | grep dbhso
</code></pre>

A saída deve parecer a seguinte amostra. No entanto, os nomes podem diferir. O nome do dispositivo também pode mudar após o vM reiniciar:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

Dependendo do estado do sistema, por vezes ajuda a reiniciar os serviços iSCSI para resolver problemas. Em seguida, execute os seguintes comandos:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


De qualquer nó, pode verificar se todos os nós estão **limpos.** Certifique-se de que utiliza o nome do dispositivo correto num nó específico:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

A saída deve ser **clara** para cada nó do cluster:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Outra verificação SBD é a opção de **despejo** do comando **sbd.** Nesta amostra de comando e saída do nó de fabricante maioritário, o nome do dispositivo foi **sdd**, não **sdm**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

A saída, para além do nome do dispositivo, deve ser a mesma em todos os nós:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

Mais uma verificação para o SBD é a possibilidade de enviar uma mensagem para outro nó. Para enviar uma mensagem ao nó 2 do trabalhador no local 2, execute o seguinte comando no nó 1 do trabalhador no local 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

No lado vm alvo, **hso-hana-vm-s2-2** neste exemplo, pode encontrar a seguinte entrada em **/var/log/messages:**

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Verifique se as entradas em **/etc/sysconfig/sbd** correspondem à descrição na configuração do [Pacemaker no SUSE Linux Enterprise Server em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing). Verifique se a configuração de arranque em **/etc/iscsid.conf** está definida como automática.

As seguintes entradas são importantes em **/etc/sysconfig/sbd**. Adaptar o valor **da id** se necessário:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Verifique a definição de arranque em **/etc/iscsid.conf**. A definição necessária deveria ter acontecido com o seguinte comando **IScsiadm,** descrito na documentação. Verifique e adapte manualmente com **vi** se é diferente.

Este comando define o comportamento da startup:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Faça esta inscrição em **/etc/iscsid.conf:**

<pre><code>
node.startup = automatic
</code></pre>

Durante os testes e verificação, após o reinício de um VM, o dispositivo SBD já não era visível em alguns casos. Houve uma discrepância entre o cenário de arranque e o que o YaST2 mostrou. Para verificar as definições, tome estas medidas:

1. Começa o YaST2.
2. Selecione **Serviços** de Rede no lado esquerdo.
3. Desloque-se para baixo no lado direito para **o ISCSI Initiator** e selecione-o.
4. No ecrã seguinte sob o separador **Serviço,** você vê o nome único do iniciador para o nó.
5. Acima do nome do iniciador, certifique-se de que o valor de início de **serviço** está definido para **Quando Iniciar**.
6. Se não for, coloque-o no **"When Booting"** em vez de **Manualmente**.
7. Em seguida, mude o separador superior para **Alvos Conectados**.
8. No ecrã **'Alvos Conectados',** deve ver uma entrada para o dispositivo SBD como esta amostra: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**.
9. Verifique se o valor **de arranque** está definido **no arranque**.
10. Caso contrário, escolha **Editar** e troque-o.
11. Guarde as alterações e saia yaST2.



## <a name="pacemaker"></a>Pacemaker

Depois de tudo estar configurado corretamente, pode executar o seguinte comando em cada nó para verificar o estado do serviço Pacemaker:

<pre><code>
systemctl status pacemaker
</code></pre>

A parte superior da saída deve parecer a seguinte amostra. É importante que o estado após o **Ative** seja mostrado como **carregado** e **ativo (em execução)** . O estado após **o Loaded** deve ser mostrado como **ativado**.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Se a regulação ainda estiver **desativada,** execute o seguinte comando:

<pre><code>
systemctl enable pacemaker
</code></pre>

Para ver todos os recursos configurados no Pacemaker, executar o seguinte comando:

<pre><code>
crm status
</code></pre>

A saída deve parecer a seguinte amostra. É bom que os recursos **cln** e **msl** sejam mostrados como parados na maioria do fabricante VM, **hso-hana-dm**. Não há instalação SAP HANA no nó da maioria. Assim, os recursos **da CLN** e **da MSL** são mostrados como parados. É importante que mostre o número total correto de VMs, **7**. Todos os VMs que fazem parte do cluster devem ser listados com o estado **Online**. O atual nó principal deve ser reconhecido corretamente. Neste exemplo, é **hso-hana-vm-s1-0:**

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Uma característica importante do Pacemaker é o modo de manutenção. Neste modo, pode fazer modificações sem provocar uma ação imediata do cluster. Um exemplo é um reboot vm. Um caso de utilização típica seria planejado manutenção de infraestruturas DE Os ou Azure. Ver [Manutenção Planeada](#planned-maintenance). Utilize o seguinte comando para colocar o Pacemaker no modo de manutenção:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Quando verificar com **o estado do CRM,** nota na saída que todos os recursos estão marcados como **não geridos**. Neste estado, o cluster não reage a quaisquer mudanças como iniciar ou parar o SAP HANA.
A amostra seguinte mostra a saída do comando de **estado crm** enquanto o cluster está no modo de manutenção:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


Esta amostra de comando mostra como terminar o modo de manutenção do cluster:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Outro comando **crm** obtém a configuração completa do cluster em um editor, para que você possa editá-lo. Depois de poupar as alterações, o cluster inicia as ações apropriadas:

<pre><code>
crm configure edit
</code></pre>

Para ver a configuração completa do cluster, use a opção **crm show:**

<pre><code>
crm configure show
</code></pre>



Após falhas nos recursos de cluster, o comando do **estado do CRM** mostra uma lista de **Ações Falhadas**. Consulte a seguinte amostra desta saída:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

É necessário fazer uma limpeza de cluster depois de falhas. Use novamente o comando **crm** e utilize a **limpeza** da opção de comando para se livrar destas entradas de ação falhadas. Nomeie o recurso de cluster correspondente da seguinte forma:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

O comando deve devolver a saída como a seguinte amostra:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>Falha ou aquisição

Como discutido em [notas importantes,](#important-notes)não deve utilizar uma paragem graciosa padrão para testar o failover do cluster ou a aquisição de SAP HANA HSR. Em vez disso, recomendamos que desencadeie um pânico de kernel, force uma migração de recursos, ou possivelmente desligue todas as redes no nível de SO de um VM. Outro método é o **crm \<nó\>** comando de espera. Consulte o [documento SUSE][sles-12-ha-paper]. 

Os seguintes três comandos de amostra podem forçar uma falha de aglomerado:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Como descrito na [manutenção planeada,](#planned-maintenance)uma boa maneira de monitorizar as atividades de cluster é executar **SAPHanaSR-showAttr** com o comando do **relógio:**

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Também ajuda a olhar para o estado da paisagem SAP HANA vindo de um script SAP Python. A configuração do cluster está à procura deste valor de estado. Torna-se claro quando se pensa numa falha no nó dos trabalhadores. Se um nó de trabalhador cair, o SAP HANA não devolve imediatamente um erro para a saúde de todo o sistema de escala. 

Há algumas tentativas para evitar falhas desnecessárias. O cluster só reage se o estado mudar de **Ok**, valor de retorno **4,** a **erro,** valor de retorno **1**. Portanto, é correto se a saída do **SAPHanaSR-showAttr** mostrar um VM com o estado **offline**. Mas ainda não há atividade para mudar o primário e o secundário. Nenhuma atividade de cluster é desencadeada desde que o SAP HANA não devolva um erro.

Pode monitorizar o estado de saúde da paisagem SAP HANA como utilizador **\<HANA SID\>adm,** chamando o script SAP Python da seguinte forma. Talvez tenha supor adaptar o caminho:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

A saída deste comando deve parecer a seguinte amostra. A coluna estado do **anfitrião** e o **estatuto de hospedeiro geral** são importantes. A saída real é mais larga, com colunas adicionais.
Para tornar a tabela de saída mais legível dentro deste documento, a maioria das colunas do lado direito foram despojadas:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Há outro comando para verificar as atividades atuais do cluster. Veja o seguinte comando e a cauda de saída depois do nó principal do local primário ter sido morto. Pode ver a lista de ações de transição como **a promoção** do antigo nó principal secundário, **hso-hana-vm-s2-0,** como o novo mestre primário. Se está tudo bem, e todas as atividades estiverem terminadas, esta lista de resumo de **transição** tem de estar vazia.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Manutenção planeada 

Existem casos de uso diferentes quando se trata de manutenção planeada. Uma questão é se é apenas manutenção de infraestruturas como alterações no nível de OS e configuração do disco ou um upgrade HANA.
Pode encontrar informações adicionais em documentos da SUSE como [Towards Zero Downtime][sles-zero-downtime-paper] ou [SAP HANA SR Performance Optimized Scenario][sles-12-for-sap]. Estes documentos também incluem amostras que mostram como migrar manualmente uma primária.

Foram efetuados testes internos intensos para verificar o caso de utilização da manutenção da infraestrutura. Para evitar quaisquer problemas relacionados com a migração das primárias, decidimos migrar sempre uma primária antes de colocar um cluster em modo de manutenção. Desta forma, não é necessário fazer o aglomerado esquecer a situação anterior: que lado era primário e que era secundário.

Há duas situações diferentes a este respeito:

- **Manutenção planeada no secundário atual.** Neste caso, pode colocar o cluster em modo de manutenção e fazer o trabalho no secundário sem afetar o cluster.

- **Manutenção planeada nas primárias atuais.** Para que os utilizadores possam continuar a trabalhar durante a manutenção, é necessário forçar uma falha. Com esta abordagem, deve desencadear a falha do cluster pelo Pacemaker e não apenas no nível SAP HANA HSR. A configuração do Pacemaker aciona automaticamente a aquisição do SAP HANA. Também precisa de realizar a falha antes de colocar o cluster no modo de manutenção.

O procedimento de manutenção no local secundário atual é o seguinte:

1. Coloque o cluster no modo de manutenção.
2. Realizar o trabalho no local secundário. 
3. Termine o modo de manutenção do cluster.

O procedimento de manutenção no local primário atual é mais complexo:

1. Desencadeie manualmente uma aquisição de failover ou SAP HANA através de uma migração de recursos Pacemaker. Veja os detalhes que se seguem.
2. SAP HANA no antigo local primário é encerrado pela configuração do cluster.
3. Coloque o cluster no modo de manutenção.
4. Depois de concluído o trabalho de manutenção, registe o primeiro primário como novo local secundário.
5. Limpe a configuração do cluster. Veja os detalhes que se seguem.
6. Termine o modo de manutenção do cluster.


Migrar um recurso adiciona uma entrada na configuração do cluster. Um exemplo é forçar uma falha. Tem de limpar estas entradas antes de terminar o modo de manutenção. Consulte a seguinte amostra.

Primeiro, forçar um aglomerado falhar migrando o recurso **msl** para o atual nó principal secundário. Este comando dá um aviso de que foi criada uma **restrição de movimento:**

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Verifique o processo de failover através do comando **SAPHanaSR-showAttr**. Para monitorizar o estado do cluster, abra uma janela de concha dedicada e inicie o comando com **o relógio:**

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

A saída deve mostrar a falha manual. O antigo nó de mestre secundário foi **promovido**, nesta amostra, **hso-hana-vm-s2-0**. O antigo local primário foi parado, **lss** valor **1** para o antigo mestre principal nó **hso-hana-vm-s1-0**: 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

Após a falha do cluster e a aquisição da SAP HANA, coloque o cluster no modo de manutenção, conforme descrito no [Pacemaker](#pacemaker).

Os comandos **SAPHanaSR-showAttr** e **crm status** não indicam nada sobre os constrangimentos criados pela migração de recursos. Uma opção para tornar estes constrangimentos visíveis é mostrar a configuração completa do recurso de cluster com o seguinte comando:

<pre><code>
crm configure show
</code></pre>

Dentro da configuração do cluster, encontra-se uma nova restrição de localização causada pela migração de recursos manuais anteriores. Esta entrada de exemplo começa com **a localização cli:**

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Infelizmente, tais constrangimentos podem ter impacto no comportamento geral do cluster. Por isso, é obrigatório removê-los de novo antes de trazer todo o sistema de volta. Com o comando **não migrado,** é possível limpar os constrangimentos de localização que foram criados antes. O nome pode ser um pouco confuso. Não tenta migrar o recurso de volta para o VM original do qual foi migrado. Apenas remove os constrangimentos de localização e também devolve as informações correspondentes quando executa o comando:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

No final dos trabalhos de manutenção, pare o modo de manutenção do cluster como mostrado no [Pacemaker](#pacemaker).



## <a name="hb_report-to-collect-log-files"></a>hb_report recolher ficheiros de registo

Para analisar os problemas do cluster Pacemaker, é útil e também solicitado pelo suporte da SUSE para executar o **utilitário hb_report.** Recolhe todos os ficheiros de registo importantes que precisa para analisar o que aconteceu. Esta chamada de amostra utiliza um tempo de início e fim onde ocorreu um incidente específico. Ver Também [notas importantes:](#important-notes)

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

O comando diz-lhe onde colocou os ficheiros de registo comprimidos:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Em seguida, pode extrair os ficheiros individuais através do comando padrão de **alcatrão:**

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Quando olhas para os ficheiros extraídos, encontras todos os ficheiros de registo. A maioria deles foram colocados em diretórios separados para cada nó do cluster:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


Dentro do intervalo de tempo especificado, o atual nó mestre **hso-hana-vm-s1-0** foi morto. Pode encontrar entradas relacionadas com este evento no **diário.log:**

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Outro exemplo é o ficheiro de registo pacemaker do mestre secundário, que se tornou o novo mestre primário. Este excerto mostra que o estado do nó principal morto foi definido para **offline:**

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


Os seguintes excertos são do ficheiro **global.ini** da SAP HANA no site do cluster 2. Este exemplo mostra as entradas de resolução de nome de anfitrião para a utilização de diferentes redes para a comunicação interna de SAP HANA e HSR:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Falcão

A solução cluster fornece uma interface de navegador que oferece um GUI para os utilizadores que preferem menus e gráficos a ter todos os comandos no nível da concha.
Para utilizar a interface do navegador, substitua **\<nó\>** por um nó SAP HANA real no seguinte URL. Em seguida, introduza as credenciais do cluster **(cluster**do utilizador):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Esta imagem mostra o painel de fragmentação:


![Painel de instrumentos de cluster hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Este exemplo mostra os constrangimentos de localização causados por uma migração de recursos de cluster, tal como explicado na [manutenção planeada:](#planned-maintenance)


![Restrições da lista de falcões](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Também pode fazer o upload da **saída hb_report** em Hawk under **History**, mostrada da seguinte forma. Consulte hb_report para recolher ficheiros de registo: 

![Saída de hb_report de upload de falcão](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Com o **History Explorer,** pode então passar por todas as transições de cluster incluídas na **saída hb_report:**

![Transições de falcão na produção hb_report](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Esta imagem final mostra a secção **Detalhes** de uma única transição. O aglomerado reagiu num acidente no nó principal, nó **hso-hana-vm-s1-0**. Está agora a promover o nó secundário como o novo mestre, **hso-hana-vm-s2-0:**

![Transição única falcão](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Passos seguintes

Este guia de resolução de problemas descreve a alta disponibilidade para o SAP HANA numa configuração de escala- out. Além da base de dados, outro componente importante numa paisagem SAP é a pilha SAP NetWeaver. Saiba mais sobre [a elevada disponibilidade para SAP NetWeaver em máquinas virtuais Azure que usam o Servidor Linux Da Empresa SUSE][sap-nw-ha-guide-sles].

