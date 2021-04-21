---
title: PERGUNTAS FREQUENTES Sobre Ficheiros Azure NetApp | Microsoft Docs
description: A análise fez perguntas frequentes sobre ficheiros Azure NetApp, tais como networking, segurança, desempenho, gestão de capacidades e migração/proteção de dados.
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
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: 6cef4860184b217e96e8967ab24a3befc632e316
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811855"
---
# <a name="faqs-about-azure-netapp-files"></a>PERGUNTAS Frequentes Sobre Ficheiros Azure NetApp

Este artigo responde a perguntas frequentes (FAQs) sobre ficheiros Azure NetApp. 

## <a name="networking-faqs"></a>Perguntas frequentes em rede

### <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>O caminho dos dados para NFS ou SMB passa pela Internet?  

N.º O caminho dos dados para NFS ou SMB não passa pela Internet. O Azure NetApp Files é um serviço nativo do Azure que está implantado na Rede Virtual Azure (VNet) onde o serviço está disponível. O Azure NetApp Files utiliza uma sub-rede delegada e fornece uma interface de rede diretamente no VNet. 

Consulte as Diretrizes para o planeamento da [rede Azure NetApp Files](./azure-netapp-files-network-topologies.md) para obter mais detalhes.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Posso ligar um VNet que já criei ao serviço Azure NetApp Files?

Sim, pode ligar VNets que criou ao serviço. 

Consulte as Diretrizes para o planeamento da [rede Azure NetApp Files](./azure-netapp-files-network-topologies.md) para obter mais detalhes.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Posso montar um volume NFS de Ficheiros Azure NetApp usando o nome DNS FQDN?

Sim, pode, se criar as entradas de DNS necessárias. A Azure NetApp Files fornece o IP de serviço para o volume aprovisionado. 

> [!NOTE] 
> Os Ficheiros Azure NetApp podem implementar IPs adicionais para o serviço, conforme necessário.  As entradas de DNS podem ter de ser atualizadas periodicamente.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Posso definir ou selecionar o meu próprio endereço IP para um volume de Ficheiros Azure NetApp?  

N.º A atribuição de IP aos volumes de Ficheiros Azure NetApp é dinâmica. A atribuição ip estática não é suportada. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Os Ficheiros Azure NetApp suportam a dupla pilha (IPv4 e IPv6) VNet?

Não, os Ficheiros Azure NetApp não suportam atualmente a VNet de pilha dupla (IPv4 e IPv6).  
 
## <a name="security-faqs"></a>Perguntas de Segurança

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>O tráfego de rede entre o Azure VM e o armazenamento pode ser encriptado?

O tráfego de dados entre clientes NFSv4.1 e volumes Azure NetApp Files pode ser encriptado usando Kerberos com encriptação AES-256. Consulte [a encriptação NFSv4.1 Kerberos para obter](configure-kerberos-encryption.md) mais detalhes.   

O tráfego de dados entre clientes NFSv3 ou SMB3 para volumes Azure NetApp Files não está encriptado. No entanto, o tráfego de um Azure VM (executando um cliente NFS ou SMB) para Azure NetApp Files é tão seguro como qualquer outro tráfego Azure-VM-para-VM. Este tráfego é local para a rede de centros de dados Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>O armazenamento pode ser encriptado em repouso?

Todos os volumes dos Ficheiros Azure NetApp são encriptados utilizando a norma FIPS 140-2. Todas as chaves são geridas pelo serviço Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Como são geridas as chaves de encriptação? 

A gestão chave para ficheiros Azure NetApp é tratada pelo serviço. Uma chave de encriptação de dados XTS-AES-256 única é gerada para cada volume. Uma hierarquia de chave de encriptação é usada para encriptar e proteger todas as chaves de volume. Estas teclas de encriptação nunca são apresentadas ou reportadas num formato não encriptado. As teclas de encriptação são eliminadas imediatamente quando um volume é eliminado.

