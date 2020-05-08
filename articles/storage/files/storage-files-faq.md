---
title: Perguntas frequentes (FAQ) para Ficheiros Azure / Microsoft Docs
description: Encontre respostas para perguntas frequentes sobre ficheiros Azure.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 5aee3a19e33204da00483d0f4ee3f6ee97e8a07d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856264"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Perguntas mais frequentes (FAQ) sobre os Ficheiros do Azure
[O Azure Files](storage-files-introduction.md) oferece partilhas de ficheiros totalmente geridas na nuvem que são acessíveis através do protocolo do Bloco de Mensagens de [Servidor (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)padrão da indústria. Pode montar partilhas de ficheiros Azure simultaneamente em implementações em nuvem ou no local de implementações de Windows, Linux e macOS. Também pode cache as partilhas de ficheiros Azure nas máquinas do Windows Server utilizando o Azure File Sync para acesso rápido perto do local onde os dados são utilizados.

Este artigo responde a perguntas comuns sobre funcionalidades e funcionalidades do Azure Files, incluindo a utilização do Azure File Sync com ficheiros Azure. Se não vir a resposta à sua pergunta, pode contactar-nos através dos seguintes canais (por ordem crescente):

1. A secção de comentários deste artigo.
2. Fórum de [Armazenamento Azure.](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Suporte da Microsoft. Para criar um novo pedido de suporte, no portal Azure, no separador **Ajuda,** selecione o botão **de suporte Ajuda +** e, em seguida, selecione Novo pedido de **suporte**.

## <a name="general"></a>Geral
* <a id="why-files-useful"></a>
  **Como é útil o Azure Files?**  
   Pode utilizar ficheiros Azure para criar partilhas de ficheiros na nuvem, sem ser responsável pela gestão da sobrecarga de um servidor, dispositivo ou aparelho físico. Fazemos o trabalho monótono para si, incluindo a aplicação de atualizações de SO e a substituição de discos ruins. Para saber mais sobre os cenários com os quais o Azure Files pode ajudá-lo, consulte [por que razão os Ficheiros Azure são úteis](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Quais são as diferentes formas de aceder a ficheiros em Ficheiros Azure?**  
    Pode montar a partilha de ficheiros na sua máquina local utilizando o protocolo SMB 3.0, ou pode utilizar ferramentas como [o Storage Explorer](https://storageexplorer.com/) para aceder a ficheiros na sua partilha de ficheiros. A partir da sua aplicação, pode utilizar bibliotecas de clientes de armazenamento, APIs REST, PowerShell ou Azure CLI para aceder aos seus ficheiros na partilha de ficheiros Azure.

* <a id="what-is-afs"></a>
  **O que é O Sincronizado de Ficheiros Azure?**  
    Pode utilizar o Azure File Sync para centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma as suas máquinas do Windows Server numa cache rápida da partilha de ficheiros Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, Sistema de Ficheiros de Rede (NFS) e Serviço de Protocolo de Transferência de Ficheiros (FTPS). Podes ter as caches que precisares em todo o mundo.

* <a id="files-versus-blobs"></a>
  **Porque usaria uma partilha de ficheiros Azure contra o armazenamento do Azure Blob para os meus dados?**  
    Os Ficheiros Azure e o armazenamento da Blob Azure oferecem formas de armazenar grandes quantidades de dados na nuvem, mas são úteis para fins ligeiramente diferentes. 
    
    O armazenamento azure Blob é útil para aplicações nativas em escala maciça que precisam armazenar dados não estruturados. Para maximizar o desempenho e a escala, o armazenamento Azure Blob é uma abstração de armazenamento mais simples do que um verdadeiro sistema de ficheiros. Só pode aceder ao armazenamento do Azure Blob através de bibliotecas de clientes baseadas em REST (ou diretamente através do protocolo baseado em REST).

    O Azure Files é especificamente um sistema de ficheiros. O Azure Files tem todos os resumos de ficheiros que conhece e adora desde anos a trabalhar com sistemas operativos no local. Tal como o armazenamento azure blob, o Azure Files oferece uma interface REST e bibliotecas de clientes baseadas em REST. Ao contrário do armazenamento da Azure Blob, a Azure Files oferece acesso sMB a ações de ficheiros Azure. Ao utilizar o SMB, pode montar uma partilha de ficheiros Azure diretamente no Windows, Linux ou macOS, quer no local quer em VMs em nuvem, sem escrever qualquer código ou anexar quaisquer controladores especiais ao sistema de ficheiros. Também pode cache as partilhas de ficheiros Azure em servidores de ficheiros no local, utilizando o Azure File Sync para acesso rápido, perto do local onde os dados são utilizados. 
   
    Para obter uma descrição mais aprofundada sobre as diferenças entre os Ficheiros Azure e o armazenamento de Blob Azure, consulte [introdução aos principais serviços](../common/storage-introduction.md)de armazenamento do Azure . Para saber mais sobre o armazenamento azure blob, consulte [Introdução ao armazenamento Blob](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Por que usaria uma parte de ficheiro Azure em vez de Discos Azure?**  
    Um disco em Discos Azure é simplesmente um disco. Para obter valor da Azure Disks, tem de fixar um disco a uma máquina virtual que está a funcionar em Azure. Os Discos Azure podem ser usados para tudo o que utilizaria num servidor no local. Pode usá-lo como um disco de sistema sO, como espaço de troca para um SISTEMA, ou como armazenamento dedicado para uma aplicação. Um uso interessante para discos Azure é criar um servidor de ficheiros na nuvem para usar nos mesmos locais onde você pode usar uma partilha de ficheiros Azure. A implementação de um servidor de ficheiros em Máquinas Virtuais Azure é uma forma de obter armazenamento de ficheiros em Azure quando necessita de opções de implementação que atualmente não são suportadas por Ficheiros Azure (como suporte ao protocolo NFS ou armazenamento premium). 

    No entanto, executar um servidor de ficheiros com Discos Azure como armazenamento de back-end normalmente é muito mais caro do que usar uma partilha de ficheiros Azure, por algumas razões. Em primeiro lugar, para além de pagar o armazenamento em disco, também deve pagar as despesas de execução de um ou mais VMs Azure. Em segundo lugar, também deve gerir os VMs que são usados para executar o servidor de ficheiros. Por exemplo, é responsável pelas atualizações de OS. Finalmente, se, em última análise, necessitar de dados para serem emcache no local, cabe-lhe a si configurar e gerir tecnologias de replicação, como a Replicação do Sistema de Ficheiros Distribuídos (DFSR), para que isso aconteça.

    Uma abordagem para obter o melhor de ambos os Ficheiros Azure e um servidor de ficheiros que está hospedado em Máquinas Virtuais Azure (além de usar discos Azure como armazenamento de back-end) é instalar o Azure File Sync num servidor de ficheiros que está hospedado num VM em nuvem. Se a partilha de ficheiros Azure estiver na mesma região que o seu servidor de ficheiros, pode ativar o tiering em nuvem e definir o volume de percentagem de espaço livre para máximo (99%). Isto garante uma duplicação mínima de dados. Também pode utilizar quaisquer aplicações que pretenda com os seus servidores de ficheiros, como aplicações que requerem suporte ao protocolo NFS.

    Para obter informações sobre uma opção para configurar um servidor de ficheiros de alto desempenho e altamente disponível no Azure, consulte a implementação de clusters de [hóspedes IaaS VM no Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Para obter uma descrição mais aprofundada das diferenças entre ficheiros Azure e Discos Azure, consulte [Introdução aos principais serviços](../common/storage-introduction.md)de armazenamento do Azure . Para saber mais sobre os Discos Azure, consulte a [visão geral dos Discos Geridos de Azure](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
  **Como posso começar a usar ficheiros Azure?**  
   Começar com ficheiros Azure é fácil. Primeiro, [crie uma partilha](storage-how-to-create-file-share.md)de ficheiros e, em seguida, monte-a no seu sistema operativo preferido: 

  * [Monte em Janelas](storage-how-to-use-files-windows.md)
  * [Monte em Linux](storage-how-to-use-files-linux.md)
  * [Monte no macOS](storage-how-to-use-files-mac.md)

    Para obter um guia mais aprofundado sobre a implementação de uma quota de ficheiros Azure para substituir as ações de ficheiros de produção na sua organização, consulte o Planeamento para uma implementação de [Ficheiros Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Que opções de redundância de armazenamento são suportadas pela Azure Files?**  
    Atualmente, a Azure Files suporta armazenamento redundante localmente (LRS), armazenamento redundante de zona (ZRS), armazenamento geo-redundante (GRS) e armazenamento geo-zona redundante (GZRS). Planeamos apoiar o armazenamento geo-redundante de acesso à leitura (RA-GRS) no futuro, mas não temos prazos para partilhar neste momento.

* <a id="tier-options"></a>
  **Que níveis de armazenamento são suportados em Ficheiros Azure?**  
    A Azure Files suporta dois níveis de armazenamento: premium e standard. As ações de ficheiropadrão são criadas em contas de armazenamento de finalidade geral (GPv1 ou GPv2) e as ações de ficheiropremium são criadas em contas de armazenamento de FileStorage. Saiba mais sobre como criar ações de [ficheiros padrão](storage-how-to-create-file-share.md) e ações de [ficheiros premium.](storage-how-to-create-premium-fileshare.md) 
    
    > [!NOTE]
    > Não é possível criar ações de ficheiroS Azure a partir de contas de armazenamento Blob ou de contas de armazenamento de finalidade geral *premium* (GPv1 ou GPv2). As ações de ficheiros Standard Azure devem ser criadas apenas em contas de finalidade geral *padrão* e as ações de ficheiros Premium Azure devem ser criadas apenas nas contas de armazenamento de FileStorage. *As* contas de armazenamento premium (GPv1 e GPv2) são apenas para bolhas de página premium. 

* <a id="give-us-feedback"></a>
  **Quero ver uma funcionalidade específica adicionada aos Ficheiros Azure. Pode adicioná-lo?**  
    A equipa do Azure Files está interessada em ouvir todos os comentários que tiver sobre o nosso serviço. Por favor, vote nos pedidos de funcionalidades no [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Estamos ansiosos para encantar-lhe com muitas novidades.

  **Os Ficheiros Azure suportam o bloqueio de ficheiros?**  
    Sim, o Azure Files suporta totalmente o bloqueio de ficheiros sMB/Windows, [ver detalhes](https://docs.microsoft.com/rest/api/storageservices/managing-file-locks). 
    
## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Que regiões são apoiadas para o Azure File Sync?**  
    A lista de regiões disponíveis pode ser consultada na secção de disponibilidade da [Região](storage-sync-files-planning.md#azure-file-sync-region-availability) do guia de planeamento do Sincronizado de Ficheiros Azure. Vamos continuar a apoiar regiões adicionais, incluindo regiões não públicas.

* <a id="cross-domain-sync"></a>
  **Posso ter servidores unidos e não unidos pelo domínio no mesmo grupo de sincronização?**  
    Sim. Um grupo de sincronização pode conter pontos finais do servidor que têm diferentes membros do Ative Directory, mesmo que não estejam unidos pelo domínio. Embora esta configuração funcione tecnicamente, não recomendamos esta configuração como uma configuração típica porque as listas de controlo de acesso (ACLs) que são definidas para ficheiros e pastas num servidor podem não ser capazes de ser aplicadas por outros servidores do grupo de sincronização. Para obter os melhores resultados, recomendamos sincronizar entre servidores que se encontram na mesma floresta de Diretório Ativo, entre servidores que se encontram em diferentes florestas de Diretório Ativo, mas que estabeleceram relações de confiança, ou entre servidores que não estão num domínio. Recomendamos que evite utilizar uma mistura destas configurações.

* <a id="afs-change-detection"></a>
  **Criei um ficheiro diretamente na minha partilha de ficheiros Azure utilizando sMB ou no portal. Quanto tempo leva para o ficheiro sincronizar com os servidores do grupo de sincronização?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Se o mesmo ficheiro for alterado em dois servidores aproximadamente ao mesmo tempo, o que acontece?**  
    O Azure File Sync usa uma estratégia simples de resolução de conflitos: mantemos ambas as alterações nos ficheiros que são alteradas em dois servidores ao mesmo tempo. A mudança escrita mais recente mantém o nome original do ficheiro. O ficheiro mais antigo tem a máquina "fonte" e o número de conflito anexado ao nome. Segue-se esta taxonomia: 
   
    \<Nome de\>-\<ficheiros\>\[-#\]sem nome de máquina de extensão . \<ext\>  

    Por exemplo, o primeiro conflito de CompanyReport.docx tornar-se-ia CompanyReport-CentralServer.docx se o CentralServer fosse onde ocorreu a escrita mais antiga. O segundo conflito seria nomeado CompanyReport-CentralServer-1.docx. O Azure File Sync suporta 100 ficheiros de conflito por ficheiro. Uma vez atingido o número máximo de ficheiros de conflito, o ficheiro não sincronizará até que o número de ficheiros de conflito seja inferior a 100.

* <a id="afs-storage-redundancy"></a>
  **O armazenamento geo-redundante é suportado para o Azure File Sync?**  
    Sim, o Azure Files suporta armazenamento localmente redundante (LRS) e armazenamento geo-redundante (GRS). Se iniciar uma falha na conta de armazenamento entre regiões emparelhadas a partir de uma conta configurada para GRS, a Microsoft recomenda que trate a nova região apenas como uma cópia de segurança de dados. O Azure File Sync não começa automaticamente a sincronizar-se com a nova região primária. 

* <a id="sizeondisk-versus-size"></a>
  **Porque é que o tamanho na propriedade *do disco* para um ficheiro corresponde à propriedade *Size* depois de usar o Azure File Sync?**  
  Ver Compreender o [Cloud Tiering](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  **Como posso saber se um ficheiro foi niveto?**  
  Ver Compreender o [Cloud Tiering](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Um ficheiro que quero usar foi nivelamento. Como posso lembrar o ficheiro para o disco para usá-lo localmente?**  
  Ver Compreender o [Cloud Tiering](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **Como posso forçar um ficheiro ou diretório a ser niveado?**  
  Ver Compreender o [Cloud Tiering](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  **Como é interpretado *o espaço livre* de volume quando tenho vários pontos finais do servidor num volume?**  
  Ver Compreender o [Cloud Tiering](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
  **Quais ficheiros ou pastas são automaticamente excluídos pelo Azure File Sync?**  
  Ver [Ficheiros ignorados](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Posso usar o Bluee File Sync com o Windows Server 2008 R2, O Linux ou o meu dispositivo de armazenamento ligado à rede (NAS) ?**  
    Atualmente, o Azure File Sync suporta apenas o Windows Server 2019, o Windows Server 2016 e o Windows Server 2012 R2. Neste momento, não temos outros planos que possamos partilhar, mas estamos abertos a apoiar plataformas adicionais baseadas na procura do cliente. Informe-nos no [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) que plataformas gostaria que nós suportemos.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Por que existem ficheiros hierárquicos fora do espaço de nome final do servidor?**  
    Antes da versão 3 do agente Do Sincronia de Ficheiros Azure, o Azure File Sync bloqueou o movimento de ficheiros hierárquicos fora do ponto final do servidor, mas no mesmo volume que o ponto final do servidor. As operações de cópia, os movimentos de ficheiros não diferenciados e os movimentos de tiered para outros volumes não foram afetados. A razão para este comportamento foi a suposição implícita de que o File Explorer e outras APIs do Windows têm que mover operações no mesmo volume são (quase) operações instantâneas de renome. Isto significa que os movimentos farão com que o File Explorer ou outros métodos de movimento (como a linha de comando ou powerShell) não pareçam sem resposta enquanto o Azure File Sync recorda os dados da nuvem. A partir da [versão 3.0.12.0 do agente Doficheiro Azure,](storage-files-release-notes.md#supported-versions)o Azure File Sync permitir-lhe-á mover um ficheiro hierárquico para fora do ponto final do servidor. Evitamos os efeitos negativos anteriormente mencionados, permitindo que o ficheiro hierárquico exista como um ficheiro hierárquico fora do ponto final do servidor e, em seguida, recordando o ficheiro em segundo plano. Isto significa que os movimentos no mesmo volume são instantâneos, e fazemos todo o trabalho para relembrar o ficheiro ao disco após a mudança ter terminado. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Estou tendo um problema com O Sincronizado de Ficheiros Azure no meu servidor (sincronização, nível de nuvem, etc.). Devo remover e recriar o ponto final do meu servidor?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Posso mover o serviço de sincronização de armazenamento e/ou a conta de armazenamento para um grupo de recursos ou subscrição diferente?**  
   Sim, o serviço de sincronização de armazenamento e/ou conta de armazenamento podem ser transferidos para um grupo de recursos diferente ou subscrição dentro do atual inquilino da AD Azure. Se a conta de armazenamento for movida, tem de dar ao Serviço de Sincronização de Ficheiros Híbridos acesso à conta de armazenamento (ver [Certifique-se](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)de que o Ficheiro Sync do Azure tem acesso à conta de armazenamento).

    > [!Note]  
    > O Azure File Sync não suporta a deslocação da subscrição para um inquilino Azure AD diferente.
    
* <a id="afs-ntfs-acls"></a>
  **O Azure File Sync preserva os NTFS ACLs de nível de ficheiros, juntamente com os dados armazenados em Ficheiros Azure?**

    A partir de 24 de fevereiro de 2020, novos ACLs e existentes tiered by Azure file sync serão persistidos no formato NTFS, e as modificações acl feitas diretamente na parte de ficheiro sincronita do ficheiro Azure sincronizarão com todos os servidores do grupo de sincronização. Quaisquer alterações nos ACLs feitas aos Ficheiros Azure serão sincronizadas através da sincronização de ficheiros Azure. Ao copiar dados para Ficheiros Azure, certifique-se de que utiliza SMB para aceder à partilha e preservar os seus ACLs. As ferramentas baseadas em REST existentes, como a AzCopy ou o Storage Explorer, não persistem em ACLs.

    Se tiver ativado o Azure Backup nas suas partilhas de ficheiros geridas por sincronização de ficheiros, os ACLs de ficheiro podem continuar a ser restaurados como parte do fluxo de trabalho de restauro de cópia de segurança. Isto funciona quer para a totalidade da parte quer para ficheiros/diretórios individuais.

    Se estiver a utilizar instantâneos como parte da solução de backup autogerida para partilhas de ficheiros geridas por sincronização de ficheiros, os seus ACLs não podem ser restaurados adequadamente aos NTFS ACLs se as imagens forem tiradas antes de 24 de fevereiro de 2020. Se isto ocorrer, considere contactar o Suporte Azure.
    
## <a name="security-authentication-and-access-control"></a>Segurança, autenticação e controlo de acesso
* <a id="ad-support"></a>
**A autenticação baseada na identidade e o controlo de acesso são suportados por Ficheiros Azure?**  
    
    Sim, o Azure Files suporta a autenticação baseada na identidade e o controlo de acesso. Pode escolher uma de duas formas de utilizar o controlo de acesso baseado na identidade: serviços de domínio ativo de diretório ativo (pré-visualização) ou Serviços de Domínio de Diretório Ativo Azure (Azure AD DS). No local, os Serviços de Domínio ativo do Diretório Ativo (AD DS) suportam a autenticação utilizando máquinas ad ds filiadas no domínio, quer no local quer no Azure, para aceder a ações de ficheiros Azure sobre sMB. A autenticação Azure AD DS sobre SMB para Ficheiros Azure permite que os VMs do Windows filiados no domínio Azure AD DS acedam a partilhas, diretórios e ficheiros utilizando credenciais Azure AD. Para mais detalhes, consulte o suporte de [autenticação baseado na identidade do Azure Files para acesso](storage-files-active-directory-overview.md)a SMB . 

    O Azure Files oferece duas formas adicionais de gerir o controlo de acesso:

    - Pode utilizar assinaturas de acesso partilhado (SAS) para gerar fichas que tenham permissões específicas, e que são válidas para um intervalo de tempo especificado. Por exemplo, pode gerar um símbolo com acesso apenas de leitura a um ficheiro específico que tem uma expiração de 10 minutos. Qualquer um que possua o símbolo enquanto o símbolo é válido tem acesso apenas a esse ficheiro durante esses 10 minutos. As chaves de assinatura de acesso partilhado são suportadas apenas através da API REST ou nas bibliotecas de clientes. Deve montar a partilha de ficheiros Azure sobre sMB utilizando as chaves da conta de armazenamento.

    - O Azure File Sync preserva e replica todos os ACLs discricionários, ou DACLs, (baseados em Diretório Ativo ou locais) a todos os pontos finais do servidor a que se sincroniza. Uma vez que o Windows Server já pode autenticar com o Diretório Ativo, o Azure File Sync é uma opção de stop-gap eficaz até chegar o suporte total para autenticação baseada em Diretório ativo e suporte ACL.
    
    Pode consultar o [Autorizo o acesso ao Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para uma representação abrangente de todos os protocolos suportados nos serviços de Armazenamento Azure. 

* <a id="ad-support-devices"></a>
**A autenticação do Azure Files Azure Ative Directory Services (Azure AD DS) suporta o acesso sMB utilizando credenciais DeA Azure de dispositivos unidos ou registados com AD Azure?**

    Não, este cenário não é apoiado.

* <a id="ad-support-rest-apis"></a>
**Existem APIs REST para suportar O Diretório/Ficheiro NTFS ACLs?**

    Sim, apoiamos APIs REST que obtêm, set ou copiam NTFS ACLs para diretórios ou ficheiros ao utilizar a API REST [2019-07](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (ou posterior).

* <a id="ad-vm-subscription"></a>
**Posso aceder a ações de ficheiros Azure com credenciais Azure AD de um VM sob uma subscrição diferente?**

    Se a subscrição sob a qual a parte de ficheiro é implantada estiver associada ao mesmo inquilino Azure AD que a implantação azure AD DS à qual o VM é associado ao domínio, poderá aceder às ações de ficheiros Azure utilizando as mesmas credenciais Azure AD. A limitação é imposta não à subscrição, mas ao inquilino da AD Azure associado.
    
* <a id="ad-support-subscription"></a>
**Posso ativar a autenticação Azure AD DS ou no local para ações de ficheiros Azure utilizando um inquilino da Azure AD que é diferente do inquilino principal da partilha de ficheiros Azure?**

    Não, o Azure Files apenas suporta a integração da Azure AD DS ou no local ad dS com um inquilino Azure AD que reside na mesma subscrição que a partilha de ficheiros. Apenas uma subscrição pode ser associada a um inquilino da AD Azure. Esta limitação aplica-se tanto aos métodos de autenticação AD DS azure como no local. Ao utilizar a AD DS no local para autenticação, [a credencial AD DS deve ser sincronizada com o Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) a que a conta de armazenamento está associada.

* <a id="ad-linux-vms"></a>
**A Autenticação AD DS da Azure AD ou no local para ações de ficheiroS Azure suporta MD linux?**

    Não, a autenticação dos VMs Linux não é suportada.

* <a id="ad-aad-smb-afs"></a>
**As ações de ficheiro geridas pelo Azure File Sync suportam a autenticação Azure AD DS ou no local AD DS (pré-visualização).**

    Sim, pode ativar a autenticação AD DS azure ou no local no local numa partilha de ficheirogerida pelo Azure File Sync. As alterações aos NTFS ACLs de diretório/ficheiro nos servidores de ficheiros locais serão nivelado para Ficheiros Azure e vice-versa.

* <a id="ad-aad-smb-files"></a>
**Como posso verificar se permiti a autenticação aD DS na minha conta de armazenamento e recuperar as informações de domínio?**

    Para obter instruções, consulte [aqui](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#1-enable-ad-authentication-for-your-account).
    
* <a id="encryption-at-rest"></a>
**Como posso garantir que a minha partilha de ficheiros Azure esteja encriptada em repouso?**  

    Sim. Para mais informações consulte a Encriptação do Serviço de [Armazenamento Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Como posso fornecer acesso a um ficheiro específico usando um navegador web?**  

    Pode utilizar assinaturas de acesso partilhado para gerar fichas que tenham permissões específicas, e que são válidas para um intervalo de tempo especificado. Por exemplo, pode gerar um símbolo que dá acesso apenas a leitura a um ficheiro específico, durante um determinado período de tempo. Qualquer pessoa que possua o URL pode aceder ao ficheiro diretamente de qualquer navegador web enquanto o token é válido. Você pode facilmente gerar uma chave de assinatura de acesso partilhado a partir de um UI como Storage Explorer.

* <a id="file-level-permissions"></a>
**É possível especificar permissões apenas de leitura ou de escrita em pastas dentro da parte?**  

    Se montar a parte do ficheiro utilizando sMB, não tem controlo de nível de pasta sobre permissões. No entanto, se criar uma assinatura de acesso partilhado utilizando a API rest ou bibliotecas de clientes, pode especificar permissões apenas de leitura ou de escrita em pastas dentro da partilha.

* <a id="ip-restrictions"></a>
**Posso implementar restrições ip para uma partilha de ficheiros Azure?**  

    Sim. O acesso à sua quota de ficheiros Azure pode ser restringido ao nível da conta de armazenamento. Para mais informações, consulte [Configure Azure Storage Firewalls e Redes Virtuais](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Que políticas de conformidade de dados suporta o Azure Files?**  

   O Azure Files funciona em cima da mesma arquitetura de armazenamento que é usada em outros serviços de armazenamento em Armazenamento Azure. O Azure Files aplica as mesmas políticas de conformidade de dados que são utilizadas noutros serviços de armazenamento do Azure. Para mais informações sobre a conformidade com os dados do Armazenamento Azure, pode consultar as ofertas de conformidade do [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)e ir ao [Microsoft Trust Center](https://microsoft.com/trustcenter/default.aspx).
   
### <a name="ad-authentication"></a>Autenticação AD
* <a id=""></a>
**A autenticação AD Azure Azure Azure suporta VMs Linux?**

    Não, a autenticação dos VMs Linux não é suportada.

* <a id="ad-multiple-forest"></a>
**A autenticação AD DS no local para as partilhas de ficheiros Azure suporta a integração com um ambiente AD DS utilizando múltiplas florestas?**    

    A autenticação AD DS do Azure Files no local apenas se integra com a floresta do serviço de domínio a que a conta de armazenamento está registada. Para apoiar a autenticação de outra floresta, o seu ambiente deve ter um fundo florestal configurado corretamente. A forma como o Azure Files se regista no AD DS é quase o mesmo que um servidor de ficheiros regular, onde cria uma identidade (conta de logon de computador ou de serviço) em DS AD para autenticação. A única diferença é que o SPN registado da conta de armazenamento termina com "file.core.windows.net" que não corresponde ao sufixo de domínio. Consulte o seu administrador de domínio para ver se é necessária alguma atualização à sua política de encaminhamento DNS para permitir a autenticação múltipla da floresta devido ao sufixo de domínio diferente.

* <a id=""></a>
**Que regiões estão disponíveis para autenticação AD DS de Ficheiros Azure (pré-visualização)?**

    Consulte a disponibilidade regional da [AD DS](storage-files-identity-auth-active-directory-enable.md#regional-availability) para mais detalhes.
    
* <a id="ad-aad-smb-afs"></a>
**Posso alavancar a autenticação do Diretório Ativo do Azure Files (AD) (pré-visualização) em ações de ficheirogeridas pelo Azure File Sync?**

    Sim, pode ativar a autenticação de AD numa partilha de ficheiro gerida pela sincronização de ficheiros Azure. As alterações aos NTFS ACLs de diretório/ficheiro nos servidores de ficheiros locais serão nivelado para Ficheiros Azure e vice-versa.

* <a id="ad-aad-smb-files"></a>
**Como posso verificar se permiti a autenticação da AD na minha conta de armazenamento e nas informações de domínio da AD?**

    Pode consultar as instruções [aqui](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#enable-ad-authentication-for-your-account) fornecidas para validar se a Autenticação AD dos Ficheiros Azure estiver ativada na sua conta de armazenamento e recuperar as informações de domínio aD.

* <a id="ad-aad-smb-files"></a>
**Há alguma diferença na criação de uma conta de computador ou conta de logon de serviço para representar a minha conta de armazenamento em AD?**

    A criação de uma [conta de computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (predefinido) ou de uma conta de [login](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) de serviço não tem qualquer diferença na forma como a autenticação funcionaria com ficheiros Azure. Você pode fazer a sua própria escolha sobre como representar uma conta de armazenamento como uma identidade no seu ambiente de AD. O conjunto de DomainAccountType predefinido em Join-AzStorageAccountForAuth cmdlet é a conta de computador. No entanto, a idade de validade da palavra-passe configurada no seu ambiente ad pode ser diferente para a conta de logon de computador ou serviço e você precisa ter isso em consideração para [Atualizar a palavra-passe da sua identidade de conta de armazenamento em AD](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#5-update-ad-account-password).

## <a name="on-premises-access"></a>Acesso no local

* <a id="port-445-blocked"></a>
**O meu ISP ou IT bloqueia o Porto 445 que está a falhar no suporte do Azure Files. O que devo fazer?**

    Você pode aprender sobre [várias maneiras de contornar a porta bloqueada 445 aqui](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked). O Azure Files apenas permite ligações utilizando SMB 3.0 (com suporte de encriptação) de fora da região ou datacenter. O protocolo SMB 3.0 introduziu muitas funcionalidades de segurança, incluindo encriptação de canais, que é muito segura para usar através da internet. No entanto, é possível que o porto 445 tenha sido bloqueado devido a razões históricas de vulnerabilidades encontradas nas versões SMB mais baixas. No caso ideal, a porta deve ser bloqueada apenas para tráfego SMB 1.0 e SMB 1.0 deve ser desligada em todos os clientes.

* <a id="expressroute-not-required"></a>
**Tenho de usar o Azure ExpressRoute para ligar aos Ficheiros Azure ou utilizar o Azure File Sync no local?**  

    Não. O ExpressRoute não é obrigado a aceder a uma partilha de ficheiros Azure. Se estiver a montar uma partilha de ficheiros Azure diretamente no local, tudo o que é necessário é ter a porta 445 (saída de TCP) aberta para acesso à Internet (esta é a porta que a SMB usa para comunicar). Se estiver a utilizar o Azure File Sync, tudo o que é necessário é a porta 443 (saída de TCP) para acesso HTTPS (sem necessidade de SMB). No entanto, *pode* utilizar o ExpressRoute com qualquer uma destas opções de acesso.

* <a id="mount-locally"></a>
**Como posso montar uma parte de ficheiro azure na minha máquina local?**  

    Pode montar a parte do ficheiro utilizando o protocolo SMB se a porta 445 (saída de TCP) estiver aberta e o seu cliente suportar o protocolo SMB 3.0 (por exemplo, se estiver a utilizar o Windows 10 ou o Windows Server 2016). Se a porta 445 estiver bloqueada pela política da sua organização ou pelo seu ISP, pode utilizar o Azure File Sync para aceder à sua partilha de ficheiros Azure.

## <a name="backup"></a>Cópia de segurança
* <a id="backup-share"></a>
**Como reforço a minha parte do ficheiro Azure?**  
    Pode utilizar [instantâneos periódicos](storage-snapshots-files.md) para proteção contra supressões acidentais. Também pode utilizar a AzCopy, Robocopy ou uma ferramenta de backup de terceiros que possa fazer backup de uma partilha de ficheiros montada. O Azure Backup oferece cópias de segurança dos Ficheiros Azure. Saiba mais sobre as ações de [ficheiro supéris azure por Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Partilhar instantâneos

### <a name="share-snapshots-general"></a>Partilhar fotos: Geral
* <a id="what-are-snaphots"></a>
**O que são fotos de partilha de ficheiros?**  
    Pode utilizar imagens de partilha de ficheiros Azure para criar uma versão apenas de leitura das suas partilhas de ficheiros. Também pode utilizar o Azure Files para copiar uma versão anterior do seu conteúdo de volta à mesma parte, para uma localização alternativa em Azure ou no local para mais modificações. Para saber mais sobre fotos partilhadas, consulte a [visão geral](storage-snapshots-files.md)do Share snapshot .

* <a id="where-are-snapshots-stored"></a>
**Onde estão armazenadas as minhas fotos de partilha?**  
    As fotos de partilha são armazenadas na mesma conta de armazenamento que a parte do ficheiro.

* <a id="snapshot-consistency"></a>
**As imagens de partilha são consistentes com a aplicação?**  
    Não, as fotos de partilha não são consistentes com aplicações. O utilizador deve descarregar as escritas da aplicação para a parte antes de tirar o instantâneo da partilha.

* <a id="snapshot-limits"></a>
**Há limites no número de fotografias de partilha que posso usar?**  
    Sim. O Azure Files pode reter um máximo de 200 fotografias de partilha. As imagens de partilha não contam para a quota de ações, pelo que não existe um limite por ação no espaço total que é usado por todos os instantâneos de partilha. Os limites da conta de armazenamento ainda se aplicam. Depois de 200 instantâneos de partilha, deve eliminar fotografias mais antigas para criar novas imagens de partilha.

* <a id="snapshot-cost"></a>
**Quanto custam as fotos?**  
    A transação padrão e o custo padrão de armazenamento aplicar-se-ão ao instantâneo. As fotos são incrementais na natureza. O instantâneo base é a própria partilha. Todos os instantâneos subsequentes são incrementais e apenas armazenam a difusão do instantâneo anterior. Isto significa que as alterações delta que serão vistas na conta serão mínimas se a sua carga de trabalho for mínima. Consulte [a página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter informações sobre preços standard Azure Files. Hoje, a forma de ver o tamanho consumido pelo instantâneo de partilha é comparando a capacidade faturada com a capacidade usada. Estamos a trabalhar na ferramenta para melhorar o relatório.

* <a id="ntfs-acls-snaphsots"></a>
**Os NTFS ACLs em diretórios e ficheiros persistem em instantâneos de partilha?**  
    Os NTFS ACLs em diretórios e ficheiros são persistentes em instantâneos de partilha.

### <a name="create-share-snapshots"></a>Criar instantâneos de partilha
* <a id="file-snaphsots"></a>
**Posso criar imagens partilhadas de ficheiros individuais?**  
    As imagens de partilha são criadas ao nível da partilha de ficheiros. Pode restaurar ficheiros individuais a partir do instantâneo de partilha de ficheiros, mas não é possível criar imagens de partilha de nível de ficheiro. No entanto, se tiver tirado uma fotografia de partilha de nível de partilha e pretender listar fotografias de partilha onde um ficheiro específico mudou, pode fazê-lo em **Versões Anteriores** numa partilha montada no Windows. 
    
    Se precisar de uma funcionalidade de instantâneo de ficheiros, informe-nos no [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Posso criar fotos partilhadas de uma partilha de ficheiros encriptada?**  
    Pode tirar uma fotografia de partilha de partilhas de ficheiros Azure que tenham encriptação ativada. Pode restaurar ficheiros de uma fotografia de partilha para uma partilha de ficheiros encriptada. Se a sua parte estiver encriptada, o seu instantâneo de partilha também está encriptado.

* <a id="geo-redundant-snaphsots"></a>
**A minha quota-instantânea é geo-redundante?**  
    As fotos partilhadas têm o mesmo despedimento que a parte do ficheiro Azure para a qual foram tiradas. Se selecionou o armazenamento geo-redundante para a sua conta, o seu instantâneo de partilha também é armazenado redundantemente na região emparelhada.

### <a name="manage-share-snapshots"></a>Gerir instantâneos de partilha
* <a id="browse-snapshots-linux"></a>
**Posso ver as minhas fotos de linux?**  
    Pode utilizar o Azure CLI para criar, listar, navegar e restaurar fotografias de partilha em Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Posso copiar as fotos da partilha para uma conta de armazenamento diferente?**  
    Pode copiar ficheiros de imagens partilhadas para outro local, mas não pode copiar as próprias imagens de partilha.

### <a name="restore-data-from-share-snapshots"></a>Restaurar os dados a partir de instantâneos de partilha
* <a id="promote-share-snapshot"></a>
**Posso promover uma imagem de partilha para a parte base?**  
    Pode copiar dados de um instantâneo de partilha para qualquer outro destino. Não pode promover uma imagem de partilha para a parte base.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Posso restaurar os dados do meu instantâneo de partilha para uma conta de armazenamento diferente?**  
    Sim. Os ficheiros de um instantâneo de partilha podem ser copiados para a localização original ou para um local alternativo que inclua a mesma conta de armazenamento ou uma conta de armazenamento diferente, quer na mesma região, quer em diferentes regiões. Também pode copiar ficheiros para um local no local ou para qualquer outra nuvem.    
  
### <a name="clean-up-share-snapshots"></a>Limpe fotos de partilha
* <a id="delete-share-keep-snapshots"></a>
**Posso apagar a minha parte, mas não apagar as minhas fotos de partilha?**  
    Se tiver imagens de partilha ativas na sua parte, não pode eliminar a sua parte. Pode utilizar uma API para eliminar fotografias partilhadas, juntamente com a partilha. Também pode eliminar tanto as imagens de partilha como a parte no portal Azure.

* <a id="delete-share-with-snapshots"></a>
**O que acontece com as minhas fotos de partilha se eu apagar a minha conta de armazenamento?**  
    Se eliminar a sua conta de armazenamento, as imagens de partilha também são eliminadas.

## <a name="billing-and-pricing"></a>Faturação e preços
* <a id="vm-file-share-network-traffic"></a>
**O tráfego de rede entre um VM Azure e uma partilha de ficheiros Azure conta como largura de banda externa que é cobrada à subscrição?**  
    Se a parte do ficheiro e a VM estiverem na mesma região de Azure, não existe qualquer encargo adicional para o tráfego entre a parte do ficheiro e a VM. Se a parte do ficheiro e o VM estiverem em diferentes regiões, o tráfego entre eles é cobrado como largura de banda externa.

* <a id="share-snapshot-price"></a>
**Quanto custam as fotos?**  
     Durante a pré-visualização, não há qualquer custo para a capacidade de partilha de instantâneos. Aplicam-se os custos de armazenamento padrão e transações. Após a disponibilidade geral, as subscrições serão cobradas pela capacidade e transações em instantâneos de ações.
     
     Partilhar fotos são de natureza incremental. O instantâneo base é a própria partilha. Todos os instantâneos de partilha subsequentes são incrementais e armazenam apenas a diferença em relação ao instantâneo de partilha anterior. Só é cobrado pela mudança de conteúdo. Se tiver uma parte com 100 GiB de dados mas apenas 5 GiB mudou desde a sua última partilha, o instantâneo de partilha consome apenas 5 GiB adicionais, e você é cobrado por 105 GiB. Para obter mais informações sobre transações e taxas de saída padrão, consulte a [página de Preços](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Escala e desempenho
* <a id="files-scale-limits"></a>
**Quais são os limites de escala dos Ficheiros Azure?**  
    Para obter informações sobre a escalabilidade e os alvos de desempenho dos Ficheiros Azure, consulte [a escalabilidade e os alvos](storage-files-scale-targets.md)de desempenho do Azure Files .

* <a id="need-larger-share"></a>
**Que tamanhos estão disponíveis para ações de ficheiros Azure?**  
    Os tamanhos de partilha de ficheiros Azure (premium e standard) podem escalar até 100 TiB. Consulte a [secção Onboard para maiores partilhas de ficheiros (nível padrão)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) do guia de planeamento para instruções de embarque para as maiores partilhas de ficheiros para o nível padrão.

* <a id="lfs-performance-impact"></a>
**Expandir a minha quota de partilha de ficheiros afeta as minhas cargas de trabalho ou o Azure File Sync?**
    
    Não. Expandir a quota não afetará as suas cargas de trabalho ou o Azure File Sync.

* <a id="open-handles-quota"></a>
**Quantos clientes podem aceder ao mesmo ficheiro simultaneamente?**   
    Há uma quota de 2.000 pegas abertas num único ficheiro. Quando se tem 2.000 pegas abertas, é apresentada uma mensagem de erro que diz que a quota é atingida.

* <a id="zip-slow-performance"></a>
**A minha atuação é lenta quando desapertei ficheiros em Ficheiros Azure. O que devo fazer?**  
    Para transferir um grande número de ficheiros para Ficheiros Azure, recomendamos que utilize o AzCopy (para Windows; na pré-visualização do Linux e UNIX) ou do Azure PowerShell. Estas ferramentas foram otimizadas para transferência de rede.

* <a id="slow-perf-windows-81-2012r2"></a>
**Porque é que o meu desempenho é lento depois de montar a minha quota de ficheiros Azure no Windows Server 2012 R2 ou Windows 8.1?**  
    Existe um problema conhecido ao montar uma partilha de ficheiros Azure no Windows Server 2012 R2 e Windows 8.1. O problema foi corrigido na atualização cumulativa de abril de 2014 para windows 8.1 e Windows Server 2012 R2. Para um desempenho ótimo, certifique-se de que todas as instâncias do Windows Server 2012 R2 e Windows 8.1 têm este patch aplicado. (Deve receber sempre patches do Windows através do Windows Update.) Para mais informações, consulte o artigo da Microsoft Knowledge Base responsável [pelo desempenho lento quando aceder a Ficheiros Azure a partir do Windows 8.1 ou Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Características e interoperabilidade com outros serviços
* <a id="cluster-witness"></a>
**Posso usar a minha partilha de ficheiros Azure como testemunha de partilha de *ficheiros* para o meu Cluster de Falhas do Servidor do Windows?**  
    Atualmente, esta configuração não é suportada para uma partilha de ficheiros Azure. Para obter mais informações sobre como configurar isto para armazenamento De Blob Azure, consulte [Deploy a Cloud Witness for a Failover Cluster](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Posso montar uma parte de ficheiro Azure numa instância do Contentor Azure?**  
    Sim, as ações de ficheiros Azure são uma boa opção quando se pretende persistir informação para além da vida útil de uma instância de contentores. Para mais informações, consulte o [Monte um ficheiro Azure com instâncias](../../container-instances/container-instances-mounting-azure-files-volume.md)do Contentor Do Azure .

* <a id="rest-rename"></a>
**Existe uma operação de renome na API REST?**  
    Neste momento, não.

* <a id="nested-shares"></a>
**Posso criar ações aninhadas? Por outras palavras, uma parte sob uma ação?**  
    Não. A partilha de *ficheiros é* o condutor virtual que pode montar, para que as ações aninhadas não sejam suportadas.

* <a id="ibm-mq"></a>
**Como uso ficheiros Azure com MQ IBM?**  
    A IBM divulgou um documento que ajuda os clientes da IBM MQ a configurar em Ficheiros Azure com o serviço IBM. Para mais informações, consulte Como configurar um gestor de fila de [várias instâncias IBM MQ com o serviço Microsoft Azure Files](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Consulte também
* [Troubleshoot Azure Files no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Troubleshoot Azure Files em Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Resolver problemas da Sincronização de Ficheiros do Azure](storage-sync-files-troubleshoot.md)
