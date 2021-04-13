---
title: Escala SAP HANA com HSR e Pacemaker em SLES | Microsoft Docs
description: Escala SAP HANA com HSR e Pacemaker em SLES.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 637616c3698cc9ec0cd13a4584bad24b6ed02c34
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315498"
---
# <a name="high-availability-for-sap-hana-scale-out-system-with-hsr-on-suse-linux-enterprise-server"></a>Alta disponibilidade para sistema de escala SAP HANA com HSR no SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Este artigo descreve como implementar um sistema SAP HANA altamente disponível numa configuração de escala com replicação do sistema HANA (HSR) e Pacemaker em Azure SUSE Linux Enterprise Server (VMs). Os sistemas de ficheiros partilhados na arquitetura apresentada são fornecidos pela [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) e são montados sobre NFS.  

Nas configurações de exemplo, comandos de instalação, e assim por diante, a instância HANA é **03** e o ID do sistema HANA é **HN1**. Os exemplos são baseados no HANA 2.0 SP4 e no SUSE Linux Enterprise Server 12 SP5. 

Antes de começar, consulte as seguintes notas e documentos SAP:

* [Documentação dos Ficheiros Azure NetApp][anf-azure-doc] 
* Sap Note [1928533] inclui:  
  * Uma lista de tamanhos Azure VM que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade dos tamanhos Azure VM
  * Combinações suportadas de software SAP e sistema operativo (OS) e de base de dados
  * A versão necessária do kernel SAP para Windows e Linux no Microsoft Azure