O suporte para chaves geridas pelo cliente (Bring Your Own Key) utilizando o Azure Dedicated HSM está disponível numa base controlada nas regiões leste dos EUA, South Central US, West US 2 e Eua Gov Virginia. Pode solicitar acesso em [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) . À medida que a capacidade se torna disponível, os pedidos serão aprovados.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Posso configurar as regras de política de exportação da NFS para controlar o acesso ao alvo de montagem de serviçoS Azure NetApp?

Sim, pode configurar até cinco regras numa única política de exportação da NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Os ficheiros Azure NetApp suportam grupos de segurança de rede?

Não, atualmente não é possível aplicar Grupos de Segurança de Rede à sub-rede delegada dos Ficheiros Azure NetApp ou às interfaces de rede criadas pelo serviço.

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Posso utilizar o Azure RBAC com ficheiros Azure NetApp?

Sim, o Azure NetApp Files suporta funcionalidades do Azure RBAC. Juntamente com os papéis Azure incorporados, pode [criar funções personalizadas](../role-based-access-control/custom-roles.md) para ficheiros Azure NetApp. 

Para obter a lista completa das permissões do Azure NetApp Files, consulte as operações do fornecedor de recursos Azure para [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp) .

### <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Os Registos de Atividade do Azure são suportados em ficheiros Azure NetApp?

Azure NetApp Files é um serviço nativo da Azure. Todos os APIs PUT, POST e DELETE contra ficheiros Azure NetApp estão registados. Por exemplo, os registos mostram atividades como quem criou o instantâneo, quem modificou o volume, e assim por diante.

Para obter a lista completa das operações da API, consulte [a AZure NetApp Files REST API](/rest/api/netapp/).

### <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Posso utilizar as políticas do Azure com ficheiros Azure NetApp?

Sim, pode criar [políticas Azure personalizadas.](../governance/policy/tutorials/create-custom-policy-definition.md) 

