---
title: PERGUNTAS Frequentes Sobre Ficheiros Azure NetApp / Microsoft Docs
description: Respostas frequentemente feitas perguntas sobre ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: b-juche
ms.openlocfilehash: 8e9ee573ca908c41fe7c7887d0f306971369f83f
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417502"
---
# <a name="faqs-about-azure-netapp-files"></a>PERGUNTAS Frequentes Sobre Ficheiros Azure NetApp

Este artigo responde a perguntas frequentes (FAQs) sobre ficheiros Azure NetApp. 

## <a name="networking-faqs"></a>Perguntas frequentes em rede

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>O caminho dos dados da NFS passa pela Internet?  

Não. O caminho dos dados da NFS não passa pela Internet. O Azure NetApp Files é um serviço nativo do Azure que está implantado na Rede Virtual Azure (VNet) onde o serviço está disponível. O Azure NetApp Files utiliza uma sub-rede delegada e fornece uma interface de rede diretamente no VNet. 

Consulte as Diretrizes para o planeamento da [rede Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para obter mais detalhes.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Posso ligar um VNet que já criei ao serviço Azure NetApp Files?

Sim, pode ligar VNets que criou ao serviço. 

Consulte as Diretrizes para o planeamento da [rede Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para obter mais detalhes.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Posso montar um volume NFS de Ficheiros Azure NetApp usando o nome DNS FQDN?

Sim, pode, se criar as entradas de DNS necessárias. A Azure NetApp Files fornece o IP de serviço para o volume aprovisionado. 

> [!NOTE] 
> Os Ficheiros Azure NetApp podem implementar IPs adicionais para o serviço, conforme necessário.  As entradas de DNS podem ter de ser atualizadas periodicamente.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Posso definir ou selecionar o meu próprio endereço IP para um volume de Ficheiros Azure NetApp?  

Não. A atribuição de IP aos volumes de Ficheiros Azure NetApp é dinâmica. A atribuição ip estática não é suportada. 
 
## <a name="security-faqs"></a>Perguntas de Segurança

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>O tráfego de rede entre o Azure VM e o armazenamento pode ser encriptado?

O tráfego de dados (tráfego dos volumes NFSv3, NFSv4.1 ou SMBv3 para Azure NetApp Files) não está encriptado. No entanto, o tráfego de um Azure VM (executando um cliente NFS ou SMB) para Azure NetApp Files é tão seguro como qualquer outro tráfego Azure-VM-para-VM. Este tráfego é local para a rede de centros de dados Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>O armazenamento pode ser encriptado em repouso?

Todos os volumes dos Ficheiros Azure NetApp são encriptados utilizando a norma FIPS 140-2. Todas as chaves são geridas pelo serviço Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Como são geridas as chaves de encriptação? 

A gestão chave para ficheiros Azure NetApp é tratada pelo serviço. Uma chave de encriptação de dados XTS-AES-256 única é gerada para cada volume. Uma hierarquia de chave de encriptação é usada para encriptar e proteger todas as chaves de volume. Estas teclas de encriptação nunca são apresentadas ou reportadas num formato não encriptado. As teclas de encriptação são eliminadas imediatamente quando um volume é eliminado.

O suporte para chaves geridas pelo utilizador (Bring Your Own Keys) utilizando o Azure Dedicated HSM está disponível numa base controlada nas regiões norte-americanas do Leste, EUA West2 e Centro Sul dos EUA.  Pode solicitar acesso em **anffeedback@microsoft.com** . À medida que a capacidade está disponível, os pedidos serão aprovados.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Posso configurar as regras de política de exportação da NFS para controlar o acesso ao alvo de montagem de serviçoS Azure NetApp?


Sim, pode configurar até cinco regras numa única política de exportação da NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Os ficheiros Azure NetApp suportam grupos de segurança de rede?

Não, atualmente não é possível aplicar Grupos de Segurança de Rede à sub-rede delegada dos Ficheiros Azure NetApp ou às interfaces de rede criadas pelo serviço.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Posso utilizar o Azure IAM com ficheiros Azure NetApp?

Sim, o Azure NetApp Files suporta funcionalidades DO RBAC com O Azure IAM.

## <a name="performance-faqs"></a>FAQs de Desempenho

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>O que devo fazer para otimizar ou sintonizar o desempenho do Azure NetApp Files?

Pode tomar as seguintes ações de acordo com os requisitos de desempenho: 
- Certifique-se de que a Máquina Virtual é dimensionada adequadamente.
- Ativar a rede acelerada para o VM.
- Selecione o nível de serviço e o tamanho desejados para o pool de capacidade.
- Criar um volume com o tamanho da quota desejada para a capacidade e desempenho.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Como posso converter níveis de serviço baseados em produção de ficheiros Azure NetApp para IOPS?

Pode converter MB/s em IOPS utilizando a seguinte fórmula:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Como posso alterar o nível de serviço de um volume?

A alteração do nível de serviço de um volume não é suportada atualmente.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Como monitorizo o desempenho dos Ficheiros Azure NetApp?

O Azure NetApp Files fornece métricas de desempenho de volume. Também pode utilizar o Azure Monitor para monitorizar as métricas de utilização dos ficheiros Azure NetApp.  Consulte [métricas para ficheiros Azure NetApp](azure-netapp-files-metrics.md) para a lista de métricas de desempenho dos ficheiros Azure NetApp.

## <a name="nfs-faqs"></a>FAQ sobre NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Quero ter um volume montado automaticamente quando um VM Azure for iniciado ou reiniciado.  Como posso configurar o meu anfitrião para volumes persistentes de NFS?

Para que um volume NFS possa ser montado automaticamente no início ou reinicialização do VM, adicione uma entrada `/etc/fstab` no ficheiro no anfitrião. 

Consulte [o Mount ou desmonte um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) para obter mais detalhes.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Porque é que o comando df do cliente NFS não mostra o tamanho do volume a provisionado?

O tamanho do volume reportado em DF é o tamanho máximo que o volume de Ficheiros Azure NetApp pode crescer. O tamanho do volume dos Ficheiros Azure NetApp no comando DF não reflete a quota ou o tamanho do volume.  Pode obter o tamanho ou quota de volume dos Ficheiros Azure NetApp através do portal Azure ou da API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Que versão NFS suporta o Azure NetApp Files?

O Azure NetApp Files suporta NFSv3 e NFSv4.1. Pode [criar um volume](azure-netapp-files-create-volumes.md) utilizando a versão NFS. 

### <a name="how-do-i-enable-root-squashing"></a>Como posso permitir o esmagamento de raízes?

Atualmente, o esmagamento de raízes não é suportado.

## <a name="smb-faqs"></a>FAQs sobre o SMB

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Quais as versões SMB suportadas por Ficheiros Azure NetApp?

O Azure NetApp Files suporta SMB 2.1 e SMB 3.1 (que inclui suporte para SMB 3.0).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>É necessária uma ligação ative directory para acesso SMB? 

Sim, tem de criar uma ligação Ative Directory antes de implantar um volume SMB. Os controladores de domínio especificados devem estar acessíveis pela sub-rede delegada dos Ficheiros Azure NetApp para uma ligação bem sucedida.  Consulte [Criar um volume SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) para mais detalhes. 

### <a name="how-many-active-directory-connections-are-supported"></a>Quantas ligações ative directy são suportadas?

O Azure NetApp Files não suporta várias ligações ative directory (AD) numa única *região*, mesmo que as ligações AD estejam em diferentes contas NetApp. No entanto, pode ter várias ligações AD numa única *subscrição,* desde que as ligações AD se encontrem em diferentes regiões. Se precisar de várias ligações AD numa única região, pode utilizar subscrições separadas para o fazer. 

Uma ligação AD é configurada por conta NetApp; a ligação AD só é visível através da conta NetApp em que é criada.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Os ficheiros Azure NetApp suportam o Azure Ative Directory? 

Tanto os [Serviços de Domínio Azure Ative (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/overview) como [os Serviços de Domínio do Diretório Ativo (DS AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) são suportados. Pode utilizar controladores de domínio ative diretório existentes com ficheiros Azure NetApp. Os controladores de domínio podem residir em Azure como máquinas virtuais, ou nas instalações via ExpressRoute ou S2S VPN. O Azure NetApp Files não suporta a participação da AD no [Azure Ative Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) neste momento.

Se estiver a utilizar ficheiros Azure NetApp com Serviços de Domínio do Diretório Ativo Azure, o percurso da unidade organizacional é `OU=AADDC Computers` quando configura o Ative Directory para a sua conta NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Quais as versões do Windows Server Ative Directory suportadas?

O Azure NetApp Files suporta versões windows Server 2008r2SP1-2019 de Serviços de Domínio de Diretório Ativo.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Porque é que o espaço disponível no meu cliente SMB não mostra o tamanho a provisionado?

O tamanho do volume reportado pelo cliente SMB é o tamanho máximo que o volume de Ficheiros Azure NetApp pode crescer. O tamanho do volume dos Ficheiros Azure NetApp, tal como mostrado no cliente SMB, não reflete a quota ou o tamanho do volume. Pode obter o tamanho ou quota de volume dos Ficheiros Azure NetApp através do portal Azure ou da API.

<!--
### Does Azure NetApp Files support Kerberos encryption?

Yes, by default, Azure NetApp Files supports both AES-128 and AES-256 encryption for traffic between the service and the targeted Active Directory domain controllers. See [Create an SMB volume for Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) for requirements. 
-->

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Perguntas frequentes de gestão da capacidade

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Como monitorar a utilização para o pool de capacidade e volume de Ficheiros Azure NetApp? 

O Azure NetApp Files fornece métricas de utilização de pool e volume de capacidade. Também pode utilizar o Azure Monitor para monitorizar a utilização de ficheiros Azure NetApp. Consulte [as métricas dos ficheiros Azure NetApp](azure-netapp-files-metrics.md) para obter mais detalhes. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Posso gerir ficheiros Azure NetApp através do Azure Storage Explorer?

Não. Os ficheiros Azure NetApp não são suportados pelo Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Como posso determinar se um diretório se aproxima do tamanho do limite?

Pode utilizar o `stat` comando de um cliente para ver se um diretório se aproxima do limite máximo de tamanho (320 MB).

Para um diretório de 320 MB, o número de blocos é de 655360, com cada tamanho de bloco a ser de 512 bytes.  (Isto é, 320x1024x1024/512.)  

Exemplos:

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>Perguntas frequentes de migração e proteção de dados

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Como posso migrar dados para ficheiros Azure NetApp?
O Azure NetApp Files fornece volumes NFS e SMB.  Pode utilizar qualquer ferramenta de cópia baseada em ficheiros para migrar dados para o serviço. 

O NetApp oferece uma solução baseada em SaaS, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  A solução permite-lhe replicar dados NFS ou SMB para exportações de Ficheiros NFS ou SMB. 

Também pode utilizar uma grande variedade de ferramentas gratuitas para copiar dados. Para o NFS, pode utilizar ferramentas de carga de trabalho como [o rsync](https://rsync.samba.org/examples.html) para copiar e sincronizar dados de origem num volume de Ficheiros Azure NetApp. Para a SMB, pode utilizar [a robocopia](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) de carga de trabalho da mesma forma.  Estas ferramentas também podem replicar permissões de ficheiros ou pastas. 

Os requisitos para a migração de dados das instalações para os Ficheiros Azure NetApp são os seguintes: 

- Certifique-se de que os Ficheiros Azure NetApp estão disponíveis na região alvo do Azure.
- Validar a conectividade da rede entre a fonte e o endereço IP do volume de destino dos Ficheiros Azure NetApp. A transferência de dados entre as instalações e o serviço Azure NetApp Files é suportada pelo ExpressRoute.
- Crie o volume de ficheiros Azure NetApp alvo.
- Transfira os dados de origem para o volume-alvo utilizando a sua ferramenta de cópia de ficheiro preferida.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Como posso criar uma cópia de um volume de Ficheiros Azure NetApp noutra região do Azure?
    
O Azure NetApp Files fornece volumes NFS e SMB.  Qualquer ferramenta de cópia baseada em ficheiros pode ser usada para replicar dados entre as regiões de Azure. 

O NetApp oferece uma solução baseada em SaaS, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  A solução permite-lhe replicar dados NFS ou SMB para exportações de Ficheiros NFS ou SMB. 

Também pode utilizar uma grande variedade de ferramentas gratuitas para copiar dados. Para o NFS, pode utilizar ferramentas de carga de trabalho como [o rsync](https://rsync.samba.org/examples.html) para copiar e sincronizar dados de origem num volume de Ficheiros Azure NetApp. Para a SMB, pode utilizar [a robocopia](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) de carga de trabalho da mesma forma.  Estas ferramentas também podem replicar permissões de ficheiros ou pastas. 

Os requisitos para replicar um volume de Ficheiros Azure NetApp para outra região do Azure são os seguintes: 
- Certifique-se de que os Ficheiros Azure NetApp estão disponíveis na região alvo do Azure.
- Validar a conectividade da rede entre VNets em cada região. Atualmente, o espreitamento global entre VNets não é suportado.  Pode estabelecer conectividade entre VNets ligando-se a um circuito ExpressRoute ou utilizando uma ligação S2S VPN. 
- Crie o volume de ficheiros Azure NetApp alvo.
- Transfira os dados de origem para o volume-alvo utilizando a sua ferramenta de cópia de ficheiro preferida.

### <a name="is-migration-with-azure-data-box-supported"></a>A migração com a Caixa de Dados Azure está suportada?

Não. A Azure Data Box não suporta ficheiros Azure NetApp atualmente. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>A migração com o serviço Azure Import/Export é suportada?

Não. O serviço Azure Import/Export não suporta atualmente ficheiros Azure NetApp.

## <a name="next-steps"></a>Passos seguintes  

- [Microsoft Azure ExpressRoute FAQs](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Microsoft Azure Virtual Network FAQ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Como criar um pedido de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [PERGUNTAS Frequentes sobre desempenho SMB para ficheiros Azure NetApp](azure-netapp-files-smb-performance.md)
