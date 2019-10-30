---
title: SAP HANA escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp Files em SUSE Linux Enterprise Server | Microsoft Docs
description: Guia de alta disponibilidade para SAP NetWeaver em SUSE Linux Enterprise Server com Azure NetApp Files para aplicativos SAP
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
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 76369c1a4beb792de03cf0ccae5c86825812f103
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934165"
---
# <a name="sap-hana-scale-out-with-standby-node-on-azure-vms-with-azure-netapp-files-on-suse-linux-enterprise-server"></a>SAP HANA escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp Files no SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Este artigo descreve como implantar o sistema HANA altamente disponível na configuração de expansão com o modo de espera em máquinas virtuais do Azure com [Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) para os volumes de armazenamento compartilhado.  
Nas configurações de exemplo, comandos de instalação e assim por diante, a instância do HANA é **03** e a ID do sistema Hana é **HN1**. Os exemplos são baseados em HANA 2,0 SP4 e SUSE Linux Enterprise Server para SAP 12 SP4. 

Leia as seguintes notas e documentos SAP primeiro:

* [Documentação do Azure NetApp Files][anf-azure-doc] 
* Nota SAP [1928533], que tem:  
  * Lista de tamanhos de VM do Azure com suporte para a implantação de software SAP
  * Informações de capacidade importantes para tamanhos de VM do Azure
  * Software SAP com suporte e combinações de so (sistema operacional) e banco de dados
  * Versão de kernel do SAP necessária para Windows e Linux em Microsoft Azure
