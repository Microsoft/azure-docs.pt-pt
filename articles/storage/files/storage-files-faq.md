---
title: Perguntas frequentes (FAQ) para arquivos do Azure | Microsoft Docs
description: Encontre respostas para perguntas frequentes sobre os arquivos do Azure.
author: roygara
ms.service: storage
ms.date: 01/02/2019
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 622a033b73ace93e98cfa0d5179002c78ec49b35
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704475"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Perguntas frequentes sobre os arquivos do Azure
Os [arquivos do Azure](storage-files-introduction.md) oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem que são acessíveis por meio do [protocolo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)padrão do setor. Você pode montar compartilhamentos de arquivos do Azure simultaneamente em implantações locais ou na nuvem do Windows, Linux e macOS. Você também pode armazenar em cache os compartilhamentos de arquivos do Azure em computadores Windows Server usando Sincronização de Arquivos do Azure para acesso rápido próximo ao local em que os dados são usados.

Este artigo responde a perguntas comuns sobre recursos e funcionalidades dos arquivos do Azure, incluindo o uso de Sincronização de Arquivos do Azure com os arquivos do Azure. Se você não vir a resposta para sua pergunta, poderá entrar em contato conosco por meio dos seguintes canais (em ordem de escalonamento):

1. A seção de comentários deste artigo.
2. [Fórum do armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [UserVoice de arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Suporte da Microsoft. Para criar uma nova solicitação de suporte, na portal do Azure, na guia **ajuda** , selecione o botão **ajuda + suporte** e, em seguida, selecione **nova solicitação de suporte**.

## <a name="general"></a>Geral
* <a id="why-files-useful"></a>
  **Como os arquivos do Azure são úteis?**  
   Você pode usar os arquivos do Azure para criar compartilhamentos de arquivos na nuvem, sem ser responsável por gerenciar a sobrecarga de um servidor físico, dispositivo ou aparelho. Fazemos o trabalho de monótono para você, incluindo a aplicação de atualizações do sistema operacional e a substituição de discos defeituosos. Para saber mais sobre os cenários nos quais os arquivos do Azure podem ajudá-lo, confira [por que os arquivos do Azure são úteis](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Quais são as diferentes maneiras de acessar arquivos nos arquivos do Azure?**  
    Você pode montar o compartilhamento de arquivos em seu computador local usando o protocolo SMB 3,0 ou pode usar ferramentas como [Gerenciador de armazenamento](https://storageexplorer.com/) para acessar arquivos em seu compartilhamento de arquivos. Em seu aplicativo, você pode usar bibliotecas de cliente de armazenamento, APIs REST, PowerShell ou CLI do Azure para acessar seus arquivos no compartilhamento de arquivos do Azure.

* <a id="what-is-afs"></a>
  **O que é Sincronização de Arquivos do Azure?**  
    Você pode usar Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em arquivos do Azure, mantendo, ao mesmo tempo, a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. Sincronização de Arquivos do Azure transforma seus computadores Windows Server em um cache rápido do seu compartilhamento de arquivos do Azure. Você pode usar qualquer protocolo que esteja disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS (Network File System) e serviço de protocolo FTP (FTPS). Você pode ter quantos caches forem necessários em todo o mundo.

* <a id="files-versus-blobs"></a>
  **Por que usar um compartilhamento de arquivos do Azure em vez do armazenamento de BLOBs do Azure para meus dados?**  
    Os arquivos do Azure e o armazenamento de BLOBs do Azure oferecem maneiras de armazenar grandes quantidades de dados na nuvem, mas são úteis para finalidades ligeiramente diferentes. 
    
    O armazenamento de BLOBs do Azure é útil para aplicativos de grande escala e nativos de nuvem que precisam armazenar dados não estruturados. Para maximizar o desempenho e a escala, o armazenamento de BLOBs do Azure é uma abstração de armazenamento mais simples do que um sistema de arquivos verdadeiro. Você pode acessar o armazenamento de BLOBs do Azure somente por meio de bibliotecas de cliente baseadas em REST (ou diretamente por meio do protocolo baseado em REST).

    O arquivos do Azure é especificamente um sistema de arquivos. Os arquivos do Azure têm todos os resumos de arquivos que você conhece e adoram de anos de trabalho com sistemas operacionais locais. Assim como o armazenamento de BLOBs do Azure, os arquivos do Azure oferecem uma interface REST e bibliotecas de cliente baseadas em REST. Ao contrário do armazenamento de blob do Azure, os arquivos do Azure oferecem acesso SMB aos compartilhamentos de arquivos do Azure Usando o SMB, você pode montar um compartilhamento de arquivos do Azure diretamente no Windows, Linux ou macOS, seja no local ou em VMs de nuvem, sem gravar nenhum código ou anexar qualquer driver especial ao sistema de arquivos. Você também pode armazenar em cache os compartilhamentos de arquivos do Azure em servidores de arquivos locais usando Sincronização de Arquivos do Azure para acesso rápido, perto de onde os dados são usados. 
   
    Para obter uma descrição mais detalhada sobre as diferenças entre os arquivos do Azure e o armazenamento de BLOBs do Azure, consulte [decidindo quando usar o armazenamento de BLOBs do Azure, os arquivos do Azure ou os discos do Azure](../common/storage-decide-blobs-files-disks.md). Para saber mais sobre o armazenamento de BLOBs do Azure, confira [introdução ao armazenamento de BLOBs](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Por que usar um compartilhamento de arquivos do Azure em vez de discos do Azure?**  
    Um disco nos discos do Azure é simplesmente um disco. Para obter o valor dos discos do Azure, você deve anexar um disco a uma máquina virtual em execução no Azure. Os discos do Azure podem ser usados para tudo o que você usaria para um disco em um servidor local. Você pode usá-lo como um disco do sistema de so, como espaço de permuta para um sistema operacional ou como armazenamento dedicado para um aplicativo. Um uso interessante para os discos do Azure é criar um servidor de arquivos na nuvem para usar nos mesmos locais em que você pode usar um compartilhamento de arquivos do Azure. Implantar um servidor de arquivos em máquinas virtuais do Azure é uma maneira de alto desempenho para obter o armazenamento de arquivos no Azure quando você precisa de opções de implantação que atualmente não são compatíveis com os arquivos do Azure (como o suporte de protocolo NFS ou armazenamento Premium). 

    No entanto, a execução de um servidor de arquivos com discos do Azure como armazenamento de back-end normalmente é muito mais cara do que usar um compartilhamento de arquivos do Azure, por alguns motivos. Primeiro, além de pagar pelo armazenamento em disco, você também deve pagar pela despesa de executar uma ou mais VMs do Azure. Em segundo lugar, você também deve gerenciar as VMs que são usadas para executar o servidor de arquivos. Por exemplo, você é responsável por atualizações do sistema operacional. Por fim, se você precisar, por fim, exigir que os dados sejam armazenados em cache localmente, cabe a você configurar e gerenciar as tecnologias de replicação, como o DFSR (replicação do Sistema de Arquivos Distribuído), para fazer isso acontecer.

    Uma abordagem para obter o melhor dos arquivos do Azure e um servidor de arquivos hospedado em máquinas virtuais do Azure (além de usar discos do Azure como armazenamento de back-end) é instalar Sincronização de Arquivos do Azure em um servidor de arquivos hospedado em uma VM de nuvem. Se o compartilhamento de arquivos do Azure estiver na mesma região que o servidor de arquivos, você poderá habilitar a disposição em camadas de nuvem e definir o volume de porcentagem de espaço livre para máximo (99%). Isso garante a duplicação mínima de dados. Você também pode usar todos os aplicativos que desejar com seus servidores de arquivos, como aplicativos que exigem suporte ao protocolo NFS.

    Para obter informações sobre uma opção para configurar um servidor de arquivos de alto desempenho e altamente disponível no Azure, consulte Implantando [clusters convidados da VM IaaS no Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Para obter uma descrição mais detalhada das diferenças entre os arquivos do Azure e os discos do Azure, consulte [decidindo quando usar o armazenamento de BLOBs do Azure, os arquivos do Azure ou os discos do Azure](../common/storage-decide-blobs-files-disks.md). Para saber mais sobre os discos do Azure, confira [visão geral do azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
  **Como fazer começar a usar os arquivos do Azure?**  
   É fácil começar a usar os arquivos do Azure. Primeiro, [crie um compartilhamento de arquivos](storage-how-to-create-file-share.md)e, em seguida, monte-o em seu sistema operacional preferido: 

  * [Montar no Windows](storage-how-to-use-files-windows.md)
  * [Montar no Linux](storage-how-to-use-files-linux.md)
  * [Montar no macOS](storage-how-to-use-files-mac.md)

    Para obter um guia mais aprofundado sobre como implantar um compartilhamento de arquivos do Azure para substituir compartilhamentos de arquivos de produção em sua organização, consulte [planejando uma implantação de arquivos do Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Quais opções de redundância de armazenamento têm suporte nos arquivos do Azure?**  
    Atualmente, os arquivos do Azure dão suporte a LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona) e GRS (armazenamento com redundância geográfica). Planejamos oferecer suporte ao armazenamento com redundância geográfica (RA-GRS) de acesso de leitura no futuro, mas não temos linhas de tempo para compartilhar neste momento.

* <a id="tier-options"></a>
  **Quais camadas de armazenamento têm suporte nos arquivos do Azure?**  
    Os arquivos do Azure dão suporte a duas camadas de armazenamento: Premium e Standard. Os compartilhamentos de arquivos padrão são criados em contas de armazenamento de uso geral (GPv1 ou GPv2) e os compartilhamentos de arquivos Premium são criados em contas de armazenamento de armazenamento de arquivo. Saiba mais sobre como criar compartilhamentos de [arquivos padrão](storage-how-to-create-file-share.md) e compartilhamentos de [arquivos Premium](storage-how-to-create-premium-fileshare.md). 
    
    > [!NOTE]
    > Você não pode criar compartilhamentos de arquivos do Azure de contas de armazenamento de BLOBs ou contas de armazenamento *Premium* de uso geral (GPv1 ou GPv2). Os compartilhamentos de arquivos padrão do Azure devem ser criados somente em contas de uso geral *padrão* e compartilhamentos de arquivos premium do Azure devem ser criados somente em contas de armazenamento de armazenamento. As contas de armazenamento de uso geral *Premium* (GPv1 e GPv2) são apenas para BLOBs de páginas Premium. 

* <a id="give-us-feedback"></a>
  **Eu realmente quero ver um recurso específico adicionado aos arquivos do Azure. Você pode adicioná-lo?**  
    A equipe de arquivos do Azure está interessada em ouvir qualquer e todos os comentários que você tem sobre nosso serviço. Vote em solicitações de recursos em [UserVoice de arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Estamos ansiosos para desiluminar você com muitos recursos novos.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Quais regiões têm suporte para Sincronização de Arquivos do Azure?**  
    A lista de regiões disponíveis pode ser encontrada na seção [disponibilidade de região](storage-sync-files-planning.md#region-availability) do guia de planejamento de sincronização de arquivos do Azure. Iremos adicionar suporte continuamente para regiões adicionais, incluindo regiões não públicas.

* <a id="cross-domain-sync"></a>
  **Posso ter servidores ingressados no domínio e não ingressados no domínio no mesmo grupo de sincronização?**  
    Sim. Um grupo de sincronização pode conter pontos de extremidade de servidor que têm diferentes associações de Active Directory, mesmo que não sejam ingressados no domínio. Embora essa configuração tecnicamente funcione, não recomendamos isso como uma configuração típica, pois as ACLs (listas de controle de acesso) definidas para arquivos e pastas em um servidor podem não ser impostas por outros servidores no grupo de sincronização. Para obter melhores resultados, é recomendável sincronizar entre servidores que estão na mesma floresta Active Directory, entre servidores que estão em diferentes florestas Active Directory, mas que estabeleceram relações de confiança ou entre servidores que não estão em um domínio. Recomendamos que você evite usar uma combinação dessas configurações.

* <a id="afs-change-detection"></a>
  **Criei um arquivo diretamente no meu compartilhamento de arquivos do Azure usando SMB ou no Portal. Quanto tempo leva para que o arquivo seja sincronizado com os servidores no grupo de sincronização?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Se o mesmo arquivo for alterado em dois servidores quase ao mesmo tempo, o que acontecerá?**  
    Sincronização de Arquivos do Azure usa uma estratégia simples de resolução de conflitos: mantemos ambas as alterações nos arquivos que são alterados em dois servidores ao mesmo tempo. A alteração gravada mais recentemente mantém o nome do arquivo original. O arquivo mais antigo tem a máquina de "origem" e o número de conflitos anexados ao nome. Ele segue esta taxonomia: 
   
    \<FileNameWithoutExtension\>MachineName-.\>\<\[-#\]\< externa\>  

    Por exemplo, o primeiro conflito de CompanyReport. docx se tornaria CompanyReport-CentralServer. docx se CentralServer for onde ocorreu a gravação mais antiga. O segundo conflito seria nomeado CompanyReport-CentralServer-1. docx.

* <a id="afs-storage-redundancy"></a>
  **O armazenamento com redundância geográfica tem suporte para Sincronização de Arquivos do Azure?**  
    Sim, os arquivos do Azure dão suporte ao armazenamento com redundância local (LRS) e ao GRS (armazenamento com redundância geográfica). Se você iniciar um failover de conta de armazenamento entre regiões emparelhadas de uma conta configurada para GRS, a Microsoft recomenda que você trate a nova região como um backup somente de dados. Sincronização de Arquivos do Azure não inicia automaticamente a sincronização com a nova região primária. 

* <a id="sizeondisk-versus-size"></a>
  **Por que o *tamanho na* Propriedade do disco para um arquivo corresponde à propriedade *size* depois de usar sincronização de arquivos do Azure?**  
  Consulte [noções básicas sobre camadas de nuvem](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  **Como posso saber se um arquivo foi colocado em camadas?**  
  Consulte [noções básicas sobre camadas de nuvem](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Um arquivo que desejo usar foi colocado em camadas. Como posso recuperar o arquivo no disco para usá-lo localmente?**  
  Consulte [noções básicas sobre camadas de nuvem](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **Como fazer forçar um arquivo ou diretório a ser colocado em camadas?**  
  Consulte [noções básicas sobre camadas de nuvem](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  **Como o *espaço livre no volume* é interpretado quando tenho vários pontos de extremidade do servidor em um volume?**  
  Consulte [noções básicas sobre camadas de nuvem](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
  **Quais arquivos ou pastas são excluídos automaticamente pelo Sincronização de Arquivos do Azure?**  
    Por padrão, Sincronização de Arquivos do Azure exclui os seguintes arquivos:
  * desktop.ini
  * thumbs.db
  * ehthumbs.db
  * ~$\*.\*
  * \*.laccdb
  * \*.tmp
  * 635D02A9D91C401B97884B82B3BCDAEA.\*

    As seguintes pastas também são excluídas por padrão:

  * Informações de volume de \System
  * \$CYCLE. COMPARTIMENTO
  * \SyncShareState

* <a id="afs-os-support"></a>
  **Posso usar Sincronização de Arquivos do Azure com o Windows Server 2008 R2, Linux ou meu dispositivo NAS (armazenamento conectado à rede)?**  
    Atualmente, Sincronização de Arquivos do Azure dá suporte apenas ao Windows Server 2019, ao Windows Server 2016 e ao Windows Server 2012 R2. Neste momento, não temos outros planos que possamos compartilhar, mas estamos abertos para dar suporte a plataformas adicionais com base na demanda do cliente. Informe-nos no [UserVoice de arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files) quais plataformas você gostaria de dar suporte.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Por que existem arquivos em camadas fora do namespace do ponto de extremidade do servidor?**  
    Antes do Sincronização de Arquivos do Azure Agent versão 3, Sincronização de Arquivos do Azure bloqueou a movimentação de arquivos em camadas fora do ponto de extremidade do servidor, mas no mesmo volume que o ponto de extremidade do servidor. As operações de cópia, as movimentações de arquivos sem camadas e as movimentações de camadas para outros volumes não foram afetadas. O motivo para esse comportamento foi a suposição implícita de que o explorador de arquivos e outras APIs do Windows têm as operações de movimentação no mesmo volume são (quase) operações de renomeação instantâneas. Isso significa que a movimentação fará com que o explorador de arquivos ou outros métodos de movimentação (como linha de comando ou PowerShell) pareçam sem resposta enquanto Sincronização de Arquivos do Azure rechamar os dados da nuvem. A partir do [sincronização de arquivos do Azure Agent versão 3.0.12.0](storage-files-release-notes.md#supported-versions), sincronização de arquivos do Azure permitirá que você mova um arquivo em camadas fora do ponto de extremidade do servidor. Evitamos os efeitos negativos mencionados anteriormente, permitindo que o arquivo em camadas exista como um arquivo em camadas fora do ponto de extremidade do servidor e, em seguida, recuperando o arquivo em segundo plano. Isso significa que as movimentações no mesmo volume são instantâneas e fazemos todo o trabalho para recuperar o arquivo no disco após a conclusão da movimentação. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Estou tendo um problema com Sincronização de Arquivos do Azure no meu servidor (sincronização, camadas de nuvem, etc.). Devo remover e recriar meu ponto de extremidade do servidor?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Posso mover o serviço de sincronização de armazenamento e/ou a conta de armazenamento para um grupo de recursos ou assinatura diferente?**  
   Sim, o serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário existente do Azure AD. Se a conta de armazenamento for movida, você precisará conceder ao serviço de Sincronização de Arquivos híbrido acesso à conta de armazenamento (consulte [garantir que sincronização de arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronização de Arquivos do Azure não dá suporte à movimentação da assinatura para um locatário do Azure AD diferente.
    
* <a id="afs-ntfs-acls"></a>
  **Sincronização de Arquivos do Azure preservar ACLs NTFS no nível de diretório/arquivo juntamente com os dados armazenados nos arquivos do Azure?**

    As ACLs de NTFS transportadas de servidores de arquivos locais são mantidas por Sincronização de Arquivos do Azure como metadados. Os arquivos do Azure não dão suporte à autenticação com as credenciais do Azure AD para acesso a compartilhamentos de arquivos gerenciados pelo serviço de Sincronização de Arquivos do Azure.
    
## <a name="security-authentication-and-access-control"></a>Segurança, autenticação e controle de acesso
* <a id="ad-support"></a>
**A autenticação baseada em Active Directory e o controle de acesso com suporte dos arquivos do Azure?**  
    
    Sim, os arquivos do Azure dão suporte à autenticação baseada em identidade e ao controle de acesso com Azure Active Directory (Azure AD) (versão prévia). A autenticação do Azure AD sobre SMB para arquivos do Azure utiliza Azure Active Directory Domain Services para permitir que VMs ingressadas no domínio acessem compartilhamentos, diretórios e arquivos usando as credenciais do Azure AD. Para obter mais detalhes, consulte [visão geral da autenticação de Azure Active Directory sobre o SMB para arquivos do Azure (versão prévia)](storage-files-active-directory-overview.md). 

    Os arquivos do Azure oferecem duas maneiras adicionais de gerenciar o controle de acesso:

    - Você pode usar SAS (assinaturas de acesso compartilhado) para gerar tokens que têm permissões específicas e que são válidos por um intervalo de tempo especificado. Por exemplo, você pode gerar um token com acesso somente leitura a um arquivo específico que tenha uma expiração de 10 minutos. Qualquer pessoa que possua o token enquanto o token for válido tem acesso somente leitura a esse arquivo para esses 10 minutos. Atualmente, as chaves de assinatura de acesso compartilhado têm suporte apenas por meio da API REST ou nas bibliotecas de cliente. Você deve montar o compartilhamento de arquivos do Azure via SMB usando as chaves da conta de armazenamento.

    - Sincronização de Arquivos do Azure preserva e Replica todas as ACLs condicionais, ou DACLs, (seja baseada em Active Directory ou local) para todos os pontos de extremidade do servidor para os quais ele está sincronizado. Como o Windows Server já pode se autenticar com o Active Directory, Sincronização de Arquivos do Azure é uma opção de intervalo de parada efetiva até que o suporte completo para autenticação baseada em Active Directory e suporte a ACL chegue.

* <a id="ad-support-regions"></a>
**A versão prévia do Azure AD sobre SMB para arquivos do Azure está disponível em todas as regiões do Azure?**

    A visualização está disponível em todas as regiões públicas.

* <a id="ad-support-on-premises"></a>
**A autenticação do Azure AD sobre SMB para arquivos do Azure (versão prévia) dá suporte à autenticação usando o Azure AD de máquinas locais?**

    Não, os arquivos do Azure não dão suporte à autenticação com o Azure AD de computadores locais na versão de visualização.

* <a id="ad-support-devices"></a>
**A autenticação do Azure AD sobre SMB para arquivos do Azure (versão prévia) dá suporte ao acesso SMB usando as credenciais do Azure AD de dispositivos ingressados ou registrados com o Azure AD?**

    Não, esse cenário não tem suporte.

* <a id="ad-support-rest-apis"></a>
**Há APIs REST para dar suporte a ACLs de NTFS de diretório/arquivo Get/Set/Copy?**

    A versão de visualização não dá suporte a APIs REST para obter, definir ou copiar ACLs NTFS para diretórios ou arquivos.

* <a id="ad-vm-subscription"></a>
**Posso acessar os arquivos do Azure com as credenciais do Azure AD de uma VM em uma assinatura diferente?**

    Se a assinatura sob a qual o compartilhamento de arquivos está implantado estiver associada ao mesmo locatário do Azure AD que a implantação de Azure AD Domain Services na qual a VM está ingressada no domínio, você poderá acessar os arquivos do Azure usando as mesmas credenciais do Azure AD. A limitação é imposta não na assinatura, mas no locatário do Azure AD associado.    
    
* <a id="ad-support-subscription"></a>
**Posso habilitar a autenticação do Azure AD sobre SMB para arquivos do Azure com um locatário do Azure AD diferente do locatário primário ao qual o compartilhamento de arquivos está associado?**

    Não, os arquivos do Azure dão suporte apenas à integração do Azure AD com um locatário do Azure AD que reside na mesma assinatura que o compartilhamento de arquivos. Somente uma assinatura pode ser associada a um locatário do Azure AD.

* <a id="ad-linux-vms"></a>
**A autenticação do Azure AD sobre SMB para arquivos do Azure (versão prévia) dá suporte a VMs do Linux?**

    Não, a autenticação de VMs do Linux não tem suporte na versão de visualização.

* <a id="ad-aad-smb-afs"></a>
**Posso aproveitar a autenticação do Azure AD sobre os recursos SMB em compartilhamentos de arquivos gerenciados pelo Sincronização de Arquivos do Azure?**

    Não, os arquivos do Azure não oferecem suporte à preservação de ACLs NTFS em compartilhamentos de arquivos gerenciados pelo Sincronização de Arquivos do Azure. As ACLs de arquivo transportadas de servidores de arquivos locais são mantidas por Sincronização de Arquivos do Azure. Todas as ACLs NTFS configuradas nativamente em relação aos arquivos do Azure serão substituídas pelo serviço Sincronização de Arquivos do Azure. Além disso, os arquivos do Azure não dão suporte à autenticação com as credenciais do Azure AD para acesso a compartilhamentos de arquivos gerenciados pelo serviço de Sincronização de Arquivos do Azure.

* <a id="encryption-at-rest"></a>
**Como garantir que meu compartilhamento de arquivos do Azure seja criptografado em repouso?**  

    Sim. Para obter mais informações, consulte [criptografia do serviço de armazenamento do Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Como posso fornecer acesso a um arquivo específico usando um navegador da Web?**  

    Você pode usar assinaturas de acesso compartilhado para gerar tokens que têm permissões específicas e que são válidas por um intervalo de tempo especificado. Por exemplo, você pode gerar um token que fornece acesso somente leitura a um arquivo específico, por um período de tempo definido. Qualquer pessoa que possua a URL pode acessar o arquivo diretamente de qualquer navegador da Web enquanto o token é válido. Você pode gerar facilmente uma chave de assinatura de acesso compartilhado de uma interface do usuário como Gerenciador de Armazenamento.

* <a id="file-level-permissions"></a>
**É possível especificar permissões somente leitura ou somente gravação em pastas no compartilhamento?**  

    Se você montar o compartilhamento de arquivos usando SMB, não terá controle no nível de pasta sobre permissões. No entanto, se você criar uma assinatura de acesso compartilhado usando a API REST ou as bibliotecas de cliente, poderá especificar permissões somente leitura ou somente gravação em pastas no compartilhamento.

* <a id="ip-restrictions"></a>
**Posso implementar restrições de IP para um compartilhamento de arquivos do Azure?**  

    Sim. O acesso ao compartilhamento de arquivos do Azure pode ser restringido no nível da conta de armazenamento. Para obter mais informações, consulte [configurar redes virtuais e firewalls de armazenamento do Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**A quais políticas de conformidade de dados os arquivos do Azure dão suporte?**  

   Os arquivos do Azure são executados na parte superior da mesma arquitetura de armazenamento usada em outros serviços de armazenamento no armazenamento do Azure. Os arquivos do Azure aplicam as mesmas políticas de conformidade de dados que são usadas em outros serviços de armazenamento do Azure. Para obter mais informações sobre a conformidade de dados do armazenamento do Azure, você pode consultar as [ofertas de conformidade do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)e ir para a [central de confiabilidade da Microsoft](https://microsoft.com/trustcenter/default.aspx).

## <a name="on-premises-access"></a>Acesso local

* <a id="port-445-blocked"></a>
**Meu ISP ou bloqueia a porta 445 que está falhando na montagem de arquivos do Azure. O que devo fazer?**

    Você pode aprender sobre [várias maneiras de solucionar a porta de solução de bloqueio 445 aqui](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked). Os arquivos do Azure só permitem conexões usando SMB 3,0 (com suporte de criptografia) de fora da região ou Datacenter. O protocolo SMB 3,0 introduziu muitos recursos de segurança, incluindo a criptografia de canal, que é muito segura para uso pela Internet. No entanto, é possível que a porta 445 tenha sido bloqueada devido a motivos históricos de vulnerabilidades encontradas em versões SMB inferiores. No caso ideal, a porta deve ser bloqueada apenas para o tráfego SMB 1,0 e o SMB 1,0 deve ser desativado em todos os clientes.

* <a id="expressroute-not-required"></a>
**É necessário usar o Azure ExpressRoute para se conectar aos arquivos do Azure ou para usar Sincronização de Arquivos do Azure local?**  

    Não. O ExpressRoute não é necessário para acessar um compartilhamento de arquivos do Azure. Se você estiver montando um compartilhamento de arquivos do Azure diretamente no local, tudo o que é necessário é ter a porta 445 (TCP de saída) aberta para acesso à Internet (essa é a porta que o SMB usa para se comunicar). Se você estiver usando Sincronização de Arquivos do Azure, tudo o que é necessário é a porta 443 (TCP de saída) para acesso HTTPS (nenhum SMB é necessário). No entanto, você *pode* usar o ExpressRoute com qualquer uma dessas opções de acesso.

* <a id="mount-locally"></a>
**Como posso montar um compartilhamento de arquivos do Azure no meu computador local?**  

    Você pode montar o compartilhamento de arquivos usando o protocolo SMB se a porta 445 (TCP de saída) estiver aberta e o cliente der suporte ao protocolo SMB 3,0 (por exemplo, se você estiver usando o Windows 10 ou o Windows Server 2016). Se a porta 445 estiver bloqueada pela política da sua organização ou por seu ISP, você poderá usar Sincronização de Arquivos do Azure para acessar o compartilhamento de arquivos do Azure.

## <a name="backup"></a>Criar cópia de segurança
* <a id="backup-share"></a>
**Como fazer fazer backup do meu compartilhamento de arquivos do Azure?**  
    Você pode usar instantâneos de [compartilhamento](storage-snapshots-files.md) periódicos para proteção contra exclusões acidentais. Você também pode usar o AzCopy, o Robocopy ou uma ferramenta de backup de terceiros que pode fazer backup de um compartilhamento de arquivos montado. O backup do Azure oferece backup de arquivos do Azure. Saiba mais sobre como [fazer backup de compartilhamentos de arquivos do Azure pelo backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Instantâneos de compartilhamento

### <a name="share-snapshots-general"></a>Instantâneos de compartilhamento: Geral
* <a id="what-are-snaphots"></a>
**O que são instantâneos de compartilhamento de arquivos?**  
    Você pode usar instantâneos de compartilhamento de arquivos do Azure para criar uma versão somente leitura de seus compartilhamentos de arquivos. Você também pode usar os arquivos do Azure para copiar uma versão anterior do seu conteúdo de volta para o mesmo compartilhamento, para um local alternativo no Azure ou no local para obter mais modificações. Para saber mais sobre instantâneos de compartilhamento, confira [visão geral do instantâneo de compartilhamento](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Onde os meus instantâneos de compartilhamento são armazenados?**  
    Os instantâneos de compartilhamento são armazenados na mesma conta de armazenamento que o compartilhamento de arquivos.

* <a id="snapshot-consistency"></a>
**Os instantâneos de compartilhamento são consistentes com o aplicativo?**  
    Não, os instantâneos de compartilhamento não são consistentes com o aplicativo. O usuário deve liberar as gravações do aplicativo para o compartilhamento antes de tirar o instantâneo de compartilhamento.

* <a id="snapshot-limits"></a>
**Há limites para o número de instantâneos de compartilhamento que posso usar?**  
    Sim. Os arquivos do Azure podem reter um máximo de 200 instantâneos de compartilhamento. Os instantâneos de compartilhamento não contam para a cota de compartilhamento, portanto, não há nenhum limite por compartilhamento no espaço total usado por todos os instantâneos de compartilhamento. Limites de conta de armazenamento ainda se aplicam. Após 200 instantâneos de compartilhamento, você deve excluir instantâneos mais antigos para criar novos instantâneos de compartilhamento.

* <a id="snapshot-cost"></a>
**Quanto custam os instantâneos de compartilhamento?**  
    A transação padrão e o custo de armazenamento padrão serão aplicados ao instantâneo. Os instantâneos são incrementais por natureza. O instantâneo base é o próprio compartilhamento. Todos os instantâneos subsequentes são incrementais e só armazenarão a comparação do instantâneo anterior. Isso significa que as alterações delta que serão vistas na fatura serão mínimas se sua variação de carga de trabalho for mínima. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter informações de preços de arquivos padrão do Azure. Hoje, a maneira de examinar o tamanho consumido pelo instantâneo de compartilhamento é comparando a capacidade cobrada com capacidade usada. Estamos trabalhando para ferramentas para melhorar o relatório.

* <a id="ntfs-acls-snaphsots"></a>
**As ACLs de NTFS em diretórios e arquivos persistem em instantâneos de compartilhamento?**
    As ACLs de NTFS em diretórios e arquivos são mantidas em instantâneos de compartilhamento.

### <a name="create-share-snapshots"></a>Criar instantâneos de compartilhamento
* <a id="file-snaphsots"></a>
**Posso criar instantâneo de compartilhamento de arquivos individuais?**  
    Os instantâneos de compartilhamento são criados no nível de compartilhamento de arquivos. Você pode restaurar arquivos individuais do instantâneo de compartilhamento de arquivos, mas não pode criar instantâneos de compartilhamento no nível de arquivo. No entanto, se você tiver feito um instantâneo de compartilhamento de nível de compartilhamento e desejar listar instantâneos de compartilhamento em que um arquivo específico foi alterado, poderá fazer isso em **versões anteriores** em um compartilhamento montado pelo Windows. 
    
    Se você precisar de um recurso de instantâneo de arquivo, informe-nos no UserVoice de [arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Posso criar instantâneos de compartilhamento de um compartilhamento de arquivos criptografado?**  
    Você pode tirar um instantâneo de compartilhamento de compartilhamentos de arquivos do Azure que têm criptografia em repouso habilitada. Você pode restaurar arquivos de um instantâneo de compartilhamento para um compartilhamento de arquivos criptografados. Se o compartilhamento for criptografado, o instantâneo de compartilhamento também será criptografado.

* <a id="geo-redundant-snaphsots"></a>
**Meus instantâneos de compartilhamento são redundantes geograficamente?**  
    Os instantâneos de compartilhamento têm a mesma redundância que o compartilhamento de arquivos do Azure para o qual foram feitos. Se você tiver selecionado armazenamento com redundância geográfica para sua conta, o instantâneo de compartilhamento também será armazenado com redundância na região emparelhada.

### <a name="manage-share-snapshots"></a>Gerenciar instantâneos de compartilhamento
* <a id="browse-snapshots-linux"></a>
**Posso procurar meus instantâneos de compartilhamento do Linux?**  
    Você pode usar CLI do Azure para criar, listar, procurar e restaurar instantâneos de compartilhamento no Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Posso copiar os instantâneos de compartilhamento para uma conta de armazenamento diferente?**  
    Você pode copiar arquivos de instantâneos de compartilhamento para outro local, mas não pode copiar os próprios instantâneos de compartilhamento.

### <a name="restore-data-from-share-snapshots"></a>Restaurar dados de instantâneos de compartilhamento
* <a id="promote-share-snapshot"></a>
**Posso promover um instantâneo de compartilhamento para o compartilhamento base?**  
    Você pode copiar dados de um instantâneo de compartilhamento para qualquer outro destino. Você não pode promover um instantâneo de compartilhamento para o compartilhamento base.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Posso restaurar dados do meu instantâneo de compartilhamento para uma conta de armazenamento diferente?**  
    Sim. Os arquivos de um instantâneo de compartilhamento podem ser copiados para o local original ou para um local alternativo que inclua a mesma conta de armazenamento ou uma conta de armazenamento diferente, na mesma região ou em regiões diferentes. Você também pode copiar arquivos para uma localização local ou para qualquer outra nuvem.    
  
### <a name="clean-up-share-snapshots"></a>Limpar instantâneos de compartilhamento
* <a id="delete-share-keep-snapshots"></a>
**Posso excluir meu compartilhamento, mas não excluir meus instantâneos de compartilhamento?**  
    Se você tiver instantâneos de compartilhamento ativos em seu compartilhamento, não será possível excluir seu compartilhamento. Você pode usar uma API para excluir instantâneos de compartilhamento, juntamente com o compartilhamento. Você também pode excluir os instantâneos de compartilhamento e o compartilhamento no portal do Azure.

* <a id="delete-share-with-snapshots"></a>
**O que acontecerá com meus instantâneos de compartilhamento se eu excluir minha conta de armazenamento?**  
    Se você excluir sua conta de armazenamento, os instantâneos de compartilhamento também serão excluídos.

## <a name="billing-and-pricing"></a>Cobrança e preços
* <a id="vm-file-share-network-traffic"></a>
**O tráfego de rede entre uma VM do Azure e um compartilhamento de arquivos do Azure conta como largura de banda externa cobrada na assinatura?**  
    Se o compartilhamento de arquivos e a VM estiverem na mesma região do Azure, não haverá nenhum custo adicional para o tráfego entre o compartilhamento de arquivos e a VM. Se o compartilhamento de arquivos e a VM estiverem em regiões diferentes, o tráfego entre eles será cobrado como largura de banda externa.

* <a id="share-snapshot-price"></a>
**Quanto custam os instantâneos de compartilhamento?**  
     Durante a visualização, não há nenhum custo para a capacidade do instantâneo de compartilhamento. Os custos de saída e transação de armazenamento padrão se aplicam. Após a disponibilidade geral, as assinaturas serão cobradas pela capacidade e pelas transações em instantâneos de compartilhamento.
     
     Os instantâneos de compartilhamento são incrementais por natureza. O instantâneo de compartilhamento base é o próprio compartilhamento. Todos os instantâneos de compartilhamento subsequentes são incrementais e armazenam apenas a diferença do instantâneo de compartilhamento anterior. Você é cobrado apenas pelo conteúdo alterado. Se você tiver um compartilhamento com 100 GiB de dados, mas apenas 5 GiB forem alterados desde o último instantâneo de compartilhamento, o instantâneo de compartilhamento consumirá apenas 5 GiB adicionais e você será cobrado por 105 GiB. Para obter mais informações sobre cobranças de transações e de saída padrão, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Dimensionamento e desempenho
* <a id="files-scale-limits"></a>
**Quais são os limites de escala dos arquivos do Azure?**  
    Para obter informações sobre escalabilidade e metas de desempenho para arquivos do Azure, consulte [metas de desempenho e escalabilidade de arquivos do Azure](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Quais tamanhos estão disponíveis para compartilhamentos de arquivos do Azure?**  
    Os tamanhos de compartilhamento de arquivos do Azure (Premium e Standard) podem ser escalados verticalmente para 100 TiB. Os compartilhamentos de arquivos premium de até 100 TiB estão disponíveis como uma oferta GA. Os tamanhos de compartilhamento de arquivos padrão de até 5 TiB estão disponíveis como uma oferta GA, enquanto os tamanhos de até 100 TiB estão em versão prévia. Consulte a seção [integração a compartilhamentos de arquivos maiores (camada Standard)](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) do guia de planejamento para obter instruções de integração à versão prévia de compartilhamentos de arquivos maiores para a camada Standard.

* <a id="open-handles-quota"></a>
**Quantos clientes podem acessar o mesmo arquivo simultaneamente?**    
    Há uma cota de 2.000 identificadores abertos em um único arquivo. Quando você tem 2.000 identificadores abertos, uma mensagem de erro é exibida dizendo que a cota é atingida.

* <a id="zip-slow-performance"></a>
**O desempenho é lento quando eu descompacto arquivos em arquivos do Azure. O que devo fazer?**  
    Para transferir grandes números de arquivos para arquivos do Azure, recomendamos que você use AzCopy (para Windows; em versão prévia para Linux e UNIX) ou Azure PowerShell. Essas ferramentas foram otimizadas para transferência de rede.

* <a id="slow-perf-windows-81-2012r2"></a>
**Por que o desempenho está lento depois de montar meu compartilhamento de arquivos do Azure no Windows Server 2012 R2 ou Windows 8.1?**  
    Há um problema conhecido ao montar um compartilhamento de arquivos do Azure no Windows Server 2012 R2 e Windows 8.1. O problema foi corrigido na atualização cumulativa de abril de 2014 para o Windows 8.1 e o Windows Server 2012 R2. Para obter o desempenho ideal, verifique se todas as instâncias do Windows Server 2012 R2 e Windows 8.1 têm esse patch aplicado. (Você sempre deve receber patches do Windows por meio do Windows Update.) Para obter mais informações, consulte o artigo associado da base de dados de conhecimento Microsoft sobre o [desempenho lento ao acessar os arquivos do Azure do Windows 8.1 ou do Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Recursos e interoperabilidade com outros serviços
* <a id="cluster-witness"></a>
**Posso usar meu compartilhamento de arquivos do Azure como uma *testemunha de compartilhamento de arquivos* para meu cluster de failover do Windows Server?**  
    Atualmente, não há suporte para essa configuração em um compartilhamento de arquivos do Azure. Para obter mais informações sobre como configurar isso para o armazenamento de BLOBs do Azure, consulte [implantar uma testemunha em nuvem para um cluster de failover](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Posso montar um compartilhamento de arquivos do Azure em uma instância de contêiner do Azure?**  
    Sim, os compartilhamentos de arquivos do Azure são uma boa opção quando você deseja manter informações além do tempo de vida de uma instância de contêiner. Para obter mais informações, consulte [montar um compartilhamento de arquivos do Azure com instâncias de contêiner do Azure](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Há uma operação de renomeação na API REST?**  
    Neste momento, não.

* <a id="nested-shares"></a>
**Posso configurar compartilhamentos aninhados? Em outras palavras, um compartilhamento em um compartilhamento?**  
    Não. O compartilhamento de arquivos *é* o driver virtual que você pode montar; portanto, não há suporte para compartilhamentos aninhados.

* <a id="ibm-mq"></a>
**Como fazer usar os arquivos do Azure com o IBM MQ?**  
    A IBM lançou um documento que ajuda os clientes do IBM MQ a configurar os arquivos do Azure com o serviço IBM. Para obter mais informações, consulte [como configurar um Gerenciador de filas de várias instâncias do IBM MQ com o serviço de Microsoft Azure arquivos](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Consulte também
* [Solucionar problemas de arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Solucionar problemas de arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Resolver problemas do Azure File Sync](storage-sync-files-troubleshoot.md)
