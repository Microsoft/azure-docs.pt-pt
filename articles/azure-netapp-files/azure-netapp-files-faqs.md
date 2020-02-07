---
title: Perguntas frequentes sobre Azure NetApp Files | Microsoft Docs
description: Responde a perguntas frequentes sobre Azure NetApp Files.
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
ms.date: 02/05/2020
ms.author: b-juche
ms.openlocfilehash: aaa7e5e65ced2a9899bef5a811ee74be42a8548f
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048818"
---
# <a name="faqs-about-azure-netapp-files"></a>Perguntas frequentes sobre Azure NetApp Files

Este artigo responde às perguntas frequentes sobre Azure NetApp Files. 

## <a name="networking-faqs"></a>Perguntas frequentes sobre rede

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>O caminho de dados do NFS passa pela Internet?  

Não. O caminho de dados do NFS não passa pela Internet. Azure NetApp Files é um serviço nativo do Azure que é implantado na VNet (rede virtual) do Azure onde o serviço está disponível. Azure NetApp Files usa uma sub-rede delegada e provisiona uma interface de rede diretamente na VNet. 

Consulte as Diretrizes para o planeamento da [rede de ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para obter detalhes.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Posso conectar uma VNet que eu já criei ao serviço de Azure NetApp Files?

Sim, você pode conectar VNets que você criou ao serviço. 

Consulte as Diretrizes para o planeamento da [rede de ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) para obter detalhes.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Posso montar um volume NFS de Azure NetApp Files usando o nome FQDN do DNS?

Sim, você pode, se criar as entradas DNS necessárias. Azure NetApp Files fornece o IP do serviço para o volume provisionado. 

> [!NOTE] 
> Azure NetApp Files pode implantar IPs adicionais para o serviço, conforme necessário.  As entradas DNS talvez precisem ser atualizadas periodicamente.

## <a name="security-faqs"></a>Perguntas frequentes sobre segurança

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>O tráfego de rede entre a VM do Azure e o armazenamento pode ser criptografado?

O tráfego de dados (tráfego do cliente NFSv3, NFSv 4.1 ou SMBv3 para Azure NetApp Files volumes) não é criptografado. No entanto, o tráfego de uma VM do Azure (executando um cliente NFS ou SMB) para Azure NetApp Files é tão seguro quanto qualquer outro tráfego do Azure-VM para VM. Esse tráfego é local para a rede do Data Center do Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>O armazenamento pode ser criptografado em repouso?

Todos os volumes de Azure NetApp Files são criptografados usando o padrão FIPS 140-2. Todas as chaves são gerenciadas pelo serviço de Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Como as chaves de criptografia são gerenciadas? 

O gerenciamento de chaves para Azure NetApp Files é tratado pelo serviço. Uma chave de criptografia de dados XTS-AES-256 exclusiva é gerada para cada volume. Uma hierarquia de chave de criptografia é usada para criptografar e proteger todas as chaves de volume. Essas chaves de criptografia nunca são exibidas ou relatadas em um formato não criptografado. As chaves de criptografia são excluídas imediatamente quando um volume é excluído.

Atualmente, não há suporte para chaves gerenciadas pelo usuário (Traga suas próprias chaves).

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Posso configurar as regras da política de exportação do NFS para controlar o acesso ao destino de montagem do serviço Azure NetApp Files?


Sim, você pode configurar até cinco regras em uma única política de exportação de NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp Files dá suporte a grupos de segurança de rede?

Não, no momento, não é possível aplicar grupos de segurança de rede à sub-rede delegada do Azure NetApp Files ou interfaces de rede criadas pelo serviço.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Posso usar IAM do Azure com Azure NetApp Files?

Sim, Azure NetApp Files dá suporte a recursos de RBAC com IAM do Azure.

## <a name="performance-faqs"></a>Perguntas frequentes sobre desempenho

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>O que devo fazer para otimizar ou ajustar o desempenho de Azure NetApp Files?

Você pode executar as seguintes ações de acordo com os requisitos de desempenho: 
- Verifique se a máquina virtual está dimensionada corretamente.
- Habilite a rede acelerada para a VM.
- Selecione o nível de serviço e o tamanho desejados para o pool de capacidade.
- Crie um volume com o tamanho de cota desejado para a capacidade e o desempenho.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Como fazer converter níveis de serviço baseados em taxa de transferência de Azure NetApp Files em IOPS?

Você pode converter MB/s em IOPS usando a seguinte fórmula:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Como fazer alterar o nível de serviço de um volume?

No momento, não há suporte para a alteração do nível de serviço de um volume.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Como fazer monitorar Azure NetApp Files desempenho?

Azure NetApp Files fornece métricas de desempenho de volume. Você também pode usar Azure Monitor para monitorar as métricas de uso de Azure NetApp Files.  Consulte [as Métricas dos Ficheiros Azure NetApp](azure-netapp-files-metrics.md) para obter a lista de métricas de desempenho para ficheiros Azure NetApp.

## <a name="nfs-faqs"></a>Perguntas frequentes sobre NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Quero ter um volume montado automaticamente quando uma VM do Azure é iniciada ou reinicializada.  Como fazer configurar meu host para volumes NFS persistentes?

Para que um volume NFS monte automaticamente em VM iniciar ou reiniciar, adicione uma entrada no ficheiro `/etc/fstab` no hospedeiro. 

Consulte [o Mount ou desmonte um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) para obter mais detalhes.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Por que o comando DF no cliente NFS não mostra o tamanho do volume provisionado?

O tamanho do volume relatado em DF é o tamanho máximo para o qual o volume de Azure NetApp Files pode crescer. O tamanho do volume de Azure NetApp Files no comando DF não é refletido da cota ou do tamanho do volume.  Você pode obter a Azure NetApp Files o tamanho ou a cota do volume por meio do portal do Azure ou da API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Qual versão do NFS Azure NetApp Files dá suporte?

Azure NetApp Files dá suporte a NFSv3 e NFSv 4.1. Você pode criar um volume usando a versão do NFS. 

> [!IMPORTANT] 
> O acesso ao recurso NFSv 4.1 requer a lista de permissões.  Para solicitar a lista geminada, submeta um pedido para <anffeedback@microsoft.com>. 


### <a name="how-do-i-enable-root-squashing"></a>Como fazer habilitar o desativação de raiz?

No momento, não há suporte para o desatualização raiz.

## <a name="smb-faqs"></a>Perguntas frequentes sobre o SMB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>É uma conexão Active Directory necessária para acesso SMB? 

Sim, você deve criar uma conexão de Active Directory antes de implantar um volume SMB. Os controladores de domínio especificados devem ser acessíveis pela sub-rede delegada do Azure NetApp Files para uma conexão bem-sucedida.  Consulte [Criar um volume SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) para mais detalhes. 

### <a name="how-many-active-directory-connections-are-supported"></a>Há suporte para quantas conexões Active Directory?

O Azure NetApp Files não suporta várias ligações de Diretório Ativo (AD) numa única *região*– mesmo que as ligações AD estejam em diferentes contas netApp. No entanto, pode ter múltiplas ligações aD numa única *subscrição,* desde que as ligações AD estejam em diferentes regiões. Se precisar de múltiplas ligações aD numa única região, pode utilizar subscrições separadas para o fazer. 

Uma ligação AD é configurada por conta NetApp; a ligação AD só é visível através da conta NetApp em que é criada.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp Files oferece suporte a Azure Active Directory? 

Ambos os Serviços de [Domínio do Diretório Ativo Azure (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/overview) e os Serviços de Domínio de [Diretório Ativo (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) são apoiados. Você pode usar os controladores de domínio Active Directory existentes com Azure NetApp Files. Os controladores de domínio podem residir no Azure como máquinas virtuais ou localmente por meio de ExpressRoute ou VPN S2S. Os Ficheiros Azure NetApp não suportam a adesão da AD ao [Azure Ative Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) neste momento.

Se estiver a utilizar ficheiros Azure NetApp com serviços de domínio de diretório ativo Azure, o percurso da unidade organizacional é `OU=AADDC Computers` quando configura o Diretório Ativo para a sua conta NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Quais versões do Windows Server Active Directory têm suporte?

O Azure NetApp Files oferece suporte a versões do Windows Server 2008r2SP1-2019 do Active Directory Domain Services.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Por que o espaço disponível no meu cliente SMB não mostra o tamanho provisionado?

O tamanho do volume relatado pelo cliente SMB é o tamanho máximo para o qual o volume Azure NetApp Files pode crescer. O tamanho do volume de Azure NetApp Files, conforme mostrado no cliente SMB, não está refletindo a cota ou o tamanho do volume. Você pode obter a Azure NetApp Files o tamanho ou a cota do volume por meio do portal do Azure ou da API.

## <a name="capacity-management-faqs"></a>Perguntas frequentes sobre gerenciamento de capacidade

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Como fazer monitorar o uso do pool de capacidade e do volume de Azure NetApp Files? 

Azure NetApp Files fornece métricas de uso de volume e pool de capacidade. Você também pode usar Azure Monitor para monitorar o uso de Azure NetApp Files. Consulte [as Métricas para Ficheiros Azure NetApp](azure-netapp-files-metrics.md) para obter mais detalhes. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Posso gerenciar Azure NetApp Files por meio de Gerenciador de Armazenamento do Azure?

Não. O Gerenciador de Armazenamento do Azure não dá suporte a Azure NetApp Files.

## <a name="data-migration-and-protection-faqs"></a>Perguntas frequentes sobre migração de dados e proteção

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Como fazer migrar dados para Azure NetApp Files?
Azure NetApp Files fornece volumes de NFS e SMB.  Você pode usar qualquer ferramenta de cópia baseada em arquivo para migrar dados para o serviço. 

O NetApp oferece uma solução baseada no SaaS, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  A solução permite-lhe replicar dados de NFS ou SMB para as exportações nFS do Azure NetApp ou ações SMB. 

Você também pode usar uma ampla variedade de ferramentas gratuitas para copiar dados. Para o NFS, pode utilizar ferramentas de carga de trabalho como [rsync](https://rsync.samba.org/examples.html) para copiar e sincronizar dados de origem num volume de Ficheiros Azure NetApp. Para sMB, pode utilizar [a robocópia](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) da mesma forma.  Essas ferramentas também podem replicar permissões de arquivo ou pasta. 

Os requisitos para a migração de dados do local para o Azure NetApp Files são os seguintes: 

- Verifique se Azure NetApp Files está disponível na região do Azure de destino.
- Valide a conectividade de rede entre a origem e o endereço IP do volume de destino Azure NetApp Files. Transferência de dados entre o local e o serviço de Azure NetApp Files tem suporte no ExpressRoute.
- Crie o volume de Azure NetApp Files de destino.
- Transfira os dados de origem para o volume de destino usando sua ferramenta de cópia de arquivo preferencial.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Como fazer criar uma cópia de um volume de Azure NetApp Files em outra região do Azure?
    
Azure NetApp Files fornece volumes de NFS e SMB.  Qualquer ferramenta de cópia baseada em arquivo pode ser usada para replicar dados entre regiões do Azure. 

O NetApp oferece uma solução baseada no SaaS, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  A solução permite-lhe replicar dados de NFS ou SMB para as exportações nFS do Azure NetApp ou ações SMB. 

Você também pode usar uma ampla variedade de ferramentas gratuitas para copiar dados. Para o NFS, pode utilizar ferramentas de carga de trabalho como [rsync](https://rsync.samba.org/examples.html) para copiar e sincronizar dados de origem num volume de Ficheiros Azure NetApp. Para sMB, pode utilizar [a robocópia](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) da mesma forma.  Essas ferramentas também podem replicar permissões de arquivo ou pasta. 

Os requisitos para replicar um volume de Azure NetApp Files para outra região do Azure são os seguintes: 
- Verifique se Azure NetApp Files está disponível na região do Azure de destino.
- Valide a conectividade de rede entre VNets em cada região. Atualmente, o emparelhamento global entre VNets não tem suporte.  Você pode estabelecer a conectividade entre VNets vinculando-se a um circuito do ExpressRoute ou usando uma conexão VPN S2S. 
- Crie o volume de Azure NetApp Files de destino.
- Transfira os dados de origem para o volume de destino usando sua ferramenta de cópia de arquivo preferencial.

### <a name="is-migration-with-azure-data-box-supported"></a>Há suporte para migração com Azure Data Box?

Não. Azure Data Box não oferece suporte a Azure NetApp Files no momento. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Há suporte para a migração com o serviço de importação/exportação do Azure?

Não. O serviço de importação/exportação do Azure não oferece suporte a Azure NetApp Files no momento.

## <a name="next-steps"></a>Passos seguintes  

- [Microsoft Azure ExpressRoute FAQs](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [FaQ da rede virtual Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Como criar um pedido de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Caixa de dados Azure](https://docs.microsoft.com/azure/databox-family/)
- [FAQs sobre desempenho sMB para Ficheiros Azure NetApp](azure-netapp-files-smb-performance.md)
