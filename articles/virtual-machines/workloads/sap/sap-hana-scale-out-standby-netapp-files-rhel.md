---
title: Escala SAP HANA com standby com ficheiros Azure NetApp na RHEL| Microsoft Docs
description: Guia de alta disponibilidade para SAP NetWeaver em Red Hat Enterprise Linux com Ficheiros Azure NetApp para aplicações SAP
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
ms.date: 02/01/2021
ms.author: radeltch
ms.openlocfilehash: 4947585b1f20b8142c51d9d7e28c6d8504b6d6d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669646"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Implementar um sistema de escala SAP HANA com nó de espera em VMs Azure utilizando ficheiros Azure NetApp no Red Hat Enterprise Linux 

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Este artigo descreve como implementar um sistema SAP HANA altamente disponível numa configuração de escala com standby em Azure Red Hat Enterprise Linux (VMs), utilizando [ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-introduction.md) para os volumes de armazenamento partilhados.  

Nas configurações de exemplo, comandos de instalação, e assim por diante, a instância HANA é **03** e o ID do sistema HANA é **HN1**. Os exemplos são baseados em HANA 2.0 SP4 e Red Hat Enterprise Linux para SAP 7.6. 

> [!NOTE]
> Este artigo contém referências aos termos *mestre* e *escravo,* termos que a Microsoft já não utiliza. Quando estes termos forem removidos do software, removê-los-emos deste artigo.


Antes de começar, consulte as seguintes notas e documentos SAP:

* [Documentação dos Ficheiros Azure NetApp][anf-azure-doc] 
* Sap Note [1928533] inclui:  
  * Uma lista de tamanhos Azure VM que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade dos tamanhos Azure VM
  * Combinações suportadas de software SAP e sistema operativo (OS) e de base de dados
  * A versão necessária do kernel SAP para Windows e Linux no Microsoft Azure
* NOTA SAP [2015553]: Lista pré-requisitos para implementações de software SAP suportadas pelo SAP em Azure
* SAP Note [2002167] recomendou definições de SO para Red Hat Enterprise Linux
* SAP Note [2009879] tem orientações SAP HANA para Red Hat Enterprise Linux
* Nota [SAP 2178632]: Contém informações detalhadas sobre todas as métricas de monitorização reportadas para a SAP em Azure
* NOTA SAP [2191498]: Contém a versão necessária do Agente anfitrião SAP para Linux em Azure
* Nota [SAP 2243692]: Contém informações sobre o licenciamento SAP em Linux em Azure
* Nota SAP [1999351]: Contém informações adicionais de resolução de problemas para a extensão de monitorização melhorada do Azure para o SAP
* Nota [SAP 1900823]: Contém informações sobre os requisitos de armazenamento SAP HANA
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Contém todas as notas SAP necessárias para Linux
* [Azure Virtual Machines planejamento e implementação para SAP em Linux][planning-guide]
* [Implementação de Máquinas Virtuais Azure para SAP em Linux][deployment-guide]
* [Azure Virtual Machines DBMS implantação para SAP em Linux][dbms-guide]
* Documentação geral do RHEL
  * [Visão geral Add-On de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Alta Disponibilidade Add-On Administração](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de Add-On de Alta Disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Guia de rede linux da empresa de chapéu vermelho](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Documentação RHEL específica do Azure:
  * [Instale SAP HANA no Red Hat Enterprise Linux para utilização no Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
* [Aplicações NETApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]
* [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Descrição Geral

Um método para alcançar a alta disponibilidade de HANA é configurando o failover de carro hospedeiro. Para configurar o failover automático do anfitrião, adicione uma ou mais máquinas virtuais ao sistema HANA e configurá-las como nós de espera. Quando o nó ativo falha, um nó de espera assume automaticamente. Na configuração apresentada com máquinas virtuais Azure, obtém-se falha automática utilizando [NFS em Ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

> [!NOTE]
> O nó de espera precisa de acesso a todos os volumes de base de dados. Os volumes HANA devem ser montados como volumes NFSv4. O mecanismo de bloqueio baseado em ficheiros melhorado no protocolo NFSv4 é utilizado para `I/O` esgrima. 

> [!IMPORTANT]
> Para construir a configuração suportada, deve implantar os dados HANA e os volumes de registo como volumes NFSv4.1 e montá-los utilizando o protocolo NFSv4.1. A configuração de auto-falha do anfitrião HANA com nó de espera não é suportada com NFSv3.

![Visão geral de alta disponibilidade do SAP NetWeaver](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

No diagrama anterior, que segue as recomendações da rede SAP HANA, estão representadas três sub-redes dentro de uma rede virtual Azure: 
* Para comunicação com o cliente
* Para comunicação com o sistema de armazenamento
* Para comunicação interna entre nól de HANA

Os volumes Azure NetApp estão em sub-rede separada, [delegada em Ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

Para esta configuração de exemplo, as sub-redes são:  

  - `client` 10.9.1.0/26  
  - `storage` 10.9.3.0/26  
  - `hana` 10.9.2.0/26  
  - `anf` 10.9.0.0/26 (sub-rede delegada para ficheiros Azure NetApp)

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Configurar a infraestrutura de Ficheiros Azure NetApp 

Antes de prosseguir com a configuração para a infraestrutura Azure NetApp Files, familiarize-se com a documentação do [Azure NetApp Files][anf-azure-doc]. 

O Azure NetApp Files está disponível em várias [regiões do Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Verifique se a região de Azure selecionada oferece ficheiros Azure NetApp.  

Para obter informações sobre a disponibilidade de Ficheiros Azure NetApp por região do Azure, consulte [Azure NetApp Files Availability by Azure Region][anf-avail-matrix].  

Antes de implementar ficheiros Azure NetApp, solicite a bordo para ficheiros Azure NetApp indo para [registar instruções de Arquivos Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implementar recursos de ficheiros Azure NetApp  

As seguintes instruções pressupõem que já implementou a sua [rede virtual Azure](../../../virtual-network/virtual-networks-overview.md). Os recursos e VMs do Azure NetApp, onde serão montados os recursos do Azure NetApp Files, devem ser implantados na mesma rede virtual Azure ou em redes virtuais Azure.  

1. Se ainda não implementou os recursos, solicite [a bordo para os Ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-register.md).  

2. Crie uma conta NetApp na região Azure selecionada seguindo as instruções na [Criação de uma conta NetApp.](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)  

3. Crie um pool de capacidade do Azure NetApp Files seguindo as instruções em [Configurar um conjunto de capacidades Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  

   A arquitetura HANA apresentada neste artigo usa um único pool de capacidade Azure NetApp Files ao nível do *Serviço Ultra.* Para cargas de trabalho HANA em Azure, recomendamos a utilização de um [nível](../../../azure-netapp-files/azure-netapp-files-service-levels.md)de serviço Azure NetApp *Files Ultra* ou *Premium* .  

4. Delege uma sub-rede para ficheiros Azure NetApp, conforme descrito nas instruções em [Delegado uma sub-rede para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Implementar volumes de ficheiros Azure NetApp seguindo as instruções na [Criar um volume NFS para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  

   Ao implementar os volumes, não se esqueça de selecionar a versão **NFSv4.1.** Implementar os volumes na [sub-rede](/rest/api/virtualnetwork/subnets)designada Azure NetApp Files . Os endereços IP dos volumes Azure NetApp são atribuídos automaticamente. 
   
   Tenha em mente que os recursos do Azure NetApp Files e os VMs Azure devem estar na mesma rede virtual Azure ou em redes virtuais Azure. Por exemplo, **HN1**-data-mnt00001, **HN1**-log-mnt00001, e assim por diante, são os nomes de volume e nfs://10.9.0.4/**HN1**-data-mnt0001, nfs://10.9.0.4/**HN1**-log-mnt00001, e assim por diante, são os caminhos de arquivo para os volumes de ficheiros Azure NetApp.  

   * volume **HN1**-data-mnt00001 (nfs://10.9.0.4/**HN1**-data-mnt00001)
   * volume **HN1**-data-mnt00002 (nfs://10.9.0.4/**HN1**-data-mnt00002)
   * volume **HN1**-log-mnt00001 (nfs://10.9.0.4/**HN1**-log-mnt00001)
   * volume **HN1**-log-mnt00002 (nfs://10.9.0.4/**HN1**-log-mnt00002)
   * volume **HN1**-compartilhado (nfs://10.9.0.4/**HN1**-partilhado)
   
   Neste exemplo, utilizamos um volume separado de Ficheiros Azure NetApp para cada dado HANA e volume de registo. Para uma configuração mais otimizada em sistemas mais pequenos ou não produtivos, é possível colocar todos os suportes de dados num único volume e todos os suportes de troncos num único volume diferente.  

### <a name="important-considerations"></a>Considerações importantes

Enquanto está a criar os seus Ficheiros Azure NetApp para escala SAP HANA com cenário stand by nodes, esteja ciente das seguintes considerações importantes:

- A capacidade mínima é de 4 tebibytes (TiB).  
- O tamanho mínimo do volume é de 100 gibibytes (GiB).
- Os ficheiros Azure NetApp e todas as máquinas virtuais onde os volumes dos Ficheiros Azure NetApp serão montados devem estar na mesma rede virtual Azure ou em [redes virtuais na](../../../virtual-network/virtual-network-peering-overview.md) mesma região.  
- A rede virtual selecionada deve ter uma sub-rede delegada nos Ficheiros Azure NetApp.
- O rendimento de um volume de Ficheiros Azure NetApp é uma função da quota de volume e do nível de serviço, conforme documentado no [nível de Serviço para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Ao dimensionar os volumes HANA Azure NetApp, certifique-se de que a produção resultante satisfaz os requisitos do sistema HANA.  
- Com a política de [exportação](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)do Azure NetApp Files, pode controlar os clientes autorizados, o tipo de acesso (ler-escrever, ler apenas, e assim por diante). 
- A funcionalidade Azure NetApp Files ainda não está ciente da zona. Atualmente, a funcionalidade não está implantada em todas as zonas de disponibilidade numa região do Azure. Esteja ciente das potenciais implicações de latência em algumas regiões de Azure.  

> [!IMPORTANT]
> Para as cargas de trabalho da SAP HANA, a baixa latência é fundamental. Trabalhe com o seu representante da Microsoft para garantir que as máquinas virtuais e os volumes dos Ficheiros Azure NetApp são implantados em proximidade.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento para base de dados HANA em Ficheiros Azure NetApp

O resultado de um volume de Ficheiros Azure NetApp é uma função do tamanho e do nível de serviço do volume, conforme documentado no [nível de Serviço para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Ao conceber a infraestrutura para SAP em Azure, esteja ciente de alguns requisitos mínimos de armazenamento por SAP, que se traduzem em características mínimas de produção:

- Leia-escreva em /hana/log de 250 megabytes por segundo (MB/s) com tamanhos de I/O de 1-MB.  
- Leia a atividade de pelo menos 400 MB/s para /hana/data para tamanhos de 16-MB e 64-MB I/O.  
- Escreva atividade de, pelo menos, 250 MB/s para /hana/dados com tamanhos de 16-MB e 64-MB I/O. 

Os [limites de produção dos ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-service-levels.md) por 1 TiB da quota de volume são:
- Nível de armazenamento premium - 64 MiB/s  
- Nível de armazenamento ultra - 128 MiB/s  

Para satisfazer os requisitos mínimos de produção do SAP para dados e registos, e as diretrizes para /hana/shared, os tamanhos recomendados seriam:

| Volume | Tamanho de<br>Nível de armazenamento premium | Tamanho de<br>Nível de armazenamento ultra | Protocolo NFS apoiado |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/dados | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/compartilhado | 1xRAM por 4 nosmes operários | 1xRAM por 4 nosmes operários | v3 ou v4.1 |

A configuração SAP HANA para o layout apresentado neste artigo, utilizando o nível de armazenamento ultra de arquivos Azure NetApp, seria:

| Volume | Tamanho de<br>Nível de armazenamento ultra | Protocolo NFS apoiado |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v4.1 |
| /hana/log/mnt00002 | 2 TiB | v4.1 |
| /hana/dados/mnt00001 | 3.2 TiB | v4.1 |
| /hana/dados/mnt00002 | 3.2 TiB | v4.1 |
| /hana/compartilhado | 2 TiB | v3 ou v4.1 |

> [!NOTE]
> As recomendações de dimensionamento dos Ficheiros Azure NetApp aqui declaradas são direcionadas para cumprir os requisitos mínimos que a SAP recomenda para os seus fornecedores de infraestruturas. Em situações reais de colocação de clientes e cenários de carga de trabalho, estes tamanhos podem não ser suficientes. Utilize estas recomendações como ponto de partida e adapte-se, com base nos requisitos da sua carga de trabalho específica.  

> [!TIP]
> Pode redimensionar os volumes Azure NetApp Files dinamicamente, sem ter de *desmontar* os volumes, parar as máquinas virtuais ou parar o SAP HANA. Esta abordagem permite flexibilidade para satisfazer as exigências de produção esperadas e imprevistas da sua aplicação.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Implementar máquinas virtuais Linux através do portal Azure

Primeiro, tem de criar os volumes dos Ficheiros Azure NetApp. Em seguida, faça os seguintes passos:
1. Crie as [sub-redes de rede virtual Azure](../../../virtual-network/virtual-network-manage-subnet.md) na sua [rede virtual Azure.](../../../virtual-network/virtual-networks-overview.md) 
1. Desdobre os VMs. 
1. Crie as interfaces de rede adicionais e ligue as interfaces de rede aos VM correspondentes.  

   Cada máquina virtual tem três interfaces de rede, que correspondem às três sub-redes de rede virtual Azure `client` (, `storage` e `hana` . 

   Para obter mais informações, consulte [Criar uma máquina virtual Linux em Azure com vários cartões de interface de rede.](../../linux/multiple-nics.md)  

> [!IMPORTANT]
> Para as cargas de trabalho da SAP HANA, a baixa latência é fundamental. Para obter baixa latência, trabalhe com o seu representante da Microsoft para garantir que as máquinas virtuais e os volumes Azure NetApp Files são implantados em proximidade. Quando estiver a bordo de [um novo sistema SAP HANA](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) que esteja a utilizar ficheiros NETApp SAP HANA Azure, envie as informações necessárias. 
 
As próximas instruções pressupõem que já criou o grupo de recursos, a rede virtual Azure e as três sub-redes de rede virtual Azure: `client` e `storage` `hana` . Quando implementar os VMs, selecione a sub-rede do cliente, de modo a que a interface da rede do cliente seja a interface principal nos VMs. Também terá de configurar uma rota explícita para os Ficheiros Azure NetApp delegados através do gateway da sub-rede de armazenamento. 

> [!IMPORTANT]
> Certifique-se de que o sistema operativo selecionado é certificado por SAP para SAP HANA nos tipos de VM específicos que está a utilizar. Para obter uma lista de tipos de VM certificados da SAP HANA e lançamentos de SO para esses tipos, aceda ao site [de plataformas iaaS certificadas da SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Clique nos detalhes do tipo VM listado para obter a lista completa de versões de SO suportadas por SAP HANA para este tipo.  

1. Crie um conjunto de disponibilidade para SAP HANA. Certifique-se de que define o domínio de atualização máxima.  

2. Criar três máquinas virtuais **(hanadb1,** **hanadb2,** **hanadb3**) fazendo os seguintes passos:  

   a. Use uma imagem red hat Enterprise Linux na galeria Azure que é suportada para SAP HANA. Neste exemplo, usamos uma imagem RHEL-SAP-HA 7.6.  

   b. Selecione o conjunto de disponibilidade que criou anteriormente para SAP HANA.  

   c. Selecione a sub-rede virtual cliente Azure. Selecione [Rede Acelerada](../../../virtual-network/create-vm-accelerated-networking-cli.md).  

   Quando implementa as máquinas virtuais, o nome da interface de rede é gerado automaticamente. Nestas instruções de simplicidade, referimo-nos às interfaces de rede geradas automaticamente, que estão ligadas à sub-rede virtual Azure cliente, como **cliente hanadb1- cliente,** **hanadb2-cliente,** e **hanadb3-cliente.** 

3. Crie três interfaces de rede, uma para cada máquina virtual, para a `storage` sub-rede de rede virtual (neste exemplo, **armazenamento hanadb1,** **armazenamento de hanadb2** e **armazenamento de hanadb3).**  

4. Crie três interfaces de rede, uma para cada máquina virtual, para a `hana` sub-rede de rede virtual (neste exemplo, **hanadb1-hana,** **hanadb2-hana**, e **hanadb3-hana).**  

5. Ligue as interfaces de rede virtuais recém-criadas às máquinas virtuais correspondentes, fazendo os seguintes passos:  

    a. Aceda à máquina virtual no [portal Azure](https://portal.azure.com/#home).  

    b. No painel esquerdo, selecione **Máquinas Virtuais.** Filtrar o nome da máquina virtual (por exemplo, **hanadb1**), e, em seguida, selecionar a máquina virtual.  

    c. No **painel de visão** geral, selecione **Stop** para negociar a máquina virtual.  

    d. Selecione **Networking** e, em seguida, anexe a interface de rede. Na lista de drop-down da interface de **rede Attach,** selecione as interfaces de rede já criadas para as `storage` `hana` sub-redes e sub-redes.  
    
    e. Selecione **Guardar**. 
 
    f. Repita os passos b através de e para as restantes máquinas virtuais (no nosso exemplo, **hanadb2** e **hanadb3).**
 
    exemplo, Deixe as máquinas virtuais em estado de paragem por enquanto. Em seguida, permitiremos [uma rede acelerada](../../../virtual-network/create-vm-accelerated-networking-cli.md) para todas as interfaces de rede recém-anexadas.  

6. Permitir uma rede acelerada para as interfaces de rede adicionais para as `storage` `hana` sub-redes e sub-redes, fazendo os seguintes passos:  

    a. Abra [a Concha da Nuvem Azure](https://azure.microsoft.com/features/cloud-shell/) no portal [Azure](https://portal.azure.com/#home).  

    b. Execute os seguintes comandos para permitir a ligação acelerada da rede para as interfaces de rede adicionais, que estão ligadas às `storage` `hana` sub-redes e às sub-redes.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Inicie as máquinas virtuais fazendo os seguintes passos:  

    a. No painel esquerdo, selecione **Máquinas Virtuais.** Filtrar o nome da máquina virtual (por exemplo, **hanadb1**), e, em seguida, selecioná-lo.  

    b. No **painel de visão** geral, selecione **Start**.  

## <a name="operating-system-configuration-and-preparation"></a>Configuração e preparação do sistema operativo

As instruções nas secções seguintes são pré-fixas com uma das seguintes:
* **[A]**: Aplicável a todos os nós
* **[1]**: Aplicável apenas ao nó 1
* **[2]**: Aplicável apenas ao nó 2
* **[3]**: Aplicável apenas ao nó 3

Configure e prepare o seu SO fazendo os seguintes passos:

1. **[A]** Mantenha os ficheiros hospedeiros nas máquinas virtuais. Incluir entradas para todas as sub-redes. Foram adicionadas as seguintes entradas `/etc/hosts` para este exemplo.  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** Adicione uma rota de rede, de modo que a comunicação aos Ficheiros Azure NetApp passe pela interface da rede de armazenamento.  

   Neste exemplo, `Networkmanager` utilizar-se-á para configurar a rota adicional da rede. As seguintes instruções pressupõem que a interface da rede de armazenamento é `eth1` .  
   Primeiro, determine o nome de ligação para o dispositivo `eth1` . Neste exemplo, o nome de ligação para o dispositivo `eth1` é `Wired connection 1` .  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Em seguida, configurar uma rota adicional para a rede delegada do Azure NetApp Files através de `eth1` .  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    Reinicie o VM para ativar as alterações.  

3. **[A]** Instale o pacote de clientes NFS.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** Prepare o SO para executar SAP HANA no Azure NetApp com NFS, conforme descrito nas [aplicações NETApp SAP no Microsoft Azure usando ficheiros Azure NetApp][anf-sap-applications-azure]. Criar ficheiro de configuração */etc/sysctl.d/netapp-hana.conf* para as definições de configuração netApp.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 
    net.ipv4.tcp_slow_start_after_idle=0 
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Criar ficheiro de configuração */etc/sysctl.d/ms-az.conf* com definições adicionais de otimização.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

> [!TIP]
> Evite configurar net.ipv4.ip_local_port_range e net.ipv4.ip_local_reserved_ports explicitamente nos ficheiros de configuração sysctl para permitir que o Agente anfitrião SAP gere as gamas de portas. Para mais detalhes consulte a nota [SAP 2382421](https://launchpad.support.sap.com/#/notes/2382421).  

5. **[A]** Ajuste as definições sunrpc, como recomendado nas [aplicações NETApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** Chapéu Vermelho para configuração HANA.

    Configure RHEL conforme descrito na Nota SAP [2292690], [2455582], [2593824] e <https://access.redhat.com/solutions/2447641> .

    > [!NOTE]
    > Se instalar o HANA 2.0 SP04, será necessário instalar o pacote `compat-sap-c++-7` conforme descrito na nota SAP [2593824,]antes de poder instalar o SAP HANA. 

## <a name="mount-the-azure-netapp-files-volumes"></a>Monte os volumes dos ficheiros Azure NetApp

1. **[A]** Criar pontos de montagem para os volumes de base de dados HANA.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Criar diretórios específicos para /usr/seiva em **HN1**-partilhados.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Verifique a definição de domínio NFS. Certifique-se de que o domínio está configurado como o domínio padrão dos Ficheiros Azure NetApp, ou seja, **`defaultv4iddomain.com`** e o mapeamento não é definido para **ninguém**.  

    > [!IMPORTANT]
    > Certifique-se de que define o domínio NFS `/etc/idmapd.conf` no VM para corresponder à configuração de domínio predefinido nos Ficheiros Azure NetApp: **`defaultv4iddomain.com`** . Se houver um desfasamento entre a configuração de domínio no cliente NFS (ou seja, o VM) e o servidor NFS, ou seja, a configuração do Azure NetApp, então as permissões para ficheiros nos volumes Azure NetApp que são montados nos VMs serão exibidas como `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** Verificar `nfs4_disable_idmapping` . Deve ser definido para **Y.** Para criar a estrutura do diretório onde `nfs4_disable_idmapping` se encontra, execute o comando de montagem. Não poderá criar manualmente o diretório em /sys/módulos, porque o acesso é reservado para o núcleo/condutores.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Para obter mais detalhes sobre como alterar `nfs4_disable_idmapping` o parâmetro consulte https://access.redhat.com/solutions/1749883 .

6. **[A]** Monte os volumes partilhados dos Ficheiros Azure NetApp.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** Monte os volumes específicos do nó em **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Monte os volumes específicos do nó no **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Monte os volumes específicos do nó em **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Verifique se todos os volumes HANA são montados com a versão **NFSv4 do protocolo NFSV4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>Instalação  

Neste exemplo para implantar o SAP HANA em configuração de escala com nó de espera com Azure, usamos HANA 2.0 SP4.  

### <a name="prepare-for-hana-installation"></a>Preparar para a instalação HANA

1. **[A]** Antes da instalação HANA, desa estale a senha de raiz. Pode desativar a palavra-passe de raiz depois de concluída a instalação. Execute como `root` comando `passwd` .  

2. **[1]** Verifique se pode iniciar sessão via SSH para **hanadb2** e **hanadb3,** sem ser solicitado para uma senha.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** Instale pacotes adicionais, que são necessários para HANA 2.0 SP4. Para mais informações, consulte a Nota [SAP 2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** Mude a propriedade da SAP HANA `data` e `log` dos diretórios para **hn1** adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **Desative** temporariamente a firewall, para que não interfira com a instalação HANA. Pode voltar a a capacitá-lo, depois de ter sido feita a instalação HANA. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>Instalação HANA

1. **[1]** Instale o SAP HANA seguindo as instruções no [guia de instalação e atualização SAP 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). Neste exemplo, instalamos a escala SAP HANA com mestre, um trabalhador e um nó de espera.  

   a. Inicie o programa **hdblcm** a partir do diretório de software de instalação HANA. Utilize o `internal_network` parâmetro e passe o espaço do endereço para a sub-rede, que é usada para a comunicação interna do nó inter-nódoa hana.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. Ao minuto, insira os seguintes valores:

     * Para **Escolher uma ação**: insira **1** (para instalação)
     * Para **componentes adicionais para instalação:** insira **2, 3**
     * Para o caminho de instalação: prima Enter (predefinições para /hana/shared)
     * Para **o nome do anfitrião local**: prima Insira para aceitar o padrão
     * Em **Baixo deseja adicionar anfitriões ao sistema?** 
     * Para **nomes de anfitriões separados em vírgula para adicionar:** insira **hanadb2, hanadb3**
     * Para **o nome do utilizador raiz** [raiz]: prima Introduzir para aceitar o padrão
     * Para funções para anfitrião hanadb2: insira **1**  (para trabalhador)
     * Para **o Grupo de Failover do anfitrião** para o anfitrião hanadb2 [predefinição]: prima Insira para aceitar o padrão
     * Para **o número de partição de armazenamento** para anfitrião hanadb2 [<<assign automatically>>]: prima Enter para aceitar o padrão
     * Para **o Grupo de Trabalhadores** para o anfitrião hanadb2 [padrão]: prima Enter para aceitar o padrão
     * Para **funções selecionadas** para o anfitrião hanadb3: insira **2** (para standby)
     * Para **o Grupo de Failover do anfitrião** para o anfitrião hanadb3 [predefinição]: prima Insira para aceitar o padrão
     * Para **o Grupo de Trabalhadores** para o anfitrião hanadb3 [padrão]: prima Enter para aceitar o padrão
     * Para **o ID do sistema SAP HANA**: insira **HN1**
     * Para **o número de exemplo** [00]: insira **03**
     * Para **o Grupo de Trabalhadores Anfitriões Locais** [predefinição]: prima Insira para aceitar o padrão
     * Para **selecionar o sistema de utilização / introduzir índice [4]**: insira **4** (para costume)
     * Para **a localização dos volumes de dados** [/hana/dados/HN1]: prima Insira para aceitar o padrão
     * Para **a localização dos volumes de registo** [/hana/log/HN1]: prima Insira para aceitar o padrão
     * Para **restringir a atribuição máxima de memória?** [n]: inserir **n**
     * Para **o nome do anfitrião do certificado para o anfitrião hanadb1** [hanadb1]: prima Insira para aceitar o padrão
     * Para **o nome do anfitrião do certificado hanadb2** [hanadb2]: prima Insira para aceitar o padrão
     * Para **o nome do anfitrião do certificado para o anfitrião hanadb3** [hanadb3]: prima Insira para aceitar o padrão
     * Para **Administrador de Sistema (hn1adm) Palavra-passe**: introduza a palavra-passe
     * Para **o Utilizador da Base de Dados do Sistema (sistema) Palavra-passe**: introduza a palavra-passe do sistema
     * Para **confirmar a palavra-passe do utilizador (sistema)** do utilizador da base de dados do sistema: introduza a palavra-passe do sistema
     * Para **reiniciar o sistema após o reinício da máquina?** [n]: inserir **n** 
     * Pois **Deseja continuar (y/n)**: valide o resumo e se tudo estiver bem, insira **y**


2. **[1]** Verificar global.ini  

   O visor global.ini e certifique-se de que a configuração para a comunicação inter-nódoa interna SAP HANA está no lugar. Verifique a secção **de comunicação.** Deve ter o espaço de endereço para a `hana` sub-rede, e `listeninterface` deve ser definido para `.internal` . Verifique a secção **internal_hostname_resolution.** Deve ter os endereços IP para as máquinas virtuais HANA que pertencem à `hana` sub-rede.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Adicione mapeamento de anfitrião para garantir que os endereços IP do cliente são usados para a comunicação do cliente. Adicione a secção `public_host_resolution` , e adicione os endereços IP correspondentes da sub-rede do cliente.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** Reinicie o SAP HANA para ativar as alterações.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Verifique se a interface do cliente utilizará os endereços IP da `client` sub-rede para comunicação.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   Para obter informações sobre como verificar a configuração, consulte a Nota SAP [2183363 - Configuração da rede interna SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

5. **[A]** Re-activar a firewall.  
   - Pare HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - Re-ativar a firewall
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - Abra as portas de firewall necessárias

       > [!IMPORTANT]
       > Crie regras de firewall para permitir a comunicação de nó inter hana e o tráfego do cliente. As portas necessárias estão listadas nas [portas TCP/IP de todos os produtos SAP](https://help.sap.com/viewer/ports). Os seguintes comandos são apenas um exemplo. Neste cenário com o sistema usado número 03.

       <pre><code>
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       </code></pre>

   - Iniciar HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. Para otimizar o ARMAZENAMENTO HANA para o armazenamento subjacente dos Ficheiros Azure NetApp, desapedaça os seguintes parâmetros SAP HANA:

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**em**
   - `async_write_submit_active`**em**
   - `async_write_submit_blocks`**todos todos**

   Para obter mais informações, consulte [aplicações NETApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]. 

   Começando pelos sistemas SAP HANA 2.0, pode definir os parâmetros em `global.ini` . Para mais informações, consulte a NOTA SAP [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   Para as versões dos sistemas SAP HANA 1.0 SPS12 e anteriores, estes parâmetros podem ser definidos durante a instalação, conforme descrito na Nota [SAP 2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. O armazenamento utilizado pelo Azure NetApp Files tem uma limitação de tamanho de ficheiro de 16 terabytes (TB). O SAP HANA não está implicitamente ciente da limitação de armazenamento e não criará automaticamente um novo ficheiro de dados quando o limite de tamanho do ficheiro de 16 TB for atingido. À medida que o SAP HANA tenta aumentar o ficheiro para além de 16 TB, essa tentativa resultará em erros e, eventualmente, numa falha no servidor de índice. 

   > [!IMPORTANT]
   > Para evitar que o SAP HANA tente cultivar ficheiros de dados para além do [limite de 16-TB](../../../azure-netapp-files/azure-netapp-files-resource-limits.md) do subsistema de armazenamento, dediboe os seguintes parâmetros em `global.ini` .  
   > - datavolume_striping = verdade
   > - datavolume_striping_size_gb = 15000 Para mais informações, consulte a nota [SAP 2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Esteja atento à Nota [SAP 2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Teste SAP HANA failover 

1. Simular um nó num nó de trabalhadores da SAP HANA. Faça o seguinte: 

   a. Antes de simular a queda do nó, executar os seguintes comandos como **hn1** adm para capturar o estado do ambiente:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Para simular uma queda de nó, executar o seguinte comando como raiz no nó do trabalhador, que é **hanadb2** neste caso:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Monitorize o sistema para a conclusão da falha. Quando a falha tiver sido concluída, capture o estado, que deve parecer o seguinte:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Quando um nó experimentar o pânico do núcleo, evite atrasos com o failover SAP HANA, fixando `kernel.panic` para 20 segundos em *todas as* máquinas virtuais HANA. A configuração é feita em `/etc/sysctl` . Reinicie as máquinas virtuais para ativar a mudança. Se esta mudança não for realizada, o failover pode demorar 10 ou mais minutos quando um nó está a sentir o pânico do núcleo.  

2. Desligue o servidor de nomes fazendo o seguinte:

   a. Antes do teste, verifique o estado do ambiente executando os seguintes comandos como **hn1** adm:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. Executar os seguintes comandos como **hn1** adm no nó mestre ativo, que é **hanadb1** neste caso:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    O nó de espera **hanadb3** assumirá o lugar de nó mestre. Aqui está o estado de recursos após o teste de failover ser concluído:  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Reinicie a instância HANA no **hanadb1** (isto é, na mesma máquina virtual, onde o servidor de nome foi morto). O **nó hanadb1** vai voltar a juntar-se ao ambiente e manterá o seu papel de prontidão.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Depois de a SAP HANA ter começado em **hanadb1,** espere o seguinte estado:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Mais uma vez, mate o servidor de nomes no nó principal atualmente ativo (isto é, no nó **hanadb3).**  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Nó **hanadb1** retomará o papel de nó mestre. Após a conclusão do teste de failover, o estado será assim:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Inicie o SAP HANA no **hanadb3,** que estará pronto para servir como nó de espera.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Depois de a SAP HANA ter começado em **hanadb3,** o estado parece o seguinte:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Passos seguintes

* [Azure Virtual Machines planejamento e implementação para SAP][planning-guide]
* [Implantação de máquinas virtuais Azure para SAP][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP][dbms-guide]
* [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](./hana-vm-operations-netapp.md)
* Para aprender a estabelecer uma elevada disponibilidade e plano para a recuperação de desastres da SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha].