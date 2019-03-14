---
title: Perguntas mais frequentes (FAQ) sobre ficheiros do Azure | Documentos da Microsoft
description: Encontre respostas para perguntas mais frequentes sobre os ficheiros do Azure.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.date: 01/02/2019
ms.author: renash
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 7305fbf21557061250a80e095998ad7ea2f35510
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792519"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Perguntas mais frequentes (FAQ) sobre os ficheiros do Azure
[Os ficheiros do Azure](storage-files-introduction.md) oferece totalmente geridos partilhas de ficheiros na cloud que são acessíveis através da norma da indústria [protocolo Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). Pode montar partilhas de ficheiros do Azure em simultâneo em implementações na cloud ou no local do Windows, Linux e macOS. Também pode colocar em cache partilhas de ficheiros do Azure em máquinas do Windows Server com o Azure File Sync para acesso rápido perto de onde os dados são utilizados.

Este artigo responde a perguntas comuns sobre recursos de ficheiros do Azure e funcionalidades, incluindo a utilização do Azure File Sync com ficheiros do Azure. Se não vir a resposta à sua pergunta, pode contactar-nos através dos canais seguintes (em ordem de cada vez maiores):

1. A secção de comentários deste artigo.
2. [Fórum de armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [UserVoice de ficheiros do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Suporte da Microsoft. Para criar um novo pedido de suporte, no portal do Azure, no **ajudar** separador, selecione a **ajuda + suporte** botão e, em seguida, selecione **novo pedido de suporte**.

## <a name="general"></a>Geral
* <a id="why-files-useful"></a>
**Ficheiros do Azure é útil?**  
   Pode utilizar ficheiros do Azure para criar partilhas de ficheiros na cloud, sem ser responsável por gerenciar a sobrecarga de um servidor físico, dispositivo ou aplicação. Podemos fazer o trabalho repetitivas para si, incluindo a aplicação de atualizações de SO e substituição de discos ruins. Para saber mais sobre os cenários de ficheiros do Azure pode ajudá-lo com, veja [por que os ficheiros do Azure é útil](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
**Quais são as diferentes maneiras de aceder aos ficheiros nos ficheiros do Azure?**  
    Pode montar a partilha de ficheiros no seu computador local utilizando o protocolo SMB 3.0, ou pode usar ferramentas como o [Explorador de armazenamento](http://storageexplorer.com/) para aceder a ficheiros na partilha de ficheiros. Desde a sua aplicação, pode utilizar bibliotecas de cliente de armazenamento, REST APIs, PowerShell ou da CLI do Azure para aceder aos seus ficheiros na partilha de ficheiros do Azure.

* <a id="what-is-afs"></a>
**O que é o Azure File Sync?**  
    Pode utilizar o Azure File Sync para centralizar as partilhas de ficheiros da sua organização nos ficheiros do Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma suas máquinas do Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode usar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, o sistema de ficheiros de rede (NFS) e o ficheiro de transferência de protocolo de serviço (FTPS). Pode ter o número de caches que precisar em todo o mundo.

* <a id="files-versus-blobs"></a>
**Por que motivo devo utilizar uma partilha de ficheiros do Azure em comparação com o armazenamento de Blobs do Azure com os meus dados?**  
    Os ficheiros do Azure e BLOBs do Azure, armazenamento, os dois oferecem formas de armazenam grandes quantidades de dados na cloud, mas eles são úteis para fins de um pouco diferentes. 
    
    Armazenamento de Blobs do Azure é útil para aplicativos de grande escala, nativas da cloud que necessitam de armazenar dados não estruturados. Para maximizar o desempenho e dimensionamento, o armazenamento de Blobs do Azure é uma abstração mais simples de armazenamento que um sistema de ficheiros verdadeiro. Pode acessar o armazenamento de Blobs do Azure apenas por meio de bibliotecas de cliente baseada em REST (ou diretamente através do protocolo baseado em REST).

    O Azure Files é especificamente um sistema de ficheiros. Os ficheiros do Azure tem todos os resumos de arquivo que conhece e gosta de anos de trabalhar com sistemas de operativos no local. Como o armazenamento de BLOBs, ficheiros do Azure oferece uma interface REST e bibliotecas de cliente baseada em REST. Ao contrário do armazenamento de Blobs do Azure, ficheiros do Azure oferece acesso SMB para partilhas de ficheiros do Azure. Com o SMB, pode montar uma partilha de ficheiros Azure diretamente no Windows, Linux ou macOS, no local ou em VMs, na cloud sem escrever qualquer código ou anexar quaisquer controladores especiais para o sistema de ficheiros. Também pode colocar em cache as partilhas de ficheiros do Azure em servidores de ficheiros no local com o Azure File Sync para um acesso rápido, próximo de onde os dados são utilizados. 
   
    Para obter uma descrição mais detalhada sobre as diferenças entre ficheiros do Azure e o armazenamento de Blobs do Azure, consulte [decidindo quando usar o armazenamento de Blobs do Azure, ficheiros do Azure ou discos do Azure para](../common/storage-decide-blobs-files-disks.md). Para saber mais sobre o armazenamento de Blobs do Azure, veja [introdução ao armazenamento de BLOBs](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Por que motivo devo utilizar uma partilha de ficheiros do Azure em vez de discos do Azure?**  
    Um disco em discos do Azure é simplesmente um disco. Para obter o valor de discos do Azure, tem de anexar um disco a uma máquina virtual que está em execução no Azure. Discos do Azure podem ser utilizados por tudo o que usaria um disco para um servidor no local. Pode usá-lo como um disco de sistema do sistema operacional, como o espaço de comutação para um sistema operacional ou como armazenamento dedicado para uma aplicação. Um uso interessante para os discos do Azure é criar um servidor de ficheiros na cloud para utilizar nos mesmos locais onde poderá utilizar uma partilha de ficheiros do Azure. Implementar um servidor de ficheiros em máquinas de virtuais do Azure é uma forma de alto desempenho para obter o armazenamento de ficheiros no Azure quando necessitar de opções de implementação que não são atualmente suportadas pelo serviço ficheiros do Azure (por exemplo, o NFS protocolo premium ou suporte de armazenamento). 

    No entanto, a executar um servidor de ficheiros com discos do Azure como armazenamento de back-end, normalmente, é muito mais barato do que a utilizar uma partilha de ficheiros do Azure, por diversos motivos. Em primeiro lugar, além de pagar por armazenamento de disco, também tem paga para a despesa de um ou mais VMs do Azure em execução. Em segundo lugar, também tem de gerir as VMs que são utilizadas para executar o servidor de ficheiros. Por exemplo, é responsável por atualizações de SO. Por fim, se precisar em última análise de dados em cache no local, cabe a a configurar e gerir as tecnologias de replicação, por exemplo, ficheiro sistema replicação distribuído (DFSR), para fazer isso acontecer.

    É uma abordagem para obter o melhor dos ficheiros do Azure e um servidor de ficheiros que está alojado em máquinas de virtuais do Azure (além de utilizar discos do Azure como armazenamento de back-end) instalar o Azure File Sync num servidor de ficheiros que está alojado numa VM de cloud. Se a partilha de ficheiros do Azure está na mesma região que o servidor de ficheiros, pode ativar a cloud em camadas e defina o volume de percentagem de espaço livre para máximo (99%). Isso garante o mínimo duplicação de dados. Também pode utilizar todas as aplicações que quiser com seus servidores de ficheiros, como suportam a aplicativos que exigem do protocolo NFS.

    Para obter informações sobre uma opção para configurar um servidor de ficheiros de elevado desempenho e de elevada disponibilidade no Azure, consulte [clusters de convidados de implantação de VM de IaaS no Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Para obter uma descrição mais aprofundada das diferenças entre os ficheiros do Azure e discos do Azure, consulte [decidindo quando usar o armazenamento de Blobs do Azure, ficheiros do Azure ou discos do Azure para](../common/storage-decide-blobs-files-disks.md). Para saber mais sobre os discos do Azure, veja [descrição geral do Managed Disks do Azure](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
**Como posso começar a utilizar com os ficheiros do Azure?**  
   Introdução ao serviço ficheiros do Azure é fácil. Primeiro, [criar uma partilha de ficheiros](storage-how-to-create-file-share.md)e, em seguida, montá-la no seu sistema operacional de preferência: 

    * [Montar no Windows](storage-how-to-use-files-windows.md)
    * [Montar no Linux](storage-how-to-use-files-linux.md)
    * [Montar em macOS](storage-how-to-use-files-mac.md)

   Para obter um guia mais aprofundado sobre a implementação de uma partilha de ficheiros do Azure substituir as partilhas de ficheiros de produção na sua organização, consulte [planear uma implementação de ficheiros do Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
**Que opções de redundância de armazenamento são suportadas pelo serviço ficheiros do Azure?**  
    Atualmente, os ficheiros do Azure suporta armazenamento localmente redundante (LRS), o armazenamento com redundância de zona (ZRS) e o armazenamento georredundante (GRS). Planeamos suportar o armazenamento do acesso de leitura georredundante (RA-GRS) no futuro, mas não temos linhas cronológicas para partilhar neste momento.

* <a id="tier-options"></a>
**As camadas de armazenamento são suportadas nos ficheiros do Azure?**  
    Atualmente, os ficheiros do Azure suporta apenas a camada de armazenamento standard. Não temos linhas cronológicas para partilhar o armazenamento premium e o armazenamento de acesso esporádico de suporte neste momento. 
    
    > [!NOTE]
    > Não é possível criar partilhas de ficheiros do Azure a partir de contas de armazenamento apenas de BLOBs ou de contas de armazenamento premium.

* <a id="give-us-feedback"></a>
**Eu realmente Quero ver uma funcionalidade específica adicionada ao serviço ficheiros do Azure. Pode adicioná-lo?**  
    A equipe de ficheiros do Azure está interessada em ouvir todos os comentários que tem sobre o nosso serviço. Votar em pedidos de funcionalidades em [UserVoice de ficheiros do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Estamos esperando agradar com muitos recursos novos.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
**Que regiões são suportadas para o Azure File Sync?**  
    A lista de regiões disponíveis pode ser encontrada na [disponibilidade das regiões](storage-sync-files-planning.md#region-availability) orientar a secção do planejamento de sincronização de ficheiros do Azure. Continuamente será adicionado suporte para regiões adicionais, incluindo as regiões de não públicas.

* <a id="cross-domain-sync"></a>
**Pode ter servidores associados a um domínio e não associados ao domínio no mesmo grupo de sincronização?**  
    Sim. Um grupo de sincronização pode conter pontos finais do servidor que tenha membros do Active Directory diferentes, mesmo que não estejam associados a um domínio. Embora esta configuração funcione tecnicamente, não recomendamos a isso como uma configuração típica porque listas de controlo de acesso (ACLs) que estão definidas para ficheiros e pastas num servidor poderão não conseguir ser imposta por outros servidores no grupo de sincronização. Para obter melhores resultados, recomendamos que a sincronização entre os servidores que estão na mesma floresta do Active Directory, entre os servidores que estão em diferentes florestas do Active Directory, mas que tenham estabelecido relações de confiança ou entre servidores que não estão num domínio. Recomendamos que evite usar uma combinação destas configurações.

* <a id="afs-change-detection"></a>
**Criei um arquivo diretamente na minha partilha de ficheiros do Azure com o SMB ou no portal. Quanto tempo demora para o ficheiro sincronizar com os servidores no grupo de sincronização?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Se o mesmo ficheiro for alterado em dois servidores, aproximadamente ao mesmo tempo, o que acontece?**  
    O Azure File Sync utiliza uma estratégia de resolução de conflitos simples: mantemos ambas as alterações aos ficheiros que são alterados em dois servidores ao mesmo tempo. A alteração mais recentemente escrita mantém o nome do ficheiro original. O ficheiro de mais antigo tem a máquina de "origem" e o número de conflito acrescentado ao nome. Ela segue este Taxonomia: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\<ext\>  

    Por exemplo, se o primeiro conflito de CompanyReport.docx tornaria CompanyReport CentralServer.docx se CentralServer é onde ocorreu a escrita mais antiga. O segundo conflito teria o nome CompanyReport-CentralServer-1.docx.

* <a id="afs-storage-redundancy"></a>
**Armazenamento georredundante é suportado para o Azure File Sync?**  
    Sim, os ficheiros do Azure suporta o armazenamento localmente redundante (LRS) e o armazenamento georredundante (GRS). Se iniciar um failover de conta de armazenamento entre regiões emparelhadas de uma conta configurada para o GRS, a Microsoft recomenda que tratar a nova região como uma cópia de segurança de dados apenas. O Azure File Sync não começa automaticamente a sincronizar com a nova região primária. 

* <a id="sizeondisk-versus-size"></a>
**Por que não a *tamanho no disco* propriedade uma correspondência de ficheiro para o *tamanho* propriedade depois de utilizar o Azure File Sync?**  
 Ver [compreensão em camada de Cloud](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
**Como posso saber se um ficheiro tem sido em camadas?**  
 Ver [compreensão em camada de Cloud](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Um ficheiro que pretende utilizar tem sido em camadas. Como posso recuperar o arquivo em disco para utilizá-lo localmente?**  
 Ver [compreensão em camada de Cloud](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
**Como posso forçar um ficheiro ou diretório para ser colocado em camadas?**  
 Ver [compreensão em camada de Cloud](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
**Como é *espaço livre do volume* interpretados quando eu tiver vários pontos de extremidade do servidor num volume?**  
 Ver [compreensão em camada de Cloud](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
**Os ficheiros ou pastas são automaticamente excluídas pelo Azure File Sync?**  
    Por predefinição, o Azure File Sync exclui os ficheiros seguintes:
    * desktop.ini
    * thumbs.db
    * ehthumbs.db
    * ~$\*.\*
    * \*.laccdb
    * \*.tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    As seguintes pastas também são excluídas por padrão:

    * \System volume Information
    * \$RECICLE. BIN
    * \SyncShareState

* <a id="afs-os-support"></a>
**Posso utilizar o Azure File Sync com o Windows Server 2008 R2, o Linux ou o meu dispositivo de armazenamento ligados à rede (NAS)?**  
    Atualmente, o Azure File Sync suporta apenas o Windows Server 2016 e o Windows Server 2012 R2. Neste momento, não temos outros planos que podemos compartilhar, mas estamos abertos para dar suporte a plataformas adicionais com base na procura dos clientes. Fale na [UserVoice de ficheiros do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files) que plataformas gostaria de ver para oferecer suporte.

* <a id="afs-tiered-files-out-of-endpoint"></a>
**Por que os ficheiros em camadas existem fora do espaço de nomes de ponto final de servidor?**  
    Antes da versão do agente do Azure File Sync 3, o Azure File Sync bloqueado a movimentação de ficheiros em camadas fora o ponto final do servidor, mas no mesmo volume que o ponto final do servidor. Operações de cópia, move de ficheiros em camadas não e se move de camadas para outros volumes foram afetados. O motivo para esse comportamento foi a pressuposição implícita que Explorador de ficheiros e outras APIs do Windows têm que operações de movimentação no mesmo volume são (quase) instantâneas mudar o nome de operações. Isso significa que move tornará o Explorador de ficheiros ou outros métodos de mudança (como linha de comandos ou PowerShell) pareça estar sem resposta enquanto o Azure File Sync solicitar os dados da cloud. A partir [versão do agente do Azure File Sync 3.0.12.0](storage-files-release-notes.md#supported-versions), Azure File Sync permite-lhe mover um ficheiro em camadas fora o ponto final do servidor. Podemos evitar os efeitos negativos mencionados anteriormente, permitindo que o ficheiro em camadas para existir como um ficheiro em camadas fora o ponto final do servidor e, em seguida, recupera um arquivo em segundo plano. Isso significa que move no mesmo volume são instantânea, e podemos fazer todo o trabalho de recuperar o ficheiro no disco após a migração foi concluída. 

* <a id="afs-do-not-delete-server-endpoint"></a>
**Estou a ter um problema com o Azure File Sync em meu servidor (sincronização, na cloud a disposição em camadas, etc). Posso remover e recriar o ponto final do meu servidor?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
**Posso mover o serviço de sincronização de armazenamento e/ou a conta de armazenamento para um grupo de recursos diferente ou uma subscrição?**  
   Sim, o serviço de sincronização de armazenamento e/ou a conta de armazenamento pode ser movida para um grupo de recursos diferente ou de uma subscrição no inquilino do Azure AD existente. Se a conta de armazenamento for movida, precisa dar o acesso de serviço de sincronização de ficheiros de híbrida para a conta de armazenamento (veja [Certifique-se o Azure File Sync tem acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > O Azure File Sync não suporta a mover a subscrição para um Azure diferente inquilino do AD.
    
* <a id="afs-ntfs-acls"></a>
**Azure File Sync preservar o nível de diretório/ficheiro ACLs do NTFS, juntamente com dados armazenados nos ficheiros do Azure?**

    ACLs do NTFS executada a partir do ficheiro no local, servidores são mantidos pelo Azure File Sync como metadados. Os ficheiros do Azure não suporta a autenticação com credenciais do Azure AD para aceder a partilhas de ficheiros geridas pelo serviço de sincronização de ficheiros do Azure.
    
## <a name="security-authentication-and-access-control"></a>Segurança, a autenticação e controlo de acesso
* <a id="ad-support"></a>
**É a autenticação baseada no Active Directory e controlo de acesso suportadas pelo serviço ficheiros do Azure?**  
    
    Sim, ficheiros do Azure suporta o controlo com base na identidade de autenticação e acesso no Azure Active Directory (Azure AD) (pré-visualização). Autenticação do Azure do AD através de SMB para ficheiros do Azure tira partido do Azure Active Directory Domain Services para ativar VMs associados a um domínio aceder a partilhas, diretórios e arquivos usando credenciais do Azure AD. Para obter mais detalhes, consulte [autenticação de descrição geral do Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização)](storage-files-active-directory-overview.md). 

    Os ficheiros do Azure oferece duas maneiras adicionais para gerir o controlo de acesso:

    - Pode utilizar assinaturas de acesso partilhado (SAS) para gerar tokens que tenham permissões específicas e que são válido durante um intervalo de tempo especificado. Por exemplo, pode gerar um token de acesso só de leitura para um ficheiro específico que tenha uma expiração de 10 minutos. Qualquer pessoa que tiver o token, enquanto o token for válido tem acesso só de leitura a esse ficheiro para esses 10 minutos. Atualmente, as chaves de assinatura de acesso partilhado são suportadas apenas através da API REST ou nas bibliotecas de cliente. Tem de montar a partilha de ficheiros do Azure através de SMB com as chaves de conta de armazenamento.

    - O Azure File Sync preserva e replica todas as ACLs discricionárias ou DACLs, (seja baseada no Active Directory ou local) para todos os pontos de extremidade do servidor que ele sincroniza para. Porque o Windows Server já pode autenticar com o Active Directory, Azure File Sync é uma opção de preventiva em vigor até que o suporte completo para a autenticação baseada no Active Directory e suporte ACL é recebido.

* <a id="ad-support-regions"></a>
**É a pré-visualização do Azure AD através de SMB para ficheiros do Azure disponível em todas as regiões do Azure?**

    A pré-visualização está disponível em todas as regiões públicas.

* <a id="ad-support-on-premises"></a>
**Autenticação do Azure AD através de SMB para ficheiros do Azure (pré-visualização) suporta a autenticação com o Azure AD a partir de máquinas no local?**

    Não, ficheiros do Azure não suporta a autenticação com o Azure AD a partir de máquinas no local na versão de pré-visualização.

* <a id="ad-support-devices"></a>
**Autenticação faz do Azure AD através de SMB para acesso SMB de suporte de ficheiros do Azure (pré-visualização) com as credenciais do Azure AD a partir de dispositivos associados ao ou registado com o Azure AD?**

    Não, este cenário não é suportado.

* <a id="ad-support-rest-apis"></a>
**Existem APIs REST para suportar Get/Set/copiar diretório/ficheiro ACLs do NTFS?**

    A versão de pré-visualização não suporta as APIs REST para obter, definir ou copiar ACLs do NTFS para os ficheiros ou diretórios.

* <a id="ad-vm-subscription"></a>
**Posso aceder a ficheiros do Azure com credenciais do Azure AD a partir de uma VM numa subscrição diferente?**

    Se a subscrição sob a qual a partilha de ficheiros é implementada está associada com o mesmo inquilino do Azure AD como a implementação do Azure AD Domain Services para que a VM está associado a um domínio, em seguida, em seguida, pode aceder a ficheiros do Azure com as mesmas credenciais do Azure AD. A limitação é imposta não na subscrição, mas no Azure AD associado de inquilino.    
    
* <a id="ad-support-subscription"></a>
**Posso ativar a autenticação do Azure AD através de SMB para ficheiros do Azure com um inquilino do Azure AD que é diferente do inquilino principal, à qual a partilha de ficheiros está associada?**

    Não, ficheiros do Azure só suporta a integração do Azure AD com um inquilino do Azure AD que residem na mesma subscrição que a partilha de ficheiros. Apenas uma subscrição pode ser associada um inquilino do Azure AD.

* <a id="ad-linux-vms"></a>
**Autenticação do Azure AD através de SMB para ficheiros do Azure (pré-visualização) suporta VMs do Linux?**

    Não, a autenticação a partir de VMs do Linux não é suportada na versão de pré-visualização.

* <a id="ad-aad-smb-afs"></a>
**Pode aproveitar autenticação do Azure AD através de capacidades SMB em partilhas de ficheiros gerenciadas pelo Azure File Sync?**

    Não, o ficheiros do Azure não suporta a preservação de ACLs do NTFS em partilhas de ficheiros gerenciadas pelo Azure File Sync. O arquivo ACLs executadas a partir de locais servidores de ficheiros são mantidos pelo Azure File Sync. Qualquer ACLs do NTFS configurado de forma nativa em relação a ficheiros do Azure serão substituídas pelo serviço Azure File Sync. Além disso, ficheiros do Azure não suporta a autenticação com credenciais do Azure AD para aceder a partilhas de ficheiros geridas pelo serviço de sincronização de ficheiros do Azure.

* <a id="encryption-at-rest"></a>
**Como garantir que a minha partilha de ficheiros do Azure é encriptada em descanso?**  

    Encriptação do serviço de armazenamento do Azure está no processo de que está a ser ativado por predefinição em todas as regiões. Para estas regiões, não precisa realizar quaisquer ações para ativar a encriptação. Para outras regiões, consulte [encriptação do lado do servidor](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Como pode fornecer acesso a um ficheiro específico usando um navegador da web?**  

    Pode utilizar assinaturas de acesso partilhado para gerar tokens que tenham permissões específicas e que são válido durante um intervalo de tempo especificado. Por exemplo, pode gerar um token que fornece acesso só de leitura para um ficheiro específico, para um determinado período de tempo. Qualquer pessoa que tiver o URL pode aceder ao ficheiro diretamente a partir de qualquer browser enquanto o token é válido. Pode facilmente gerar uma chave de assinatura de acesso partilhado a partir de uma interface do Usuário, como o Explorador de armazenamento.

* <a id="file-level-permissions"></a>
**É possível especificar só de leitura ou permissões só de escrita em pastas dentro da partilha?**  

    Se montar a partilha de ficheiros com o SMB, não tem controlo ao nível da pasta sobre as permissões. No entanto, se criar uma assinatura de acesso partilhado com a REST API ou bibliotecas de cliente, pode especificar permissões só de leitura ou só de escrita em pastas dentro da partilha.

* <a id="ip-restrictions"></a>
**Pode implementar restrições de IP para uma partilha de ficheiros do Azure?**  

    Sim. Acesso à partilha de ficheiros do Azure pode ser restringido ao nível da conta de armazenamento. Para obter mais informações, consulte [configurar Firewalls de armazenamento do Azure e redes virtuais](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Que políticas de conformidade de dados suporta ficheiros do Azure?**  

   Os ficheiros do Azure é executado sobre a mesma arquitetura de armazenamento que é utilizada noutros serviços de armazenamento no armazenamento do Azure. Os ficheiros do Azure aplica-se as mesmas políticas de conformidade de dados que são usadas em outros serviços de armazenamento do Azure. Para obter mais informações sobre a conformidade de dados do armazenamento do Azure, pode consultar [ofertas de conformidade de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)e vá para o [Microsoft Trust Center](https://microsoft.com/trustcenter/default.aspx).

## <a name="on-premises-access"></a>Acesso no local

* <a id="port-445-blocked"></a>
**Monte de meu ISP ou a porta de blocos IT 445 que está a falhar ficheiros do Azure. O que devo fazer?**

    Pode saber mais sobre [várias formas de resolver bloqueada a porta 445 aqui](https://docs.microsoft.com/en-us/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked). Os ficheiros do Azure permite apenas conexões através de SMB 3.0 (com suporte de criptografia) de fora da região ou datacenter. Protocolo SMB 3.0 introduziu muitos recursos de segurança, incluindo a encriptação de canal, que é muito segura para utilizar a Internet. No entanto, sua possível que a porta 445 tenha sido bloqueado devido a motivos históricos de vulnerabilidades encontradas em versões SMB inferior. No caso do ideal, a porta deve ser bloqueada para apenas para o tráfego SMB 1.0 e o SMB 1.0 deve ser desativada em todos os clientes.

* <a id="expressroute-not-required"></a>
**Tenho de utilizar o Azure ExpressRoute para ligar a ficheiros do Azure ou para utilizar o Azure File Sync no local?**  

    Não. ExpressRoute não é necessário para aceder a uma partilha de ficheiros do Azure. Se montar um arquivo do Azure partilhar diretamente no local, tudo isso é necessário é fazer com que a porta 445 (saída de TCP) abra para acesso à internet (esta é a porta que o SMB utiliza para comunicar). Se estiver a utilizar o Azure File Sync, tudo o que é necessário é a porta 443 (saída de TCP) para acesso HTTPS (não necessário SMB). No entanto, *pode* utilizar o ExpressRoute com uma destas opções de acesso.

* <a id="mount-locally"></a>
**Como posso montar uma partilha de ficheiros do Azure no meu computador local?**  

    Pode montar a partilha de ficheiros utilizando o protocolo SMB, se a porta 445 (saída de TCP) esteja aberta e o cliente suportar o protocolo SMB 3.0 (por exemplo, se estiver a utilizar o Windows 10 ou Windows Server 2016). Se a porta 445 estiver bloqueada pela política da sua organização ou pelo seu ISP, pode utilizar o Azure File Sync para aceder a partilha de ficheiros do Azure.

## <a name="backup"></a>Cópia de segurança
* <a id="backup-share"></a>
**Como posso fazer cópia de segurança meu arquivo do Azure partilhar?**  
    Pode usar periódica [instantâneos de partilha](storage-snapshots-files.md) para proteção contra eliminações acidentais. Também pode utilizar o AzCopy, Robocopy ou uma ferramenta de cópia de segurança de terceiros que pode fazer backup de uma partilha de ficheiros instalado. Cópia de segurança do Azure oferece a cópia de segurança de ficheiros do Azure. Saiba mais sobre [cópia de segurança do Azure partilhas pelo Azure Backup de ficheiros](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Instantâneos de partilha

### <a name="share-snapshots-general"></a>Instantâneos de partilha: Geral
* <a id="what-are-snaphots"></a>
**Quais são os instantâneos de partilha de ficheiros?**  
    Pode utilizar instantâneos de partilha de ficheiros do Azure para criar uma versão só de leitura de suas partilhas de ficheiros. Também pode utilizar ficheiros do Azure para copiar uma versão anterior do seu conteúdo de volta para a mesma partilha, para uma localização alternativa no Azure ou no local para obter mais modificações. Para saber mais sobre os instantâneos de partilha, consulte a [descrição geral de instantâneo de partilha](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Onde estão armazenados as minhas instantâneos de partilha?**  
    Instantâneos de partilha são armazenados na mesma conta de armazenamento como a partilha de ficheiros.

* <a id="snapshot-perf-impact"></a>
**Há alguma implicação de desempenho para a utilização de instantâneos de partilha?**  
    Instantâneos de partilha não tem qualquer sobrecarga de desempenho.

* <a id="snapshot-consistency"></a>
**Instantâneos de partilha são consistentes com aplicações?**  
    Não, os instantâneos de partilha não são consistentes com aplicações. O utilizador deve liberar as gravações no aplicativo para a partilha antes de tirar o instantâneo de partilha.

* <a id="snapshot-limits"></a>
**Existem limites no número de instantâneos de partilha, que posso usar?**  
    Sim. Os ficheiros do Azure podem reter um máximo de instantâneos de partilha de 200. Instantâneos de partilha não contam para a quota de partilha, portanto, não há nenhum limite por partilha no total de espaço utilizado por todos os instantâneos de partilha. Limites de conta de armazenamento ainda se aplicam. Depois de instantâneos de partilha de 200, tem de eliminar instantâneos anteriores para criar instantâneos de partilha de novo.

* <a id="snapshot-cost"></a>
**Quanto deve partilhar instantâneos de custo?**  
    Padrão transação e o custo de armazenamento standard serão aplicada a instantâneo. Os instantâneos são incrementais por natureza. O instantâneo de base é a partilha em si. Todos os instantâneos subsequentes são incrementais e apenas irão armazenar diff partir do instantâneo anterior. Isso significa que as alterações que serão vistas na fatura será mínimas se o volume de alterações de carga de trabalho é mínimo. Ver [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para ficheiros do Azure padrão, informações sobre preços. Hoje, a forma de examinar o tamanho consumido por instantâneo de partilha é ao comparar a capacidade faturada com utilizado capacidade. Estamos a trabalhar em ferramentas para melhorar a geração de relatórios.

* <a id="ntfs-acls-snaphsots"></a>
**São ACLs do NTFS em diretórios e arquivos que permanecem no instantâneos de partilha?**
    ACLs do NTFS em diretórios e arquivos são mantidas no instantâneos de partilha.

### <a name="create-share-snapshots"></a>Criar instantâneos de partilha
* <a id="file-snaphsots"></a>
**Pode criar instantâneo de partilha de ficheiros individuais?**  
    Instantâneos de partilha são criados ao nível de partilha de ficheiros. Pode restaurar ficheiros individuais a partir de instantâneo de partilha de ficheiros, mas não é possível criar instantâneos de partilha ao nível do ficheiro. No entanto, se efetuou um instantâneo de partilha de nível de compartilhamento e pretende listar instantâneos de partilha onde foi alterada um ficheiro específico, pode fazê-lo sob **versões anteriores** numa partilha montada do Windows. 
    
    Se precisar de uma funcionalidade de instantâneo de ficheiro, fale na [UserVoice de ficheiros do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Pode criar instantâneos de partilha de uma partilha de ficheiros encriptados?**  
    Pode tirar um instantâneo de partilha de partilhas de ficheiros do Azure que tem a encriptação em repouso ativado. Pode restaurar ficheiros a partir de um instantâneo de partilha para uma partilha de ficheiros encriptados. Se a partilha é criptografada, o instantâneo de partilha também é encriptado.

* <a id="geo-redundant-snaphsots"></a>
**Meu instantâneos de partilha são georredundante?**  
    Instantâneos de partilha têm a mesma redundância de que a partilha de ficheiros do Azure para o qual foram executadas. Se tiver selecionado o armazenamento com redundância geográfica para a sua conta, a partilha de instantâneo também é armazenada maneira redundante na região associada.

### <a name="manage-share-snapshots"></a>Gerir instantâneos de partilha
* <a id="browse-snapshots-linux"></a>
**Posso navegar pelos meus instantâneos de partilha do Linux?**  
    Pode utilizar a CLI do Azure para criar, listar, procurar e restaurar instantâneos de partilha no Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Pode copiar os instantâneos de partilha para uma conta de armazenamento diferente?**  
    Pode copiar ficheiros de instantâneos de partilha para outra localização, mas não é possível copiar os instantâneos de partilha propriamente ditas.

### <a name="restore-data-from-share-snapshots"></a>Restaurar dados a partir de instantâneos de partilha
* <a id="promote-share-snapshot"></a>
**Pode promover instantâneo de partilha para a partilha de base?**  
    Pode copiar dados a partir de um instantâneo de partilha para qualquer outro tipo de destino. Não é possível promover instantâneo de partilha para a partilha de base.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Posso restaurar os dados do meu instantâneo de partilha para uma conta de armazenamento diferente?**  
    Sim. Ficheiros a partir de um instantâneo de partilha podem ser copiados para a localização original ou para uma localização alternativa que inclui a mesma conta de armazenamento ou uma conta de armazenamento diferente na mesma região ou em regiões diferentes. Também pode copiar ficheiros para uma localização no local ou para qualquer outra cloud.    
  
### <a name="clean-up-share-snapshots"></a>Limpar os instantâneos de partilha
* <a id="delete-share-keep-snapshots"></a>
**Pode eliminar a minha partilha mas não eliminar o meu instantâneos de partilha?**  
    Se tiver instantâneos de partilha do Active Directory em seu compartilhamento, não é possível eliminar a partilha. Pode utilizar uma API para eliminar os instantâneos de partilha, juntamente com a partilha. Também pode eliminar os instantâneos de partilha e a partilha no portal do Azure.

* <a id="delete-share-with-snapshots"></a>
**O que acontece aos meus instantâneos de partilha se posso excluir minha conta de armazenamento?**  
    Se eliminar a conta de armazenamento, os instantâneos de partilha também são eliminados.

## <a name="billing-and-pricing"></a>Faturação e preços
* <a id="vm-file-share-network-traffic"></a>
**O tráfego de rede entre uma VM do Azure e uma partilha de ficheiros do Azure conta como largura de banda externa que é cobrada na subscrição?**  
    Se a partilha de ficheiros e a VM na mesma região do Azure, não há sem custos adicionais para o tráfego entre a partilha de ficheiros e a VM. Se a partilha de ficheiros e a VM estiverem em regiões diferentes, o tráfego entre as mesmas são cobradas como largura de banda externa.

* <a id="share-snapshot-price"></a>
**Quanto deve partilhar instantâneos de custo?**  
     Durante a pré-visualização, está sem custos para a capacidade de instantâneo de partilha. Os custos de saída e transações de armazenamento Standard aplicam-se. Após a disponibilidade geral, são cobradas as assinaturas de capacidade e transações nos mesmos.
     
     Instantâneos de partilha são incrementais por natureza. O instantâneo de partilha de base é a partilha em si. Todos os instantâneos de partilha subsequentes são incrementais e armazenam apenas a diferença de instantâneo de partilha anterior. É-lhe cobrada apenas para o conteúdo alterado. Se tiver uma partilha de 100 gib de dados, mas apenas 5 GiB foi alterada desde sua última instantâneo de partilha, o instantâneo de partilha consome apenas 5 GiB adicionais e é faturado por 105 GiB. Para obter mais informações sobre transações e os custos de saída padrão, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Dimensionamento e desempenho
* <a id="files-scale-limits"></a>
**Quais são os limites de dimensionamento de ficheiros do Azure?**  
    Para obter informações sobre metas de escalabilidade e desempenho para ficheiros do Azure, consulte [metas de escalabilidade e desempenho de ficheiros do Azure](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Preciso de uma partilha de ficheiros maior do que os ficheiros do Azure oferece atualmente. Pode aumentar o tamanho da minha partilha de ficheiros do Azure?**  
    Não. O tamanho máximo de uma partilha de ficheiros do Azure é de 5 TiB. Atualmente, este é um limite rígido que nós não é possível ajustar. Estamos a trabalhar numa solução para aumentar o tamanho de partilha para TiB de 100, mas não temos linhas cronológicas para partilhar neste momento.

* <a id="open-handles-quota"></a>
**O número de clientes pode aceder ao mesmo ficheiro simultaneamente?**   
    Existe uma quota de 2.000 identificadores abertos num único arquivo. Quando tiver de 2.000 identificadores abertos, é apresentada uma mensagem de erro que diz que a quota é atingida.

* <a id="zip-slow-performance"></a>
**O meu desempenho é lento quando eu deszipar ficheiros nos ficheiros do Azure. O que devo fazer?**  
    Para transferir um grande número de ficheiros para ficheiros do Azure, recomendamos que utilize o AzCopy (para Windows; em pré-visualização para Linux e UNIX) ou o Azure PowerShell. Estas ferramentas foram otimizadas para transferência de rede.

* <a id="slow-perf-windows-81-2012r2"></a>
**Por que é o meu desempenho lento depois posso montar a minha partilha de ficheiros do Azure no Windows Server 2012 R2 ou Windows 8.1?**  
    Existe um problema conhecido ao montar uma partilha de ficheiros do Azure no Windows Server 2012 R2 e Windows 8.1. O problema foi corrigido na atualização cumulativa de Abril de 2014 para Windows 8.1 e Windows Server 2012 R2. Para um desempenho ideal, certifique-se de que todas as instâncias do Windows Server 2012 R2 e Windows 8.1 têm esse patch aplicada. (Sempre deverá receber patches do Windows por meio do Windows Update.) Para obter mais informações, consulte o artigo associado da Base de dados de conhecimento da Microsoft [diminuir o desempenho ao aceder aos ficheiros do Azure do Windows 8.1 ou Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funcionalidades e a interoperabilidade com outros serviços
* <a id="cluster-witness"></a>
**Posso usar minha partilha de ficheiros do Azure como um *testemunho de partilha de ficheiros* para o meu Cluster de ativação pós-falha do Windows Server?**  
    Atualmente, esta configuração não é suportada para uma partilha de ficheiros do Azure. Para obter mais informações sobre como definir isso tendo em vista o armazenamento de Blobs do Azure, consulte [implementar um testemunho de nuvem para um Cluster de ativação pós-falha](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Pode montar uma partilha de ficheiros do Azure numa instância de contentor do Azure?**  
    Sim, as partilhas de ficheiros do Azure são uma boa opção quando pretender manter as informações além do tempo de vida de uma instância de contentor. Para obter mais informações, consulte [montar uma partilha de ficheiros do Azure com o Azure Container instances](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Existe uma operação de mudança de nome na REST API?**  
    Neste momento, não.

* <a id="nested-shares"></a>
**Posso configurar partilhas aninhadas? Em outras palavras, uma partilha numa partilha?**  
    Não. A partilha de ficheiros *é* o controlador virtual que é possível montar, pelo que não são suportadas partilhas aninhadas.

* <a id="ibm-mq"></a>
**Como posso utilizar ficheiros do Azure com o IBM MQ?**  
    A IBM lançou um documento que ajuda os clientes do IBM MQ configurar ficheiros do Azure com o serviço da IBM. Para obter mais informações, consulte [como configurar um Gestor de filas de várias instâncias do IBM MQ com o serviço de ficheiros do Microsoft Azure](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Consulte também
* [Resolver problemas de ficheiros do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Resolver problemas de ficheiros do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Resolver problemas da sincronização de ficheiros do Azure](storage-sync-files-troubleshoot.md)
