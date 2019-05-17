---
title: FAQs sobre ficheiros NetApp do Azure | Documentos da Microsoft
description: Respostas a perguntas mais frequentes sobre os ficheiros do Azure NetApp.
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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 6f1ca3398678b59a81e5c22b51b36a1f5505d4c2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806390"
---
# <a name="faqs-about-azure-netapp-files"></a>Perguntas frequentes sobre os ficheiros do Azure NetApp

Este artigo responde às perguntas mais frequentes (FAQ) sobre os ficheiros do Azure NetApp. 

## <a name="networking-faqs"></a>FAQs do funcionamento em rede

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>O caminho de dados NFS é transmitido através da Internet?  

Não. O caminho de dados NFS não entram na Internet. Os ficheiros NetApp do Azure é um serviço do Azure nativo que é implementado numa rede Virtual do Azure (VNet) em que o serviço está disponível. Os ficheiros NetApp do Azure utiliza uma sub-rede de delegado e aprovisiona uma interface de rede diretamente na VNet. 

Ver [planeamento de rede de diretrizes para os ficheiros do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para obter detalhes.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Pode ligar uma VNet que já criei para o serviço de ficheiros de NetApp do Azure?

Sim, pode ligar VNets que criou para o serviço. 

Ver [planeamento de rede de diretrizes para os ficheiros do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para obter detalhes.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Pode montar um volume NFS dos ficheiros de NetApp do Azure com o nome FQDN de DNS?

Sim, pode, se criar as entradas de DNS necessárias. Os ficheiros NetApp do Azure fornece o IP do serviço do volume aprovisionado. 

> [!NOTE] 
> Os ficheiros do Azure NetApp pode implementar IPs adicionais para o serviço, conforme necessário.  As entradas de DNS poderão ter de ser atualizadas periodicamente.

## <a name="security-faqs"></a>FAQs sobre a segurança

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>O tráfego de rede entre a VM do Azure e o armazenamento pode ser encriptado?

Tráfego de dados (tráfego do cliente NFSv3 ou SMBv3 para volumes de ficheiros do Azure NetApp) não está encriptado. No entanto, o tráfego de uma VM do Azure (em execução de um cliente NFS ou SMB) para ficheiros de NetApp do Azure é tão seguro quanto qualquer outro tráfego do Azure-VM para VM. Este tráfego seja local para a rede de centro de dados do Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>O armazenamento pode ser encriptado em inatividade?

Todos os volumes de ficheiros de NetApp do Azure são encriptados com o FIPS 140-2 padrão. Todas as chaves são geridas pelo serviço de ficheiros do Azure NetApp. 

### <a name="how-are-encryption-keys-managed"></a>Como são geridas as chaves de encriptação? 

Gestão de chaves para ficheiros do Azure NetApp é processada pelo serviço.  Atualmente, não são suportadas chaves gerida pelo utilizador (Bring Your Keys própria).

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Pode configurar as regras de política de exportação NFS para controlar o acesso para o destino de montagem do serviço de ficheiros de NetApp do Azure?


Sim, pode configurar até cinco regras numa única diretiva de exportação NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>O serviço ficheiros do Azure NetApp oferece suporte a grupos de segurança de rede?

Não, atualmente não pode aplicar grupos de segurança de rede para a sub-rede do delegado de ficheiros do Azure NetApp ou as interfaces de rede criadas pelo serviço.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Posso utilizar o Azure IAM com ficheiros de NetApp do Azure?

Sim, o serviço ficheiros do Azure NetApp suporta funcionalidades RBAC com IAM do Azure.

## <a name="performance-faqs"></a>FAQs de desempenho

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>O que devo fazer para otimizar ou otimizar o desempenho de ficheiros de NetApp do Azure?

Pode efetuar as seguintes ações pelos requisitos de desempenho: 
- Certifique-se de que a Máquina Virtual tem o tamanho adequado.
- Ative redes aceleradas para a VM.
- Selecione o nível de serviço desejado e o tamanho para o conjunto de capacidade.
- Crie um volume com o tamanho de quota pretendida para a capacidade e desempenho.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Como converter níveis de serviço com base na taxa de transferência de ficheiros do Azure NetApp IOPS?

Pode converter MB/s em IOPS, utilizando a seguinte fórmula:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Como posso alterar o nível de serviço de um volume?

Atualmente, o alterar o nível de serviço de um volume não é suportado.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Como posso monitorizar o desempenho de ficheiros de NetApp do Azure?

Os ficheiros NetApp do Azure fornece as métricas de desempenho do volume. Também pode utilizar o Azure Monitor para monitorizar as métricas de utilização para os ficheiros do Azure NetApp.  Ver [métricas para os ficheiros do Azure NetApp](azure-netapp-files-metrics.md) para a lista de métricas de desempenho para os ficheiros do Azure NetApp.

## <a name="nfs-faqs"></a>FAQs NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Quero ter um volume montado automaticamente quando uma VM do Azure é iniciada ou reiniciada.  Como posso configurar meu host para volumes NFS persistentes?

Para um volume NFS montar automaticamente ao início da VM ou um reinício, adicione uma entrada para o `/etc/fstab` ficheiro do anfitrião. 

Por exemplo: `$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    O endereço IP do volume de ficheiros do Azure NetApp encontrado no painel de propriedades de volume
- $FILEPATH  
    O caminho de exportação do volume de ficheiros do Azure NetApp
- $MOUNTPOINT  
    O diretório criado no anfitrião Linux usado para montar a exportação NFS

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Por que o comando DF no cliente NFS não mostrar o tamanho do volume aprovisionado?

O tamanho do volume comunicado no DF é o tamanho máximo que o volume de ficheiros do Azure NetApp pode atingir. O tamanho do volume de ficheiros do Azure NetApp no comando DF není refletem a quota ou o tamanho do volume.  Pode obter o tamanho do volume de ficheiros do Azure NetApp ou quota através do portal do Azure ou a API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Qual versão NFS suporta ficheiros de NetApp do Azure?

Atualmente, os ficheiros NetApp do Azure suporta NFSv3.

### <a name="how-do-i-enable-root-squashing"></a>Como ativar a raiz eliminando?

Raiz eliminando não é atualmente suportada.

## <a name="smb-faqs"></a>FAQs SMB

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Ficheiros de NetApp do Azure suporta o Azure Active Directory?

Não, atualmente não é suportada.  Active Directory Domain Services (traga seu próprio AD), que pode utilizar os controladores de domínio do Active Directory existentes com o Azure NetApp ficheiros de suporte de NetApp os ficheiros do Azure. Controladores de domínio podem residir no Azure como máquinas virtuais ou no local através do ExpressRoute.

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>É uma ligação de Active Directory necessária para acesso SMB? 

Sim, tem de criar uma ligação do Active Directory antes de implementar um volume SMB. Os controladores de domínio especificado deve ser acessíveis para a sub-rede do delegado de ficheiros de NetApp do Azure para uma ligação com êxito.  Ver [criar um volume SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes#create-an-smb-volume) para obter detalhes. 

### <a name="how-many-active-directory-connections-are-supported"></a>Quantas ligações do Active Directory são suportadas?

Os ficheiros NetApp do Azure suporta atualmente uma ligação de Active Directory por subscrição. Além disso, a ligação do Active Directory é específica para uma única conta de NetApp; Não é partilhado entre contas. 

### <a name="what-versions-of-windows-ad-are-supported"></a>Quais as versões do AD do Windows são suportadas?

Os ficheiros NetApp do Azure suporta a versão do Windows Server 2008r2SP1 2016 dos serviços de domínio do Active Directory.

## <a name="capacity-management-faqs"></a>FAQs sobre a gestão de capacidade

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Como posso monitorizar utilização para o conjunto de capacidade e o volume de ficheiros de NetApp do Azure? 

Os ficheiros do Azure NetApp fornece métricas de utilização de agrupamento e o volume de capacidade. Também pode utilizar o Azure Monitor para monitorizar a utilização de ficheiros do Azure NetApp. Ver [métricas para os ficheiros do Azure NetApp](azure-netapp-files-metrics.md) para obter detalhes. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Pode gerir ficheiros de NetApp do Azure através do Explorador de armazenamento do Azure?

Não. Os ficheiros NetApp do Azure não é suportado pelo Explorador de armazenamento do Azure.

## <a name="data-migration-and-protection-faqs"></a>Perguntas freqüentes de migração e a proteção de dados

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Como posso migrar dados para ficheiros de NetApp do Azure?
Os ficheiros do Azure NetApp fornece volumes SMB e NFS.  Pode utilizar qualquer ferramenta de cópia baseada em ficheiros para migrar dados para o serviço. 

NetApp oferece uma solução baseada em SaaS [NetApp da sincronização da Cloud](https://cloud.netapp.com/cloud-sync-service).  A solução permite-lhe replicar NFS ou partilham de dados SMB, NFS de ficheiros do Azure NetApp exportações ou SMB. 

Também pode utilizar um vasto leque de ferramentas gratuitas para copiar dados. Para NFS, pode utilizar ferramentas de cargas de trabalho como [rsync](https://rsync.samba.org/examples.html) para copiar e sincronizar dados de origem para um volume de ficheiros do Azure NetApp. Para SMB, pode utilizar as cargas de trabalho [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) da mesma forma.  Essas ferramentas também podem replicar as permissões de ficheiro ou pasta. 

Os requisitos para migração de dados no local para o Azure NetApp ficheiros são os seguintes: 

- Certifique-se de que os ficheiros de NetApp do Azure está disponível na região do Azure de destino.
- Valide a conectividade de rede entre a origem e o endereço IP do volume de destino NetApp serviço ficheiros do Azure. Transferência de dados entre no local e o serviço de ficheiros do Azure NetApp é suportada através do ExpressRoute.
- Crie o volume de ficheiros de NetApp do Azure de destino.
- Transferir os dados de origem para o volume de destino utilizando a ferramenta de cópia de arquivo preferencial.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Como posso criar uma cópia de um volume de ficheiros do Azure NetApp noutra região do Azure?
    
Os ficheiros do Azure NetApp fornece volumes SMB e NFS.  Qualquer ferramenta de cópia com base em arquivo pode ser utilizada para replicar dados entre regiões do Azure. 

NetApp oferece uma solução de SaaS, com base [NetApp da sincronização da Cloud](https://cloud.netapp.com/cloud-sync-service).  A solução permite-lhe replicar NFS ou partilham de dados SMB, NFS de ficheiros do Azure NetApp exportações ou SMB. 

Também pode utilizar um vasto leque de ferramentas gratuitas para copiar dados. Para NFS, pode utilizar ferramentas de cargas de trabalho como [rsync](https://rsync.samba.org/examples.html) para copiar e sincronizar dados de origem para um volume de ficheiros do Azure NetApp. Para SMB, pode utilizar as cargas de trabalho [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) da mesma forma.  Essas ferramentas também podem replicar as permissões de ficheiro ou pasta. 

Os requisitos para replicar um volume de ficheiros de NetApp do Azure para outra região do Azure são da seguinte forma: 
- Certifique-se de que os ficheiros de NetApp do Azure está disponível na região do Azure de destino.
- Valide a conectividade de rede entre VNets em cada região. Atualmente, a global peering entre VNets não é suportado.  Pode estabelecer conectividade entre VNets ao ligar-se com um circuito do ExpressRoute ou utilizar uma ligação S2S VPN. 
- Crie o volume de ficheiros de NetApp do Azure de destino.
- Transferir os dados de origem para o volume de destino utilizando a ferramenta de cópia de arquivo preferencial.

### <a name="is-migration-with-azure-data-box-supported"></a>É a migração com o Azure Data Box suportada?

Não. O Azure Data Box não suporta ficheiros de NetApp do Azure atualmente. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>É a migração com o serviço importar/exportar do Azure suportado?

Não. O serviço importar/exportar do Azure não suporta ficheiros de NetApp do Azure atualmente.

## <a name="next-steps"></a>Passos Seguintes  

- [Perguntas frequentes do Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [FAQ da rede Virtual do Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Como criar um pedido de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)