No entanto, não é possível criar políticas Azure (políticas de nomeação personalizada) na interface Azure NetApp Files. Consulte as Diretrizes para o planeamento da [rede de ficheiros Azure NetApp](azure-netapp-files-network-topologies.md#considerations).

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

Pode alterar o nível de serviço de um volume existente movendo o volume para outro pool de capacidade que utiliza o [nível](azure-netapp-files-service-levels.md) de serviço que deseja para o volume. Consulte [alterar dinamicamente o nível de serviço de um volume](dynamic-change-volume-service-level.md). 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Como monitorizo o desempenho dos Ficheiros Azure NetApp?

O Azure NetApp Files fornece métricas de desempenho de volume. Também pode utilizar o Azure Monitor para monitorizar as métricas de utilização dos ficheiros Azure NetApp.  Consulte [métricas para ficheiros Azure NetApp](azure-netapp-files-metrics.md) para a lista de métricas de desempenho dos ficheiros Azure NetApp.

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Qual é o impacto de desempenho de Kerberos na NFSv4.1?

Consulte [o impacto de desempenho dos Kerberos nos volumes NFSv4.1](performance-impact-kerberos.md) para obter informações sobre opções de segurança para o NFSv4.1, os vetores de desempenho testados e o impacto de desempenho esperado. 

## <a name="nfs-faqs"></a>FAQ sobre NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Quero ter um volume montado automaticamente quando um VM Azure for iniciado ou reiniciado.  Como posso configurar o meu anfitrião para volumes persistentes de NFS?

Para que um volume NFS possa ser montado automaticamente no início ou reinicialização do VM, adicione uma entrada `/etc/fstab` no ficheiro no anfitrião. 

Consulte [o Mount ou desmonte um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) para obter mais detalhes.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Porque é que o comando df do cliente NFS não mostra o tamanho do volume a provisionado?

O tamanho do volume reportado em DF é o tamanho máximo que o volume de Ficheiros Azure NetApp pode crescer. O tamanho do volume dos Ficheiros Azure NetApp no comando DF não reflete a quota ou o tamanho do volume.  Pode obter o tamanho ou quota de volume dos Ficheiros Azure NetApp através do portal Azure ou da API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Que versão NFS suporta o Azure NetApp Files?

O Azure NetApp Files suporta NFSv3 e NFSv4.1. Pode [criar um volume](azure-netapp-files-create-volumes.md) utilizando a versão NFS. 

### <a name="how-do-i-enable-root-squashing"></a>Como posso permitir o esmagamento de raízes?

Pode especificar se a conta raiz pode aceder ao volume ou não utilizando a política de exportação do volume. Consulte [a política de exportação configurar para um volume NFS](azure-netapp-files-configure-export-policy.md) para obter mais detalhes.

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Posso utilizar o mesmo caminho de ficheiro (símbolo de criação de volume) para vários volumes?

Sim, pode. No entanto, o caminho do ficheiro deve ser utilizado numa subscrição diferente ou numa região diferente.   

Por exemplo, cria-se um volume chamado `vol1` . E depois cria-se outro volume também chamado `vol1` de um pool de capacidade diferente, mas na mesma subscrição e região. Neste caso, a utilização do mesmo nome de volume `vol1` causará um erro. Para utilizar o mesmo caminho de arquivo, o nome deve estar numa região ou subscrição diferente.

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Quando tento aceder aos volumes de NFS através de um cliente Windows, porque é que o cliente demora muito tempo a pesquisar pastas e sub-dobradores?

Certifique-se de que `CaseSensitiveLookup` está ativado no cliente do Windows para acelerar a procura de pastas e sub-dobradores:

1. Utilize o seguinte comando PowerShell para ativar o CaseSensitiveLookup:   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Monte o volume no servidor Windows.   
    Exemplo:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Como é que os Ficheiros Azure NetApp suportam o bloqueio de ficheiros NFSv4.1? 

Para os clientes NFSv4.1, a Azure NetApp Files suporta o mecanismo de bloqueio de ficheiros NFSv4.1 que mantém o estado de todas as fechaduras de ficheiros sob um modelo baseado em locação. 

Por RFC 3530, a Azure NetApp Files define um período único de locação para todos os estados detidos por um cliente NFS. Se o cliente não renovar o seu contrato de arrendamento dentro do período definido, todos os estados associados ao arrendamento do cliente serão liberados pelo servidor.  

Por exemplo, se um cliente que monta um volume não reagir ou se bater para além dos intervalos, as fechaduras serão libertadas. O cliente pode renovar o seu contrato de arrendamento de forma explícita ou implícita através da realização de operações como a leitura de um ficheiro.   

Um período de carência define um período de processamento especial no qual os clientes podem tentar recuperar o seu estado de bloqueio durante a recuperação do servidor. O tempo limite padrão para os locados é de 30 segundos com um período de carência de 45 segundos. Depois desse tempo, o contrato de arrendamento do cliente será liberado.   

## <a name="smb-faqs"></a>FAQs sobre o SMB

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Quais as versões SMB suportadas por Ficheiros Azure NetApp?

O Azure NetApp Files suporta SMB 2.1 e SMB 3.1 (que inclui suporte para SMB 3.0).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>É necessária uma ligação ative directory para acesso SMB? 

Sim, tem de criar uma ligação Ative Directory antes de implantar um volume SMB. Os controladores de domínio especificados devem estar acessíveis pela sub-rede delegada dos Ficheiros Azure NetApp para uma ligação bem sucedida.  Consulte [Criar um volume SMB](./azure-netapp-files-create-volumes-smb.md) para mais detalhes. 

### <a name="how-many-active-directory-connections-are-supported"></a>Quantas ligações ative directy são suportadas?

O Azure NetApp Files não suporta várias ligações ative directory (AD) numa única *região*, mesmo que as ligações AD estejam em diferentes contas NetApp. No entanto, pode ter várias ligações AD numa única *subscrição,* desde que as ligações AD se encontrem em diferentes regiões. Se precisar de várias ligações AD numa única região, pode utilizar subscrições separadas para o fazer. 

Uma ligação AD é configurada por conta NetApp; a ligação AD só é visível através da conta NetApp em que é criada.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Os ficheiros Azure NetApp suportam o Azure Ative Directory? 

Tanto os [Serviços de Domínio Azure Ative (AD)](../active-directory-domain-services/overview.md) como [os Serviços de Domínio do Diretório Ativo (DS AD)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) são suportados. Pode utilizar controladores de domínio ative diretório existentes com ficheiros Azure NetApp. Os controladores de domínio podem residir em Azure como máquinas virtuais, ou nas instalações via ExpressRoute ou S2S VPN. O Azure NetApp Files não suporta a participação da AD no [Azure Ative Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) neste momento.

Se estiver a utilizar ficheiros Azure NetApp com Serviços de Domínio do Diretório Ativo Azure, o percurso da unidade organizacional é `OU=AADDC Computers` quando configura o Ative Directory para a sua conta NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Quais as versões do Windows Server Ative Directory suportadas?

O Azure NetApp Files suporta versões windows Server 2008r2SP1-2019 de Serviços de Domínio de Diretório Ativo.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Porque é que o espaço disponível no meu cliente SMB não mostra o tamanho a provisionado?

O tamanho do volume reportado pelo cliente SMB é o tamanho máximo que o volume de Ficheiros Azure NetApp pode crescer. O tamanho do volume dos Ficheiros Azure NetApp, tal como mostrado no cliente SMB, não reflete a quota ou o tamanho do volume. Pode obter o tamanho ou quota de volume dos Ficheiros Azure NetApp através do portal Azure ou da API.

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Estou a ter problemas em ligar-me à minha parte da SMB. O que devo fazer?

Como melhor prática, desloque a tolerância máxima para a sincronização do relógio do computador para cinco minutos. Para obter mais informações, consulte [a tolerância máxima para a sincronização do relógio do computador](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)). 

### <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>Posso gerir `SMB Shares` , e através da Consola de `Sessions` `Open Files` Gestão de Computadores (MMC)?

A gestão de `SMB Shares` , e através da Consola de `Sessions` `Open Files` Gestão de Computadores (MMC) não é suportada.

### <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>Como posso obter o endereço IP de um volume SMB através do portal?

Utilize o link **JSON View** no painel de visão geral do volume e procure o identificador **startIp** em **propriedades**  ->  **mountTargets**.

### <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>Pode um Azure NetApp Files SMB agir como uma raiz dfs namespace (DFS-N)

N.º No entanto, as ações SMB de Ficheiros Azure NetApp podem servir como alvo de pasta DFS Namespace (DFS-N).   
Para utilizar uma partilha SMB de Ficheiros Azure NetApp como alvo de pasta DFS-N, forneça o caminho de montagem da Azure NetApp Files SMB utilizando o procedimento [DFS Add Folder Target.](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target)  

### <a name="smb-encryption-faqs"></a>Perguntas frequentes de encriptação SMB

Esta secção responde a perguntas comumente sobre encriptação SMB (SMB 3.0 e SMB 3.1.1).

#### <a name="what-is-smb-encryption"></a>O que é encriptação SMB?  

[A encriptação SMB](/windows-server/storage/file-server/smb-security) fornece encriptação de ponta a ponta de dados SMB e protege os dados de escutas de ocorrências em redes não fidedifusas. A encriptação SMB é suportada em SMB 3.0 e maior. 

#### <a name="how-does-smb-encryption-work"></a>Como funciona a encriptação SMB?

Ao enviar um pedido para o armazenamento, o cliente encripta o pedido, que o armazenamento então desencripta. As respostas são igualmente encriptadas pelo servidor e desencriptadas pelo cliente.

#### <a name="which-clients-support-smb-encryption"></a>Que clientes suportam encriptação SMB?

Windows 10, Windows 2012 e versões posteriores suportam encriptação SMB.

#### <a name="with-azure-netapp-files-at-what-layer-is-smb-encryption-enabled"></a>Com ficheiros Azure NetApp, em que camada está ativada a encriptação SMB?  

