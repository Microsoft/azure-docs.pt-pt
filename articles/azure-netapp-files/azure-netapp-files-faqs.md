---
title: PERGUNTAS SOBRE Ficheiros Azure NetApp [ Microsoft Docs
description: Respostas frequentemente colocadas perguntas sobre ficheiros Azure NetApp.
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
ms.date: 04/27/2020
ms.author: b-juche
ms.openlocfilehash: a8c299a6f0e6732d50b40fc29bde07179fc2c412
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82185647"
---
# <a name="faqs-about-azure-netapp-files"></a>PERGUNTAS FREQUENTES Sobre ficheiros Azure NetApp

Este artigo responde a perguntas frequentes (PERGUNTAS) sobre ficheiros Azure NetApp. 

## <a name="networking-faqs"></a>Networking FAQs

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>O caminho dos dados da NFS passa pela Internet?  

Não. O caminho de dados da NFS não passa pela Internet. O Azure NetApp Files é um serviço nativo azure que é implantado na Rede Virtual Azure (VNet) onde o serviço está disponível. O Azure NetApp Files utiliza uma subnet delegada e disponibiliza uma interface de rede diretamente no VNet. 

Consulte as Diretrizes para o planeamento da [rede de ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para obter detalhes.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Posso ligar um VNet que já criei ao serviço Azure NetApp Files?

Sim, pode ligar VNets que criou ao serviço. 

Consulte as Diretrizes para o planeamento da [rede de ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para obter detalhes.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Posso montar um volume NFS de Ficheiros Azure NetApp usando o nome DNS FQDN?

Sim, pode, se criar as entradas DNS necessárias. O Azure NetApp Files fornece o IP de serviço para o volume provisionado. 

> [!NOTE] 
> Os Ficheiros Azure NetApp podem implementar iPs adicionais para o serviço, conforme necessário.  As entradas de DNS podem ter de ser atualizadas periodicamente.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Posso definir ou selecionar o meu próprio endereço IP para um volume de Ficheiros Azure NetApp?  

Não. A atribuição de IP aos volumes de Ficheiros Azure NetApp é dinâmica. A atribuição de IP estática não é suportada. 
 
## <a name="security-faqs"></a>Perguntas de Segurança

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>O tráfego de rede entre o Azure VM e o armazenamento pode ser encriptado?

O tráfego de dados (tráfego dos volumes NFSv3, NFSv4.1 ou SMBv3 para os volumes de Ficheiros Azure NetApp) não está encriptado. No entanto, o tráfego de um Azure VM (que gere um cliente NFS ou SMB) para o Azure NetApp Files é tão seguro como qualquer outro tráfego Azure-VM-to-VM. Este tráfego é local para a rede de data-center Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>O armazenamento pode ser encriptado em repouso?

Todos os volumes de Ficheiros Azure NetApp são encriptados utilizando a norma FIPS 140-2. Todas as chaves são geridas pelo serviço Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Como são geridas as chaves de encriptação? 

A gestão chave dos Ficheiros Azure NetApp é tratada pelo serviço. Uma chave única de encriptação de dados XTS-AES-256 é gerada para cada volume. Uma hierarquia de chave de encriptação é usada para encriptar e proteger todas as teclas de volume. Estas chaves de encriptação nunca são apresentadas ou reportadas num formato não encriptado. As teclas de encriptação são eliminadas imediatamente quando um volume é eliminado.

O suporte para chaves geridas pelo utilizador (Bring Your Own Keys) utilizando o Azure Dedicated HSM está disponível numa base controlada nas regiões do Leste dos EUA, US West2 e Us South Central.  Pode solicitar acesso **anffeedback@microsoft.com**a . Como a capacidade está disponível, os pedidos serão aprovados.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Posso configurar as regras de política de exportação da NFS para controlar o acesso ao alvo de montagem do serviço Azure NetApp Files?


Sim, pode configurar até cinco regras numa única política de exportação da NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Os Ficheiros Azure NetApp suportam grupos de segurança da rede?

Não, atualmente não é possível aplicar Grupos de Segurança de Rede à subnet delegada dos Ficheiros Azure NetApp ou às interfaces de rede criadas pelo serviço.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Posso usar o Azure IAM com ficheiros Azure NetApp?

Sim, o Azure NetApp Files suporta funcionalidades RBAC com o Azure IAM.

## <a name="performance-faqs"></a>Perguntas-Gerais de Desempenho

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>O que devo fazer para otimizar ou afinar o desempenho dos Ficheiros Azure NetApp?

Pode tomar as seguintes ações de acordo com os requisitos de desempenho: 
- Certifique-se de que a Máquina Virtual é dimensionada adequadamente.
- Ativar a rede acelerada para o VM.
- Selecione o nível de serviço desejado e o tamanho para a piscina de capacidade.
- Crie um volume com o tamanho de quota desejado para a capacidade e desempenho.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Como converter os níveis de serviço baseados em recursos de entrada de Ficheiros Azure NetApp para IOPS?

Pode converter MB/s em IOPS utilizando a seguinte fórmula:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Como posso alterar o nível de serviço de um volume?

A alteração do nível de serviço de um volume não é atualmente suportada.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Como posso monitorizar o desempenho do Azure NetApp Files?

Os Ficheiros Azure NetApp fornecem métricas de desempenho de volume. Também pode utilizar o Monitor Azure para monitorizar as métricas de utilização dos Ficheiros Azure NetApp.  Consulte [as Métricas dos Ficheiros Azure NetApp](azure-netapp-files-metrics.md) para obter a lista de métricas de desempenho para ficheiros Azure NetApp.

## <a name="nfs-faqs"></a>Perguntas frequentes nfs

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Quero ter um volume montado automaticamente quando um VM Azure for iniciado ou reiniciado.  Como configurar o meu anfitrião para volumes persistentes de NFS?

Para que um volume NFS monte automaticamente em VM iniciar `/etc/fstab` ou reiniciar, adicione uma entrada no ficheiro no hospedeiro. 

Consulte [o Mount ou desmonte um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) para obter mais detalhes.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Porque é que o comando DF sobre o cliente nFS não mostra o tamanho do volume provisionado?

O tamanho do volume reportado em DF é o tamanho máximo que o volume de Ficheiros Azure NetApp pode crescer. O tamanho do volume de Ficheiros Azure NetApp no comando DF não reflete a quota ou o tamanho do volume.  Pode obter o volume de volume ou quota dos Ficheiros Azure NetApp através do portal Azure ou da API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Que versão NFS suporta o Azure NetApp Files?

O Azure NetApp Files suporta nFSv3 e NFSv4.1. Pode [criar um volume](azure-netapp-files-create-volumes.md) utilizando qualquer versão NFS. 

### <a name="how-do-i-enable-root-squashing"></a>Como posso permitir o esmagamento de raízes?

Atualmente, a esmagamento de raízes não é suportada.

## <a name="smb-faqs"></a>Perguntas-Gerais SMB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>É necessária uma ligação de Diretório Ativo para acesso sMB? 

Sim, deve criar uma ligação de Diretório Ativo antes de implementar um volume SMB. Os controladores de domínio especificados devem ser acessíveis pela sub-rede delegada dos Ficheiros Azure NetApp para uma ligação bem sucedida.  Consulte [Criar um volume SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) para mais detalhes. 

### <a name="how-many-active-directory-connections-are-supported"></a>Quantas ligações de Diretório Ativo são suportadas?

O Azure NetApp Files não suporta várias ligações de Diretório Ativo (AD) numa única *região*– mesmo que as ligações AD estejam em diferentes contas netApp. No entanto, pode ter múltiplas ligações aD numa única *subscrição,* desde que as ligações AD estejam em diferentes regiões. Se precisar de múltiplas ligações aD numa única região, pode utilizar subscrições separadas para o fazer. 

Uma ligação AD é configurada por conta NetApp; a ligação AD só é visível através da conta NetApp em que é criada.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Os Ficheiros Azure NetApp suportam o Diretório Ativo Azure? 

Ambos os Serviços de [Domínio do Diretório Ativo Azure (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/overview) e os Serviços de Domínio de [Diretório Ativo (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) são apoiados. Pode utilizar controladores de domínio Ative Diretório existentes com Ficheiros Azure NetApp. Os controladores de domínio podem residir em Azure como máquinas virtuais, ou nas instalações via ExpressRoute ou S2S VPN. Os Ficheiros Azure NetApp não suportam a adesão da AD ao [Azure Ative Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) neste momento.

Se estiver a utilizar ficheiros Azure NetApp com serviços de `OU=AADDC Computers` domínio de diretório ativo Azure, o percurso da unidade organizacional é quando configura o Diretório Ativo para a sua conta NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Que versões do Diretório Ativo do Windows Server são suportadas?

O Azure NetApp Files suporta as versões Windows Server 2008r2SP1-2019 dos Serviços de Domínio do Diretório Ativo.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Porque é que o espaço disponível no meu cliente SMB não mostra o tamanho previsto?

O volume reportado pelo cliente SMB é o tamanho máximo que o volume de Ficheiros Azure NetApp pode crescer. O tamanho do volume de Ficheiros Azure NetApp, tal como mostrado no cliente SMB, não reflete a quota ou o tamanho do volume. Pode obter o volume de volume ou quota dos Ficheiros Azure NetApp através do portal Azure ou da API.

<!--
### Does Azure NetApp Files support Kerberos encryption?

Yes, by default, Azure NetApp Files supports both AES-128 and AES-256 encryption for traffic between the service and the targeted Active Directory domain controllers. See [Create an SMB volume for Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) for requirements. 
-->

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>FaQs de gestão de capacidade

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Como posso monitorizar a utilização para o pool de capacidade e volume de Ficheiros Azure NetApp? 

Os Ficheiros Azure NetApp fornecem métricas de utilização de piscina e volume. Também pode utilizar o Monitor Azure para monitorizar a utilização de Ficheiros Azure NetApp. Consulte [as Métricas para Ficheiros Azure NetApp](azure-netapp-files-metrics.md) para obter mais detalhes. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Posso gerir ficheiros Azure NetApp através do Azure Storage Explorer?

Não. Os Ficheiros Azure NetApp não são suportados pelo Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Como determino se um diretório se aproxima do tamanho limite?

Pode utilizar `stat` o comando de um cliente para ver se um diretório se aproxima do limite máximo de tamanho (320 MB).

Para um diretório de 320 MB, o número de blocos é de 655360, sendo cada tamanho de bloco 512 bytes.  (Isto é, 320x1024x1024/512.)  

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

## <a name="data-migration-and-protection-faqs"></a>FaQs de migração e proteção de dados

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Como migrar dados para ficheiros Azure NetApp?
Os Ficheiros Azure NetApp fornecem volumes NFS e SMB.  Pode utilizar qualquer ferramenta de cópia baseada em ficheiros para migrar dados para o serviço. 

O NetApp oferece uma solução baseada no SaaS, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  A solução permite-lhe replicar dados de NFS ou SMB para as exportações nFS do Azure NetApp ou ações SMB. 

Também pode utilizar um vasto leque de ferramentas gratuitas para copiar dados. Para o NFS, pode utilizar ferramentas de carga de trabalho como [rsync](https://rsync.samba.org/examples.html) para copiar e sincronizar dados de origem num volume de Ficheiros Azure NetApp. Para sMB, pode utilizar [a robocópia](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) da mesma forma.  Estas ferramentas também podem replicar permissões de ficheiros ou pastas. 

Os requisitos para a migração de dados de instalações para Ficheiros Azure NetApp são os seguintes: 

- Certifique-se de que o Azure NetApp Files está disponível na região-alvo do Azure.
- Valide a conectividade da rede entre a fonte e o endereço IP de volume de volume alvo do Volume Azure NetApp. A transferência de dados entre as instalações e o serviço Azure NetApp Files é suportada no ExpressRoute.
- Crie o volume de ficheiros Azure NetApp.
- Transfira os dados de origem para o volume de destino utilizando a sua ferramenta de cópia de ficheiro seleções preferida.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Como posso criar uma cópia de um volume de Ficheiros Azure NetApp noutra região do Azure?
    
Os Ficheiros Azure NetApp fornecem volumes NFS e SMB.  Qualquer ferramenta de cópia baseada em ficheiros pode ser usada para replicar dados entre regiões do Azure. 

O NetApp oferece uma solução baseada no SaaS, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  A solução permite-lhe replicar dados de NFS ou SMB para as exportações nFS do Azure NetApp ou ações SMB. 

Também pode utilizar um vasto leque de ferramentas gratuitas para copiar dados. Para o NFS, pode utilizar ferramentas de carga de trabalho como [rsync](https://rsync.samba.org/examples.html) para copiar e sincronizar dados de origem num volume de Ficheiros Azure NetApp. Para sMB, pode utilizar [a robocópia](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) da mesma forma.  Estas ferramentas também podem replicar permissões de ficheiros ou pastas. 

Os requisitos para replicar um volume de Ficheiros Azure NetApp para outra região do Azure são os seguintes: 
- Certifique-se de que o Azure NetApp Files está disponível na região-alvo do Azure.
- Validar a conectividade da rede entre VNets em cada região. Atualmente, o peering global entre VNets não é apoiado.  Pode estabelecer conectividade entre VNets ligando-se a um circuito ExpressRoute ou utilizando uma ligação VPN S2S. 
- Crie o volume de ficheiros Azure NetApp.
- Transfira os dados de origem para o volume de destino utilizando a sua ferramenta de cópia de ficheiro seleções preferida.

### <a name="is-migration-with-azure-data-box-supported"></a>A migração com a Caixa de Dados Azure é suportada?

Não. A Tualmente, a Azure Data Box não suporta ficheiros Azure NetApp. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>A migração com o serviço azure import/exportação é suportada?

Não. O serviço Azure Import/Export não suporta atualmente ficheiros Azure NetApp.

## <a name="next-steps"></a>Passos seguintes  

- [Microsoft Azure ExpressRoute FAQs](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [FaQ da rede virtual Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Como criar um pedido de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [FAQs sobre desempenho sMB para Ficheiros Azure NetApp](azure-netapp-files-smb-performance.md)