* O SAP Note [2015553] lista os pré-requisitos para implantações de software SAP com suporte no SAP no Azure.
* A observação do SAP [2205917] tem as configurações do sistema operacional recomendadas para SuSE Linux Enterprise Server para aplicativos SAP
* A nota SAP [1944799] tem diretrizes sap para SuSE Linux Enterprise Server para aplicativos SAP
* A nota SAP [2178632] tem informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure.
* A nota SAP [2191498] tem a versão do agente de host do SAP necessária para Linux no Azure.
* A nota SAP [2243692] tem informações sobre o licenciamento SAP no Linux no Azure.
* A nota SAP [1984787] tem informações gerais sobre o SuSE Linux Enterprise Server 12.
* A nota SAP [1999351] tem informações adicionais para solução de problemas para a extensão de monitoramento avançado do Azure para SAP.
* A nota SAP [1900823] tem informações sobre os requisitos de armazenamento SAP Hana
* O [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
* [Planejamento e implementação de máquinas virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP no Linux][dbms-guide]
* [Guias de práticas recomendadas do SUSE SAP ha][suse-ha-guide] Os guias contêm todas as informações necessárias para configurar o NetWeaver HA e a replicação de sistema SAP HANA no local. Use esses guias como uma linha de base geral. Eles fornecem informações muito mais detalhadas.
* [Notas de versão da extensão de alta disponibilidade do SUSE 12 SP3][suse-ha-12sp3-relnotes]
* [Aplicativos SAP da NetApp em Microsoft Azure usando Azure NetApp Files][anf-sap-applications-azure]
* [SAP Hana em sistemas NetApp com NFS](https://www.netapp.com/us/media/tr-4435.pdf). O guia de configuração contém informações sobre como configurar SAP HANA, usando o NFS fornecido pelo Azure NetApp Files.


## <a name="overview"></a>Visão geral

Um dos métodos para obter a alta disponibilidade do HANA é o failover automático do host. Para configurar o failover automático do host, uma ou mais máquinas virtuais são adicionadas ao sistema HANA e configuradas para serem nós em espera. Quando o nó ativo falha, um nó em espera é automaticamente assumido. Na configuração apresentada com as máquinas virtuais do Azure, isso é realizado pelo [NFS em Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

O nó em espera precisa ter acesso a todos os volumes de banco de dados. Os volumes HANA devem ser montados como volumes NFSv4. O mecanismo de bloqueio baseado em concessão de arquivo aprimorado no protocolo NFSv4 é usado para `I/O` isolamento. 

> [!IMPORTANT]
> É obrigatório implantar os volumes de dados e de log do HANA como NFSv 4.1 volumes e montá-los, usando o protocolo NFSv 4.1, para ter a configuração com suporte. Não há suporte para a configuração de failover automático do host HANA com o nó em espera com o NFSv3.

![Visão geral de alta disponibilidade do SAP NetWeaver](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

Seguindo as recomendações de rede SAP HANA, três sub-redes foram criadas em uma rede virtual do Azure: para comunicação com o sistema de armazenamento, para comunicação interna do HANA entre nós e para comunicação do cliente. Os volumes do Azure NetApp estão em sub-rede separada, [delegados para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

Para esta configuração de exemplo, as sub-redes são:  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configurando a infraestrutura de Azure NetApp Files 

Antes de prosseguir com a configuração da infraestrutura de arquivos do Azure NetApp, familiarize-se com a [documentação do Azure NetApp files][anf-azure-doc]. Os arquivos do Azure NetApp estão disponíveis em várias [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Verifique se a região do Azure selecionada oferece Azure NetApp Files.  

O link a seguir mostra a disponibilidade de Azure NetApp Files pela região do Azure: [Azure NetApp files disponibilidade pela região do Azure][anf-avail-matrix].  
Antes de implantar Azure NetApp Files, solicite a integração ao Azure NetApp Files, seguindo as [instruções registrar para arquivos do Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implantar Azure NetApp Files recursos  

As etapas a seguir pressupõem que você já implantou a [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Os recursos de Azure NetApp Files e as VMs, em que os recursos de Azure NetApp Files serão montados devem ser implantados na mesma rede virtual do Azure ou em redes virtuais emparelhadas do Azure.  

1. Se você ainda não fez isso, solicite [a integração ao Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Crie a conta do NetApp na região do Azure selecionada, seguindo as [instruções para criar uma conta do NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Configure Azure NetApp Files pool de capacidade, seguindo as [instruções sobre como configurar o pool de capacidade Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
A arquitetura do HANA apresentada neste artigo usa o pool de capacidade de Azure NetApp Files único, o nível de ultra serviço. É recomendável Azure NetApp Files nível de [serviço](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) ultra ou Premium para cargas de trabalho do Hana no Azure.  

4. Delegue uma sub-rede para os arquivos do Azure NetApp conforme descrito nas [instruções delegar uma sub-rede para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Implante Azure NetApp Files volumes, seguindo as [instruções para criar um volume para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  Certifique-se de selecionar a versão **nfsv 4.1** , ao implantar os volumes. Atualmente, o acesso ao NFSv 4.1 requer uma lista de permissões adicional. Implante os volumes na [sub-rede](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Azure NetApp files designada. Tenha em mente que os recursos Azure NetApp Files e as VMs do Azure devem estar na mesma rede virtual do Azure ou em redes virtuais emparelhadas do Azure. Por exemplo, <b>HN1</b>-data-Mnt00001, <b>HN1</b>-log-mnt00001, etc. são os nomes de volume e NFS://10.23.1.5/<b>HN1</b>-data-mnt00001, NFS://10.23.1.4/<b>HN1</b>-log-mnt00001, etc. são os caminhos de arquivo para os volumes de Azure NetApp files.  

   1. volume <b>HN1</b>-data-mnt00001 (NFS://10.23.1.5/<b>HN1</b>-data-mnt00001)
   2. volume <b>HN1</b>-data-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-data-mnt00002)
   3. volume <b>HN1</b>-log-mnt00001 (NFS://10.23.1.4/<b>HN1</b>-log-mnt00001)
   4. volume <b>HN1</b>-log-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-log-mnt00002)
   5. volume <b>HN1</b>-compartilhado (NFS://10.23.1.4/<b>HN1</b>-Shared)
   
   Neste exemplo, usamos Azure NetApp Files separadas para cada volume de dados e de log do HANA. Para uma configuração mais econômica em sistemas menores ou não produtivos, é possível posicionar todas as montagens de dados e todos os logs montados em um único volume.  

### <a name="important-considerations"></a>Considerações importantes

Ao considerar Azure NetApp Files para o SAP NetWeaver na arquitetura de alta disponibilidade do SUSE, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é 4 TiB.  
- O tamanho mínimo do volume é 100 GiB
- Azure NetApp Files e todas as máquinas virtuais, nas quais Azure NetApp Files volumes serão montados, devem estar na mesma rede virtual do Azure ou em [redes virtuais emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) na mesma região.  
- A rede virtual selecionada deve ter uma sub-rede, delegada para Azure NetApp Files.
- A taxa de transferência de um volume do Azure NetApp é uma função da cota de volume e do nível de serviço, conforme documentado em [nível de serviço para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Ao dimensionar os volumes da NetApp do Azure do HANA, verifique se a taxa de transferência resultante atende aos requisitos do sistema do HANA.  
- O Azure NetApp Files oferece [política de exportação](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): você pode controlar os clientes permitidos, o tipo de acesso (leitura & gravação, somente leitura, etc.). 
- O recurso de Azure NetApp Files ainda não reconhece a zona. Atualmente Azure NetApp Files recurso não está implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja atento às possíveis implicações de latência em algumas regiões do Azure.  
- É importante ter as máquinas virtuais implantadas de perto para o armazenamento do Azure NetApp para baixa latência. Para cargas de trabalho de SAP HANA, baixa latência é crítica. Trabalhe com seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de Azure NetApp Files sejam implantados de perto.  
- A ID de usuário para <b>Sid</b>ADM e a ID de grupo para `sapsys` nas máquinas virtuais devem corresponder à configuração no Azure NetApp files. 

> [!IMPORTANT]
> Para cargas de trabalho de SAP HANA, baixa latência é crítica. Trabalhe com seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de Azure NetApp Files sejam implantados de perto.  

> [!IMPORTANT]
> Se houver uma incompatibilidade entre a ID de usuário para <b>Sid</b>ADM e a ID de grupo para `sapsys` entre a máquina virtual e a configuração do Azure NetApp, as permissões para arquivos nos volumes do Azure NetApp, montadas em máquinas virtuais, serão exibidas como `nobody`. Certifique-se de especificar a ID de usuário correta para <b>Sid</b>ADM e a ID de grupo para `sapsys`, quando [um novo sistema for integrado](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) ao Azure NetApp files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento para o banco de dados HANA no Azure NetApp Files

A taxa de transferência de um volume do Azure NetApp é uma função do tamanho do volume e do nível de serviço, conforme documentado em [nível de serviço para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Ao projetar a infraestrutura para SAP no Azure, você deve estar ciente de alguns requisitos mínimos de armazenamento pelo SAP, que se convertem em características de taxa de transferência mínima:

- Habilitar leitura/gravação em/Hana/log de 250 MB/s com tamanhos de e/s de 1 MB  
- Habilite a atividade de leitura de pelo menos 400 MB/s para/Hana/data para os tamanhos de e/s de 16 MB e 64 MB  
- Habilitar a atividade de gravação de pelo menos 250 MB/s para/Hana/data com tamanhos de e/s de 16 MB e 64 MB  

Os [limites de taxa de transferência de Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) por 1 Tib de cota de volume são:
- Camada de armazenamento Premium-64 MiB/s  
- Camada de armazenamento ultra-128 MiB/s  

Para atender aos requisitos de taxa de transferência mínima do SAP para dados e log, e de acordo com as diretrizes para `/hana/shared`, os tamanhos recomendados seriam semelhantes a:

| Volume | Tamanho<br /> Camada de armazenamento Premium | Tamanho<br /> Camada de armazenamento ultra | Protocolo NFS com suporte |
| --- | --- | --- |
| /Hana/log | 4 TiB | 2 TiB | 4\.1 |
| /Hana/data | 6,3 TiB | 3,2 TiB | 4\.1 |
| /Hana/Shared | Max (512 GB, 1xRAM) por 4 nós de trabalho | Max (512 GB, 1xRAM) por 4 nós de trabalho | V3 ou v 4.1 |

A configuração de SAP HANA para o layout apresentado neste artigo, usando Azure NetApp Files camada de armazenamento ultra seria semelhante a:

| Volume | Tamanho<br /> Camada de armazenamento ultra | Protocolo NFS com suporte |
| --- | --- |
| /hana/log/mnt00001 | 2 TiB | 4\.1 |
| /hana/log/mnt00002 | 2 TiB | 4\.1 |
| /hana/data/mnt00001 | 3,2 TiB | 4\.1 |
| /hana/data/mnt00002 | 3,2 TiB | 4\.1 |
| /Hana/Shared | 2 TiB | V3 ou v 4.1 |

> [!NOTE]
> As recomendações de dimensionamento Azure NetApp Files declaradas aqui estão direcionadas para atender aos requisitos mínimos do SAP expressos em direção aos seus provedores de infraestrutura. Em implantações reais de clientes e cenários de carga de trabalho, isso pode não ser suficiente. Use essas recomendações como um ponto de partida e adapte-se com base nos requisitos de sua carga de trabalho específica.  

> [!TIP]
> Você pode redimensionar Azure NetApp Files volumes dinamicamente, sem a necessidade de `unmount` os volumes, parar as máquinas virtuais ou parar SAP HANA. Isso permite flexibilidade para atender ao seu aplicativo demandas de produtividade esperadas e imprevistas.

## <a name="deploy-linux-virtual-machines-via-azure-portal"></a>Implantar máquinas virtuais do Linux via portal do Azure

Primeiro, você precisa criar os volumes de Azure NetApp Files. Crie as [sub-redes da rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) na [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Implante as máquinas virtuais. Crie as interfaces de rede adicionais e anexe as interfaces de rede às VMs correspondentes. Cada máquina virtual tem três interfaces de rede, correspondentes às três sub-redes da rede virtual do Azure (`storage`, `hana` e `client`).  Consulte [como criar uma máquina virtual Linux no Azure com várias placas de interface de rede](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> Para cargas de trabalho de SAP HANA, baixa latência é crítica. Trabalhe com seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de Azure NetApp Files sejam implantados de perto para atingir baixa latência. Envie as informações necessárias ao integrar o [novo SAP Hana System](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u), que está usando SAP Hana Azure NetApp files.  
> 
As próximas etapas pressupõem que você já criou o grupo de recursos, a rede virtual do Azure e as três sub-redes da rede virtual do Azure: `storage`, `hana` e `client`.  Ao implantar as máquinas virtuais, selecione a sub-rede de armazenamento para que a interface de rede de armazenamento seja a interface principal nas máquinas virtuais. Se isso não for possível, uma rota explícita para a sub-rede delegada do Azure NetApp por meio do gateway de sub-rede de armazenamento deve ser configurada. 

> [!IMPORTANT]
> Certifique-se de que o sistema operacional selecionado é certificado pelo SAP para SAP HANA nos tipos específicos de VM que você está usando. A lista de tipos de VM certificados SAP HANA e as versões do sistema operacional para aqueles podem ser pesquisadas em [plataformas IaaS SAP Hana certificados](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Certifique-se de clicar nos detalhes do tipo de VM listado para obter a lista completa de SAP HANA versões de sistema operacional com suporte para o tipo de VM específico.  

1. Crie um conjunto de disponibilidade para SAP HANA. Certifique-se de definir o domínio de atualização máximo.  

2. Criar três máquinas virtuais (**hanadb1**, **hanadb2**, **hanadb3**)  
   - Use uma imagem SLES4SAP na galeria do Azure com suporte para SAP HANA. Usamos a imagem do SLES4SAP 12 SP4 neste exemplo.  
   - Selecione conjunto de disponibilidade criado anteriormente para SAP HANA.  
   - Selecione a sub-rede de rede virtual do Azure de armazenamento. Selecione [rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  
Quando você implanta as máquinas virtuais, o nome da interface de rede é gerado automaticamente. Iremos nos referir às interfaces de rede, anexadas à sub-rede de rede virtual do Azure de armazenamento como **hanadb1-Storage**, **hanadb2-** Storage e **hanadb3-Storage**. 
3. Crie três interfaces de rede: uma para cada máquina virtual, para a sub-rede `hana` rede virtual. Neste exemplo: **hanadb1-Hana**, **hanadb2-Hana**e **hanadb3-Hana**.  
4. Crie três interfaces de rede: uma para cada máquina virtual, para a sub-rede de rede virtual do **cliente** . Neste exemplo: **hanadb1-Client**, **hanadb2-Client**e **hanadb3-Client**.  
5. Anexe as interfaces de rede virtual recém-criadas às máquinas virtuais correspondentes  

    1. Navegue até a máquina virtual em [portal do Azure](https://portal.azure.com/#home).  
    2. Selecione máquinas virtuais no painel de navegação à esquerda. Filtre o nome da máquina virtual, por exemplo, **hanadb1**. Clique na máquina virtual.  
    3. Como você está em visão geral, selecione parar para desalocar a máquina virtual.  
    4. Selecione rede, anexar interface de rede. Selecione na lista suspensa em "anexar interface de rede" as interfaces de rede já criadas para as sub-redes **`hana`** e **cliente** .  Guarde. 
    5. Repita para as máquinas virtuais restantes. Em nosso exemplo, **hanadb2** e **hanadb3**.
    6. Deixe as máquinas virtuais no estado parado por enquanto. Em seguida, Habilitaremos a [rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para todas as interfaces de rede recentemente anexadas.  

6. Habilite a rede acelerada para as interfaces de rede adicionais para as sub-redes **`hana`** e **`client`** .  

    1. Abrir o [Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) no [portal do Azure](https://portal.azure.com/#home)  
    2. Execute os comandos a seguir para habilitar a rede acelerada para as interfaces de rede adicionais, anexadas a sub-redes **`hana`** e **`client`** .  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>
7. Iniciar as máquinas virtuais  

    1. Selecione máquinas virtuais no painel de navegação à esquerda. Filtre o nome da máquina virtual, por exemplo, **hanadb1**. Clique na máquina virtual.  
    2. Como você está em visão geral, selecione Iniciar.  

## <a name="operating-system-configuration-and-preparation"></a>Configuração e preparação do sistema operacional

Os itens a seguir são prefixados com **[A]** -aplicável a todos os nós **[1]** -aplicável somente ao nó 1, **[2]** -aplicável somente ao nó 2 ou **[3]** – aplicável somente ao nó 3.

1. **[A]** manter os arquivos de hosts nas máquinas virtuais. Inclua entradas para todas as sub-redes. As entradas a seguir foram adicionadas a `/etc/hosts` para este exemplo.  
    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** alterar as configurações de DHCP e de configuração de nuvem para evitar alterações de nome de host não intencional.  
    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** preparar o sistema operacional para executar o SAP Hana em sistemas NetApp com NFS, conforme descrito em [SAP Hana em sistemas NetApp AFF com guia de configuração do NFS](https://www.netapp.com/us/media/tr-4435.pdf). Criar arquivo de configuração para as definições de configuração do NetApp: `/etc/sysctl.d/netapp-hana.conf`.  

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

4. **[A]** criar um arquivo de configuração com as definições de configuração da Microsoft para o Azure: `/etc/sysctl.d/ms-az.conf`.  

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

4. **[A]** ajuste as configurações de SunRPC conforme recomendado no [SAP Hana em sistemas AFF NetApp com guia de configuração do NFS](https://www.netapp.com/us/media/tr-4435.pdf).  
    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Montar os volumes de Azure NetApp Files

1. **[A]** criar pontos de montagem para os volumes de banco de dados do Hana.  
    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** crie diretórios específicos de nó para `/usr/sap` em **HN1**-Shared.  

    <pre><code>
    # Create a temporary directory to mount  <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** verificar a configuração de domínio do NFS. Verifique se o domínio está configurado como **`localdomain`** e se o mapeamento está definido como **ninguém**.  
    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** desabilitar o mapeamento de ID NFSv4. Execute o comando mount para criar a estrutura de diretório, onde `nfs4_disable_idmapping` está localizado.  Você não poderá criar o diretório manualmente em/sys/modules, pois o acesso é reservado para o kernel/drivers.  

    <pre><code>
    
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>

5. **[A]** crie o grupo de SAP Hana e o usuário manualmente. As IDs para os SAPs de grupo e o usuário **hn1**ADM devem ser definidas com as mesmas IDs, fornecidas durante a integração. Neste exemplo, as IDs são definidas como **1001**. Caso contrário, não será possível acessar os volumes.  As IDs de grupo de SAPs e contas de usuário **hn1**ADM e sapadm devem ser as mesmas em todas as máquinas virtuais.  

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

6. **[A]** montar os volumes de Azure NetApp files compartilhados.  

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

7. **[1]** montar os volumes específicos do nó em **hanadb1**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** montar os volumes específicos do nó em **hanadb2**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** montar os volumes específicos do nó em **hanadb3**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Verifique se todos os volumes do Hana estão montados com o protocolo NFS versão **NFSv4**.  
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

Neste exemplo para implantar SAP HANA na configuração de expansão com o nó em espera com o Azure, usamos o HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Preparar para a instalação do HANA

1. **[A]** defina a senha raiz antes da instalação do Hana (você pode desabilitar a senha raiz depois que a instalação for concluída). `passwd`de comando execute as `root`.  

2. **[1]** Verifique se você pode SSH para **hanadb2** e **hanadb3**, sem ser solicitado a fornecer a senha.  
    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** instale pacotes adicionais, necessários para o Hana 2,0 SP4. Consulte a observação do SAP [2593824](https://launchpad.support.sap.com/#/notes/2593824) para obter detalhes. 
    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2, 3]** Altere a propriedade de SAP HANA `data` e `log` diretórios para **hn1**ADM.   
    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>Instalação do HANA

1. **[1]** instale o SAP Hana, seguindo as instruções no [Guia de instalação e atualização do SAP Hana 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). Neste exemplo, instalamos SAP HANA expansão com Master, um Worker e um nó em espera.  
   Inicie o programa **hdblcm** do diretório de software de instalação do Hana. Use o parâmetro `internal_network` e passe o espaço de endereço para sub-rede, usado para a comunicação interna do HANA entre nós.  

    <pre><code>./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   Insira os valores a seguir no prompt.

     * Escolha uma ação: insira **1** (para instalar)
     * Selecionar componentes adicionais para a instalação: insira **2, 3**
     * Insira o caminho de instalação: pressione Enter (o padrão é/Hana/Shared)
     * Insira o nome do host local: pressione ENTER para aceitar o padrão
     * Deseja adicionar hosts ao sistema? Digite **y**
     * Insira nomes de host separados por vírgula para adicionar: **hanadb2, hanadb3**
     * Insira o nome de usuário raiz [raiz]: pressione ENTER para aceitar o padrão
     * Insira a senha do usuário raiz: Insira a senha da raiz
     * Selecione funções para o host hanadb2: Enter **1** (para o trabalho)
     * Insira o grupo de failover do host para o host hanadb2 [padrão]: pressione ENTER para aceitar o padrão
     * Insira o número da partição de armazenamento para o host hanadb2 [<<assign automatically>>]: pressione ENTER para aceitar o padrão
     * Insira o grupo de trabalho para o host hanadb2 [padrão]: pressione ENTER para aceitar o padrão
     * Selecione funções para o host hanadb3: Enter **2** (para espera)
     * Insira o grupo de failover do host para o host hanadb3 [padrão]: pressione ENTER para aceitar o padrão
     * Insira o grupo de trabalho para o host hanadb3 [padrão]: pressione ENTER para aceitar o padrão
     * Insira SAP HANA ID do sistema: insira **HN1**
     * Insira o número da instância [00]: Enter **03**
     * Insira o grupo de trabalho do host local [padrão]: pressione ENTER para aceitar o padrão
     * Selecione uso do sistema/inserir índice [4]: Enter **4** (para personalizado)
     * Insira o local dos volumes de dados [/hana/data/HN1]: pressione ENTER para aceitar o padrão
     * Insira o local dos volumes de log [/hana/log/HN1]: pressione ENTER para aceitar o padrão
     * Restringir a alocação máxima de memória? [n]: insira **n**
     * Insira o nome do host do certificado para o host hanadb1 [hanadb1]: pressione ENTER para aceitar o padrão
     * Insira o nome do host do certificado para o host hanadb2 [hanadb2]: pressione ENTER para aceitar o padrão
     * Insira o nome do host do certificado para o host hanadb3 [hanadb3]: pressione ENTER para aceitar o padrão
     * Insira a senha do administrador do sistema (hn1adm): Insira a senha
     * Insira a senha do usuário do banco de dados do sistema (sistema): Insira a senha do sistema
     * Confirmar senha do usuário do banco de dados do sistema (sistema): Insira a senha do sistema
     * Reiniciar o sistema após a reinicialização do computador? [n]: insira **n** 
     * Deseja continuar (s/n): validar o resumo e, se tudo estiver correto, insira **y**


2. **[1]** verificar global. ini  

   Exiba global. ini e verifique se a configuração da comunicação interna SAP HANA entre nós está em vigor. Verifique a **comunicação**da seção. Ele deve ter o espaço de endereço para **`hana`** sub-rede e `listeninterface` deve ser definido como `.internal`. Verifique a seção **internal_hostname_resolution**. Ele deve ter os endereços IP para as máquinas virtuais HANA que pertencem à sub-rede **`hana`** .  

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

3. **[1]** adicionar mapeamento de host para garantir que os endereços IP do cliente sejam usados para comunicação do cliente. Adicione a seção `public_host_resolution` e adicione os endereços IP correspondentes da sub-rede do cliente.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** reinicie SAP Hana para ativar as alterações.  
   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Verifique se a interface do cliente usará os endereços IP da sub-rede do **cliente** para comunicação.  
   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Consulte SAP Note [2183363-configuração de SAP Hana rede interna](https://launchpad.support.sap.com/#/notes/2183363) para obter detalhes sobre como verificar a configuração.  

6. Para otimizar SAP HANA para o armazenamento de arquivos subjacente do Azure NetApp, defina os seguintes parâmetros de SAP HANA:

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **em**
   - `async_write_submit_active` **em**
   - `async_write_submit_blocks` **tudo**

   Para obter detalhes, consulte [SAP Hana nos sistemas NetApp AFF com guia de configuração do NFS](https://www.netapp.com/us/media/tr-4435.pdf). 

   A partir dos sistemas SAP HANA 2,0, você pode definir os parâmetros em `global.ini`. Consulte a observação do SAP [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   Para sistemas SAP HANA 1,0, versões até SPS12, esses parâmetros podem ser definidos durante a instalação, conforme descrito em SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. O armazenamento usado pelo Azure NetApp Files tem uma limitação de tamanho de arquivo ou 16 TB. SAP HANA não está ciente implicitamente da limitação de armazenamento e não criará automaticamente um novo arquivo de dados, quando o limite de tamanho de arquivo de 16 TB for atingido. Como SAP HANA tenta aumentar o arquivo além de 16 TB, isso resultará em erros e, eventualmente, falha no servidor de índice. 

   > [!IMPORTANT]
   > Para evitar SAP HANA tentando aumentar os arquivos de dados além do [limite de 16TB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) do armazenamento Sybsystem, defina os parâmetros a seguir em `global.ini`.  
   > -  datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 para obter detalhes, consulte SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Cuidado com o SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Testar failover de SAP HANA 

1. Simular falha de nó em um nó de trabalho SAP HANA  

   Execute os comandos a seguir como **hn1**ADM para capturar o status do ambiente, antes de simular a falha do nó.  

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

   Execute o seguinte comando como raiz no nó de trabalho, que, neste caso, **hanadb2** para simular uma falha de nó.  
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   Monitore o sistema para a conclusão do failover. Quando o failover tiver concluído a captura do status, ele deverá ser semelhante ao status apresentado abaixo.  
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
   > Para evitar atrasos com o failover de SAP HANA, quando um nó experimenta um pane no kernel, defina `kernel.panic` como 20 segundos em **todas as** máquinas virtuais do Hana. A configuração é feita em `/etc/sysctl`. Reinicialize as máquinas virtuais para ativar a alteração. O failover, quando um nó está apresentando um pane no kernel pode levar 10 ou mais minutos, se essa alteração não for executada.  

2. Eliminar o servidor de nomes  
   Execute os comandos a seguir como **hn1**ADM para verificar o status do ambiente, antes do teste:  

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

   Execute os comandos a seguir como **hn1**ADM no nó mestre ativo, que é, neste caso, **hanadb1**.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   O nó em espera **hanadb3** assumirá o nó mestre. Estado do recurso após o teste de failover ser concluído:  

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

   Inicie novamente a instância do HANA em **hanadb1**, ou seja, na mesma máquina virtual, onde o servidor de nomes foi eliminado. O nó **hanadb1** se reassociará ao ambiente e manterá sua função em espera.  
   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Espere o seguinte status, depois que SAP HANA foi iniciado em **hanadb1**:  
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

   Agora, elimine novamente o servidor de nomes no nó mestre ativo no momento, ou seja, em hanadb3.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   O nó **hanadb1** reiniciará a função do nó mestre. O status, após a conclusão do teste de failover, terá a seguinte aparência:
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

   Iniciar SAP HANA em **hanadb3** – ele estará pronto para servir como um nó em espera.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Status após a SAP HANA foi iniciado em **hanadb3**.  
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

* [Planejamento e implementação de máquinas virtuais do Azure para SAP][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP][deployment-guide]
* [Implantação de DBMS de máquinas virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre do SAP 
* HANA no Azure (instâncias grandes), consulte [SAP Hana (instâncias grandes) alta disponibilidade e recuperação de desastre no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA em VMs do Azure, consulte [alta disponibilidade de SAP Hana em VMS (máquinas virtuais) do Azure][sap-hana-ha]