A encriptação SMB está ativada ao nível das ações.

#### <a name="what-forms-of-smb-encryption-are-used-by-azure-netapp-files"></a>Que formas de encriptação SMB são utilizadas pelos Ficheiros Azure NetApp?

SMB 3.0 emprega algoritmo AES-CCM, enquanto SMB 3.1.1 emprega o algoritmo AES-GCM

#### <a name="is-smb-encryption-required"></a>É necessária encriptação SMB?

A encriptação SMB não é necessária. Como tal, só está ativado para uma determinada participação se o utilizador solicitar que o Azure NetApp Files o permita. As ações do Azure NetApp Files nunca são expostas à internet. Só são acessíveis dentro de um determinado VNet, sobre VPN ou via expressa, pelo que as ações do Azure NetApp Files são inerentemente seguras. A escolha para ativar a encriptação SMB é inteiramente da decisão do utilizador. Esteja atento à penalização de desempenho prevista antes de ativar esta funcionalidade.

#### <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a><a name="smb_encryption_impact"></a>Qual é o impacto previsto da encriptação SMB nas cargas de trabalho dos clientes?

Embora a encriptação SMB tenha impacto tanto para o cliente (cpu overhead para encriptar e desencriptar mensagens) como para o armazenamento (reduções na produção), a tabela seguinte destaca apenas o impacto do armazenamento. Deve testar o impacto do desempenho da encriptação contra as suas próprias aplicações antes de colocar cargas de trabalho na produção.

|     Perfil de I/O       |     Impacto        |
|-  |-  |
|     Ler e escrever cargas de trabalho      |     10% a 15%        |
|     Metadados intensivos        |     5%    |

## <a name="capacity-management-faqs"></a>Perguntas frequentes de gestão da capacidade

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Como monitorar a utilização para o pool de capacidade e volume de Ficheiros Azure NetApp? 

O Azure NetApp Files fornece métricas de utilização de pool e volume de capacidade. Também pode utilizar o Azure Monitor para monitorizar a utilização de ficheiros Azure NetApp. Consulte [as métricas dos ficheiros Azure NetApp](azure-netapp-files-metrics.md) para obter mais detalhes. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Posso gerir ficheiros Azure NetApp através do Azure Storage Explorer?

N.º Os ficheiros Azure NetApp não são suportados pelo Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Como posso determinar se um diretório se aproxima do tamanho do limite?

Pode utilizar o `stat` comando de um cliente para ver se um diretório está a aproximar-se do limite máximo de tamanho para metadados de diretório (320 MB).   

Para um diretório de 320-MB, o número de blocos é de 655360, com cada tamanho de bloco a ser de 512 bytes.  (Isto é, 320x1024x1024/512.)  Este número traduz-se em aproximadamente 4 milhões de ficheiros no máximo para um diretório de 320-MB. No entanto, o número real de ficheiros máximos pode ser menor, dependendo de fatores como o número de ficheiros que contêm caracteres não ASCII no diretório. Como tal, deve utilizar o comando da `stat` seguinte forma para determinar se o seu diretório está a aproximar-se do seu limite.  

Exemplos:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>Perguntas frequentes de migração e proteção de dados

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Como posso migrar dados para ficheiros Azure NetApp?
O Azure NetApp Files fornece volumes NFS e SMB.  Pode utilizar qualquer ferramenta de cópia baseada em ficheiros para migrar dados para o serviço. 

O NetApp oferece uma solução baseada em SaaS, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  A solução permite-lhe replicar dados NFS ou SMB para exportações de Ficheiros NFS ou SMB. 

