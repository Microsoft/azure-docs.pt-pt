---
title: Solucionar SAP HANA problemas de configuração do HSR 2,0 scale out-pacemaker com SLES 12 SP3 em máquinas virtuais do Azure | Microsoft Docs
description: Guia para verificar e solucionar problemas de uma configuração complexa de alta disponibilidade de SAP HANA com base na HSR (replicação do sistema SAP HANA) e no pacemaker no SLES 12 SP3 em execução em máquinas virtuais do Azure
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
ms.openlocfilehash: 299fba8a082f19f17ab581a6ac2bfac9fd3f8cf1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099664"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Verificar e solucionar problemas SAP HANA configuração de alta disponibilidade em expansão no SLES 12 SP3 

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


Este artigo ajuda a verificar a configuração do cluster pacemaker para SAP HANA expansão que é executada em VMs (máquinas virtuais) do Azure. A configuração do cluster foi realizada em combinação SAP HANA com a HSR (replicação do sistema) e o pacote SAPHanaSR do SUSE RPM. Todos os testes foram feitos somente no SUSE SLES 12 SP3. As seções do artigo abrangem áreas diferentes e incluem exemplos de comandos e trechos de arquivos de configuração. Recomendamos esses exemplos como um método para verificar e verificar toda a configuração do cluster.



## <a name="important-notes"></a>Observações importantes

Todos os testes de SAP HANA expansão em combinação com a replicação de sistema SAP HANA e pacemaker foram feitos apenas com SAP HANA 2,0. A versão do sistema operacional foi SUSE Linux Enterprise Server 12 SP3 para aplicativos SAP. O pacote RPM mais recente, SAPHanaSR-scale out da SUSE, foi usado para configurar o cluster pacemaker.
O SUSE publicou uma [Descrição detalhada dessa configuração com otimização de desempenho][sles-hana-scale-out-ha-paper].

Para os tipos de máquina virtual com suporte para expansão de SAP HANA, verifique o [diretório de IaaS certificado SAP Hana][sap-hana-iaas-list].

Houve um problema técnico com SAP HANA expansão em combinação com várias sub-redes e vNICs e Configurando o HSR. É obrigatório usar os patches mais recentes do SAP HANA 2,0 em que esse problema foi corrigido. Há suporte para as seguintes versões de SAP HANA: 

* Rev 2.00.024.04 ou superior 
* Rev 2.00.032 ou superior

Se você precisar de suporte do SUSE, siga este [guia][suse-pacemaker-support-log-files]. Colete todas as informações sobre o SAP HANA cluster de alta disponibilidade (HA), conforme descrito no artigo. O suporte a SUSE precisa dessas informações para análise posterior.

