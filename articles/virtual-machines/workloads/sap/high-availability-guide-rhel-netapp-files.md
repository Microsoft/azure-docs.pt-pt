---
title: Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver em Red Hat Enterprise Linux com Azure NetApp Files | Microsoft Docs
description: Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 98a12e6892ac8710ae2195cd2c29df43b4c65aba
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71706287"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver em Red Hat Enterprise Linux com Azure NetApp Files para aplicativos SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7,50 altamente disponível, usando [Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
Nas configurações de exemplo, comandos de instalação etc. A instância de ASCS é o número 00, a instância ERS é o número 01, a instância de aplicativo principal (PAS) é 02 e a instância do aplicativo (AAS) é 03. A ID do sistema SAP QAS é usada. 

A camada de banco de dados não é abordada em detalhes neste artigo.  

Leia as seguintes notas e documentos SAP primeiro:

* [Documentação do Azure NetApp Files][anf-azure-doc] 
* Nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure com suporte para a implantação de software SAP
  * Informações de capacidade importantes para tamanhos de VM do Azure
  * Software SAP com suporte e combinações de so (sistema operacional) e banco de dados
  * Versão de kernel do SAP necessária para Windows e Linux em Microsoft Azure

* O SAP Note [2015553] lista os pré-requisitos para implantações de software SAP com suporte no SAP no Azure.
* A observação do SAP [2002167] tem as configurações do sistema operacional recomendadas para Red Hat Enterprise Linux
* A observação do SAP [2009879] tem diretrizes SAP HANA para Red Hat Enterprise Linux
* A nota SAP [2178632] tem informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure.
* A nota SAP [2191498] tem a versão do agente de host do SAP necessária para Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento SAP no Linux no Azure.
* A nota SAP [1999351] tem informações adicionais para solução de problemas para a extensão de monitoramento avançado do Azure para SAP.
* O [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Planejamento e implementação de máquinas virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [SAP NetWeaver no cluster pacemaker](https://access.redhat.com/articles/3150081)
* Documentação geral do RHEL
  * [Visão geral do complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configurando o ASCS/ERS para SAP NetWeaver com recursos autônomos no RHEL 7,5](https://access.redhat.com/articles/3569681)
  * [Configurar o SAP S/4HANA ASCS/ERS com o servidor de enfileiramento autônomo 2 (ENSA2) no pacemaker no RHEL](https://access.redhat.com/articles/3974941)
* Documentação do RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade RHEL-Máquinas Virtuais do Microsoft Azure como membros do cluster](https://access.redhat.com/articles/3131341)
  * [Instalando e configurando um cluster de alta disponibilidade Red Hat Enterprise Linux 7,4 (e posterior) no Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Aplicativos SAP da NetApp em Microsoft Azure usando Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Descrição geral

HA (alta disponibilidade) para serviços centrais do SAP NetWeaver requer armazenamento compartilhado.
Para conseguir isso no Red Hat Linux até o momento, era necessário criar um cluster GlusterFS de alta disponibilidade separado. 

Agora é possível obter a alta disponibilidade do SAP NetWeaver usando o armazenamento compartilhado, implantado em Azure NetApp Files. O uso de Azure NetApp Files para o armazenamento compartilhado elimina a necessidade de um [cluster GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)adicional. O pacemaker ainda é necessário para HA dos serviços centrais do SAP NetWeaver (ASCS/SCS).

![Visão geral de alta disponibilidade do SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e o banco de dados SAP HANA usar o nome de host virtual e endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. A lista a seguir mostra a configuração do balanceador de carga com IPs de front-end separados para (A) SCS e ERS.

> [!IMPORTANT]
> **Não há suporte para**clustering de vários SIDs do SAP ASCS/ers com Red Hat Linux como sistema operacional convidado em VMs do Azure. O clustering de vários SID descreve a instalação de várias instâncias do SAP ASCS/ERS com SIDs diferentes em um cluster pacemaker.

### <a name="ascs"></a>UM SCS

* Configuração de front-end
  * 192.168.14.9 de endereço IP
* Configuração de back-end
  * Conectado às interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte do cluster (A) SCS/ERS
* Porta de investigação
  * Porta 620<strong>&lt;NR&gt;</strong>
* Regras de balanceamento de carga
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * <strong>5&lt;NR&gt;</strong>13 TCP
  * <strong>5&lt;NR&gt;</strong>14 TCP
  * <strong>5&lt;NR&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configuração de front-end
  * 192.168.14.10 de endereço IP
* Configuração de back-end
  * Conectado às interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte do cluster (A) SCS/ERS
* Porta de investigação
  * Porta 621<strong>&lt;NR&gt;</strong>
* Regras de balanceamento de carga
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * <strong>5&lt;NR&gt;</strong>13 TCP
  * <strong>5&lt;NR&gt;</strong>14 TCP
  * <strong>5&lt;NR&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configurando a infraestrutura de Azure NetApp Files 

O SAP NetWeaver requer armazenamento compartilhado para o diretório de transporte e de perfil.  Antes de prosseguir com a configuração da infraestrutura de arquivos do Azure NetApp, familiarize-se com a [documentação do Azure NetApp files][anf-azure-doc]. Verifique se a região do Azure selecionada oferece Azure NetApp Files. O link a seguir mostra a disponibilidade de Azure NetApp Files pela região do Azure: [Disponibilidade de Azure NetApp files pela região do Azure][anf-avail-matrix].

Os arquivos do Azure NetApp estão disponíveis em várias [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Antes de implantar Azure NetApp Files, solicite a integração ao Azure NetApp Files, seguindo as [instruções registrar para arquivos do Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implantar Azure NetApp Files recursos  

As etapas pressupõem que você já tenha implantado a [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Os recursos de Azure NetApp Files e as VMs, em que os recursos de Azure NetApp Files serão montados devem ser implantados na mesma rede virtual do Azure ou em redes virtuais emparelhadas do Azure.  

1. Se você ainda não fez isso, solicite [a integração ao Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Crie a conta do NetApp na região do Azure selecionada, seguindo as [instruções para criar uma conta do NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Configure Azure NetApp Files pool de capacidade, seguindo as [instruções sobre como configurar o pool de capacidade Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
A arquitetura do SAP NetWeaver apresentada neste artigo usa um único pool de capacidade de Azure NetApp Files, SKU Premium. É recomendável Azure NetApp Files SKU Premium para carga de trabalho do aplicativo SAP NetWeaver no Azure.  
4. Delegue uma sub-rede para os arquivos do Azure NetApp conforme descrito nas [instruções delegar uma sub-rede para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Implante Azure NetApp Files volumes, seguindo as [instruções para criar um volume para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Implante os volumes na [sub-rede](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Azure NetApp files designada. Tenha em mente que os recursos Azure NetApp Files e as VMs do Azure devem estar na mesma rede virtual do Azure ou em redes virtuais emparelhadas do Azure. Neste exemplo, usamos dois volumes Azure NetApp Files: SAP<b>QAS</b> e transSAP. Os caminhos de arquivo montados nos pontos de montagem correspondentes são/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS</b>/usrsap<b>QAS sys,</b>etc.  

   1. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ASCs)
   3. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ERS)
   5. volume transSAP (nfs://192.168.24.4/transSAP)
   6. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>Pas)
   7. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)
  
Neste exemplo, usamos Azure NetApp Files para todos os sistemas de arquivos do SAP NetWeaver para demonstrar como Azure NetApp Files pode ser usado. Os sistemas de arquivos SAP que não precisam ser montados via NFS também podem ser implantados como [armazenamento em disco do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . Neste exemplo, <b>a-e</b> deve estar em Azure NetApp Files e <b>f-g</b> (ou seja,/usr/SAP/<b>QAS</b>/d<b>02</b>,/usr/SAP/<b>QAS</b>/d<b>03</b>) pode ser implantado como armazenamento em disco do Azure. 

### <a name="important-considerations"></a>Considerações importantes

Ao considerar Azure NetApp Files para o SAP NetWeaver na arquitetura de alta disponibilidade do SUSE, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é 4 TiB. O tamanho do pool de capacidade deve estar em múltiplos de 4 TiB.
- O volume mínimo é 100 GiB
- Azure NetApp Files e todas as máquinas virtuais, nas quais Azure NetApp Files volumes serão montados, devem estar na mesma rede virtual do Azure ou em [redes virtuais emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) na mesma região. Agora há suporte para o acesso Azure NetApp Files sobre o emparelhamento VNET na mesma região. Ainda não há suporte para o acesso do Azure NetApp sobre o emparelhamento global.
- A rede virtual selecionada deve ter uma sub-rede, delegada para Azure NetApp Files.
- Azure NetApp Files atualmente dá suporte apenas a NFSv3 
- O Azure NetApp Files oferece [política de exportação](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): você pode controlar os clientes permitidos, o tipo de acesso (leitura & gravação, somente leitura, etc.). 
- O recurso de Azure NetApp Files ainda não reconhece a zona. Atualmente Azure NetApp Files recurso não está implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja atento às possíveis implicações de latência em algumas regiões do Azure. 

## <a name="setting-up-ascs"></a>Configurando o (A) SCS

Neste exemplo, os recursos foram implantados manualmente por meio do [portal do Azure](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Implantar o Linux manualmente por meio de portal do Azure

Primeiro, você precisa criar os volumes de Azure NetApp Files. Implante as VMs. Posteriormente, você criará um balanceador de carga e usará as máquinas virtuais nos pools de back-end.

1. Criar um Load Balancer (interno)  
   1. Criar os endereços IP de front-end
      1. Endereço IP 192.168.14.9 para o ASCS
         1. Abra o balanceador de carga, selecione pool de IPS de front-end e clique em Adicionar
         1. Insira o nome do novo pool de IPS de front-end (por exemplo, **frontend. QAS. ASCS**)
         1. Defina a atribuição como estática e insira o endereço IP (por exemplo **192.168.14.9**)
         1. Clique em OK
      1. Endereço IP 192.168.14.10 para ASCS ERS
         * Repita as etapas acima em "a" para criar um endereço IP para o ERS (por exemplo, **192.168.14.10** e **frontend. QAS. ERS**)
   1. Criar os pools de back-end
      1. Criar um pool de back-end para o ASCS
         1. Abra o balanceador de carga, selecione pools de back-end e clique em Adicionar
         1. Insira o nome do novo pool de back-end (por exemplo, o **back-end. QAS**)
         1. Clique em adicionar uma máquina virtual.
         1. Selecione o conjunto de disponibilidade que você criou anteriormente para ASCS 
         1. Selecione as máquinas virtuais do cluster (A) SCS
         1. Clique em OK
   1. Criar as investigações de integridade
      1. Porta 620**00** para ASCS
         1. Abra o balanceador de carga, selecione investigações de integridade e clique em Adicionar
         1. Insira o nome da nova investigação de integridade (por exemplo, **integridade. QAS. ASCS**)
         1. Selecione TCP como protocolo, porta 620**00**, manter intervalo 5 e limite não íntegro 2
         1. Clique em OK
      1. Porta 621**01** para ASCS ers
            * Repita as etapas acima em "c" para criar uma investigação de integridade para o ERS (por exemplo, 621**01** e a **integridade. QAS. ERS**)
   1. Regras de balanceamento de carga
      1. 32**00** TCP para ASCS
         1. Abra o balanceador de carga, selecione regras de balanceamento de carga e clique em Adicionar
         1. Insira o nome da nova regra do balanceador de carga (por exemplo, **lb. QAS. ASCS. 3200**)
         1. Selecione o endereço IP de front-end para ASCS, pool de back-end e investigação de integridade que você criou anteriormente (por exemplo, **frontend. QAS. ASCS**)
         1. Mantenha o protocolo **TCP**, insira a porta **3200**
         1. Aumentar o tempo limite de ociosidade para 30 minutos
         1. **Certifique-se de habilitar o IP flutuante**
         1. Clique em OK
      1. Portas adicionais para o ASCS
         * Repita as etapas acima em "d" para as portas**36 00**,**39 00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 e TCP para o ASCS
      1. Portas adicionais para o ASCS ERS
         * Repita as etapas acima em "d" para as portas 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 e TCP para o ASCS ers


> [!IMPORTANT]
> Não habilite carimbos de data/hora TCP em VMs do Azure colocadas por trás Azure Load Balancer. Habilitar carimbos de data/hora TCP fará com que as investigações de integridade falhem. Defina o parâmetro **net. IPv4. TCP _timestamps** como **0**. Para obter detalhes, consulte [Load Balancer investigações de integridade](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Criar cluster pacemaker

Siga as etapas em [Configurando pacemaker em Red Hat Enterprise Linux no Azure](high-availability-guide-rhel-pacemaker.md) para criar um cluster pacemaker básico para esse servidor (a) SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Preparar para a instalação do SAP NetWeaver

Os seguintes itens são prefixados com ambos **[A]** - aplicáveis a todos os nós, **[1]** – apenas aplicável no nó 1 ou **[2]** – apenas aplicável a nó 2.

1. **[A]**  Configurar a resolução de nomes de anfitrião

   Pode utilizar um servidor DNS ou modificar os /etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir

    ```
    sudo vi /etc/hosts
    ```

   Insira as seguintes linhas ao /etc/hosts. Alterar o endereço IP e o nome de anfitrião para corresponder ao seu ambiente

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** crie diretórios SAP no volume Azure NetApp files.  
   Monte temporariamente o volume Azure NetApp Files em uma das VMs e crie os diretórios SAP (caminhos de arquivo).  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp
    ``` 

1. **[A]** criar os diretórios compartilhados

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** instalar cliente NFS e outros requisitos

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** verificar a versão do recurso-agentes-SAP

   Certifique-se de que a versão do pacote Resource-Agents-SAP está no mínimo 3.9.5 -124. EL7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** adicionar entradas de montagem

   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Montar os novos compartilhamentos

   ```
   sudo mount -a  
   ```

1. **[A]** configurar arquivo de permuta

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Reiniciar o agente para ativar a alteração

   ```
   sudo service waagent restart
   ```

1. Configuração **de [A]** RHEL

   Configure o RHEL conforme descrito em SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalando o SAP NetWeaver ASCS/ERS

1. **[1]** criar um recurso de IP virtual e uma investigação de integridade para a instância de ASCS

   ```
   sudo pcs node standby anftstsapcl2
   
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Verifique se o status do cluster é OK e se todos os recursos foram iniciados. Não é importante em qual nó os recursos estão em execução.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** instalar o ASCS do SAP NetWeaver  

   Instale o ASCS do SAP NetWeaver como raiz no primeiro nó usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o ASCS, por exemplo, <b>anftstsapvh</b>, <b>192.168.14.9</b> e o número da instância que você usou para o investigação do balanceador de carga, por exemplo <b>00</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Se a instalação não conseguir criar uma subpasta no/usr/SAP/**QAS**/ASCS**00**, tente definir o proprietário e o grupo da pasta ASCS**00** e tente novamente.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** criar um recurso de IP virtual e uma investigação de integridade para a instância de ers

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' \
    --group g-QAS_AERS

   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Verifique se o status do cluster é OK e se todos os recursos foram iniciados. Não é importante em qual nó os recursos estão em execução.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** instalar o ers do SAP NetWeaver  

   Instale o ERS do SAP NetWeaver como raiz no segundo nó usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o ERS, por exemplo, <b>anftstsapers</b>, <b>192.168.14.10</b> e o número da instância que você usou para o investigação do balanceador de carga, por exemplo <b>01</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Se a instalação não conseguir criar uma subpasta no/usr/SAP/**QAS**/ers**01**, tente definir o proprietário e o grupo da pasta ers**01** e tente novamente.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** adaptar os perfis de instância ASCS/SCS e ers

   * Perfil do ASCS/SCS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * Perfil de ERS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** configurar Keep Alive

   A comunicação entre o servidor de aplicativos SAP NetWeaver e o ASCS/SCS é roteada por meio de um balanceador de carga de software. O balanceador de carga desconecta conexões inativas após um tempo limite configurável. Para evitar isso, você precisa definir um parâmetro no perfil do SAP NetWeaver ASCS/SCS e alterar as configurações do sistema Linux. Leia a [Observação do SAP 1410736][1410736] para obter mais informações.

   O parâmetro de perfil ASCS/SCS enfileirar/encni/set_so_keepalive já foi adicionado na última etapa.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** atualizar o arquivo/usr/SAP/sapservices

   Para evitar o início das instâncias pelo script de inicialização sapinit, todas as instâncias gerenciadas pelo pacemaker devem ser comentadas no arquivo/usr/SAP/sapservices. Não comente a SAP HANA instância se ela será usada com o HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** criar os recursos de cluster do SAP

   Se estiver usando a arquitetura do enqueue Server 1 (ENSA1), defina os recursos da seguinte maneira:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   O SAP introduziu o suporte para o enqueue Server 2, incluindo a replicação, a partir do SAP NW 7,52. A partir do ABAP Platform 1809, o enqueue Server 2 é instalado por padrão. Consulte o SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) para suporte ao servidor de enfileiramento 2.
   Se estiver usando[ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)(Enqueue Server 2 Architecture), instale o agente de recurso Resource-Agents-SAP-4.1.1 12. EL7. x86_64 ou mais recente e defina os recursos da seguinte maneira:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Se você estiver atualizando de uma versão mais antiga e alternando para o servidor de enfileiramento 2, consulte a observação do SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Verifique se o status do cluster é OK e se todos os recursos foram iniciados. Não é importante em qual nó os recursos estão em execução.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** adicionar regras de firewall para ASCS e ers em ambos os nós adicione as regras de firewall para ASCS e ers em ambos os nós.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparação do servidor de aplicativos do SAP NetWeaver

   Alguns bancos de dados exigem que a instalação da instância do banco de dados seja executada em um servidor de aplicativos. Prepare as máquinas virtuais do servidor de aplicativos para poder usá-las nesses casos.  

   As etapas abaixo pressupõem que você instale o servidor de aplicativos em um servidor diferente dos servidores ASCS/SCS e HANA. Caso contrário, algumas das etapas abaixo (como configurar a resolução de nome de host) não são necessárias.  

   Os seguintes itens são prefixados com **[A]** -aplicável ao pas e ao AAS, **[P]** – aplicável somente ao pas ou **[S]** – aplicável somente ao AAS.  

1. **[A]** configurar resolução de nome de host você pode usar um servidor DNS ou modificar o/etc/hosts em todos os nós. Este exemplo mostra como utilizar o ficheiro /etc/hosts.
   Substitua o endereço IP e o nome do host nos seguintes comandos:  

   ```
   sudo vi /etc/hosts
   ```

   Insira as seguintes linhas ao /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** criar o diretório sapmnt crie o diretório sapmnt.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** instalar cliente NFS e outros requisitos  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** adicionar entradas de montagem  

   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Montar os novos compartilhamentos

   ```
   sudo mount -a
   ```

1. **[P]** criar e montar o diretório do Pas  

   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

1. **[S]** criar e montar o diretório AAS  

   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```


1. **[A]** configurar arquivo de permuta
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Reiniciar o agente para ativar a alteração

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Instalar banco de dados

Neste exemplo, o SAP NetWeaver está instalado em SAP HANA. Você pode usar cada banco de dados com suporte para esta instalação. Para obter mais informações sobre como instalar SAP HANA no Azure, consulte [alta disponibilidade de SAP Hana em VMs do Azure no Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Executar a instalação da instância do banco de dados SAP

   Instale a instância de banco de dados do SAP NetWeaver como raiz usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o banco de dados.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Instalação do servidor de aplicativos SAP NetWeaver

Siga estas etapas para instalar um servidor de aplicativos SAP.

1. Preparar servidor de aplicativos

   Siga as etapas no capítulo [preparação do servidor de aplicativos SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) acima para preparar o servidor de aplicativos.

1. Instalar o servidor de aplicativos SAP NetWeaver

   Instale um servidor de aplicativos SAP NetWeaver primário ou adicional.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Atualizar SAP HANA repositório seguro

   Atualize o SAP HANA repositório seguro para apontar para o nome virtual da configuração de replicação do sistema do SAP HANA.

   Execute o comando a seguir para listar as entradas como \<sapsid > ADM

   ```
   hdbuserstore List
   ```

   Isso deve listar todas as entradas e deve ser semelhante a
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   A saída mostra que o endereço IP da entrada padrão está apontando para a máquina virtual e não para o endereço IP do balanceador de carga. Essa entrada precisa ser alterada para apontar para o nome de host virtual do balanceador de carga. Certifique-se de usar a mesma porta (**30313** na saída acima) e o nome do banco de dados (**QAS** na saída acima)!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

1. Migrar manualmente a instância de ASCS

   Estado do recurso antes de iniciar o teste:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Execute os comandos a seguir como raiz para migrar a instância de ASCS.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Simular falha de nó

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Execute o seguinte comando como raiz no nó em que a instância ASCS está em execução

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   O status após o nó ser iniciado novamente deve ter a seguinte aparência.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Use o comando a seguir para limpar os recursos com falha.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Eliminar processo do servidor de mensagens

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Execute os seguintes comandos como raiz para identificar o processo do servidor de mensagens e desmatar.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   Se você eliminar apenas o servidor de mensagens uma vez, ele será reiniciado por `sapstart`. Se você o eliminar com frequência suficiente, o pacemaker eventualmente moverá a instância ASCS para o outro nó. Execute os comandos a seguir como raiz para limpar o estado do recurso da instância ASCS e ERS após o teste.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Processo do servidor de eliminação de enfileiramento

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Execute os comandos a seguir como raiz no nó em que a instância ASCS está sendo executada para eliminar o servidor de enfileiramento.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   A instância ASCS deve fazer failover imediatamente para o outro nó. A instância ERS também deve fazer failover depois que a instância ASCS é iniciada. Execute os comandos a seguir como raiz para limpar o estado do recurso da instância ASCS e ERS após o teste.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Processo do servidor de replicação do Kill enqueue

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Execute o seguinte comando como raiz no nó em que a instância ERS está em execução para eliminar o processo do servidor de replicação de enfileiramento.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Se você executar o comando apenas uma vez `sapstart` , o reiniciará o processo. Se você executá-lo com frequência `sapstart` suficiente, o não reiniciará o processo e o recurso estará em um estado parado. Execute os comandos a seguir como raiz para limpar o estado do recurso da instância ERS após o teste.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Eliminar enqueue sapstartsrv processo

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Execute os comandos a seguir como raiz no nó em que o ASCS está em execução.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   O processo sapstartsrv sempre deve ser reiniciado pelo agente de recursos do pacemaker como parte do monitoramento. Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Passos seguintes

* [Planejamento e implementação de máquinas virtuais do Azure para SAP][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA no Azure (instâncias grandes), consulte [alta disponibilidade e recuperação de desastre do SAP Hana (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA em VMs do Azure, consulte [alta disponibilidade de SAP Hana em VMS (máquinas virtuais) do Azure][sap-hana-ha]