Também pode utilizar uma grande variedade de ferramentas gratuitas para copiar dados. Para o NFS, pode utilizar ferramentas de carga de trabalho como [o rsync](https://rsync.samba.org/examples.html) para copiar e sincronizar dados de origem num volume de Ficheiros Azure NetApp. Para a SMB, pode utilizar [a robocopia](/windows-server/administration/windows-commands/robocopy) de carga de trabalho da mesma forma.  Estas ferramentas também podem replicar permissões de ficheiros ou pastas. 

Os requisitos para a migração de dados das instalações para os Ficheiros Azure NetApp são os seguintes: 

- Certifique-se de que os Ficheiros Azure NetApp estão disponíveis na região alvo do Azure.
- Validar a conectividade da rede entre a fonte e o endereço IP do volume de destino dos Ficheiros Azure NetApp. A transferência de dados entre as instalações e o serviço Azure NetApp Files é suportada pelo ExpressRoute.
- Crie o volume de ficheiros Azure NetApp alvo.
- Transfira os dados de origem para o volume-alvo utilizando a sua ferramenta de cópia de ficheiro preferida.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Como posso criar uma cópia de um volume de Ficheiros Azure NetApp noutra região do Azure?
    
O Azure NetApp Files fornece volumes NFS e SMB.  Qualquer ferramenta de cópia baseada em ficheiros pode ser usada para replicar dados entre as regiões de Azure. 

O NetApp oferece uma solução baseada em SaaS, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  A solução permite-lhe replicar dados NFS ou SMB para exportações de Ficheiros NFS ou SMB. 

Também pode utilizar uma grande variedade de ferramentas gratuitas para copiar dados. Para o NFS, pode utilizar ferramentas de carga de trabalho como [o rsync](https://rsync.samba.org/examples.html) para copiar e sincronizar dados de origem num volume de Ficheiros Azure NetApp. Para a SMB, pode utilizar [a robocopia](/windows-server/administration/windows-commands/robocopy) de carga de trabalho da mesma forma.  Estas ferramentas também podem replicar permissões de ficheiros ou pastas. 

Os requisitos para replicar um volume de Ficheiros Azure NetApp para outra região do Azure são os seguintes: 
- Certifique-se de que os Ficheiros Azure NetApp estão disponíveis na região alvo do Azure.
- Validar a conectividade da rede entre VNets em cada região. Atualmente, o espreitamento global entre VNets não é suportado.  Pode estabelecer conectividade entre VNets ligando-se a um circuito ExpressRoute ou utilizando uma ligação S2S VPN. 
- Crie o volume de ficheiros Azure NetApp alvo.
- Transfira os dados de origem para o volume-alvo utilizando a sua ferramenta de cópia de ficheiro preferida.

### <a name="is-migration-with-azure-data-box-supported"></a>A migração com a Caixa de Dados Azure está suportada?

N.º A Azure Data Box não suporta ficheiros Azure NetApp atualmente. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>A migração com o serviço Azure Import/Export é suportada?

N.º O serviço Azure Import/Export não suporta atualmente ficheiros Azure NetApp.

## <a name="product-faqs"></a>Perguntas frequentes de produtos

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Posso utilizar volumes NFS ou SMB de Ficheiros Azure NetApp com Solução VMware Azure (AVS)?

Pode montar volumes NFS em VMs do Azure NetApp ou em VMs do Azure NetApp. Pode mapear as ações SMB dos Ficheiros Azure NetApp em VMs do AVS Windows. Para mais detalhes, consulte [os Ficheiros Azure NetApp com Solução VMware Azure]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md).  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Que regiões são suportadas para a utilização de volumes NFS ou SMB de Ficheiros Azure NetApp ou SMB com Solução VMware Azure (AVS)?

A utilização de volumes NFS ou SMB de Ficheiros Azure NetApp com AVS é suportada nas seguintes regiões - Leste dos EUA, Eua Ocidental, Europa Ocidental e Austrália Oriental.

## <a name="next-steps"></a>Passos seguintes  

- [Microsoft Azure ExpressRoute FAQs](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Virtual Network FAQ](../virtual-network/virtual-networks-faq.md)
- [Como criar um pedido de suporte do Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [PERGUNTAS Frequentes sobre desempenho SMB para ficheiros Azure NetApp](azure-netapp-files-smb-performance.md)