Durante o teste interno, a configuração do cluster ficou confusa por um desligamento normal de VM normais por meio do portal do Azure. Portanto, recomendamos que você teste um failover de cluster por outros métodos. Use métodos como forçar um pane no kernel ou desligar as redes ou migrar o recurso **MSL** . Consulte os detalhes nas seções a seguir. A suposição é que um desligamento padrão ocorra com intenção. O melhor exemplo de um desligamento intencional é para manutenção. Consulte os detalhes em [manutenção planejada](#planned-maintenance).

Além disso, durante o teste interno, a configuração do cluster ficou confusa depois que um SAP HANA manual tomada enquanto o cluster estava no modo de manutenção. Recomendamos que você o alterne novamente manualmente antes de encerrar o modo de manutenção do cluster. Outra opção é disparar um failover antes de colocar o cluster no modo de manutenção. Para obter mais informações, consulte [manutenção planejada](#planned-maintenance). A documentação do SUSE descreve como você pode redefinir o cluster dessa forma usando o comando **CRM** . Mas a abordagem mencionada anteriormente era robusta durante o teste interno e nunca mostrou nenhum efeito colateral inesperado.

Ao usar o comando **CRM Migrate** , certifique-se de limpar a configuração do cluster. Ele adiciona restrições de local às quais você talvez não esteja ciente. Essas restrições afetam o comportamento do cluster. Veja mais detalhes em [manutenção planejada](#planned-maintenance).



## <a name="test-system-description"></a>Descrição do sistema de teste

 Para SAP HANA verificação e certificação de escalabilidade horizontal, uma configuração foi usada. Ele consistiu em dois sistemas com três nós SAP HANA cada: um mestre e dois trabalhadores. A tabela a seguir lista os nomes de VM e os endereços IP internos. Todos os exemplos de verificação que se seguem foram feitos nessas VMs. Usando esses nomes de VM e endereços IP nos exemplos de comando, você pode entender melhor os comandos e suas saídas:


| Tipo de nó | o nome da VM | Endereço IP |
| --- | --- | --- |
| Nó mestre no site 1 | hso-hana-vm-s1-0 | 10.0.0.30 |
| Nó de trabalho 1 no site 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| Nó de trabalho 2 no site 1 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Nó mestre no site 2 | hso-hana-vm-s2-0 | 10.0.0.40 |
| Nó de trabalho 1 no site 2 | hso-hana-vm-s2-1 | 10.0.0.41 |
| Nó de trabalho 2 no site 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Nó do fabricante principal | HSO-Hana-DM | 10.0.0.13 |
| Servidor do dispositivo SBD | HSO-Hana-SBD | 10.0.0.19 |
| | | |
| Servidor NFS 1 | hso-nfs-vm-0 | 10.0.0.15 |
| Servidor NFS 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Várias sub-redes e vNICs

A seguir SAP HANA recomendações de rede, três sub-redes foram criadas em uma rede virtual do Azure. A expansão do SAP HANA no Azure deve ser instalada no modo não compartilhado. Isso significa que cada nó usa volumes de disco local para **/Hana/data** e **/Hana/log**. Como os nós usam apenas volumes de disco locais, não é necessário definir uma sub-rede separada para armazenamento:

- 10.0.2.0/24 para comunicação entre nós SAP HANA
- 10.0.1.0/24 para replicação de sistema de SAP HANA (HSR)
- 10.0.0.0/24 para todo o resto

Para obter informações sobre SAP HANA configuração relacionada ao uso de várias redes, consulte [SAP Hana global. ini](#sap-hana-globalini).

Cada VM no cluster tem três vNICs que correspondem ao número de sub-redes. [Como criar uma máquina virtual do Linux no Azure com várias placas de interface de rede][azure-linux-multiple-nics] descreve um possível problema de roteamento no Azure ao implantar uma VM do Linux. Este artigo de roteamento específico se aplica somente ao uso de vários vNICs. O problema é resolvido pelo SUSE por padrão no SLES 12 SP3. Para obter mais informações, consulte [multi-NIC com Cloud-netconfig no EC2 e no Azure][suse-cloud-netconfig].


Para verificar se SAP HANA está configurado corretamente para usar várias redes, execute os comandos a seguir. Primeiro, verifique no nível do sistema operacional se todos os três endereços IP internos para todas as três sub-redes estão ativos. Se você tiver definido as sub-redes com intervalos de endereços IP diferentes, precisará adaptar os comandos:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

A saída de exemplo a seguir é do segundo nó de trabalho no site 2. Você pode ver três endereços IP internos diferentes de eth0, eth1 e ETH2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Em seguida, verifique as portas SAP HANA para o servidor de nome e HSR. SAP HANA deve escutar nas sub-redes correspondentes. Dependendo do número da instância do SAP HANA, você precisa adaptar os comandos. Para o sistema de teste, o número da instância era **00**. Há diferentes maneiras de descobrir quais portas são usadas. 

A instrução SQL a seguir retorna a ID da instância, o número da instância e outras informações:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Para localizar os números de porta corretos, você pode procurar, por exemplo, no HANA Studio em **configuração** ou por meio de uma instrução SQL:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Para localizar todas as portas usadas na pilha de software SAP, incluindo SAP HANA, pesquise [portas TCP/IP de todos os produtos SAP][sap-list-port-numbers].

Dado o número de instância **00** no sistema de teste SAP Hana 2,0, o número da porta para o servidor de nome é **30001**. O número da porta para a comunicação de metadados HSR é **40002**. Uma opção é entrar em um nó de trabalho e, em seguida, verificar os serviços do nó mestre. Para este artigo, verificamos o nó 2 de trabalho no site 2 tentando se conectar ao nó mestre no site 2.

Verifique a porta do servidor de nomes:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Para provar que a comunicação entre os nós usa a sub-rede **10.0.2.0/24**, o resultado deve ser semelhante ao exemplo de saída a seguir.
Somente a conexão por meio da sub-rede **10.0.2.0/24** deve ter sucesso:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Agora, verifique a porta HSR **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Para provar que a comunicação HSR usa a sub-rede **10.0.1.0/24**, o resultado deve ser semelhante ao exemplo de saída a seguir.
Somente a conexão por meio da sub-rede **10.0.1.0/24** deve ter sucesso:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


O arquivo de configuração **corosync** deve estar correto em todos os nós do cluster, incluindo o nó do criador de maioria. Se a junção de cluster de um nó não funcionar conforme o esperado, crie ou copie **/etc/corosync/corosync.conf** manualmente em todos os nós e reinicie o serviço. 

O conteúdo de **corosync. conf** do sistema de teste é um exemplo.

A primeira seção é **totem**, conforme descrito em [instalação do cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation), etapa 11. Você pode ignorar o valor de **mcastaddr**. Basta manter a entrada existente. As entradas de **token** e **consenso** devem ser definidas de acordo com a [documentação de SAP Hana Microsoft Azure][sles-pacemaker-ha-guide].

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

A segunda seção, **Logging**, não foi alterada dos padrões fornecidos:

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

A terceira seção mostra o **NodeList**. Todos os nós do cluster precisam aparecer com seus NodeId:

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

Na última seção, **Quorum**, é importante definir o valor de **expected_votes** corretamente. Ele deve ser o número de nós, incluindo o nó do fabricante principal. E o valor de **two_node** deve ser **0**. Não remova a entrada completamente. Basta definir o valor como **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Reinicie o serviço por meio de **systemctl**:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>Dispositivo SBD

Como configurar um dispositivo SBD em uma VM do Azure é descrito em [isolamento de SBD](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing).

Primeiro, verifique a VM do servidor SBD se houver entradas de ACL para cada nó no cluster. Execute o seguinte comando na VM do servidor SBD:


<pre><code>
targetcli ls
</code></pre>


No sistema de teste, a saída do comando é semelhante ao exemplo a seguir. Nomes de ACL como **iqn. 2006-04. HSO-dB-0. local: HSO-dB-0** devem ser inseridos como os nomes de iniciador correspondentes nas VMs. Cada VM precisa de uma diferente.

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

Em seguida, verifique se os nomes do iniciador em todas as VMs são diferentes e correspondem às entradas mostradas anteriormente. Este exemplo é do nó de trabalho 1 no site 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

A saída é semelhante ao exemplo a seguir:

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

Em seguida, verifique se a **descoberta** funciona corretamente. Execute o seguinte comando em cada nó de cluster usando o endereço IP da VM do servidor SBD:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

A saída deve ser semelhante ao exemplo a seguir:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

O próximo ponto de prova é verificar se o nó vê o dispositivo SDB. Verifique em cada nó, incluindo o nó do fabricante principal:

<pre><code>
lsscsi | grep dbhso
</code></pre>

A saída deve ser semelhante ao exemplo a seguir. No entanto, os nomes podem ser diferentes. O nome do dispositivo também pode ser alterado após a reinicialização da VM:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

Dependendo do status do sistema, às vezes ajuda a reiniciar os serviços iSCSI para resolver problemas. Em seguida, execute os seguintes comandos:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Em qualquer nó, você pode verificar se todos os nósestão claros. Certifique-se de usar o nome de dispositivo correto em um nó específico:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

A saída deve mostrar **claro** para cada nó no cluster:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Outra verificação de SBD é a opção de **despejo** do comando **SBD** . Neste comando de exemplo e saída do nó do fabricante principal, o nome do dispositivo era **SSD**, não **SDM**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

A saída, além do nome do dispositivo, deve ter a mesma aparência em todos os nós:

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

Mais uma verificação do SBD é a possibilidade de enviar uma mensagem para outro nó. Para enviar uma mensagem para o nó de trabalho 2 no site 2, execute o seguinte comando no nó de trabalho 1 no site 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

No lado da VM de destino, **HSO-Hana-VM-S2-2** neste exemplo, você pode encontrar a seguinte entrada em **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Verifique se as entradas em **/etc/sysconfig/SBD** correspondem à descrição em Configurando [pacemaker em SuSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing). Verifique se a configuração de inicialização em **/etc/iSCSI/iscsid.conf** está definida como automática.

As entradas a seguir são importantes no **/etc/sysconfig/SBD**. Adapte o valor de **ID** , se necessário:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Verifique a configuração de inicialização em **/etc/iSCSI/iscsid.conf**. A configuração necessária deve ter acontecido com o seguinte comando **iscsiadm** , descrito na documentação. Verifique e adapte-o manualmente com **vi** se ele for diferente.

Este comando define o comportamento de inicialização:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Tornar esta entrada em **/etc/iSCSI/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Durante o teste e a verificação, após a reinicialização de uma VM, o dispositivo SBD não ficou mais visível em alguns casos. Houve uma discrepância entre a configuração de inicialização e o que YaST2 mostrou. Para verificar as configurações, siga estas etapas:

1. Inicie o YaST2.
2. Selecione **serviços de rede** no lado esquerdo.
3. Role para baixo do lado direito para o **iniciador iSCSI** e selecione-o.
4. Na próxima tela, na guia **serviço** , você verá o nome do iniciador exclusivo para o nó.
5. Acima do nome do iniciador, verifique se o valor de **início do serviço** está definido como **ao inicializar**.
6. Se não for, defina-o como **ao inicializar** em vez de **manualmente**.
7. Em seguida, alterne a guia superior para **destinos conectados**.
8. Na tela **destinos conectados** , você deverá ver uma entrada para o dispositivo SBD como este exemplo: **10.0.0.19:3260 iqn. 2006-04. dbhso. local: dbhso**.
9. Verifique se o valor de inicialização está definido como **on boot**.
10. Caso contrário, escolha **Editar** e altere-o.
11. Salve as alterações e saia do YaST2.



## <a name="pacemaker"></a>Pacemaker

Depois que tudo estiver configurado corretamente, você poderá executar o seguinte comando em cada nó para verificar o status do serviço pacemaker:

<pre><code>
systemctl status pacemaker
</code></pre>

A parte superior da saída deve ser semelhante ao exemplo a seguir. É importante que o status após **ativo** seja mostrado como **carregado** e **ativo (em execução)** . O status após o **carregamento** deve ser mostrado como **habilitado**.

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

Se a configuração ainda estiver **desabilitada**, execute o seguinte comando:

<pre><code>
systemctl enable pacemaker
</code></pre>

Para ver todos os recursos configurados no pacemaker, execute o seguinte comando:

<pre><code>
crm status
</code></pre>

A saída deve ser semelhante ao exemplo a seguir. É bom que os recursos **CLN** e **MSL** sejam mostrados como interrompidos na VM do fabricante principal, **HSO-Hana-DM**. Não há SAP HANA instalação no nó do fabricante principal. Portanto, os recursos **CLN** e **MSL** são mostrados como interrompidos. É importante que ele mostre o número total correto de VMs, **7**. Todas as VMs que fazem parte do cluster devem estar listadas com o status **online**. O nó mestre primário atual deve ser reconhecido corretamente. Neste exemplo, é **HSO-Hana-VM-S1-0**:

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

Um recurso importante do pacemaker é o modo de manutenção. Nesse modo, você pode fazer modificações sem provocativa uma ação de cluster imediata. Um exemplo é uma reinicialização da VM. Um caso de uso típico seria o sistema operacional planejado ou a manutenção da infraestrutura do Azure. Consulte [manutenção planejada](#planned-maintenance). Use o seguinte comando para colocar pacemaker no modo de manutenção:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Ao verificar o **status do CRM**, você observa na saída que todos os recursos estão marcados como **não gerenciados**. Nesse estado, o cluster não reage em nenhuma alteração como iniciar ou parar SAP HANA.
O exemplo a seguir mostra a saída do comando **CRM status** enquanto o cluster está no modo de manutenção:

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


Este exemplo de comando mostra como terminar o modo de manutenção do cluster:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Outro comando **CRM** Obtém a configuração completa do cluster em um editor, para que você possa editá-la. Depois de salvar as alterações, o cluster inicia as ações apropriadas:

<pre><code>
crm configure edit
</code></pre>

Para examinar a configuração completa do cluster, use a opção de **exibição CRM** :

<pre><code>
crm configure show
</code></pre>



Após falhas dos recursos de cluster, o comando **status do CRM** mostra uma lista de **ações com falha**. Consulte o seguinte exemplo dessa saída:


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

É necessário fazer uma limpeza do cluster após falhas. Use o comando **CRM** novamente e use a opção de comando **Cleanup** para eliminar essas entradas de ação com falha. Nomeie o recurso de cluster correspondente da seguinte maneira:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

O comando deve retornar uma saída como a seguinte amostra:

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



## <a name="failover-or-takeover"></a>Failover ou tomada

Conforme discutido em [observações importantes](#important-notes), você não deve usar um desligamento normal padrão para testar o failover de cluster ou SAP Hana HSR tomada. Em vez disso, recomendamos que você dispare um pane no kernel, Force uma migração de recursos ou, possivelmente, desligue todas as redes no nível do sistema operacional de uma VM. Outro método é o comando de  **\<espera do nó\> CRM** . Consulte o [documento SuSE][sles-12-ha-paper]. 

Os três comandos de exemplo a seguir podem forçar um failover de cluster:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Conforme descrito em [manutenção planejada](#planned-maintenance), uma boa maneira de monitorar as atividades de cluster é executar **SAPHanaSR-showAttr** com o comando **Watch** :

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Ele também ajuda a examinar o status de SAP HANA paisagem proveniente de um script de Python da SAP. A configuração do cluster está procurando esse valor de status. Fica claro quando você pensar em uma falha de nó de trabalho. Se um nó de trabalho ficar inativo, SAP HANA não retornará imediatamente um erro para a integridade de todo o sistema de expansão. 

Há algumas tentativas para evitar failovers desnecessários. O cluster reage somente se o status for alterado de **OK**, valor de retorno **4**, para **erro**, valor de retorno **1**. Portanto, ele estará correto se a saída de **SAPHanaSR-showAttr** mostrar uma VM com o estado **offline**. Mas ainda não há atividade para alternar entre primário e secundário. Nenhuma atividade de cluster é disparada enquanto SAP HANA não retorna um erro.

Você pode monitorar o status de integridade do SAP Hana paisagem como  **\<ADM\>do Sid** do usuário ao chamar o script Python do SAP da seguinte maneira. Talvez seja necessário adaptar o caminho:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

A saída desse comando deve ser semelhante ao exemplo a seguir. A coluna **status do host** e o **status geral do host** são importantes. A saída real é mais larga, com colunas adicionais.
Para tornar a tabela de saída mais legível dentro deste documento, a maioria das colunas no lado direito foram removidas:

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


Há outro comando para verificar as atividades atuais do cluster. Consulte o comando a seguir e a parte final de saída depois que o nó mestre do site primário foi eliminado. Você pode ver a lista de ações de transição como **promover** o nó mestre secundário anterior, **HSO-Hana-VM-S2-0**, como o novo mestre primário. Se tudo estiver correto e todas as atividades forem concluídas, essa lista de **Resumo de transição** terá que estar vazia.

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

Há casos de uso diferentes quando se trata de manutenção planejada. Uma pergunta é se é apenas a manutenção da infraestrutura, como alterações no nível do sistema operacional e configuração de disco ou uma atualização do HANA.
Você pode encontrar informações adicionais em documentos do SUSE, como em [direção a zero tempo][sles-zero-downtime-paper] de inatividade ou [SAP Hana cenário otimizado para desempenho Sr][sles-12-for-sap]. Esses documentos também incluem exemplos que mostram como migrar manualmente um primário.

O teste interno intenso foi feito para verificar o caso de uso de manutenção da infraestrutura. Para evitar problemas relacionados à migração do primário, decidimos sempre migrar um primário antes de colocar um cluster no modo de manutenção. Dessa forma, não é necessário fazer com que o cluster se esqueça da situação anterior: qual lado era primário e qual era secundário.

Há duas situações diferentes nesse aspecto:

- **Manutenção planejada no secundário atual**. Nesse caso, você pode apenas colocar o cluster no modo de manutenção e fazer o trabalho no secundário sem afetar o cluster.

- **Manutenção planejada no primário atual**. Para que os usuários possam continuar a trabalhar durante a manutenção, você precisa forçar um failover. Com essa abordagem, você deve disparar o failover de cluster por pacemaker e não apenas no nível de SAP HANA HSR. A instalação do pacemaker dispara automaticamente o SAP HANA tomada. Você também precisa realizar o failover antes de colocar o cluster no modo de manutenção.

O procedimento para manutenção no site secundário atual é o seguinte:

1. Coloque o cluster no modo de manutenção.
2. Realize o trabalho no site secundário. 
3. Encerre o modo de manutenção do cluster.

O procedimento para manutenção no site primário atual é mais complexo:

1. Dispare manualmente um failover ou SAP HANA tomada por meio de uma migração de recurso pacemaker. Veja os detalhes a seguir.
2. SAP HANA no site primário anterior é desligado pela configuração do cluster.
3. Coloque o cluster no modo de manutenção.
4. Depois que o trabalho de manutenção for concluído, registre o antigo primário como o novo site secundário.
5. Limpe a configuração do cluster. Veja os detalhes a seguir.
6. Encerre o modo de manutenção do cluster.


A migração de um recurso adiciona uma entrada à configuração do cluster. Um exemplo é forçar um failover. Você precisa limpar essas entradas antes de encerrar o modo de manutenção. Consulte o exemplo a seguir.

Primeiro, Force um failover de cluster migrando o recurso **MSL** para o nó mestre secundário atual. Esse comando fornece um aviso de que uma **restrição de movimentação** foi criada:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Verifique o processo de failover por meio do comando **SAPHanaSR-showAttr**. Para monitorar o status do cluster, abra uma janela dedicada do Shell e inicie o comando com **Watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

A saída deve mostrar o failover manual. O nó mestre secundário anterior foi **promovido**, neste exemplo, **HSO-Hana-VM-S2-0**. O site primário anterior foi interrompido, valor LSS **1** para o nó mestre primário anterior **HSO-Hana-VM-S1-0**: 

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

Após o failover de cluster e SAP HANA tomada, coloque o cluster no modo de manutenção, conforme descrito em [pacemaker](#pacemaker).

Os comandos **SAPHanaSR-showAttr** e **CRM status** não indicam nada sobre as restrições criadas pela migração de recursos. Uma opção para tornar essas restrições visíveis é mostrar a configuração completa do recurso de cluster com o seguinte comando:

<pre><code>
crm configure show
</code></pre>

Na configuração do cluster, você encontra uma nova restrição de local causada pela migração manual de recursos anterior. Esta entrada de exemplo começa com a **CLI de localização**:

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Infelizmente, essas restrições podem afetar o comportamento geral do cluster. Portanto, é obrigatório removê-los novamente antes de colocar o backup do sistema inteiro. Com o comando unmigrate, é possível limpar as restrições de local que foram criadas antes. A nomenclatura pode ser um pouco confusa. Ele não tenta migrar o recurso de volta para a VM original da qual ele foi migrado. Ele apenas remove as restrições de local e também retorna informações correspondentes quando você executa o comando:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

No final do trabalho de manutenção, você interrompe o modo de manutenção do cluster, conforme mostrado em [pacemaker](#pacemaker).



## <a name="hb_report-to-collect-log-files"></a>hb_report para coletar arquivos de log

Para analisar problemas de cluster pacemaker, ele é útil e também solicitado pelo suporte do SUSE para executar o utilitário **hb_report** . Ele coleta todos os arquivos de log importantes que você precisa para analisar o que aconteceu. Esta chamada de exemplo usa uma hora de início e de término onde um incidente específico ocorreu. Consulte também [observações importantes](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

O comando informa onde ele colocou os arquivos de log compactados:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Em seguida, você pode extrair os arquivos individuais por meio do comando **tar** padrão:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Ao examinar os arquivos extraídos, você encontrará todos os arquivos de log. A maioria deles foi colocada em diretórios separados para cada nó no cluster:

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


Dentro do intervalo de tempo especificado, o nó mestre atual **HSO-Hana-VM-S1-0** foi eliminado. Você pode encontrar entradas relacionadas a esse evento no **Journal. log**:

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

Outro exemplo é o arquivo de log pacemaker no mestre secundário, que se tornou o novo mestre primário. Este trecho mostra que o status do nó mestre primário eliminado foi definido como **offline**:

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





## <a name="sap-hana-globalini"></a>SAP HANA global. ini


Os trechos a seguir são do SAP HANA arquivo **global. ini** no site do cluster 2. Este exemplo mostra as entradas de resolução de nome de host para usar redes diferentes para SAP HANA comunicação entre nós e HSR:

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



## <a name="hawk"></a>Hawk

A solução de cluster fornece uma interface de navegador que oferece uma GUI para usuários que preferem menus e gráficos para ter todos os comandos no nível do Shell.
Para usar a interface do navegador,  **\<substitua\> o nó** por um nó de SAP Hana real na URL a seguir. Em seguida, insira as credenciais do cluster ( **cluster**do usuário):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Esta captura de tela mostra o painel do cluster:


![Painel do cluster Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Este exemplo mostra as restrições de local causadas por uma migração de recurso de cluster, conforme explicado em [manutenção planejada](#planned-maintenance):


![Restrições da lista de Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Você também pode carregar a saída **hb_report** em Hawk em **history**, mostrada da seguinte maneira. Consulte hb_report para coletar arquivos de log: 

![Saída de hb_report de upload Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Com o **history Explorer**, você pode percorrer todas as transições de cluster incluídas na saída do **hb_report** :

![Transições Hawk na saída hb_report](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Esta captura de tela final mostra a seção de **detalhes** de uma única transição. O cluster reajam em uma falha de nó mestre primário, nó **HSO-Hana-VM-S1-0**. Agora, ele está promovendo o nó secundário como o novo mestre, **HSO-Hana-VM-S2-0**:

![Transição única Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Passos Seguintes

Este guia de solução de problemas descreve a alta disponibilidade para SAP HANA em uma configuração de expansão. Além do banco de dados, outro componente importante em uma estrutura SAP é a pilha do SAP NetWeaver. Saiba mais sobre [a alta disponibilidade do SAP NetWeaver em máquinas virtuais do Azure que usam o servidor SuSE Enterprise Linux][sap-nw-ha-guide-sles].

