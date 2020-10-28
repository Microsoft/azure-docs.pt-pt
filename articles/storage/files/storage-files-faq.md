---
title: Perguntas frequentes (FAQ) para Ficheiros Azure Microsoft Docs
description: Obtenha respostas para ficheiros Azure frequentemente feitas perguntas. Pode montar ações de ficheiros Azure simultaneamente nas implementações de Windows, Linux ou macOS em nuvem ou no local.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 609f6d5fd0bf75b1a2056c01c8d22ae9e08ab9cb
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746825"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Perguntas mais frequentes (FAQ) sobre os Ficheiros do Azure
[O Azure Files](storage-files-introduction.md) oferece ações de ficheiros totalmente geridas na nuvem que são acessíveis através do protocolo do Bloco de [Mensagens do Servidor (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) padrão da indústria e do [protocolo do Sistema de Ficheiros de Rede (NFS)](https://en.wikipedia.org/wiki/Network_File_System) (pré-visualização). Pode montar ações de ficheiros Azure simultaneamente em implementações em nuvem ou no local de Windows, Linux e macOS. Também pode cache ações de ficheiros Azure em máquinas do Windows Server utilizando o Azure File Sync para um acesso rápido perto do local onde os dados são utilizados.

Este artigo responde a perguntas comuns sobre funcionalidades e funcionalidades do Azure Files, incluindo a utilização de Azure File Sync com Ficheiros Azure. Se não vir a resposta à sua pergunta, pode contactar-nos através dos seguintes canais (por ordem de escalada):

1. A secção de comentários deste artigo.
2. [Microsoft Q&Uma página de perguntas para o Azure Storage](https://docs.microsoft.com/answers/topics/azure-file-storage.html).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Suporte microsoft. Para criar um novo pedido de suporte, no portal Azure, no separador **Ajuda,** selecione o botão **de suporte Ajuda +** e, em seguida, selecione Novo pedido de **suporte** .

## <a name="general"></a>Geral
* <a id="why-files-useful"></a>
  **Como é útil o Azure Files?**  
   Pode utilizar os Ficheiros Azure para criar partilhas de ficheiros na nuvem, sem ser responsável pela gestão da sobrecarga de um servidor físico, dispositivo ou aparelho. Fazemos o trabalho monótono para si, incluindo a aplicação de atualizações de SO e a substituição de discos ruins. Para saber mais sobre os cenários que o Azure Files pode ajudá-lo, veja [por que os Ficheiros Azure são úteis](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Quais são as diferentes formas de aceder a ficheiros em Ficheiros Azure?**  
    As ações de ficheiro SMB podem ser montadas na sua máquina local utilizando o protocolo SMB 3.0, ou pode utilizar ferramentas como [o Storage Explorer](https://storageexplorer.com/) para aceder a ficheiros na sua partilha de ficheiros. As partilhas de ficheiros NFS podem ser montadas na sua máquina local copiando/colando o script fornecido pelo portal Azure. A partir da sua aplicação, pode utilizar bibliotecas de clientes de armazenamento, REST APIs, PowerShell ou Azure CLI para aceder aos seus ficheiros na partilha de ficheiros Azure.

* <a id="what-is-afs"></a>
  **O que é Azure File Sync?**  
    Pode utilizar o Azure File Sync para centralizar as ações de ficheiros da sua organização nos Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma as suas máquinas do Windows Server numa cache rápida da sua partilha de ficheiros Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, Sistema de Ficheiros de Rede (NFS) e Serviço de Protocolo de Transferência de Ficheiros (FTPS). Podes ter o número de caches que precisares em todo o mundo.

* <a id="files-versus-blobs"></a>
  **Por que usaria uma partilha de ficheiros Azure contra o armazenamento de Azure Blob para os meus dados?**  
    Os Ficheiros Azure e o armazenamento Azure Blob oferecem formas de armazenar grandes quantidades de dados na nuvem, mas são úteis para fins ligeiramente diferentes. 
    
    O armazenamento Azure Blob é útil para aplicações nativas em nuvem em larga escala que precisam armazenar dados não estruturados. Para maximizar o desempenho e a escala, o armazenamento Azure Blob é uma abstração de armazenamento mais simples do que um verdadeiro sistema de ficheiros. Só pode aceder ao armazenamento do Azure Blob através de bibliotecas de clientes baseadas em REST (ou diretamente através do protocolo baseado em REST).

    O Azure Files é especificamente um sistema de ficheiros. O Azure Files tem todos os resumos de ficheiros que conhece e adora de anos a trabalhar com sistemas operativos no local. Tal como o armazenamento Azure Blob, a Azure Files oferece uma interface REST e bibliotecas de clientes baseadas em REST. Ao contrário do armazenamento Azure Blob, a Azure Files oferece acesso AMB ou NFS a ações de ficheiros Azure. As ações de ficheiros podem ser montadas diretamente no Windows, Linux ou macOS, quer no local, quer em VMs em nuvem, sem escrever nenhum código ou anexar quaisquer controladores especiais ao sistema de ficheiros. Também pode cache Azure SMB ações de ficheiros em servidores de ficheiros no local, utilizando o Azure File Sync para acesso rápido, perto do local onde os dados são utilizados. 
   
    Para obter uma descrição mais aprofundada das diferenças entre os Ficheiros Azure e o armazenamento Azure Blob, consulte [Introdução aos principais serviços de armazenamento Azure](../common/storage-introduction.md). Para saber mais sobre o armazenamento da Azure Blob, consulte [o armazenamento Introdução ao Blob.](../blobs/storage-blobs-introduction.md)

* <a id="files-versus-disks"></a>**Por que usaria uma partilha de ficheiros Azure em vez de Discos Azure?**  
    Um disco em Discos Azure é simplesmente um disco. Para obter valor da Azure Disks, tem de anexar um disco a uma máquina virtual que está a funcionar em Azure. Os Discos Azure podem ser utilizados para tudo o que usarias num servidor no local. Pode usá-lo como disco de sistema SO, como espaço de troca de espaço para um SISTEMA, ou como armazenamento dedicado para uma aplicação. Uma utilização interessante para discos Azure é criar um servidor de ficheiros na nuvem para usar nos mesmos locais onde poderá utilizar uma partilha de ficheiros Azure. Implementar um servidor de ficheiros em Azure Virtual Machines é uma forma de obter armazenamento de ficheiros em Azure quando necessita de opções de implementação que atualmente não são suportadas por Ficheiros Azure. 

    No entanto, executar um servidor de ficheiros com Discos Azure como armazenamento de back-end normalmente é muito mais caro do que usar uma partilha de ficheiros Azure, por algumas razões. Em primeiro lugar, para além de pagar o armazenamento do disco, também tem de pagar as despesas de execução de um ou mais VMs Azure. Em segundo lugar, também deve gerir os VMs que são utilizados para executar o servidor de ficheiros. Por exemplo, é responsável por atualizações de SO. Finalmente, se, em última análise, necessitar de dados para serem cached no local, cabe-lhe a si configurar e gerir tecnologias de replicação, como a Replicação do Sistema de Ficheiros Distribuídos (DFSR), para que isso aconteça.

    Uma abordagem para obter o melhor dos ficheiros Azure e de um servidor de ficheiros que está hospedado em Azure Virtual Machines (além de utilizar discos Azure como armazenamento de back-end) é instalar o Azure File Sync num servidor de ficheiros que está hospedado num VM de nuvem. Se a partilha de ficheiros Azure estiver na mesma região que o seu servidor de ficheiros, pode ativar o tiering de nuvem e definir o volume de percentagem de espaço livre para máximo (99%). Isto garante a duplicação mínima de dados. Também pode utilizar quaisquer aplicações que pretenda com os seus servidores de ficheiros, como aplicações que requerem suporte ao protocolo NFS.

    Para obter informações sobre uma opção de configuração de um servidor de ficheiros de alto desempenho e altamente disponível em Azure, consulte [a implementação de clusters de hóspedes IaaS VM no Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Para obter uma descrição mais aprofundada das diferenças entre os Ficheiros Azure e os Discos Azure, consulte [Introdução aos principais serviços de armazenamento Azure](../common/storage-introduction.md). Para saber mais sobre discos Azure, consulte [a visão geral dos Discos Geridos Azure](../../virtual-machines/managed-disks-overview.md).

* <a id="get-started"></a>
  **Como é que começo a usar ficheiros Azure?**  
   Começar com a Azure Files é fácil. Em primeiro lugar, [ou criar uma partilha de ficheiros SMB](storage-how-to-create-file-share.md) ou uma partilha de [NFS](storage-files-how-to-create-nfs-shares.md), e depois montá-la no seu sistema operativo preferido: 

  * [Monte uma participação SMB no Windows](storage-how-to-use-files-windows.md)
  * [Monte uma participação da SMB no Linux](storage-how-to-use-files-linux.md)
  * [Monte uma participação SMB no macOS](storage-how-to-use-files-mac.md)
  * [Monte uma partilha de ficheiros NFS](storage-files-how-to-mount-nfs-shares.md)

    Para obter um guia mais aprofundado sobre a implementação de uma partilha de ficheiros Azure para substituir as ações de ficheiros de produção na sua organização, consulte [Planeamento para uma implementação de Ficheiros Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Que opções de redundância de armazenamento são suportadas pela Azure Files?**  
    Atualmente, a Azure Files suporta armazenamento localmente redundante (LRS), armazenamento redundante de zona (ZRS), armazenamento geo-redundante (GRS) e armazenamento de zonas geo-redundantes (GZRS). Atualmente, o nível premium da Azure Files apenas suporta LRS e ZRS.

* <a id="tier-options"></a>
  **Que níveis de armazenamento são suportados em Ficheiros Azure?**  
    A Azure Files suporta dois níveis de armazenamento: premium e standard. As ações de ficheiros standard são criadas em contas de armazenamento de finalidade geral (GPv1 ou GPv2) e as ações de ficheiros premium são criadas em contas de armazenamento de fileStorage. Saiba mais sobre como criar [ações de ficheiros padrão](storage-how-to-create-file-share.md) e [ações de ficheiros premium.](storage-how-to-create-premium-fileshare.md) 
    
    > [!NOTE]
    > Não é possível criar ações de ficheiros Azure a partir de contas de armazenamento Blob ou contas de armazenamento *premium* (GPv1 ou GPv2). As ações de ficheiros Standard Azure devem ser criadas apenas em contas de finalidade geral *padrão* e as ações de ficheiros Premium Azure devem ser criadas apenas em contas de armazenamento fileStorage. *As* contas de armazenamento premium (GPv1 e GPv2) são apenas para bolhas de página premium. 

* <a id="file-locking"></a>
  **O Azure Files suporta o bloqueio de ficheiros?**  
    Sim, a Azure Files suporta totalmente o bloqueio de ficheiros estilo SMB/Windows, [ver detalhes](https://docs.microsoft.com/rest/api/storageservices/managing-file-locks).

* <a id="give-us-feedback"></a>
  **Quero ver uma funcionalidade específica adicionada aos Ficheiros Azure. Pode adicioná-lo?**  
    A equipa da Azure Files está interessada em ouvir qualquer feedback que tenha sobre o nosso serviço. Por favor, vote nos pedidos de funcionalidade no [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Estamos ansiosos para deliciá-lo com muitas novidades.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Que regiões são suportadas para o Azure File Sync?**  
    A lista das regiões disponíveis pode ser encontrada na secção de disponibilidade da [Região](storage-sync-files-planning.md#azure-file-sync-region-availability) do guia de planeamento do Azure File Sync. Acrescentaremos continuamente apoio a regiões adicionais, incluindo regiões não públicas.

* <a id="cross-domain-sync"></a>
  **Posso ter servidores unidos por domínio e não-domínios no mesmo grupo de sincronização?**  
    Sim. Um grupo de sincronização pode conter pontos finais do servidor que têm diferentes membros do Ative Directory, mesmo que não estejam unidos pelo domínio. Embora esta configuração funcione tecnicamente, não recomendamos esta configuração como uma configuração típica porque as listas de controlo de acesso (ACLs) que são definidas para ficheiros e pastas num servidor podem não ser aplicadas por outros servidores do grupo de sincronização. Para obter melhores resultados, recomendamos sincronização entre servidores que estão na mesma floresta ative de Diretório, entre servidores que estão em diferentes florestas de Ative Directory mas que estabeleceram relações de confiança, ou entre servidores que não estão em um domínio. Recomendamos que evite utilizar uma mistura destas configurações.

* <a id="afs-change-detection"></a>
  **Criei um ficheiro diretamente na minha partilha de ficheiros Azure usando SMB ou no portal. Quanto tempo demora o ficheiro a sincronizar com os servidores do grupo de sincronização?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Quanto tempo demora o Azure File Sync a carregar 1TiB de dados?**
  
    O desempenho variará em função das suas configurações ambientais, configuração e se se trata de uma sincronização inicial ou de uma sincronização em curso. Para obter mais informações, consulte [as métricas de desempenho do Azure File Sync](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**Se o mesmo ficheiro for alterado em dois servidores aproximadamente ao mesmo tempo, o que acontece?**  
    O Azure File Sync utiliza uma simples estratégia de resolução de conflitos: mantemos ambas as alterações nos ficheiros que são alterados em dois pontos finais ao mesmo tempo. A alteração escrita mais recentemente mantém o nome original do ficheiro. O ficheiro mais antigo (determinado pela LastWriteTime) tem o nome de ponto final e o número de conflito anexado ao nome de ficheiro. Para os pontos finais do servidor, o nome do ponto final é o nome do servidor. Para pontos finais de nuvem, o nome do ponto final é **Cloud** . O nome segue esta taxonomia: 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    Por exemplo, o primeiro conflito de CompanyReport.docx tornar-se-ia CompanyReport-CentralServer.docx se a CentralServer fosse onde ocorreu a escrita mais antiga. O segundo conflito seria nomeado CompanyReport-CentralServer-1.docx. O Azure File Sync suporta 100 ficheiros de conflito por ficheiro. Uma vez atingido o número máximo de ficheiros de conflito, o ficheiro deixará de sincronizar até que o número de ficheiros de conflito seja inferior a 100.

* <a id="afs-storage-redundancy"></a>
  **O armazenamento geo-redundante é suportado para o Azure File Sync?**  
    Sim, a Azure Files suporta tanto o armazenamento localmente redundante (LRS) como o armazenamento geo-redundante (GRS). Se iniciar uma falha de conta de armazenamento entre regiões emparelhadas a partir de uma conta configurada para GRS, a Microsoft recomenda que trate a nova região como uma cópia de segurança dos dados. O Azure File Sync não começa automaticamente a sincronizar-se com a nova região primária. 

* <a id="sizeondisk-versus-size"></a>
  **Por que o tamanho na propriedade *do disco* para um ficheiro corresponde à propriedade *Size* depois de usar Azure File Sync?**  
  Consulte [a compreensão do nível da nuvem](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  **Como posso saber se um ficheiro foi divulgado?**  
  Consulte [a compreensão do nível da nuvem](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Um ficheiro que quero usar foi hierarquizado. Como posso lembrar-me do ficheiro para o disco para o usar localmente?**  
  Consulte [a compreensão do nível da nuvem](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **Como posso forçar um ficheiro ou diretório a ser hierárquico?**  
  Consulte [a compreensão do nível da nuvem](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  **Como é interpretado *o espaço livre de volume* quando tenho vários pontos finais do servidor num volume?**  
  Consulte [a compreensão do nível da nuvem](storage-sync-cloud-tiering.md#afs-effective-vfs).
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **Tenho camadas de nuvem desativadas, por que há ficheiros hierárquicos na localização do ponto final do servidor?**  
  Consulte [a compreensão do nível da nuvem](storage-sync-cloud-tiering.md#afs-tiering-disabled).

* <a id="afs-files-excluded"></a>
  **Quais ficheiros ou pastas são automaticamente excluídos pelo Azure File Sync?**  
  Ver [Ficheiros ignorados](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Posso utilizar o Azure File Sync com o Windows Server 2008 R2, Linux ou com o meu dispositivo de armazenamento (NAS) ligado à rede?**  
    Atualmente, o Azure File Sync suporta apenas o Windows Server 2019, o Windows Server 2016 e o Windows Server 2012 R2. Neste momento, não temos outros planos que possamos partilhar, mas estamos abertos a apoiar plataformas adicionais baseadas na procura do cliente. Informe-nos na [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) quais as plataformas que pretende que suportemos.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Por que existem ficheiros hierárquicos fora do espaço de nome do ponto final do servidor?**  
    Antes da versão 3 do agente Azure File Sync, o Azure File Sync bloqueou a deslocação de ficheiros hierarquizados fora do ponto final do servidor, mas no mesmo volume que o ponto final do servidor. As operações de cópia, os movimentos de ficheiros não hierárquicos e os movimentos de tiered para outros volumes não foram afetados. A razão para este comportamento foi o pressuposto implícito de que o File Explorer e outras APIs do Windows têm que mover operações no mesmo volume são (quase) operações instantâneas de renome. Isto significa que os movimentos farão com que o File Explorer ou outros métodos de movimento (como a linha de comando ou o PowerShell) apareçam sem resposta enquanto o Azure File Sync recorda os dados da nuvem. Começando pela [versão 3.0.12.0 do agente Azure File](storage-files-release-notes.md#supported-versions)Sync, o Azure File Sync permitir-lhe-á mover um ficheiro hierárquico para fora do ponto final do servidor. Evitamos os efeitos negativos mencionados anteriormente, permitindo que o ficheiro hierárquico exista como um ficheiro hierarquizado fora do ponto final do servidor e, em seguida, relembramos o ficheiro em segundo plano. Isto significa que os movimentos no mesmo volume são instantâneos, e fazemos todo o trabalho para chamar o ficheiro para o disco depois de concluído o movimento. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Estou tendo um problema com Azure File Sync no meu servidor (sincronização, nivelamento de nuvem, etc.). Devo remover e recriar o ponto final do meu servidor?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Posso mover o serviço de sincronização de armazenamento e/ou conta de armazenamento para um grupo de recursos diferente, subscrição ou inquilino AD AZure?**  
   Sim, o serviço de sincronização de armazenamento e/ou conta de armazenamento pode ser transferido para um grupo de recursos diferente, subscrição ou inquilino AD AZure. Depois de o serviço de sincronização de armazenamento ou a conta de armazenamento ser movido, é necessário dar à aplicação Microsoft.StorageSync acesso à conta de armazenamento (ver [Ensure Azure File Sync tem acesso à conta de armazenamento).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Ao criar o ponto final da nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento devem estar no mesmo inquilino AD AZure. Uma vez criado o ponto final da nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento podem ser transferidos para diferentes inquilinos AD AZure.
    
* <a id="afs-ntfs-acls"></a>
  **O Azure File Sync preserva o diretório/nível de ficheiro NTFS ACLs juntamente com os dados armazenados em Ficheiros Azure?**

    A partir de 24 de fevereiro de 2020, novos ACLs existentes e existentes tiered by Azure file sync serão persistidos no formato NTFS, e as modificações ACL feitas diretamente para a partilha de ficheiros Azure irão sincronizar com todos os servidores do grupo de sincronização. Quaisquer alterações nos ACLs efetuadas aos Ficheiros Azure sincronizar-se-ão através da sincronização de ficheiros Azure. Ao copiar dados para ficheiros Azure, certifique-se de que utiliza uma ferramenta de cópia que suporta a "fidelidade" necessária para copiar atributos, timetamps e ACLs numa partilha de ficheiros Azure - seja através de SMB ou REST. Ao utilizar ferramentas de cópia Azure, como a AzCopy, é importante utilizar a versão mais recente. Consulte a tabela de [ferramentas de cópia](storage-files-migration-overview.md#file-copy-tools) de ficheiros para obter uma visão geral das ferramentas de cópia do Azure para garantir que pode copiar todos os metadados importantes de um ficheiro.

    Se tiver ativado o Azure Backup nas partilhas de ficheiros geridos por ficheiros, os ACLs de ficheiros podem continuar a ser restaurados como parte do fluxo de trabalho de restauração de backup. Isto funciona para a totalidade das ações ou ficheiros/diretórios individuais.

    Se estiver a utilizar instantâneos como parte da solução de backup auto-gerida para ações de ficheiros geridas por sincronização de ficheiros, os seus ACLs podem não ser restaurados adequadamente para NTFS ACLs se as imagens foram tiradas antes de 24 de fevereiro de 2020. Se isso ocorrer, considere contactar o Suporte Azure.
    
## <a name="security-authentication-and-access-control"></a>Segurança, autenticação e controlo de acessos
* <a id="ad-support"></a>
**A autenticação e o controlo de acesso baseados na identidade são suportados por Ficheiros Azure?**  
    
    Sim, o Azure Files suporta a autenticação baseada na identidade e o controlo de acesso. Pode escolher uma de duas formas de utilizar o controlo de acesso baseado na identidade: no local Serviços de Domínio de Diretório Ativo ou Serviços de Domínio do Diretório Ativo Azure (Azure AD DS). No local, os Serviços de Domínio do Diretório Ativo (AD DS) suportam a autenticação utilizando máquinas aderidas ao domínio AD DS, no local ou no Azure, para aceder a ações de ficheiros Azure através de SMB. A autenticação Azure AD DS sobre SMB para Ficheiros Azure permite que os VMS do Windows AD AD AD aderidos ao domínio Azure DS acedam a partilhas, diretórios e ficheiros utilizando credenciais Azure AD. Para mais detalhes, consulte [o suporte de autenticação baseado em identidade do Azure Files para acesso ao SMB.](storage-files-active-directory-overview.md) 

    A Azure Files oferece duas formas adicionais de gerir o controlo de acessos:

    - Pode utilizar assinaturas de acesso partilhado (SAS) para gerar fichas que tenham permissões específicas e que sejam válidas para um intervalo de tempo especificado. Por exemplo, pode gerar um token com acesso apenas de leitura a um ficheiro específico que tenha uma expiração de 10 minutos. Qualquer pessoa que possua o símbolo enquanto o símbolo é válido tem acesso apenas de leitura a esse ficheiro durante esses 10 minutos. As chaves de assinatura de acesso partilhado são suportadas apenas através da API REST ou nas bibliotecas dos clientes. Tem de montar a partilha de ficheiros Azure sobre a SMB utilizando as chaves da conta de armazenamento.

    - O Azure File Sync preserva e replica todos os ACLs discricionários, ou DACLs ( baseados em Diretórios Ativos ou locais) a todos os pontos finais do servidor a que sincroniza. 
    
    Pode consultar o [Acesso autorizado ao Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para uma representação completa de todos os protocolos suportados nos serviços de Armazenamento Azure. 
    
* <a id="encryption-at-rest"></a>
**Como posso garantir que a minha partilha de ficheiros Azure seja encriptada em repouso?**  

    Sim. Para mais informações consulte [a Encriptação do Serviço de Armazenamento Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Como posso fornecer acesso a um ficheiro específico usando um navegador web?**  

    Pode utilizar assinaturas de acesso partilhadas para gerar fichas que tenham permissões específicas e que sejam válidas para um intervalo de tempo especificado. Por exemplo, pode gerar um token que dá acesso apenas de leitura a um ficheiro específico, por um período de tempo definido. Qualquer pessoa que possua o URL pode aceder ao ficheiro diretamente a partir de qualquer navegador da Web enquanto o token é válido. Pode facilmente gerar uma chave de assinatura de acesso partilhado a partir de uma UI como o Storage Explorer.

* <a id="file-level-permissions"></a>
**É possível especificar permissões apenas de leitura ou de escrita em pastas dentro da partilha?**  

    Se montar a partilha de ficheiros utilizando o SMB, não tem controlo de nível de pasta sobre permissões. No entanto, se criar uma assinatura de acesso partilhado utilizando a API REST ou as bibliotecas de clientes, pode especificar permissões apenas de leitura ou de escrita em pastas dentro da partilha.

* <a id="ip-restrictions"></a>
**Posso implementar restrições de IP para uma partilha de ficheiros Azure?**  

    Sim. O acesso à sua parte de ficheiro Azure pode ser restringido ao nível da conta de armazenamento. Para mais informações, consulte [as firewalls de armazenamento Configure Azure e as redes virtuais.](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

* <a id="data-compliance-policies"></a>
**Que políticas de conformidade com dados suportam os Ficheiros Azure?**  

   A Azure Files funciona em cima da mesma arquitetura de armazenamento que é usada em outros serviços de armazenamento em Azure Storage. A Azure Files aplica as mesmas políticas de conformidade de dados que são utilizadas noutros serviços de armazenamento Azure. Para obter mais informações sobre a conformidade com os dados do Azure Storage, pode consultar [as ofertas de conformidade do Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)e ir ao [Microsoft Trust Center](https://microsoft.com/trustcenter/default.aspx).

* <a id="file-auditing"></a>
**Como posso auditar o acesso a ficheiros e alterações nos Ficheiros Azure?**

  Existem duas opções que fornecem funcionalidade de auditoria para ficheiros Azure:
  - Se os utilizadores estiverem a aceder diretamente à partilha de ficheiros Azure, [os registos de armazenamento Azure (pré-visualização)](https://docs.microsoft.com/azure/storage/common/monitor-storage?tabs=azure-powershell#logs-in-azure-monitor-preview) podem ser utilizados para rastrear alterações de ficheiros e acesso ao utilizador. Estes registos podem ser utilizados para efeitos de resolução de problemas e os pedidos são registados numa base de melhor esforço.
  - Se os utilizadores estiverem a aceder à partilha de ficheiros Azure através de um Servidor do Windows que tenha instalado o agente Azure File Sync, utilize uma política de [auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) ou um produto de terceira parte para rastrear as alterações de ficheiros e o acesso do utilizador no Servidor do Windows. 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS & Autenticação AD DS
* <a id="ad-support-devices"></a>
**O Azure Files Azure Ative Directory Domain Services (Azure AD DS) Suporta o acesso SMB utilizando credenciais AD Azure de dispositivos ligados ou registados no Azure AD?**

    Não, este cenário não é apoiado.

* <a id="ad-vm-subscription"></a>
**Posso aceder a ações de ficheiros Azure com credenciais AD AZure de um VM sob uma subscrição diferente?**

    Se a subscrição sob a qual a parte do ficheiro é implantada estiver associada ao mesmo inquilino Azure AD que a implantação Azure AD DS à qual o VM está ligado ao domínio, pode então aceder às ações de ficheiros Azure usando as mesmas credenciais AZure AD. A limitação é imposta não na subscrição, mas no inquilino associado da Azure AD.
    
* <a id="ad-support-subscription"></a>
**Posso permitir a autenticação Azure AD DS ou no local para ações de ficheiros Azure usando um inquilino AZure AD que seja diferente do inquilino principal da azure?**

    Não, a Azure Files apenas suporta a integração AZure AD DS ou no local com um inquilino AD AZure que reside na mesma subscrição que a partilha de ficheiros. Apenas uma subscrição pode ser associada a um inquilino AZure AD. Esta limitação aplica-se tanto aos métodos de autenticação AD DS AD AD AZure e no local. Ao utilizar o DS AD no local para autenticação, [a credencial AD DS deve ser sincronizada com a AD AZure](../../active-directory/hybrid/how-to-connect-install-roadmap.md) a que a conta de armazenamento está associada.

* <a id="ad-linux-vms"></a>
**A Azure AD DS ou no local a autenticação AD DS para ações de ficheiros Azure suportam Os VMs Linux?**

    Não, a autenticação dos VMs Linux não é suportada.

* <a id="ad-aad-smb-afs"></a>
**As ações de ficheiro geridas pela Azure File Sync suportam a autenticação AZure AD DS ou no local?**

    Sim, pode ativar a autenticação AZure AD DS ou no local numa partilha de ficheiros gerida pela Azure File Sync. As alterações ao diretório/ficheiro NTFS ACLs nos servidores de ficheiros locais serão hierárquicas para ficheiros Azure e vice-versa.

* <a id="ad-aad-smb-files"></a>
**Como posso verificar se habilifiquei a autenticação AD DS na minha conta de armazenamento e recuperar a informação de domínio?**

    Para instruções, consulte [aqui.](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#1-enable-ad-authentication-for-your-account)

* <a id=""></a>
**A azure Files Azure AD autenticação AD suporta VMs Linux?**

    Não, a autenticação dos VMs Linux não é suportada.

* <a id="ad-multiple-forest"></a>
**A autenticação AD DS no local para a ações de ficheiros Azure suporta a integração com um ambiente AD DS utilizando múltiplas florestas?**    

    A autenticação Azure Files no local AD DS apenas se integra com a floresta do serviço de domínio a que a conta de armazenamento está registada. Para apoiar a autenticação de outra floresta, o seu ambiente deve ter uma confiança florestal configurada corretamente. A forma como os Ficheiros Azure registam em DS AD quase o mesmo que um servidor de ficheiros regular, onde cria uma identidade (conta de início de serviço ou de computador) em DS AD para autenticação. A única diferença é que a SPN registada da conta de armazenamento termina com "file.core.windows.net" que não corresponde ao sufixo de domínio. Consulte o seu administrador de domínio para ver se é necessária alguma atualização da sua política de encaminhamento de sufixos para ativar a autenticação múltipla da floresta devido ao sufixo de domínio diferente. Damos um exemplo abaixo para configurar a política de encaminhamento de sufixos.
    
    Exemplo: Quando os utilizadores na floresta Um domínio pretende chegar a uma partilha de ficheiros com a conta de armazenamento registada contra um domínio na floresta B, isso não funcionará automaticamente porque o principal de serviço da conta de armazenamento não tem um sufixo correspondente ao sufixo de qualquer domínio na floresta A. Podemos resolver esta questão configurando manualmente uma regra de encaminhamento de sufixo da floresta A para a floresta B para um sufixo personalizado de "file.core.windows.net".
    Primeiro, deve adicionar um novo sufixo personalizado na floresta B. Certifique-se de que tem as permissões administrativas adequadas para alterar a configuração e, em seguida, siga estes passos:   
    1. Logon a um domínio de máquina junto à floresta B
    2.  Abra a consola "Domínios e Fidedignos ativos"
    3.  Clique à direita em "Domínios e Fidedignos ativos"
    4.  Clique em "Propriedades"
    5.  Clique em "Adicionar"
    6.  Adicione "file.core.windows.net" como os Sufixos da UPN
    7.  Clique em "Apply", em seguida, "OK" para fechar o assistente
    
    Em seguida, adicione a regra de encaminhamento de sufixo na floresta A, de modo que redirecione para a floresta B.
    1.  Logon a um domínio de máquina junto à floresta A
    2.  Abra a consola "Domínios e Fidedignos ativos"
    3.  Clique com o botão direito no domínio que pretende aceder à partilha de ficheiros, em seguida, clique no separador "Trusts" e selecione o domínio da floresta B a partir de fundos de saída. Se não tem confiança entre as duas florestas, precisa de criar a confiança primeiro.
    4.  Clique em "Propriedades..." em seguida, "Nome Sfixix Encaminhamento"
    5.  Verifique se o "*.file.core.windows.net" aparece. Caso contrário, clique em 'Refresh'
    6.  Selecione "*.file.core.windows.net", em seguida, clique em "Ative" e "Apply"

* <a id=""></a>
**Quais as regiões disponíveis para a autenticação Azure Files AD DS?**

    Consulte a [disponibilidade regional da AD DS](storage-files-identity-auth-active-directory-enable.md#regional-availability) para mais detalhes.
    
* <a id="ad-aad-smb-afs"></a>
**Posso aproveitar a autenticação do Azure Files Ative Directory (AD) em ações de ficheiros geridas pela Azure File Sync?**

    Sim, pode ativar a autenticação de AD numa partilha de ficheiros gerida pela sincronização de ficheiros Azure. As alterações ao diretório/ficheiro NTFS ACLs nos servidores de ficheiros locais serão hierárquicas para ficheiros Azure e vice-versa.

* <a id="ad-aad-smb-files"></a>
**Como posso verificar se habilifiquei a autenticação da AD na minha conta de armazenamento e na informação de domínio da AD?**

    Pode consultar as instruções [aqui](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#enable-ad-authentication-for-your-account) fornecidas para validar se a autenticação AD dos ficheiros Azure estiver ativada na sua conta de armazenamento e recuperar as informações de domínio AD.

* <a id="ad-aad-smb-files"></a>
**Existe alguma diferença na criação de uma conta de computador ou de uma conta de início de serviço para representar a minha conta de armazenamento em AD?**

    A criação de uma [conta de computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (padrão) ou de uma conta de início de [serviço](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) não tem qualquer diferença na forma como a autenticação funcionaria com os Ficheiros Azure. Pode fazer a sua própria escolha sobre como representar uma conta de armazenamento como identidade no seu ambiente de AD. O padrão DomainAccountType definido em Join-AzStorageAccountForAuth cmdlet é conta de computador. No entanto, a idade de validade da palavra-passe configurada no seu ambiente de AD pode ser diferente para conta de início de sítido de computador ou serviço e tem de ter isso em consideração para [atualizar a palavra-passe da sua identidade de conta de armazenamento em AD.](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#5-update-ad-account-password)
 
* <a id="ad-support-rest-apis"></a>
**Existem APIs REST para suportar Get/set/Copy directy/file Windows ACLs?**

    Sim, apoiamos AS APIs rest que obtenham, definir ou copiar ACLs NTFS para diretórios ou ficheiros quando utilizar a API REST [2019-07](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (ou posterior) REST. Também suportamos ACLs do Windows em ferramentas baseadas em REST: [AzCopy v10.4+](https://github.com/Azure/azure-storage-azcopy/releases).

## <a name="network-file-system"></a>Sistema de Ficheiros de Rede

* <a id="when-to-use-nfs"></a>
**Quando devo utilizar o Azure Files NFS?**

    Ver [ações da NFS (pré-visualização)](storage-files-compare-protocols.md#nfs-shares-preview).

* <a id="backup-nfs-data"></a>
**Como faço os dados de backup armazenados em ações da NFS?**

    O backup dos seus dados sobre ações NFS pode ser orquestrado usando ferramentas familiares como rsync ou produtos de um dos nossos parceiros de backup de terceiros. Vários parceiros de backup, incluindo [Commvault,](https://documentation.commvault.com/commvault/v11/article?p=92634.htm) [Veeam](https://www.veeam.com/blog/?p=123438)e [Veritas](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001) faziam parte da nossa pré-visualização inicial e alargaram as suas soluções para trabalhar com smb 3.0 e NFS 4.1 para Azure Files.

* <a id="migrate-nfs-data"></a>
**Posso migrar os dados existentes para uma parte da NFS?**

    Dentro de uma região, você pode usar ferramentas padrão como scp, rsync ou SSHFS para mover dados. Uma vez que o Azure Files NFS pode ser acedido a partir de múltiplas instâncias de computação simultaneamente, pode melhorar as velocidades de cópia com uploads paralelos. Se pretender trazer dados de fora de uma região, utilize uma VPN ou uma Rota Express para montar no seu sistema de ficheiros a partir do seu centro de dados no local.

## <a name="on-premises-access"></a>Acesso no local

* <a id="port-445-blocked"></a>
**O meu ISP ou IT bloqueia a porta 445 que está a falhar na montagem dos Ficheiros Azure. O que devo fazer?**

    Você pode aprender sobre [várias maneiras de encontrar soluções bloqueou o porto 445 aqui.](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked) O Azure Files só permite ligações utilizando SMB 3.0 (com suporte de encriptação) de fora da região ou do datacenter. O protocolo SMB 3.0 introduziu muitas funcionalidades de segurança, incluindo encriptação de canais, que é muito segura para usar através da internet. No entanto, é possível que a porta 445 tenha sido bloqueada devido a razões históricas de vulnerabilidades encontradas nas versões SMB mais baixas. No caso ideal, a porta deve ser bloqueada apenas para tráfego SMB 1.0 e SMB 1.0 deve ser desligado em todos os clientes.

* <a id="expressroute-not-required"></a>
**Tenho de usar o Azure ExpressRoute para ligar aos Ficheiros Azure ou para utilizar o Azure File Sync no local?**  

    Não. O ExpressRoute não é obrigado a aceder a uma partilha de ficheiros Azure. Se estiver a montar uma partilha de ficheiros Azure diretamente no local, tudo o que é necessário é ter a porta 445 (saída TCP) aberta para acesso à Internet (esta é a porta que o SMB utiliza para comunicar). Se estiver a utilizar o Azure File Sync, tudo o que é necessário é a porta 443 (saída TCP) para acesso HTTPS (sem necessidade de SMB). No entanto, *pode* utilizar o ExpressRoute com qualquer uma destas opções de acesso.

* <a id="mount-locally"></a>
**Como posso montar uma parte do ficheiro Azure na minha máquina local?**  

    Pode montar a partilha de ficheiros utilizando o protocolo SMB se a porta 445 (saída TCP) estiver aberta e o seu cliente suportar o protocolo SMB 3.0 (por exemplo, se estiver a utilizar o Windows 10 ou o Windows Server 2016). Se a porta 445 estiver bloqueada pela política da sua organização ou pelo seu ISP, pode utilizar o Azure File Sync para aceder à sua partilha de ficheiros Azure.

## <a name="backup"></a>Cópia de segurança
* <a id="backup-share"></a>
**Como posso apoiar a minha parte do ficheiro Azure?**  
    Pode utilizar [instantâneos periódicos](storage-snapshots-files.md) para proteção contra supressões acidentais. Também pode utilizar a AzCopy, Robocopy ou uma ferramenta de backup de terceiros que pode fazer backup de uma partilha de ficheiros montada. O Azure Backup oferece cópia de segurança dos Ficheiros Azure. Saiba mais sobre [backup ações de ficheiros Azure by Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Partilhar fotos

### <a name="share-snapshots-general"></a>Partilhar fotos: Geral
* <a id="what-are-snaphots"></a>
**O que são fotos de partilha de ficheiros?**  
    Pode utilizar instantâneos de partilha de ficheiros Azure para criar uma versão apenas de leitura das suas partilhas de ficheiros. Também pode utilizar os Ficheiros Azure para copiar uma versão anterior do seu conteúdo de volta para a mesma partilha, para uma localização alternativa em Azure, ou no local para mais modificações. Para saber mais sobre partilhar fotos, consulte a [visão geral](storage-snapshots-files.md)do snapshot da Partilha.

* <a id="where-are-snapshots-stored"></a>
**Onde estão as fotos da minha parte guardadas?**  
    As imagens de partilha são armazenadas na mesma conta de armazenamento que a parte do ficheiro.

* <a id="snapshot-consistency"></a>
**As partilhas são consistentes com a aplicação de snapshots?**  
    Não, as fotos partilhadas não são consistentes com aplicações. O utilizador deve descarregar as gravações da aplicação para a partilha antes de tirar a fotografia da partilha.

* <a id="snapshot-limits"></a>
**Há limites para o número de fotos partilhadas que posso usar?**  
    Sim. Os Ficheiros Azure podem reter um máximo de 200 instantâneos de partilha. As snapshots de partilha não contam para a quota de ações, pelo que não existe um limite por partilha no espaço total que é usado por todos os instantâneos de partilha. Os limites da conta de armazenamento ainda se aplicam. Depois de 200 fotos partilhadas, deve eliminar instantâneos mais antigos para criar novas fotos de partilha.

* <a id="snapshot-cost"></a>
**Quanto custam as fotos partilhadas?**  
    A transação padrão e o custo normal de armazenamento aplicar-se-ão ao snapshot. Os instantâneos são incrementais na natureza. O instantâneo base é a parte em si. Todos os instantâneos subsequentes são incrementais e apenas armazenarão a difusão do instantâneo anterior. Isto significa que as alterações delta que serão vistas na conta serão mínimas se a sua carga de trabalho for mínima. Consulte [a página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter informações sobre preços dos Ficheiros Standard Azure. Hoje, a forma de olhar para o tamanho consumido pelo snapshot de partilha é comparando a capacidade faturada com a capacidade usada. Estamos a trabalhar na ferramenta para melhorar o relatório.

* <a id="ntfs-acls-snaphsots"></a>
**Os ACLs da NTFS em diretórios e ficheiros persistem em instantâneos de partilha?**  
    Os ACLs NTFS em diretórios e ficheiros são persistidos em instantâneos de partilha.

### <a name="create-share-snapshots"></a>Criar instantâneos de partilha
* <a id="file-snaphsots"></a>
**Posso criar uma imagem partilhada de ficheiros individuais?**  
    As imagens de partilha são criadas ao nível da partilha de ficheiros. Pode restaurar ficheiros individuais a partir do instantâneo da partilha de ficheiros, mas não é possível criar imagens de partilha ao nível do ficheiro. No entanto, se tiver tirado uma snapshot de partilha ao nível de partilha e pretender listar imagens de partilha onde um ficheiro específico foi alterado, pode fazê-lo em **versões anteriores** numa ação montada no Windows. 
    
    Se precisar de uma funcionalidade de instantâneo de ficheiro, informe-nos no [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Posso criar imagens partilhadas de uma partilha de ficheiros encriptadas?**  
    Pode tirar uma fotografia de partilha de ações de ficheiros Azure que tenham encriptação em repouso ativada. Pode restaurar os ficheiros de uma imagem partilhada para uma partilha de ficheiros encriptada. Se a sua parte estiver encriptada, o instantâneo da sua partilha também é encriptado.

* <a id="geo-redundant-snaphsots"></a>
**As minhas fotos são geo-redundantes?**  
    As fotos partilhadas têm a mesma redundância que a parte do ficheiro Azure para a qual foram tiradas. Se selecionou o armazenamento geo-redundante para a sua conta, o seu instantâneo de partilha também é armazenado de forma redundante na região emparelhada.

### <a name="manage-share-snapshots"></a>Gerir instantâneos de partilha
* <a id="browse-snapshots-linux"></a>
**Posso ver as fotos da minha parte do Linux?**  
    Pode utilizar o Azure CLI para criar, listar, navegar e restaurar imagens de partilha em Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Posso copiar as fotos de partilha para uma conta de armazenamento diferente?**  
    Pode copiar ficheiros de imagens partilhadas para outro local, mas não pode copiar as imagens de partilha por si mesmas.

### <a name="restore-data-from-share-snapshots"></a>Restaurar dados a partir de instantâneos de partilha
* <a id="promote-share-snapshot"></a>
**Posso promover uma foto partilhada para a parte base?**  
    Pode copiar dados de uma imagem partilhada para qualquer outro destino. Não pode promover uma imagem partilhada para a parte base.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Posso restaurar os dados da minha parte instantânea para uma conta de armazenamento diferente?**  
    Sim. Os ficheiros de um instantâneo de partilha podem ser copiados para o local original ou para um local alternativo que inclua a mesma conta de armazenamento ou uma conta de armazenamento diferente, quer na mesma região, quer em diferentes regiões. Também pode copiar ficheiros para um local no local ou para qualquer outra nuvem.    
  
### <a name="clean-up-share-snapshots"></a>Limpe fotos de partilha
* <a id="delete-share-keep-snapshots"></a>
**Posso apagar a minha parte, mas não apagar as minhas fotos partilhadas?**  
    Se tiver imagens de partilha ativas na sua parte, não poderá eliminar a sua parte. Pode utilizar uma API para eliminar imagens partilhadas, juntamente com a partilha. Também pode eliminar as imagens de partilha e a partilha no portal Azure.

* <a id="delete-share-with-snapshots"></a>
**O que acontece com as fotos da minha parte se eu apagar a minha conta de armazenamento?**  
    Se eliminar a sua conta de armazenamento, as imagens de partilha também são eliminadas.

## <a name="billing-and-pricing"></a>Faturação e preços
* <a id="vm-file-share-network-traffic"></a>
**O tráfego de rede entre um Azure VM e uma partilha de ficheiros Azure conta como largura de banda externa que é cobrada à subscrição?**  
    Se a partilha de ficheiros e a VM estiverem na mesma região de Azure, não existe qualquer custo adicional para o tráfego entre a partilha de ficheiros e o VM. Se a partilha de ficheiros e o VM estiverem em diferentes regiões, o tráfego entre eles é cobrado como largura de banda externa.

* <a id="share-snapshot-price"></a>
**Quanto custam as fotos partilhadas?**  
     Durante a pré-visualização, não há qualquer custo para a capacidade de partilha de instantâneos. Aplicam-se saídas de armazenamento padrão e custos de transação. Após a disponibilidade geral, as subscrições serão cobradas pela capacidade e transações em instantâneos de ações.
     
     As fotos partilhadas são incrementais na natureza. O instantâneo base é a parte em si. Todas as imagens de partilha subsequentes são incrementais e armazenam apenas a diferença do instantâneo de partilha anterior. É cobrado apenas pelo conteúdo alterado. Se você tem uma parte com 100 GiB de dados mas apenas 5 GiB mudou desde a sua última partilha snapshot, o instantâneo de partilha consome apenas 5 GiB adicional, e você é cobrado por 105 GiB. Para obter mais informações sobre transações e taxas de saída padrão, consulte [a página de Preços](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Escala e desempenho
* <a id="files-scale-limits"></a>
**Quais são os limites de escala dos Ficheiros Azure?**  
    Para obter informações sobre a escalabilidade e os objetivos de desempenho dos Ficheiros Azure, consulte [os objetivos de escalabilidade e desempenho dos Ficheiros Azure](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Que tamanhos estão disponíveis para ações de ficheiros Azure?**  
    Os tamanhos das ações de ficheiros Azure (premium e standard) podem escalar até 100 TiB. Consulte a secção [onboard para ações de ficheiros maiores (nível padrão)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) do guia de planeamento para instruções de embarque para as partilhas de ficheiros maiores para o nível padrão.

* <a id="lfs-performance-impact"></a>
**A expansão da minha quota de ações de ficheiros afeta as minhas cargas de trabalho ou o Azure File Sync?**
    
    Não. A expansão da quota não afetará as suas cargas de trabalho ou o Azure File Sync.

* <a id="open-handles-quota"></a>
**Quantos clientes podem aceder ao mesmo ficheiro simultaneamente?**   
    Há uma quota de 2.000 pegas abertas num único ficheiro. Quando se tem 2.000 pegas abertas, é apresentada uma mensagem de erro que diz que a quota é alcançada.

* <a id="zip-slow-performance"></a>
**A minha atuação é lenta quando desaperto ficheiros em Ficheiros Azure. O que devo fazer?**  
    Para transferir um grande número de ficheiros para ficheiros Azure, recomendamos que utilize o AzCopy (para Windows; na pré-visualização para Linux e UNIX) ou Azure PowerShell. Estas ferramentas foram otimizadas para a transferência de rede.

* <a id="slow-perf-windows-81-2012r2"></a>
**Porque é que o meu desempenho é lento depois de montar a minha partilha de ficheiros Azure no Windows Server 2012 R2 ou no Windows 8.1?**  
    Existe um problema conhecido ao montar uma partilha de ficheiros Azure no Windows Server 2012 R2 e Windows 8.1. O problema foi corrigido na atualização cumulativa de abril de 2014 para o Windows 8.1 e Windows Server 2012 R2. Para obter um desempenho ótimo, certifique-se de que todas as instâncias do Windows Server 2012 R2 e Windows 8.1 têm este patch aplicado. (Deve sempre receber patches windows através do Windows Update.) Para obter mais informações, consulte o artigo associado da Base de Conhecimento do [Microsoft, desempenho lento quando aceder aos Ficheiros Azure do Windows 8.1 ou do Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Características e interoperabilidade com outros serviços
* <a id="cluster-witness"></a>
**Posso usar a minha partilha de ficheiros Azure como testemunha de partilha de *ficheiros* para o meu Cluster de Falha do Servidor do Windows?**  
    Atualmente, esta configuração não é suportada para uma partilha de ficheiros Azure. Para obter mais informações sobre como configurar isto para o armazenamento da Azure Blob, consulte [Implementar uma Testemunha em Nuvem para um Cluster failover](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Posso montar uma partilha de ficheiros Azure numa instância do Azure Container?**  
    Sim, as ações de ficheiros Azure são uma boa opção quando se pretende persistir informações para além da vida útil de uma instância de contentor. Para obter mais informações, consulte [a partilha de ficheiros Mount azure com as instâncias do Azure Container](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Existe uma operação de renome na API REST?**  
    Neste momento, não.

* <a id="nested-shares"></a>
**Posso criar ações aninhadas? Por outras palavras, uma parte sob uma ação?**  
    Não. A partilha de ficheiros *é* o controlador virtual que pode montar, por isso as ações aninhadas não são suportadas.

* <a id="ibm-mq"></a>
**Como uso ficheiros Azure com MQ IBM?**  
    A IBM divulgou um documento que ajuda os clientes da IBM MQ a configurar ficheiros Azure com o serviço IBM. Para obter mais informações, consulte [Como configurar um gestor de fila de vários instâncias IBM MQ com o serviço Microsoft Azure Files](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Ver também
* [Resolução de problemas Ficheiros Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Resolução de problemas Ficheiros Azure em Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Resolver problemas do Azure File Sync](storage-sync-files-troubleshoot.md)