* NOTA SAP [2015553]: Lista pré-requisitos para implementações de software SAP suportadas pelo SAP em Azure
* NOTA SAP [2205917]: Contém definições de SO recomendadas para SUSE Linux Enterprise Server para aplicações SAP
* Nota SAP [1944799]: Contém diretrizes SAP para SUSE Linux Enterprise Server para aplicações SAP
* Nota [SAP 2178632]: Contém informações detalhadas sobre todas as métricas de monitorização reportadas para a SAP em Azure
* NOTA SAP [2191498]: Contém a versão necessária do Agente anfitrião SAP para Linux em Azure
* Nota [SAP 2243692]: Contém informações sobre o licenciamento SAP em Linux em Azure
* NOTA SAP [1984787]: Contém informações gerais sobre o SUSE Linux Enterprise Server 12
* Nota SAP [1999351]: Contém informações adicionais de resolução de problemas para a extensão de monitorização melhorada do Azure para o SAP
* Nota [SAP 1900823]: Contém informações sobre os requisitos de armazenamento SAP HANA
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Contém todas as notas SAP necessárias para Linux
* [Azure Virtual Machines planejamento e implementação para SAP em Linux][planning-guide]
* [Implementação de Máquinas Virtuais Azure para SAP em Linux][deployment-guide]
* [Azure Virtual Machines DBMS implantação para SAP em Linux][dbms-guide]
* [Guias de boas práticas SUSE SAP HA][suse-ha-guide]: Contém todas as informações necessárias para configurar a Alta Disponibilidade do NetWeaver e a replicação do sistema SAP HANA no local (a ser utilizada como base geral; fornecem informações muito mais detalhadas)
* [SUSE Extensão de Alta Disponibilidade 12 Notas de lançamento SP5](https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP5/)
* [Manipulação falhada da participação da NFS no cluster SUSE HA para replicação do sistema HANA](https://www.suse.com/support/kb/doc/?id=000019904)
* [Aplicações NETApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]
* [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Descrição Geral

Um método para alcançar a alta disponibilidade de HANA para instalações de escala HANA, é configurar a replicação do sistema HANA e proteger a solução com o cluster Pacemaker para permitir a falha automática. Quando um nó ativo falha, o cluster falha sobre os recursos HANA para o outro local.  
A configuração apresentada mostra três nós HANA em cada site, mais nó de fabricante maioritário para evitar cenário de cérebro dividido. As instruções podem ser adaptadas, para incluir mais VMs como nós HANA DB.  

O sistema de ficheiros partilhados HANA `/hana/shared` na arquitetura apresentada é fornecido pela [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md). É montado via NFSv4.1 em cada nó HANA no mesmo local de replicação do sistema HANA. Sistemas de `/hana/data` ficheiros e `/hana/log` são sistemas de ficheiros locais e não são partilhados entre os nós HANA DB. O SAP HANA será instalado em modo não partilhado. 

> [!TIP]
> Para configurações de armazenamento SAP HANA recomendadas, consulte [as configurações de armazenamento SAP HANA Azure VMs](./hana-vm-operations-storage.md).   

[![Escala SAP HANA com cluster HSR e Pacemaker em SLES](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse.png)](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse-detail.png#lightbox)

No diagrama anterior, três sub-redes estão representadas numa rede virtual Azure, seguindo as recomendações da rede SAP HANA: 
* para comunicação com o cliente - `client` 10.23.0.0/24  
* para a comunicação interna do nó inter-nódoa - `inter` 10.23.1.128/26  
* para a replicação do sistema HANA - `hsr` 10.23.1.192/26  

Como `/hana/data` e `/hana/log` são implantados em discos locais, não é necessário implementar sub-redes separadas e cartões de rede virtuais separados para comunicação ao armazenamento.  

Os volumes Azure NetApp são implantados numa sub-rede separada, delegada nos [Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet): `anf` 10.23.1.0/26.   

> [!IMPORTANT]
> A replicação do sistema para um 3º site não é suportada. Para mais detalhes consulte a secção "Pré-requisitos importantes" no [cenário otimizado do desempenho otimizado do desempenho da replicação do sistema SLES-SAP HANA](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_important_prerequisites).     

## <a name="set-up-the-infrastructure"></a>Configurar a infraestrutura

Nas instruções que se seguem, assumimos que já criou o grupo de recursos, a rede virtual Azure com três sub-redes de rede Azure: `client` e `inter` `hsr` .

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Implementar máquinas virtuais Linux através do portal Azure
1. Desdobre os VMs Azure.  
Para a configuração apresentada neste documento, desloque sete máquinas virtuais: 
   - três máquinas virtuais para servir como nó de HANA DB para o local de replicação HANA 1: **hana-s1-db1,** **hana-s1-db2** e **hana-s1-db3**  
   - três máquinas virtuais para servir como nó de HANA DB para o local de replicação HANA 2: **hana-s2-db1,** **hana-s2-db2** e **hana-s2-db3**  
   - uma pequena máquina virtual para servir de *fabricante maioritário*: **hana-s-mm**

   Os VMs, implantados como nós SAP DB HANA devem ser certificados pela SAP para HANA, conforme publicado no diretório de [hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ao colocar os nós HANA DB, certifique-se de que a [Rede Acelerada](../../../virtual-network/create-vm-accelerated-networking-cli.md) está selecionada.  
  
   Para o nó de fabricante maioritário, você pode implementar um pequeno VM, uma vez que este VM não executam nenhum dos recursos SAP HANA. O VM criador maioritário é usado na configuração do cluster para alcançar um número ímpar de nós de cluster em um cenário de cérebro dividido. O fabricante maioritário VM só precisa de uma interface de rede virtual `client` na sub-rede neste exemplo.        

   Implemente discos geridos locais para `/hana/data` e `/hana/log` . A configuração mínima recomendada de armazenamento para `/hana/data` e é descrita nas `/hana/log` [configurações de armazenamento SAP HANA Azure VMs](./hana-vm-operations-storage.md).

   Implementar a interface de rede primária para cada VM na `client` sub-rede de rede virtual.  
   Quando o VM é implantado através do portal Azure, o nome da interface de rede é gerado automaticamente. Nestas instruções de simplicidade, referimo-nos às interfaces de rede primária geradas automaticamente, que estão ligadas à `client` sub-rede virtual Azure como **hana-s1-db1-cliente,** **hana-s1-db2-cliente,** **hana-s1-db3-cliente**, e assim por diante.  


   > [!IMPORTANT]
   > Certifique-se de que o sistema operativo selecionado é certificado por SAP para SAP HANA nos tipos de VM específicos que está a utilizar. Para obter uma lista de tipos de VM certificados da SAP HANA e lançamentos de SO para esses tipos, aceda ao site [de plataformas iaaS certificadas da SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Clique nos detalhes do tipo VM listado para obter a lista completa de versões de SO suportadas por SAP HANA para este tipo.  
  

2. Criar seis interfaces de rede, uma para cada máquina virtual HANA DB, na `inter` sub-rede de rede virtual (neste exemplo, **hana-s1-db1-inter,** **hana-s1-db2-inter,** **hana-s1-db3-inter,** **hana-s2-db1-inter,** **hana-s2-db2-inter,** e **hana-s2-db3-inter).**  

3. Criar seis interfaces de rede, um para cada máquina virtual HANA DB, na `hsr` sub-rede de rede virtual (neste exemplo, **hana-s1-db1-hsr,** **hana-s1-db2-hsr**, **hana-s1-db3-hsr**, **hana-s2-db1-hsr**, **hana-s2-db2-hsr**, e **hana-s2-db3-hsrs .**  

4. Anexar as interfaces de rede virtual recém-criadas às máquinas virtuais correspondentes:  

    a. Aceda à máquina virtual no [portal Azure](https://portal.azure.com/#home).  

    b. No painel esquerdo, selecione **Máquinas Virtuais.** Filtrar o nome da máquina virtual (por exemplo, **hana-s1-db1**), e, em seguida, selecionar a máquina virtual.  

    c. No **painel de visão** geral, selecione **Stop** para negociar a máquina virtual.  

    d. Selecione **Networking** e, em seguida, anexe a interface de rede. Na lista de drop-down da interface de **rede Attach,** selecione as interfaces de rede já criadas para as `inter` `hsr` sub-redes e sub-redes.  
    
    e. Selecione **Guardar**. 
 
    f. Repita os passos b através de e para as restantes máquinas virtuais (no nosso exemplo, **hana-s1-db2**, **hana-s1-db3**, **hana-s2-db1**, **hana-s2-db2** e **hana-s2-db3).**
 
    exemplo, Deixe as máquinas virtuais em estado de paragem por enquanto. Em seguida, permitiremos [uma rede acelerada](../../../virtual-network/create-vm-accelerated-networking-cli.md) para todas as interfaces de rede recém-anexadas.  

5. Permitir uma rede acelerada para as interfaces de rede adicionais para as `inter` `hsr` sub-redes e sub-redes, fazendo os seguintes passos:  

    a. Abra [a Concha da Nuvem Azure](https://azure.microsoft.com/features/cloud-shell/) no portal [Azure](https://portal.azure.com/#home).  

    b. Execute os seguintes comandos para permitir a ligação acelerada da rede para as interfaces de rede adicionais, que estão ligadas às `inter` `hsr` sub-redes e às sub-redes.  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. Inicie as máquinas virtuais HANA DB

### <a name="deploy-azure-load-balancer"></a>Implementar esquilibrador de carga Azure

1. Recomendamos a utilização do balanceador de carga padrão. Siga estes passos de configuração para implementar o balanceador de carga padrão:
   1. Primeiro, crie um pool IP frontal:

      1. Abra o balançador de carga, selecione **o pool IP frontend** e selecione **Adicionar**.
      1. Insira o nome do novo pool IP frontal (por exemplo, **hana-frontend).**
      1. Desaponda a **Estática** e introduza o endereço IP (por exemplo, **10.23.0.27**). 
      1. Selecione **OK**.
      1. Após a criação do novo pool IP frontal, note o endereço IP do pool.

   1. Em seguida, crie uma piscina traseira e adicione todos os VMs de cluster à piscina de backend:

      1. Abra o balançador de carga, selecione **piscinas de backend** e selecione **Adicionar**.
      1. Insira o nome da nova piscina traseira (por exemplo, **hana-backend).**
      1. **Selecione Adicionar uma máquina virtual**.
      1. Selecione **Máquina virtual**.
      1. Selecione as máquinas virtuais do cluster SAP HANA e os seus endereços IP para a `client` sub-rede.
      1. Selecione **Adicionar**.

   1. Em seguida, criar uma sonda de saúde:

      1. Abra o equilibrador de carga, selecione **sondas de saúde** e selecione **Adicionar**.
      1. Insira o nome da nova sonda de saúde (por exemplo, **hana-hp).**
      1. Selecione **TCP** como protocolo e porta 625 **03**. Mantenha o valor **de intervalo** definido para 5, e o valor do **limiar insalubre** definido para 2.
      1. Selecione **OK**.

   1. Em seguida, crie as regras de equilíbrio de carga:
   
      1. Abra o balançador de carga, selecione **as regras de equilíbrio de carga** e selecione **Adicionar**.
      1. Introduza o nome da nova regra do balançador de carga (por exemplo, **hana-lb**).
      1. Selecione o endereço IP frontal, o pool traseiro e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend,** **hana-backend** e **hana-hp).**
      1. Selecione **portas HA**.
      1. Certifique-se de que ativa o **IP flutuante**.
      1. Selecione **OK**.

   > [!IMPORTANT]
   > O IP flutuante não é suportado numa configuração IP secundária do NIC em cenários de equilíbrio de carga. Para mais detalhes consulte [as limitações do balançador de carga Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se precisar de um endereço IP adicional para o VM, implante um segundo NIC.    
   
   > [!Note]
   > Quando os VMs sem endereços IP públicos forem colocados no pool de backend de saldos de carga standard Azure (sem endereço IP público), não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para obter detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Azure Standard Load Balancer em cenários de alta disponibilidade SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  


   > [!IMPORTANT]
   > Não ative os cartas temporais TCP em VMs Azure colocados atrás do Balançador de Carga Azure. Permitir os tempos de TCP fará com que as sondas de saúde falhem. Definir o parâmetro **net.ipv4.tcp_timestamps** a **0**. Para mais detalhes consulte [as sondas de saúde load balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).
   > Consulte também a nota [SAP 2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Implementar a infraestrutura de Ficheiros Azure NetApp 

Implemente os volumes ANF para o `/hana/shared` sistema de ficheiros. Necessitará de um volume separado `/hana/shared` para cada site de replicação do sistema HANA. Para obter mais informações, consulte [configurar a infraestrutura Azure NetApp Files](./sap-hana-scale-out-standby-netapp-files-suse.md#set-up-the-azure-netapp-files-infrastructure).

Neste exemplo, foram utilizados os seguintes volumes Azure NetApp Files: 

* volume **HN1**-shared-s1 (nfs://10.23.1.7/**HN1**-shared-s1)
* volume **HN1**-shared-s2 (nfs://10.23.1.7/**HN1**-shared-s2)


## <a name="operating-system-configuration-and-preparation"></a>Configuração e preparação do sistema operativo

As instruções nas secções seguintes são pré-fixas com uma das seguintes abreviaturas:
* **[A]**: Aplicável a todos os nós
* **[AH]**: Aplicável a todos os nós HANA DB
* **[M]**: Aplicável ao nó de fabricante maioritário
* **[AH1]**: Aplicável a todos os nós HANA DB no SITE 1
* **[AH2]**: Aplicável a todos os nós HANA DB no SITE 2
* **[1]**: Aplicável apenas ao nó HANA DB 1, SITE 1
* **[2]**: Aplicável apenas ao nó HANA DB 1, SITE 2

Configure e prepare o seu SO fazendo os seguintes passos:

1. **[A]** Mantenha os ficheiros hospedeiros nas máquinas virtuais. Incluir entradas para todas as sub-redes. Foram adicionadas as seguintes entradas `/etc/hosts` para este exemplo.  

    ```bash
     # Client subnet
     10.23.0.19      hana-s1-db1
     10.23.0.20      hana-s1-db2
     10.23.0.21      hana-s1-db3
     10.23.0.22      hana-s2-db1
     10.23.0.23      hana-s2-db2
     10.23.0.24      hana-s2-db3
     10.23.0.25      hana-s-mm    
     # Internode subnet
     10.23.1.132     hana-s1-db1-inter
     10.23.1.133     hana-s1-db2-inter
     10.23.1.134     hana-s1-db3-inter
     10.23.1.135     hana-s2-db1-inter
     10.23.1.136     hana-s2-db2-inter
     10.23.1.137     hana-s2-db3-inter
     # HSR subnet
     10.23.1.196     hana-s1-db1-hsr
     10.23.1.197     hana-s1-db2-hsr
     10.23.1.198     hana-s1-db3-hsr
     10.23.1.199     hana-s2-db1-hsr
     10.23.1.200     hana-s2-db2-hsr
     10.23.1.201     hana-s2-db3-hsr
    ```

2. **[A]** SUSE fornece agentes de recursos especiais para SAP HANA e por defeito agentes para SAP HANA ScaleUp são instalados. Desinstalar as embalagens para ScaleUp, se instaladas e instalar as embalagens para o cenário SAP HANAScaleOut. O passo tem de ser dado em todos os VMs do cluster, incluindo o fabricante maioritário.   

    ```bash
     # Uninstall ScaleUp packages and patterns
     zypper remove patterns-sap-hana
     zypper remove SAPHanaSR 
     zypper remove SAPHanaSR-doc
     zypper remove yast2-sap-ha
     # Install the ScaleOut packages and patterns
     zypper in SAPHanaSR-ScaleOut  SAPHanaSR-ScaleOut-doc 
     zypper in -t pattern ha_sles
    ```

3. **[AH]** Prepare os VMs - aplique as definições recomendadas por nota SAP [2205917] para SUSE Linux Enterprise Server para aplicações SAP.  

## <a name="prepare-the-file-systems"></a>Preparar os sistemas de ficheiros
### <a name="mount-the-shared-file-systems"></a>Monte os sistemas de ficheiros partilhados

Neste exemplo, os sistemas de ficheiros HANA partilhados são implantados em Ficheiros Azure NetApp e montados sobre NFSv4.  

1. **[AH]** Crie pontos de montagem para os volumes de base de dados HANA.  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[AH]** Verifique a definição de domínio NFS. Certifique-se de que o domínio está configurado como o domínio padrão dos Ficheiros Azure NetApp, isto é, **`defaultv4iddomain.com`** e o mapeamento não é definido para **ninguém**.  
   Este passo só é necessário se utilizar o Azure NetAppFiles NFSv4.1.  

    > [!IMPORTANT]
    > Certifique-se de que define o domínio NFS `/etc/idmapd.conf` no VM para corresponder à configuração de domínio predefinido nos Ficheiros Azure NetApp: **`defaultv4iddomain.com`** . Se houver um desfasamento entre a configuração de domínio no cliente NFS (ou seja, o VM) e o servidor NFS, ou seja, a configuração do Azure NetApp, então as permissões para ficheiros nos volumes Azure NetApp que são montados nos VMs serão exibidas como `nobody` .  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[AH]** Verificar `nfs4_disable_idmapping` . Deve ser definido para **Y.** Para criar a estrutura do diretório onde `nfs4_disable_idmapping` se encontra, execute o comando de montagem. Não poderá criar manualmente o diretório em /sys/módulos, porque o acesso é reservado para o núcleo/condutores.  
   Este passo só é necessário se utilizar o Azure NetAppFiles NFSv4.1.  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.7:/HN1-share-s1 /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

4. **[AH1]** Monte os volumes de Ficheiros Azure NetApp partilhados nos VMs DO SITE1 HANA DB.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s1 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    ```

5. **[AH2]** Monte os volumes de Ficheiros Azure NetApp partilhados nos VMs HANA DB site2.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s2 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    ```


10. **[AH]** Verifique se os `/hana/shared/` sistemas de ficheiros correspondentes estão montados em todos os VMs HANA DB com a versão **NFSv4** do protocolo NFS .  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>Prepare os dados e registe os sistemas de ficheiros locais
Na configuração apresentada, os sistemas de ficheiros `/hana/data` `/hana/log` são implantados em disco gerido e são ligados localmente a cada HANA DB VM. Terá de executar os passos para criar os dados locais e registar volumes em cada máquina virtual HANA DB. 

Configurar o layout do disco com  **o Gestor de Volume Lógico (LVM)**. O exemplo a seguir pressupõe que cada máquina virtual HANA tem três discos de dados ligados, que são usados para criar dois volumes.

1. **[AH]** Listar todos os discos disponíveis:
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   Exemplo de saída:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[AH]** Crie volumes físicos para todos os discos que pretende utilizar:
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[AH]** Criar um grupo de volume para os ficheiros de dados. Utilize um grupo de volume para os ficheiros de registo e outro para o diretório partilhado do SAP HANA:
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[AH]** Criar os volumes lógicos. 
   Um volume linear é criado quando se utiliza `lvcreate` sem o `-i` interruptor. Sugerimos que crie um volume às riscas para um melhor desempenho de E/S e alinhe os tamanhos das listras com os valores documentados nas [configurações de armazenamento SAP HANA VM](./hana-vm-operations-storage.md). O `-i` argumento deve ser o número dos volumes físicos subjacentes e o argumento é o tamanho das `-I` listras. Neste documento, são utilizados dois volumes físicos para o volume de dados, pelo que o argumento do `-i` interruptor é definido para **2**. O tamanho das riscas para o volume de dados é **de 256 KiB**. Um volume físico é utilizado para o volume de registo, pelo que nenhum `-i` ou `-I` interruptores são explicitamente utilizados para os comandos de volume de registo.  

   > [!IMPORTANT]
   > Utilize o `-i` interruptor e desa cospe-o para o número do volume físico subjacente quando utilizar mais de um volume físico para cada volume de dados ou registo. Utilize o `-I` interruptor para especificar o tamanho das listras, ao criar um volume às riscas.  
   > Consulte [as configurações de armazenamento SAP HANA VM](./hana-vm-operations-storage.md) para configurações de armazenamento recomendadas, incluindo tamanhos de listras e número de discos.  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[AH]** Crie os diretórios de montagem e copie o UUID de todos os volumes lógicos:
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[AH]** Criar `fstab` entradas para os volumes lógicos e montar:
    ```bash
    sudo vi /etc/fstab
    ```

   Insira a seguinte linha no `/etc/fstab` ficheiro:

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Monte os novos volumes:

    ```bash
    sudo mount -a
    ```

## <a name="create-a-pacemaker-cluster"></a>Criar um cluster pacemaker

Siga os passos na configuração do [Pacemaker no SUSE Linux Enterprise Server em Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster pacemaker básico para este servidor HANA.
Inclua todas as máquinas virtuais, incluindo o fabricante maioritário no cluster.  

> [!IMPORTANT]
> Não desapas `quorum expected-votes` para 2, já que este não é um aglomerado de dois nós.  
> Certifique-se de que a propriedade do cluster `concurrent-fencing`  está ativada, de modo a que a esgrima do nó seja dessaserializada.   

## <a name="installation"></a>Instalação  

Neste exemplo para implantar o SAP HANA na configuração de escala com HSR em VMs Azure, usamos HANA 2.0 SP4.  

### <a name="prepare-for-hana-installation"></a>Preparar para a instalação HANA

1. **[AH]** Antes da instalação HANA, desa estale a senha de raiz. Pode desativar a palavra-passe de raiz depois de concluída a instalação. Execute como `root` comando `passwd` .  

2. **[1,2]** Alterar as permissões `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** Verifique se pode iniciar sessão via SSH para os VMs HANA DB neste site **hana-s1-db2** e **hana-s1-db3,** sem ser solicitado para uma senha. Se não for esse o caso, troque as chaves ssh como descrito no [Enable SSH Access via Public Key](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_enable_ssh_access_via_public_key_optional).  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** Verifique se pode iniciar sessão via SSH para os VMs HANA DB neste site **hana-s2-db2** e **hana-s2-db3,** sem ser solicitado para uma senha.  
   Se não for esse o caso, troque as chaves.    
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[AH]** Instale embalagens adicionais, que são necessárias para HANA 2.0 SP4. Para mais informações, consulte o SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) para a sua versão SLES. 

    ```bash
    # In this example, using SLES12 SP5
    sudo zypper install libgcc_s1 libstdc++6 libatomic1
    ```
### <a name="hana-installation-on-the-first-node-on-each-site"></a>Instalação HANA no primeiro nó em cada site

1. **[1]** Instale o SAP HANA seguindo as instruções no [guia de instalação e atualização SAP 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). Nas instruções que se seguem, mostramos a instalação SAP HANA no primeiro nó no LOCAL 1.   

   a. Inicie o programa **hdblcm** `root` a partir do diretório de software de instalação HANA. Utilize o `internal_network` parâmetro e passe o espaço do endereço para a sub-rede, que é usada para a comunicação interna do nó inter-nódoa hana.  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. Ao minuto, insira os seguintes valores:

     * Para **Escolher uma ação**: insira **1** (para instalação)
     * Para **componentes adicionais para instalação:** insira **2, 3**
     * Para o caminho de instalação: prima Enter (predefinições para /hana/shared)
     * Para **o nome do anfitrião local**: prima Insira para aceitar o padrão
     * Pois **deseja adicionar anfitriões ao sistema?** 
     * Para **o ID do sistema SAP HANA**: insira **HN1**
     * Para **o número de exemplo** [00]: insira **03**
     * Para **o Grupo de Trabalhadores Anfitriões Locais** [predefinição]: prima Insira para aceitar o padrão
     * Para **selecionar o sistema de utilização / introduzir índice [4]**: insira **4** (para costume)
     * Para **a localização dos volumes de dados** [/hana/dados/HN1]: prima Insira para aceitar o padrão
     * Para **a localização dos volumes de registo** [/hana/log/HN1]: prima Insira para aceitar o padrão
     * Para **restringir a atribuição máxima de memória?** [n]: inserir **n**
     * Para **o nome do anfitrião do certificado hana-s1-db1** [hana-s1-db1]: prima Insira para aceitar o padrão
     * Para **o utilizador do agente anfitrião SAP (sapadm) :** introduza a palavra-passe
     * Para **confirmar o utilizador do agente anfitrião SAP (sapadm) :** introduza a palavra-passe
     * Para **Administrador de Sistema (hn1adm) Palavra-passe**: introduza a palavra-passe
     * Para **o Diretório doméstico do administrador do sistema** [/usr/sap/HN1/home]: prima Insira para aceitar o padrão
     * Para **a porta de início de sessão do administrador do sistema** [/bin/sh]: prima Insira para aceitar o padrão
     * Para **o ID do utilizador do administrador do sistema** [1001]: prima Insira para aceitar o padrão
     * Para **introduzir iD do Grupo de Utilizador (sapsys)** [79]: prima Enter para aceitar o padrão
     * Para **o Utilizador da Base de Dados do Sistema (sistema) Palavra-passe**: introduza a palavra-passe do sistema
     * Para **confirmar a palavra-passe do utilizador (sistema)** do utilizador da base de dados do sistema: introduza a palavra-passe do sistema
     * Para **reiniciar o sistema após o reinício da máquina?** [n]: inserir **n** 
     * Pois **Deseja continuar (y/n)**: valide o resumo e se tudo estiver bem, insira **y**

2. **[2]** Repita o passo anterior para instalar o SAP HANA no primeiro nó no LOCAL 2.   

3. **[1,2]** Verificar global.ini  

   O visor global.ini e certifique-se de que a configuração para a comunicação inter-nódoa interna SAP HANA está no lugar. Verifique a secção **de comunicação.** Deve ter o espaço de endereço para a `inter` sub-rede, e `listeninterface` deve ser definido para `.internal` . Verifique a secção **internal_hostname_resolution.** Deve ter os endereços IP para as máquinas virtuais HANA que pertencem à `inter` sub-rede.  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.132 = hana-s1-db1
     10.23.1.133 = hana-s1-db2
     10.23.1.134 = hana-s1-db3
   ```

4. **[1,2]** `global.ini` Prepare-se para a instalação em ambiente não partilhado, conforme descrito na nota SAP [2080991](https://launchpad.support.sap.com/#/notes/0002080991).  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1,2]** Reinicie o SAP HANA para ativar as alterações.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1,2]** Verifique se a interface do cliente utilizará os endereços IP da `client` sub-rede para comunicação.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.22"
   ```

   Para obter informações sobre como verificar a configuração, consulte a Nota SAP [2183363 - Configuração da rede interna SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[AH]** Altere permissões nos dados e nos diretórios de registo para evitar erros de instalação da HANA.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** Instale os nós HANA secundários. As instruções de exemplo neste passo são para o SITE 1.  
   a. Inicie o programa **hdblcm** residente como `root` .    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. Ao minuto, insira os seguintes valores:

     * Para **Escolher uma ação**: insira **2** (para adicionar anfitriões)
     * Para **entrar em vírgula nomes de anfitriões separados para adicionar**: hana-s1-db2, hana-s1-db3
     * Para **componentes adicionais para instalação:** insira **2, 3**
     * Para **introduzir o nome do utilizador raiz [raiz]**: prima Introduza para aceitar o padrão
     * Para **funções selecionadas para o anfitrião 'hana-s1-db2' [1]**: 1 (para trabalhador)
     * Para **introduzir o Grupo de Failover do anfitrião 'hana-s1-db2' [predefinição] :** prima Insira para aceitar o padrão
     * Para **introduzir o número de partição de armazenamento para o anfitrião 'hana-s1-db2' [<<assign automatically>>]**: prima Introduza para aceitar o padrão
     * Para **entrar no Grupo de Trabalhadores para o anfitrião 'hana-s1-db2' [predefinição] :** prima Enter para aceitar o padrão
     * Para **funções selecionadas para o anfitrião 'hana-s1-db3' [1]**: 1 (para trabalhador)
     * Para **introduzir o Grupo de Failover do anfitrião 'hana-s1-db3' [predefinição] :** prima Insira para aceitar o padrão
     * Para **introduzir o número de partição de armazenamento para o anfitrião 'hana-s1-db3' [<<assign automatically>>]**: prima Introduza para aceitar o padrão
     * Para **entrar no Grupo de Trabalhadores para o anfitrião 'hana-s1-db3' [predefinição] :** prima Enter para aceitar o padrão
     * Para **Administrador de Sistema (hn1adm) Palavra-passe**: introduza a palavra-passe
     * Para **introduzir a palavra-passe do agente anfitrião SAP (sapadm):** introduza a palavra-passe
     * Para **confirmar o utilizador do agente anfitrião SAP (sapadm) :** introduza a palavra-passe
     * Para **o nome do anfitrião do certificado hana-s1-db2** [hana-s1-db2]: prima Insira para aceitar o padrão
     * Para **o nome do anfitrião do certificado hana-s1-db3** [hana-s1-db3]: prima Insira para aceitar o padrão
     * Pois **Deseja continuar (y/n)**: valide o resumo e se tudo estiver bem, insira **y**

9. **[2]** Repita o passo anterior para instalar os nós SAP HANA secundários no LOCAL 2.   

## <a name="configure-sap-hana-20-system-replication"></a>Configurar a replicação do sistema SAP HANA 2.0

1. **[1]** Configurar a replicação do sistema no LOCAL 1:

   Ressou as bases de dados como **hn1** adm:

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Copie os ficheiros PKI do sistema para o site secundário:

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Criar o site principal:

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** Configurar a replicação do sistema no LOCAL 2:
    
   Registe o segundo site para iniciar a replicação do sistema. Executar o seguinte comando como <\> adm hanasid:

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** Verificar o estado da replicação

   Verifique o estado de replicação e aguarde até que todas as bases de dados estejam sincronizadas.

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1,2]** Alterar a configuração HANA de modo a que a comunicação para a replicação do sistema HANA seja dirigida embora as interfaces de rede virtual de replicação do sistema HANA.   
   - Pare HANA em ambos os sites
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - Editar global.ini para adicionar o mapeamento do anfitrião para a replicação do sistema HANA: utilize os endereços IP a partir da `hsr` sub-rede.  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.196 = hana-s1-db1
    10.23.1.197 = hana-s1-db2
    10.23.1.198 = hana-s1-db3
    10.23.1.199 = hana-s2-db1
    10.23.1.200 = hana-s2-db2
    10.23.1.201 = hana-s2-db3
    ```

   - Inicie HANA em ambos os sites
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Para obter mais informações, consulte [a resolução do Nome anfitrião para a replicação do sistema.](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html)  

## <a name="create-file-system-resources"></a>Criar recursos do sistema de ficheiros

Crie um recurso de cluster de sistema de ficheiros falso, que monitorize e reporte falhas, caso exista um problema de acesso ao sistema de ficheiros montado em NFS `/hana/shared` . Isto permite que o cluster desencadeie falhas, caso haja um problema de `/hana/shared` acesso. Para mais detalhes, veja [a partilha falhada da NFS no cluster SUSE HA para a replicação do sistema HANA](https://www.suse.com/support/kb/doc/?id=000019904) 

1. **[1]** Coloque o pacemaker em modo de manutenção, em preparação para a criação dos recursos de cluster HANA.  
    ```bash
    crm configure property maintenance-mode=true
    ```

2. **[1,2]** Criar o diretório no volume ANF/hana/sahred, que será utilizado no recurso especial de monitorização do sistema de ficheiros. Os diretórios precisam de ser criados em ambos os sites.  
    ```bash
    mkdir -p /hana/shared/HN1/check
    ```

2. **[AH]** Crie o diretório, que será utilizado para montar o recurso especial de monitorização do sistema de ficheiros. O diretório precisa de ser criado em todos os nós de cluster HANA.  
    ```bash
    mkdir -p /hana/check
    ```

3. **[1]** Criar os recursos de cluster do sistema de ficheiros.     

    ```bash
    crm configure primitive fs_HN1_HDB03_fscheck Filesystem \
      params device="/hana/shared/HN1/check" \
      directory="/hana/check" fstype=nfs4 \
      options="bind,defaults,rw,hard,proto=tcp,intr,noatime,vers=4.1,lock" \
      op monitor interval=120 timeout=120 on-fail=fence \
      op_params OCF_CHECK_LEVEL=20 \
      op start interval=0 timeout=120 op stop interval=0 timeout=120

    crm configure clone cln_fs_HN1_HDB03_fscheck fs_HN1_HDB03_fscheck \
      meta clone-node-max=1 interleave=true

    crm configure location loc_cln_fs_HN1_HDB03_fscheck_not_on_mm \
      cln_fs_HN1_HDB03_fscheck -inf: hana-s-mm    
    ```
 
   `OCF_CHECK_LEVEL=20` o atributo é adicionado à operação do monitor, de modo a que as operações do monitor realizem um teste de leitura/escrita no sistema de ficheiros. Sem este atributo, a operação do monitor apenas verifica se o sistema de ficheiros está montado. Isto pode ser um problema porque quando a conectividade é perdida, o sistema de ficheiros pode permanecer montado, apesar de ser inacessível.  

   `on-fail=fence` o atributo também é adicionado à operação do monitor. Com esta opção, se a operação do monitor falhar num nó, esse nó é imediatamente vedado.   

## <a name="create-sap-hana-cluster-resources"></a>Criar recursos de cluster SAP HANA

1. **[1,2]** Instale o "gancho de replicação do sistema" HANA. O gancho precisa de ser instalado num nó HANA DB em cada local de replicação do sistema.         

   1. Prepare o gancho como `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Pare a HANA em ambos os locais de replicação do sistema. Execute como <sid \> adm:
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Ajustar `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[AH]** O cluster requer a configuração dos sudoers no nó do cluster para <\> adm do sid. Neste exemplo que é conseguido através da criação de um novo ficheiro. Execute os comandos como `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
    Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
    EOF
    ```

3. **[1,2]** Iniciar o SAP HANA em ambos os locais de replicação. Execute como <sid \> adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** Verifique a instalação do gancho. Execute como <\> adm sid no site de replicação do sistema HANA ativo.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # 2021-03-31 01:02:42.695244 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:02:58.966856 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.453100 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.619768 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.743444 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:04:15.062181 ha_dr_SAPHanaSR SOK

    ```

5. **[1]** Criar os recursos de cluster HANA. Execute os seguintes comandos como `root` .    
   1. Certifique-se de que o cluster já está em modo de manutenção.  
    
   2. Em seguida, crie o recurso HANA Topology.  
      ```bash
      sudo crm configure primitive rsc_SAPHanaTopology_HN1_HDB03 ocf:suse:SAPHanaTopology \
        op monitor interval="10" timeout="600" \
        op start interval="0" timeout="600" \
        op stop interval="0" timeout="300" \
        params SID="HN1" InstanceNumber="03"

      sudo crm configure clone cln_SAPHanaTopology_HN1_HDB03 rsc_SAPHanaTopology_HN1_HDB03 \
       meta clone-node-max="1" target-role="Started" interleave="true"
      ```

   3. Em seguida, crie o recurso de instância HANA.  
      > [!NOTE] 
      > Este artigo contém referências aos termos *mestre* e *escravo,* termos que a Microsoft já não utiliza. Quando estes termos forem removidos do software, removê-los-emos deste artigo.
 
      ```bash
      sudo crm configure primitive rsc_SAPHana_HN1_HDB03 ocf:suse:SAPHanaController \
        op start interval="0" timeout="3600" \
        op stop interval="0" timeout="3600" \
        op promote interval="0" timeout="3600" \
        op monitor interval="60" role="Master" timeout="700" \
        op monitor interval="61" role="Slave" timeout="700" \
        params SID="HN1" InstanceNumber="03" PREFER_SITE_TAKEOVER="true" \
        DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

      sudo crm configure ms msl_SAPHana_HN1_HDB03 rsc_SAPHana_HN1_HDB03 \
        meta clone-node-max="1" master-max="1" interleave="true"
      ```
      > [!IMPORTANT]
      > Recomendamos como uma boa prática que apenas coloque AUTOMATED_REGISTER para **não,** enquanto realiza testes de insucesso completos, para evitar que a primeira instância falhada se registe automaticamente como secundária. Uma vez concluídos os testes de fail-over com sucesso, desembaraça AUTOMATED_REGISTER **sim**, de modo a que após a replicação do sistema de aquisição possa ser retomada automaticamente. 

   4. Criar IP Virtual e recursos associados.  
      ```bash
      sudo crm configure primitive rsc_ip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="10.23.0.27"

      sudo crm configure primitive rsc_nc_HN1_HDB03 azure-lb port=62503 \
        meta resource-stickiness=0

      sudo crm configure group g_ip_HN1_HDB03 rsc_ip_HN1_HDB03 rsc_nc_HN1_HDB03
      ```

   5. Criar os constrangimentos do cluster  
      ```bash
      # Colocate the IP with HANA master
      sudo crm configure colocation col_saphana_ip_HN1_HDB03 4000: g_ip_HN1_HDB03:Started \
        msl_SAPHana_HN1_HDB03:Master  

      # Start HANA Topology before HANA  instance
      sudo crm configure order ord_SAPHana_HN1_HDB03 Optional: cln_SAPHanaTopology_HN1_HDB03 \
        msl_SAPHana_HN1_HDB03

      # HANA resources don't run on the majority maker node
      sudo crm configure location loc_SAPHanaCon_not_on_majority_maker msl_SAPHana_HN1_HDB03 -inf: hana-s-mm
      sudo crm configure location loc_SAPHanaTop_not_on_majority_maker cln_SAPHanaTopology_HN1_HDB03 -inf: hana-s-mm
      ```

6. **[1]** Configure propriedades adicionais de cluster   
    ```bash
    sudo crm configure rsc_defaults resource-stickiness=1000
    sudo crm configure rsc_defaults migration-threshold=50
    ```
7. **[1]** Verificar a comunicação entre o GANCHO e o cluster
    ```bash
    crm_attribute -G -n hana_hn1_glob_srHook
    # Expected result
    # crm_attribute -G -n hana_hn1_glob_srHook
    # scope=crm_config  name=hana_hn1_glob_srHook value=SOK
    ```

8. **[1]** Coloque o cluster fora do modo de manutenção. Certifique-se de que o estado do cluster está bem e que todos os recursos são iniciados.  
    ```bash
    # Cleanup any failed resources - the following command is example 
    crm resource cleanup rsc_SAPHana_HN1_HDB03

    # Place the cluster out of maintenance mode
    sudo crm configure property maintenance-mode=false
    ```
  
   > [!NOTE]
   > Os intervalos de tempo na configuração acima são apenas exemplos e podem ter de ser adaptados à configuração específica do HANA. Por exemplo, poderá ter de aumentar o tempo de início, se demorar mais tempo a iniciar a base de dados SAP HANA.
  

## <a name="test-sap-hana-failover"></a>Teste SAP HANA failover 

> [!NOTE]
> Este artigo contém referências aos termos *mestre* e *escravo,* termos que a Microsoft já não utiliza. Quando estes termos forem removidos do software, removê-los-emos deste artigo.

1. Antes de iniciar um teste, verifique o estado de replicação do cluster e do sistema SAP HANA.    

   a. Verifique se não existem ações falhadas de cluster  
     ```bash
     #Verify that there are no failed cluster actions
     crm status
     # Example 
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Full list of resources:
     #
     # stonith-sbd    (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     ```

   b. Verifique se a replicação do sistema SAP HANA está sincronizada

     ```bash
     # Verify HANA HSR is in sync
     sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
     #| Database | Host         | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary    | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
     #|          |              |       |              |           |         |           | Host         | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
     #| -------- | ------------ | ----- | ------------ | --------- | ------- | --------- | ------------ | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
     #| SYSTEMDB | hana-s1-db1  | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1  |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1  |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db3  | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db3  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db2  | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db2  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #
     #status system replication site "1": ACTIVE
     #overall system replication status: ACTIVE
     #
     #Local System Replication State
     #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     #
     #mode: PRIMARY
     #site id: 1
     #site name: HANA_S1
     ```

2. Recomendamos validar completamente a configuração do cluster SAP HANA, realizando os testes, documentados em [HA para SAP HANA em VMs Azure em SLES](./sap-hana-high-availability.md#test-the-cluster-setup) e em [SLES Replication Scale-out Performance Optimized Scenario](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_testing_the_cluster).

3. Verifique a configuração do cluster para um cenário de falha, quando um nó perde o acesso à parte NFS ( `/hana/shared` ).  

   Os agentes de recursos SAP HANA dependem de binários, armazenados `/hana/shared` para executar operações durante o failover. O sistema de `/hana/shared` ficheiros é montado sobre NFS na configuração apresentada. Um teste que pode ser realizado, é criar uma regra de firewall temporária para bloquear o acesso ao `/hana/shared` volume ANF em um dos VMs do site primário. Esta abordagem valida que o cluster falhará, se o acesso `/hana/shared` se perder no site de replicação do sistema ativo.  

   **Resultado esperado**: Quando bloquear o acesso ao `/hana/shared` volume ANF num dos VMs do site primário, a operação de monitorização que executa a operação de leitura/escrita no sistema de ficheiros, falhará, uma vez que não consegue aceder ao sistema de ficheiros e irá desencadear a falha do recurso HANA. O mesmo resultado é esperado quando o seu nó HANA perde acesso à parte NFS.  
     
   Pode verificar o estado dos recursos do cluster executando `crm_mon` ou `crm status` . Estado dos recursos antes de iniciar o teste:
     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1     
      ```

   Para simular a `/hana/shared` falha, primeiro confirme o endereço IP para o `/hana/shared` volume ANF no local primário. Podes fazê-lo `df -kh|grep /hana/shared` correndo. 
   Em seguida, crie uma regra de firewall temporária para bloquear o acesso ao endereço IP do `/hana/shared` volume ANF executando o seguinte comando num dos VMs do site de replicação do sistema HANA primário.
   Neste exemplo, o comando foi executado em hana-s1-db1.

     ```bash
     iptables -A INPUT -s 10.23.1.7 -j DROP; iptables -A OUTPUT -d 10.23.1.7 -j DROP
     ```

   Os recursos do cluster serão migrados para o outro local de replicação do sistema HANA.    
              
   Se definir AUTOMATED_REGISTER="falso", terá de configurar a replicação do sistema SAP HANA no local secundário. Neste caso, pode executar estes comandos para reconfigurar o SAP HANA como secundário.   

     ```bash
     # Execute on the secondary 
     su - hn1adm
     # Make sure HANA is not running on the secondary site. If it is started, stop HANA
     sapcontrol -nr 03 -function StopWait 600 10
     # Register the HANA secondary site
     hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
     # Switch back to root and cleanup failed resources
     crm resource cleanup SAPHana_HN1_HDB03
     ```

   O estado dos recursos, após o teste: 

     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s2-db1 ]
     #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1
     ```


## <a name="next-steps"></a>Passos seguintes

* [Azure Virtual Machines planejamento e implementação para SAP][planning-guide]
* [Implantação de máquinas virtuais Azure para SAP][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP][dbms-guide]
* [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](./hana-vm-operations-netapp.md)
* Para aprender a estabelecer uma elevada disponibilidade e plano para a recuperação de desastres da SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha].