---
title: SAP HANA scale-out com standby com Ficheiros Azure NetApp em SLES / Microsoft Docs
description: Guia de alta disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server com Ficheiros Azure NetApp para aplicações SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/24/2020
ms.author: radeltch
ms.openlocfilehash: 15cdd4c53105998488d2ae1f544e34c1e07a157a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147134"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>Implemente um sistema de escala SAP HANA com nó de standby em VMs Azure utilizando ficheiros Azure NetApp no SUSE Linux Enterprise Server 

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


Este artigo descreve como implementar um sistema SAP HANA altamente disponível numa configuração de escala com standby em máquinas virtuais Azure (VMs) utilizando [ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) para os volumes de armazenamento partilhados.  

Nas configurações de exemplo, comandos de instalação, e assim por diante, a instância HANA é **03** e o ID do sistema HANA é **HN1**. Os exemplos são baseados em HANA 2.0 SP4 e SUSE Linux Enterprise Server para SAP 12 SP4. 

Antes de começar, consulte as seguintes notas e documentos SAP:

* [Documentação de Ficheiros Azure NetApp][anf-azure-doc] 
* Nota SAP [1928533] inclui:  
  * Uma lista de tamanhos De VM Azure que são suportados para a implementação de software SAP
  * Informações importantes sobre a capacidade para tamanhos de VM Azure
  * Software SAP suportado e sistema operativo (OS) e combinações de bases de dados
  * A versão necessária para o kernel SAP necessário para Windows e Linux no Microsoft Azure
* Nota [SAP 2015553]: Lista os pré-requisitos para implementações de software SAP suportadas pelo SAP em Azure
* Nota [SAP 2205917]: Contém definições de OS recomendadas para o Servidor Empresarial SUSE Linux para Aplicações SAP
* Nota SAP [1944799]: Contém diretrizes sAP para servidor empresarial SUSE Linux para aplicações SAP
* Nota [SAP 2178632]: Contém informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure
* Nota [SAP 2191498]: Contém a versão necessária do Agente anfitrião SAP para linux em Azure
* Nota [SAP 2243692]: Contém informações sobre licenciamento SAP em Linux em Azure
* Nota [SAP 1984787]: Contém informações gerais sobre o SUSE Linux Enterprise Server 12
* Nota [SAP 1999351]: Contém informações adicionais de resolução de problemas para a extensão de monitorização melhorada do Azure para o SAP
* Nota [SAP 1900823]: Contém informações sobre os requisitos de armazenamento do SAP HANA
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Contém todas as notas SAP necessárias para Linux
* [Planeamento e implementação de Máquinas Virtuais Azure para SAP em Linux][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP em Linux][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP em Linux][dbms-guide]
* [Guias de boas práticas SUSE SAP HA][suse-ha-guide]: Contém todas as informações necessárias para configurar a Alta Disponibilidade do NetWeaver e a Replicação do Sistema SAP HANA no local (a ser usado como base geral; fornecem informações muito mais detalhadas)
* [Extensão de alta disponibilidade sUSE 12 Notas de lançamento SP3][suse-ha-12sp3-relnotes]
* [Aplicações NetApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]


## <a name="overview"></a>Descrição geral

Um método para alcançar a alta disponibilidade da HANA é configurando a falha automática do anfitrião. Para configurar a falha automática do anfitrião, adicione uma ou mais máquinas virtuais ao sistema HANA e configure-as como nós de espera. Quando o nó ativo falha, um nó de espera assume automaticamente. Na configuração apresentada com máquinas virtuais Azure, obtém-se falhas automáticas utilizando [o NFS em Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

> [!NOTE]
> O nó de espera precisa de acesso a todos os volumes de base de dados. Os volumes HANA devem ser montados como volumes NFSv4. O mecanismo de bloqueio melhorado baseado no aluguer de ficheiros `I/O` no protocolo NFSv4 é utilizado para esgrima. 

> [!IMPORTANT]
> Para construir a configuração suportada, deve implantar os dados hana e os volumes de registo como volumes NFSv4.1 e montá-los utilizando o protocolo NFSv4.1. A configuração de falha automática do hospedeiro HANA com nó de standby não é suportada com NFSv3.

![Visão geral de alta disponibilidade da SAP NetWeaver](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

No diagrama anterior, que segue as recomendações da rede SAP HANA, três subredes estão representadas dentro de uma rede virtual Azure: 
* Para comunicação com clientes
* Para comunicação com o sistema de armazenamento
* Para comunicação interna hana inter-nó

Os volumes Azure NetApp estão em subnet separada, [delegada em Ficheiros Azure NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

Para esta configuração de exemplo, as subredes são:  

  - `client`10.23.0.0/24  
  - `storage`10.23.2.0/24  
  - `hana`10.23.3.0/24  
  - `anf`10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Configurar a infraestrutura de ficheiros Azure NetApp 

Antes de avançar com a configuração da infraestrutura de Ficheiros Azure NetApp, familiarize-se com a documentação dos [Ficheiros Azure NetApp][anf-azure-doc]. 

Os Ficheiros Azure NetApp estão disponíveis em [várias regiões do Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Verifique se a sua região azure selecionada oferece ficheiros Azure NetApp.  

Para obter informações sobre a disponibilidade de Ficheiros Azure NetApp pela região do Azure, consulte a disponibilidade de [ficheiros Azure NetApp pela região do Azure.][anf-avail-matrix]  

Antes de implementar ficheiros Azure NetApp, solicite o embarque nos Ficheiros Azure NetApp, indo ao Registo de Instruções de [Ficheiros Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implementar recursos de Ficheiros Azure NetApp  

As seguintes instruções pressupõem que já implementou a sua [rede virtual Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Os recursos e VMs do Azure NetApp, onde serão montados os recursos dos Ficheiros Azure NetApp, devem ser implantados na mesma rede virtual Azure ou em redes virtuais Azure.  

1. Se ainda não disponibilizou os recursos, solicite [o embarque nos Ficheiros AzurApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)do Azure .  

2. Crie uma conta NetApp na sua região Azure selecionada seguindo as instruções em [Criar uma conta NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Criar um conjunto de capacidades de Ficheiros Azure NetApp seguindo as instruções em Configurar um conjunto de [capacidades de Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  

   A arquitetura HANA apresentada neste artigo utiliza um único conjunto de capacidades azure NetApp Files ao nível do *Ultra Service.* Para cargas de trabalho HANA no Azure, recomendamos a utilização de um [Nível](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)de serviço Azure NetApp Files *Ultra* ou *Premium* .  

4. Delege uma sub-rede para ficheiros Azure NetApp, conforme descrito nas instruções em [Delegar uma sub-rede para ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Implemente volumes de Ficheiros Azure NetApp seguindo as instruções em [Criar um volume NFS para Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  

   À medida que está a implementar os volumes, certifique-se de selecionar a versão **NFSv4.1.** Atualmente, o acesso ao NFSv4.1 requer uma lista geminada adicional. Implementar os volumes na [sub-rede](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)de Ficheiros Azure NetApp designados . Os endereços IP dos volumes Azure NetApp são atribuídos automaticamente. 
   
   Tenha em mente que os recursos do Azure NetApp Files e os VMs Azure devem estar na mesma rede virtual Azure ou em redes virtuais Azure. Por exemplo, **HN1**-data-mnt00001, **HN1**-log-mnt00001, e assim por diante, são os nomes de volume e nfs://10.23.1.5/**HN1**-data-mnt00001, nfs://10.23.1.4/**HN1**-log-mnt00001, e assim por diante, são os caminhos de arquivo para os volumes de Ficheiros Azure NetApp.  

   * volume **HN1**-data-mnt00001 (nfs://10.23.1.5/**HN1**-data-mnt00001)
   * volume **HN1**-data-mnt00002 (nfs://10.23.1.6/**HN1**-data-mnt00002)
   * volume **HN1**-log-mnt00001 (nfs://10.23.1.4/**HN1**-log-mnt00001)
   * volume **HN1**-log-mnt00002 (nfs://10.23.1.6/**HN1**-log-mnt00002)
   * volume **HN1**-partilhado (nfs://10.23.1.4/**HN1**-partilhado)
   
   Neste exemplo, usamos um volume de Ficheiros Azure NetApp separado para cada dados hana e volume de registo. Para uma configuração mais otimizada em sistemas mais pequenos ou não produtivos, é possível colocar todos os suportes de dados e todos os registos montados num único volume.  

### <a name="important-considerations"></a>Considerações importantes

Enquanto está a criar os seus Ficheiros Azure NetApp para SAP NetWeaver na arquitetura de alta disponibilidade da SUSE, esteja ciente das seguintes considerações importantes:

- A piscina de capacidade mínima é de 4 tebibytes (TiB).  
- O tamanho mínimo do volume é de 100 gibibytes (GiB).
- Os Ficheiros Azure NetApp e todas as máquinas virtuais onde serão montados os volumes dos Ficheiros Azure NetApp devem estar na mesma rede virtual Azure ou em [redes virtuais na](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mesma região.  
- A rede virtual selecionada deve ter uma subrede delegada nos Ficheiros Azure NetApp.
- A produção de um volume de Ficheiros Azure NetApp é uma função do nível de quota de volume e de serviço, tal como documentado no nível de [Serviço para Ficheiros Azure NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) Ao dimensionar os volumes HANA Azure NetApp, certifique-se de que a entrada resultante satisfaz os requisitos do sistema HANA.  
- Com a política de [exportação](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)de Ficheiros Azure NetApp, pode controlar os clientes permitidos, o tipo de acesso (ler, ler apenas, e assim por diante). 
- A funcionalidade De Ficheiros Azure NetApp ainda não está ciente da zona. Atualmente, a funcionalidade não é implantada em todas as zonas de disponibilidade de uma região do Azure. Esteja ciente das potenciais implicações de latência em algumas regiões de Azure.  
-  

> [!IMPORTANT]
> Para as cargas de trabalho da SAP HANA, a baixa latência é crítica. Trabalhe com o seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de Ficheiros Azure NetApp são implantados nas proximidades.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento para base de dados HANA em Ficheiros Azure NetApp

A entrada de um volume de Ficheiros Azure NetApp é uma função do tamanho do volume e nível de serviço, conforme documentado no nível de [Serviço para Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Ao conceber a infraestrutura para SAP em Azure, esteja ciente de alguns requisitos mínimos de armazenamento pela SAP, que se traduzem em características mínimas de rendimento:

- Ativar a leitura-escrita em /hana/log de 250 megabytes por segundo (MB/s) com tamanhos de 1-MB I/O.  
- Ativar a atividade de leitura de pelo menos 400 MB/s para /hana/dados para tamanhos de 16 MB e 64-MB I/O.  
- Ativar a atividade de escrita de pelo menos 250 MB/s para /hana/dados com tamanhos de 16 MB e 64-MB I/O. 

Os limites de produção dos [Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) por 1 TiB de quota de volume são:
- Premium Nível de Armazenamento - 64 MiB/s  
- Nível de Ultra Armazenamento - 128 MiB/s  

Para satisfazer os requisitos mínimos de entrada de dados e registos saps, e as diretrizes para /hana/partilhado, os tamanhos recomendados seriam:

| Volume | Tamanho de<br>Nível de armazenamento premium | Tamanho de<br>Nível ultra armazenamento | Protocolo NFS suportado |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/dados | 6.3 Tib | 3.2 Tib | v4.1 |
| /hana/compartilhado | Máx (512 GB, 1xRAM) por 4 nós de trabalhador | Máx (512 GB, 1xRAM) por 4 nós de trabalhador | v3 ou v4.1 |

A configuração SAP HANA para o layout que é apresentado neste artigo, utilizando o nível de Ultra Armazenamento de Ficheiros Azure NetApp, seria:

| Volume | Tamanho de<br>Nível ultra armazenamento | Protocolo NFS suportado |
| --- | --- | --- |
| /hana/log/mnt000001 | 2 TiB | v4.1 |
| /hana/log/mnt000002 | 2 TiB | v4.1 |
| /hana/data/mnt000001 | 3.2 Tib | v4.1 |
| /hana/data/mnt000002 | 3.2 Tib | v4.1 |
| /hana/compartilhado | 2 TiB | v3 ou v4.1 |

> [!NOTE]
> As recomendações de dimensionamento do Azure NetApp, aqui indicadas, destinam-se a cumprir os requisitos mínimos que a SAP recomenda para os seus fornecedores de infraestruturas. Em implementações reais de clientes e cenários de carga de trabalho, estes tamanhos podem não ser suficientes. Utilize estas recomendações como ponto de partida e adapte-se, com base nos requisitos da sua carga de trabalho específica.  

> [!TIP]
> Pode redimensionar os volumes do Azure NetApp De forma dinâmica, sem ter de *desmontar* os volumes, parar as máquinas virtuais ou parar o SAP HANA. Esta abordagem permite flexibilidade para satisfazer as exigências de entrada esperadas e imprevistas da sua aplicação.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Implementar máquinas virtuais Linux através do portal Azure

Primeiro, é necessário criar os volumes de Ficheiros Azure NetApp. Em seguida, faça os seguintes passos:
1. Crie as [subredes de rede virtual Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) na sua [rede virtual Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 
1. Implante os VMs. 
1. Crie as interfaces de rede adicionais e fixe as interfaces de rede aos VMs correspondentes.  

   Cada máquina virtual tem três interfaces de rede, que correspondem `storage` `hana`às três redes virtuais Azure (e).`client` 

   Para mais informações, consulte [Criar uma máquina virtual Linux em Azure com vários cartões](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)de interface de rede .  

> [!IMPORTANT]
> Para as cargas de trabalho da SAP HANA, a baixa latência é crítica. Para obter baixa latência, trabalhe com o seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de Ficheiros Azure NetApp são implantados nas proximidades. Quando estiver [a embarcar no novo sistema SAP HANA](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) que está a utilizar ficheiros SAP HANA Azure NetApp, submeta as informações necessárias. 
 
As próximas instruções assumem que já criou o grupo de recursos, a rede virtual `client` `storage` Azure e as três redes virtuais Azure: e `hana`. Quando implementar os VMs, selecione a subnet do cliente, de modo a que a interface de rede do cliente seja a interface principal nos VMs. Também terá de configurar uma rota explícita para a sub-rede delegada dos Ficheiros Azure Net através do portal de sub-rede de armazenamento. 

> [!IMPORTANT]
> Certifique-se de que o SISTEMA selecionado é certificado por SAP HANA nos tipos de VM específicos que está a utilizar. Para obter uma lista de tipos vM certificados SAP HANA e lançamentos de SO para esses tipos, vá ao site de [plataformas IaaS certificadas sAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Clique nos detalhes do tipo VM listado para obter a lista completa de lançamentos de SAP HANA suportados por SAP HANA para esse tipo.  

1. Crie um conjunto de disponibilidade para o SAP HANA. Certifique-se de que define o domínio da atualização máxima.  

2. Criar três máquinas virtuais (**hanadb1,** **hanadb2,** **hanadb3**) fazendo os seguintes passos:  

   a. Use uma imagem SLES4SAP na galeria Azure que é suportada para SAP HANA. Usamos uma imagem SLES4SAP 12 SP4 neste exemplo.  

   b. Selecione o conjunto de disponibilidade que criou anteriormente para o SAP HANA.  

   c. Selecione a subnet de rede virtual do cliente Azure. Selecione [Rede Acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  

   Quando se implanta as máquinas virtuais, o nome da interface de rede é gerado automaticamente. Nestas instruções de simplicidade, referimo-nos às interfaces de rede geradas automaticamente, que estão ligadas à subnet de rede virtual do cliente Azure, como **hanadb1-cliente**, **hanadb2-client,** e **hanadb3-client**. 

3. Crie três interfaces de rede, uma `storage` para cada máquina virtual, para a subnet de rede virtual (neste exemplo, **hanadb1-storage,** **hanadb2-storage,** e **hanadb3-storage).**  

4. Crie três interfaces de rede, uma `hana` para cada máquina virtual, para a subnet de rede virtual (neste exemplo, **hanadb1-hana,** **hanadb2-hana**, e **hanadb3-hana).**  

5. Fixe as interfaces de rede virtual recém-criadas às máquinas virtuais correspondentes, fazendo os seguintes passos:  

    a. Vá à máquina virtual no [portal Azure.](https://portal.azure.com/#home)  

    b. No painel esquerdo, selecione **Máquinas Virtuais**. Filtre no nome da máquina virtual (por exemplo, **hanadb1**), e, em seguida, selecione a máquina virtual.  

    c. No painel **de visão geral,** selecione **Parar** para desalojar a máquina virtual.  

    d. Selecione **Networking**e, em seguida, fixe a interface de rede. Na lista de drop-down da interface da **rede Attach,** selecione as interfaces de rede já criadas para as `storage` redes e `hana` subnets.  
    
    e. Selecione **Guardar**. 
 
    f. Repita os passos b através e para as restantes máquinas virtuais (no nosso exemplo, **hanadb2** e **hanadb3**).
 
    g. Deixe as máquinas virtuais em estado de parada por enquanto. Em seguida, permitiremos uma [rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para todas as interfaces de rede recém-anexadas.  

6. Ativar a ligação acelerada de rede `storage` `hana` para as interfaces de rede adicionais para as e subnets adicionais, fazendo os seguintes passos:  

    a. Abra a [Nuvem Azure](https://azure.microsoft.com/features/cloud-shell/) no [portal Azure.](https://portal.azure.com/#home)  

    b. Execute os seguintes comandos para permitir a ligação de rede `storage` acelerada para as interfaces de rede adicionais, que estão ligadas às e `hana` subredes.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Inicie as máquinas virtuais fazendo os seguintes passos:  

    a. No painel esquerdo, selecione **Máquinas Virtuais**. Filtre no nome da máquina virtual (por exemplo, **hanadb1**), e, em seguida, selecione-o.  

    b. No painel **de visão geral,** selecione **Iniciar**.  

## <a name="operating-system-configuration-and-preparation"></a>Configuração e preparação do sistema operativo

As instruções nas secções seguintes são pré-fixadas com uma das seguintes:
* **[A]**: Aplicável a todos os nós
* **[1]**: Aplicável apenas ao nó 1
* **[2]**: Aplicável apenas ao nó 2
* **[3]**: Aplicável apenas ao nó 3

Configure e prepare o seu SISTEMA fazendo os seguintes passos:

1. **[A]** Mantenha os ficheiros do anfitrião nas máquinas virtuais. Incluir entradas para todas as subredes. Para este exemplo, `/etc/hosts` foram adicionadas as seguintes entradas.  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** Alterar as definições de DHCP e cloud config para a interface de rede para armazenamento para evitar alterações não intencionais do nome de anfitrião.  

    As seguintes instruções pressupõem `eth1`que a interface da rede de armazenamento é . 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. **[A]** Adicione uma rota de rede, de modo que a comunicação aos Ficheiros Azure NetApp vá através da interface da rede de armazenamento.  

    As seguintes instruções pressupõem `eth1`que a interface da rede de armazenamento é .  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    Reiniciar o VM para ativar as alterações.  

3. **[A]** Prepare o SISTEMA para executar SAP HANA em Sistemas NetApp com NFS, conforme descrito nas [aplicações NetApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]. Crie ficheirode configuração */etc/sysctl.d/netapp-hana.conf* para as definições de configuração do NetApp.  

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

4. **[A]** Criar ficheiro de configuração */etc/sysctl.d/ms-az.conf* com a Microsoft para configurações de configuração Azure.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

4. **[A]** Ajuste as definições sunrpc, conforme recomendado nas [aplicações NetApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Monte os volumes de ficheiros Azure NetApp

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
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Verifique a definição de domínio NFS. Certifique-se de que o domínio está configurado como o domínio **`defaultv4iddomain.com`** padrão dos Ficheiros Azure NetApp, ou seja, e o mapeamento não está definido para **ninguém**.  

    > [!IMPORTANT]
    > Certifique-se de que configura `/etc/idmapd.conf` o domínio NFS no VM para corresponder **`defaultv4iddomain.com`** à configuração de domínio predefinido nos Ficheiros Azure NetApp: . Se houver uma incompatibilidade entre a configuração de domínio no cliente NFS (isto é, o VM) e o servidor NFS, ou seja, a configuração Azure NetApp, então `nobody`as permissões para ficheiros em volumes Azure NetApp que são montados nos VMs serão apresentadas como .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`Verificar . Deve ser definido para **Y.** Para criar a estrutura `nfs4_disable_idmapping` do diretório onde está localizada, execute o comando do suporte. Não poderá criar manualmente o diretório em /sys/módulos, porque o acesso é reservado para o núcleo/controladores.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. **[A]** Criar manualmente o grupo SAP HANA e o utilizador. Os IDs para sapsys de grupo e o utilizador **hn1**adm devem ser definidos para os mesmos IDs, que são fornecidos durante o embarque. (Neste exemplo, as iDs são fixadas para **1001**.) Se as identificações não estiverem corretamente definidas, não conseguirá aceder aos volumes. Os IDs para sapsys de grupo e contas de utilizador **hn1**adm e sapadm devem ser os mesmos em todas as máquinas virtuais.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** Monte os volumes de ficheiros Azure NetApp partilhados.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** Monte os volumes específicos do nó em **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Monte os volumes específicos do nó em **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Monte os volumes específicos do nó em **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Verifique se todos os volumes HANA são montados com a versão **nFS protocol nFSv4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Instalação  

Neste exemplo para a implementação do SAP HANA em configuração de escala-out com nó de espera com Azure, usamos HANA 2.0 SP4.  

### <a name="prepare-for-hana-installation"></a>Preparar para a instalação HANA

1. **[A]** Antes da instalação da HANA, desloque a palavra-passe de raiz. Pode desativar a palavra-passe da raiz depois de concluída a instalação. Executar `root` como `passwd`comando .  

2. **[1]** Verifique se pode fazer login via SSH para **hanadb2** e **hanadb3**, sem ser solicitado por uma senha.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** Instale pacotes adicionais, que são necessários para HANA 2.0 SP4. Para mais informações, consulte A Nota SAP [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2], [3]** Mude a propriedade da `data` `log` SAP HANA e os diretórios para **hn1**adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>Instalação HANA

1. **[1]** Instale o SAP HANA seguindo as instruções no guia de [instalação e atualização SAP HANA 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). Neste exemplo, instalamos sap HANA scale-out com mestre, um trabalhador, e um nó de espera.  

   a. Inicie o programa **hdblcm** a partir do diretório de software de instalação HANA. Utilize `internal_network` o parâmetro e passe o espaço de endereço para a sub-rede, que é utilizado para a comunicação inter-nó interna HANA.  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. A pedido, introduza os seguintes valores:

     * Para **escolher uma ação**: insira **1** (para instalação)
     * Para **componentes adicionais para instalação:** insira **2, 3**
     * Para o caminho de instalação: prima Enter (predefinições para /hana/partilhado)
     * Para nome do **anfitrião local**: prima Introduza para aceitar o padrão
     * Sob **o seu deseja adicionar anfitriões ao sistema?** **y**
     * Para **nomes de anfitriões separados com vírina a adicionar:** insira **hanadb2, hanadb3**
     * Para o nome do **utilizador raiz** [raiz]: prima Introduza para aceitar o padrão
     * Para **palavra-passe**do utilizador raiz : introduza a palavra-passe do utilizador-raiz
     * Para funções para o anfitrião hanadb2: insira **1** (para trabalhador)
     * Para **o Grupo de Failover do Anfitrião** para o anfitrião hanadb2 [padrão]: prima Entrar para aceitar o padrão
     * Para **o número de partição** <assign automatically> de armazenamento para o anfitrião hanadb2 [<>]: prima Introduza para aceitar o padrão
     * Para **o Grupo de Trabalhadores** para o anfitrião hanadb2 [padrão]: prima Entrar para aceitar o padrão
     * Para **selecionar funções** para o anfitrião hanadb3: insira **2** (para standby)
     * Para **o Grupo de Failover do Anfitrião** para o anfitrião hanadb3 [padrão]: prima Entrar para aceitar o padrão
     * Para **o Grupo de Trabalhadores** para o anfitrião hanadb3 [padrão]: prima Entrar para aceitar o padrão
     * Para **Identificação do Sistema SAP HANA**: insira **HN1**
     * Por **exemplo número** [00]: insira **03**
     * Para o **Grupo local de trabalho anfitrião** [padrão]: prima Entrar para aceitar o padrão
     * Para **selecionar a utilização do sistema / Entrar no índice [4]**: introduzir **4** (para personalizado)
     * Para **a localização dos volumes de dados** [/hana/data/HN1]: prima Introduza para aceitar o padrão
     * Para **a localização dos volumes de registo** [/hana/log/HN1]: prima Introduza para aceitar o padrão
     * Para restringir a **atribuição máxima de memória?** [n]: inserir **n**
     * Para o nome do **anfitrião do certificado para o anfitrião hanadb1** [hanadb1]: prima Introduza para aceitar o padrão
     * Para o nome do **anfitrião do certificado para o anfitrião hanadb2** [hanadb2]: prima Introduza para aceitar o padrão
     * Para o nome do **anfitrião do certificado para o anfitrião hanadb3** [hanadb3]: prima Introduza para aceitar o padrão
     * Para **administrador de sistema (hn1adm) Palavra-passe**: insira a palavra-passe
     * Para o **utilizador (sistema) utilizador (sistema) utilizador**do sistema : introduza a palavra-passe do sistema
     * Para confirmar a **palavra-passe do utilizador (sistema)** do utilizador do sistema : introduzir a palavra-passe do sistema
     * Para **reiniciar o sistema após a reinicialização da máquina?** [n]: inserir **n** 
     * Pois **você quer continuar (y/n)**: validar o resumo e se tudo parecer bom, insira **y**


2. **[1]** Verificar global.ini  

   Exiba global.ini e certifique-se de que a configuração para a comunicação inter-nó interna SAP HANA está em vigor. Verifique a secção de **comunicação.** Deve ter o espaço `hana` de endereço `listeninterface` para a `.internal`sub-rede e deve ser definido para . Verifique a secção **internal_hostname_resolution.** Deve ter os endereços IP das máquinas virtuais `hana` HANA que pertencem à sub-rede.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Adicione o mapeamento do anfitrião para garantir que os endereços IP do cliente são usados para a comunicação do cliente. Adicione `public_host_resolution`a secção e adicione os endereços IP correspondentes da subnet do cliente.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** Reiniciar o SAP HANA para ativar as alterações.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Verifique se a interface do cliente utilizará os endereços IP da `client` subnet para comunicação.  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Para obter informações sobre como verificar a configuração, consulte o SAP Note [2183363 - Configuração da rede interna SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

6. Para otimizar o SAP HANA para o armazenamento subjacente aos Ficheiros Azure NetApp, defina os seguintes parâmetros SAP HANA:

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**em**
   - `async_write_submit_active`**em**
   - `async_write_submit_blocks`**todos os**

   Para mais informações, consulte [as aplicações NetApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp][anf-sap-applications-azure]. 

   A partir dos sistemas SAP HANA 2.0, `global.ini`pode definir os parâmetros em . Para mais informações, consulte SAP Nota [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   Para versões de sistemas SAP HANA 1.0 SPS12 e anteriores, estes parâmetros podem ser definidos durante a instalação, conforme descrito na Nota SAP [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. O armazenamento utilizado pelos Ficheiros Azure NetApp tem uma limitação de tamanho de ficheiro de 16 terabytes (TB). O SAP HANA não está implicitamente ciente da limitação de armazenamento, e não criará automaticamente um novo ficheiro de dados quando o limite de tamanho de ficheiro de 16 TB for atingido. À medida que o SAP HANA tenta aumentar o ficheiro para além de 16 TB, essa tentativa resultará em erros e, eventualmente, num crash de servidor de índice. 

   > [!IMPORTANT]
   > Para evitar que o SAP HANA tente cultivar ficheiros de dados para além do `global.ini`limite de [16 TB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) do subsistema de armazenamento, defina os seguintes parâmetros em .  
   > - datavolume_striping = verdadeiro
   > - datavolume_striping_size_gb = 15000 Para mais informações, consulte a Nota SAP [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Esteja atento à Nota [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Teste SAP HANA falha 

1. Simular um acidente de nó num nó de trabalhador da SAP HANA. Faça o seguinte: 

   a. Antes de simular a queda do nó, execute os seguintes comandos como **hn1**adm para capturar o estado do ambiente:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
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

   b. Para simular uma queda no nó, corra o seguinte comando como raiz no nó do trabalhador, que é **hanadb2** neste caso:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Monitorize o sistema para a conclusão da falha. Quando a falha estiver concluída, capture o estado, que deve parecer o seguinte:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Quando um nó experimenta o pânico do kernel, evite atrasos com a falha do SAP HANA, fixando-se `kernel.panic` em 20 segundos em todas *as* máquinas virtuais HANA. A configuração `/etc/sysctl`é feita em . Reinicie as máquinas virtuais para ativar a mudança. Se esta mudança não for realizada, a falha pode demorar 10 ou mais minutos quando um nó está a sentir pânico no núcleo.  

2. Mate o servidor de nomes fazendo o seguinte:

   a. Antes do teste, verifique o estado do ambiente executando os seguintes comandos como **hn1**adm:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   b. Execute os seguintes comandos como **hn1**adm no nó principal ativo, que é **hanadb1** neste caso:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    O nó de espera **hanadb3** assumirá o lugar de mestre nó. Aqui está o estado de recursos após o teste de failover ser concluído:  

    <pre><code>
        # Check the instance status
        sapcontrol -nr 03 -function GetSystemInstanceList
        GetSystemInstanceList
        OK
        hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
        hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
        hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
        hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
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

   c. Reinicie a instância HANA no **hanadb1** (isto é, na mesma máquina virtual, onde o servidor de nome foi morto). O nó **hanadb1** voltará a juntar-se ao ambiente e manterá o seu papel de standby.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Depois de o SAP HANA ter começado em **hanadb1,** espere o seguinte estatuto:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Mais uma vez, mate o servidor de nome sintetizado no nó principal atualmente ativo (isto é, no nó **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   O nó **hanadb1** retomará o papel de mestre nó. Após a conclusão do teste de failover, o estado será assim:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

   e. Inicie o SAP HANA em **hanadb3,** que estará pronto para servir como um nó de espera.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Depois de o SAP HANA ter começado em **hanadb3,** o estado parece ser o seguinte:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

* [Planeamento e implementação de Máquinas Virtuais Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais Azure para SAP][deployment-guide]
* [Implantação de DBMS de Máquinas Virtuais Azure para SAP][dbms-guide]
* Para aprender como estabelecer alta disponibilidade e plano para a recuperação de desastres de SAP HANA em VMs Azure, consulte [Alta Disponibilidade de SAP HANA em Máquinas Virtuais Azure (VMs)][sap-hana-ha].
