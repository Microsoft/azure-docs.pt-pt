---
title: Planejando uma implantação de Ficheiros Azure | Microsoft Docs
description: Compreenda o planeamento de uma implantação de Ficheiros Azure. Pode montar diretamente uma partilha de ficheiros Azure ou partilhar ficheiros cache Azure no local com o Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 2cb3bee770653173f1a40b209c27d2dc92c7df11
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718040"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planear uma implementação dos Ficheiros do Azure
[Os Ficheiros Azure](storage-files-introduction.md) podem ser implementados de duas maneiras principais: montando diretamente as ações de ficheiros Azure sem servidor ou caching Azure file shares on-in usando Azure File Sync. Qual a opção de implementação que escolhe altera as coisas que precisa de considerar como planeia para a sua implantação. 

- **Montagem direta de uma partilha de ficheiros Azure**: Uma vez que os Ficheiros Azure fornecem acesso ao Bloco de Mensagens do Servidor (SMB) ou ao Sistema de Ficheiros de Rede (NFS), pode montar ações de ficheiros Azure no local ou na nuvem utilizando os clientes padrão SMB ou NFS disponíveis no seu SO. Como as ações de ficheiros Azure são sem servidor, a implementação para cenários de produção não requer a gestão de um servidor de ficheiros ou de um dispositivo NAS. Isto significa que não tem de aplicar patches de software ou trocar discos físicos. 

- **A partilha de ficheiros Cache Azure no local com o Azure File Sync**: O Azure File Sync permite centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma um Servidor Windows no local (ou nuvem) numa cache rápida da sua partilha de ficheiros Azure SMB. 

Este artigo aborda principalmente considerações de implementação para a implementação de uma partilha de ficheiros Azure a ser montada diretamente por um cliente no local ou na nuvem. Para planear uma implementação de Azure File Sync, consulte [o Planeamento para uma implementação de Sincronização de Ficheiros Azure](storage-sync-files-planning.md).

## <a name="available-protocols"></a>Protocolos disponíveis

A Azure Files oferece dois protocolos que podem ser utilizados na montagem das suas ações de ficheiros, SMB e Sistema de Ficheiros de Rede (NFS). Para mais informações sobre estes protocolos, consulte os [protocolos de partilha de ficheiros Azure](storage-files-compare-protocols.md).

> [!IMPORTANT]
> A maior parte do conteúdo deste artigo aplica-se apenas às ações da SMB. Tudo o que se aplica às ações da NFS indicará especificamente que é aplicável.

## <a name="management-concepts"></a>Conceitos de gestão
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Ao colocar ações de ficheiros Azure em contas de armazenamento, recomendamos:

- Apenas a implementação de ações de ficheiros Azure em contas de armazenamento com outras ações de ficheiros Azure. Embora as contas de armazenamento do GPv2 permitam ter contas de armazenamento mistas, uma vez que os recursos de armazenamento, como as ações de ficheiros Azure e os contentores blob, partilham os limites da conta de armazenamento, misturar recursos em conjunto pode dificultar a resolução de problemas de desempenho mais tarde. 

- Prestar atenção às limitações de IOPS de uma conta de armazenamento ao implementar ações de ficheiros Azure. Idealmente, você mapeia ações de arquivo 1:1 com contas de armazenamento, no entanto isso pode nem sempre ser possível devido a vários limites e restrições, tanto da sua organização como da Azure. Quando não for possível ter apenas uma ação de ficheiro implantada numa única conta de armazenamento, considere quais as ações que serão altamente ativas e quais as ações menos ativas para garantir que as ações de ficheiros mais quentes não sejam colocadas na mesma conta de armazenamento em conjunto.

- Apenas implemente contas de GPv2 e FileStorage e atualize o GPv1 e as contas de armazenamento clássicas quando as encontrar no seu ambiente. 

## <a name="identity"></a>Identidade
Para aceder a uma partilha de ficheiros Azure, o utilizador da partilha de ficheiros deve ser autenticado e ter autorização para aceder à partilha. Isto é feito com base na identidade do utilizador que acede à partilha de ficheiros. A Azure Files integra-se com três principais fornecedores de identidade:
- **No local Serviços de Domínio de Diretório Ativo (DS AD ou DS no local)**: As contas de armazenamento Azure podem ser unidas a um serviço de domínio de propriedade do cliente, de propriedade do cliente, serviços de domínio de diretório ativo, tal como um servidor de ficheiros do Windows Server ou um dispositivo NAS. Pode implantar um controlador de domínio no local, num VM Azure, ou mesmo como VM em outro fornecedor de nuvem; O Azure Files é agnóstico até onde o seu controlador de domínio está hospedado. Uma vez que uma conta de armazenamento é unida ao domínio, o utilizador final pode montar uma partilha de ficheiro com a conta de utilizador com a qual assinou no seu PC. A autenticação baseada em AD utiliza o protocolo de autenticação Kerberos.
- **Azure Ative Directory Domain Services (Azure AD DS)**: A azure AD DS fornece um controlador de domínio gerido pela Microsoft que pode ser usado para recursos Azure. O domínio que une a sua conta de armazenamento ao Azure AD DS proporciona benefícios semelhantes ao domínio que o une a um Ative Directory de propriedade do cliente. Esta opção de implementação é mais útil para cenários de elevação e mudança de aplicações que requerem permissões baseadas em AD. Uma vez que a Azure AD DS fornece autenticação baseada em AD, esta opção também utiliza o protocolo de autenticação Kerberos.
- **Chave da conta de armazenamento Azure**: As ações de ficheiros Azure também podem ser montadas com uma chave de conta de armazenamento Azure. Para montar uma partilha de ficheiros desta forma, o nome da conta de armazenamento é usado como nome de utilizador e a chave da conta de armazenamento é usada como senha. A utilização da chave de conta de armazenamento para montar a partilha de ficheiros Azure é efetivamente uma operação de administrador, uma vez que a partilha de ficheiros montada terá permissões completas em todos os ficheiros e pastas da partilha, mesmo que tenham ACLs. Ao utilizar a chave de conta de armazenamento para montar sobre o SMB, é utilizado o protocolo de autenticação NTLMv2.

Para os clientes que migram de servidores de ficheiros no local ou criam novas ações de ficheiros em Ficheiros Azure destinados a comportarem-se como servidores de ficheiros Windows ou aparelhos NAS, o domínio que une a sua conta de armazenamento ao **Ative Directory do Cliente** é a opção recomendada. Para saber mais sobre o domínio que une a sua conta de armazenamento a um Ative Directory de propriedade do cliente, consulte [a visão geral do Azure Files Ative Directory](storage-files-active-directory-overview.md).

Se pretender utilizar a chave da conta de armazenamento para aceder às suas ações de ficheiros Azure, recomendamos a utilização de pontos finais de serviço, conforme descrito na secção [Networking.](#networking)

## <a name="networking"></a>Rede
As ações de ficheiros Azure estão acessíveis a partir de qualquer lugar através do ponto final público da conta de armazenamento. Isto significa que os pedidos autenticados, tais como pedidos autorizados pela identidade de início de súmã de um utilizador, podem ter origem seguramente dentro ou fora de Azure. Em muitos ambientes de clientes, uma montagem inicial da partilha de ficheiros Azure na sua estação de trabalho no local falhará, mesmo que os suportes dos VMs Azure tenham sucesso. A razão para isso é que muitas organizações e fornecedores de serviços de internet (ISPs) bloqueiam a porta que o SMB usa para comunicar, porta 445. Para ver o resumo de ISPs que permitem ou não o acesso a partir da porta 445, aceda a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para desbloquear o acesso à sua partilha de ficheiros Azure, tem duas opções principais:

- Desbloqueie a porta 445 para a rede de instalações da sua organização. As ações de ficheiros Azure só podem ser acedidas externamente através do ponto final público utilizando protocolos de segurança na Internet, tais como SMB 3.0 e a API FileREST. Esta é a forma mais fácil de aceder à sua partilha de ficheiros Azure a partir das instalações, uma vez que não requer uma configuração avançada de rede para além de alterar as regras de saída da sua organização, no entanto, recomendamos que remova o legado e versões prepretadas do protocolo SMB, nomeadamente SMB 1.0. Para aprender a fazê-lo, consulte [o Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) e [o Linux de Segurança](storage-how-to-use-files-linux.md#securing-linux).

- Aceda a ações de ficheiros Azure sobre uma ligação ExpressRoute ou VPN. Ao aceder à sua partilha de ficheiros Azure através de um túnel de rede, pode montar a sua partilha de ficheiros Azure como uma partilha de ficheiros no local, uma vez que o tráfego SMB não atravessa o seu limite organizacional.   

Embora do ponto de vista técnico seja consideravelmente mais fácil montar as suas ações de ficheiros Azure através do ponto final público, esperamos que a maioria dos clientes opte por montar as suas ações de ficheiroS Azure através de uma ligação ExpressRoute ou VPN. A montagem com estas opções é possível com as ações da SMB e da NFS. Para isso, terá de configurar o seguinte para o seu ambiente:  

- **Fazer túneis de rede utilizando expressRoute, Site-to-Site ou VPN ponto-a-local**: Fazer túneis numa rede virtual permite aceder a partilhas de ficheiros Azure a partir do local, mesmo que a porta 445 esteja bloqueada.
- **Pontos finais privados**: Os pontos finais privados dão à sua conta de armazenamento um endereço IP dedicado a partir do espaço de endereço da rede virtual. Isto permite a escavação de rede sem necessidade de abrir redes no local até todas as gamas de endereços IP detidas pelos clusters de armazenamento Azure. 
- **DNS reencaminha :** Configure o SEU DNS no local para resolver o nome da sua conta de armazenamento `storageaccount.file.core.windows.net` (para as regiões de nuvem pública) para resolver o endereço IP dos seus pontos finais privados.

Para planear a rede associada à implementação de uma partilha de ficheiros Azure, consulte [considerações de networking de ficheiros Azure Files](storage-files-networking-overview.md).

## <a name="encryption"></a>Encriptação
O Azure Files suporta dois tipos diferentes de encriptação: encriptação em trânsito, que se relaciona com a encriptação utilizada na montagem/acesso à partilha de ficheiros Azure, e encriptação em repouso, que se relaciona com a forma como os dados são encriptados quando são armazenados no disco. 

### <a name="encryption-in-transit"></a>Encriptação de dados em circulação

> [!IMPORTANT]
> Esta secção cobre a encriptação em detalhes de trânsito para ações SMB. Para obter detalhes sobre encriptação em trânsito com ações NFS, consulte [Segurança](storage-files-compare-protocols.md#security).

Por padrão, todas as contas de armazenamento Azure têm encriptação em trânsito ativada. Isto significa que quando monta uma partilha de ficheiros sobre o SMB ou acede-lo através do protocolo FileREST (por exemplo através do portal Azure, PowerShell/CLI ou Azure SDKs), os Ficheiros Azure só permitirão a ligação se for feita com SMB 3.0+ com encriptação ou HTTPS. Os clientes que não suportam SMB 3.0 ou clientes que suportem encriptação SMB 3.0 mas não SMB não poderão montar a partilha de ficheiros Azure se a encriptação em trânsito estiver ativada. Para obter mais informações sobre quais os sistemas operativos que suportam SMB 3.0 com encriptação, consulte a nossa documentação detalhada para [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)e [Linux.](storage-how-to-use-files-linux.md) Todas as versões atuais do suporte de PowerShell, CLI e SDKs HTTPS.  

Pode desativar a encriptação em trânsito para uma conta de armazenamento Azure. Quando a encriptação é desativada, os Ficheiros Azure também permitirão chamadas SMB 2.1, SMB 3.0 sem encriptação e chamadas de API de ficheiros não encriptadas em HTTP. A principal razão para desativar a encriptação em trânsito é suportar uma aplicação antiga que deve ser executada num sistema operativo mais antigo, como o Windows Server 2008 R2 ou a distribuição linux mais antiga. A Azure Files só permite ligações SMB 2.1 na mesma região de Azure que a partilha de ficheiros Azure; um cliente SMB 2.1 fora da região de Azure da partilha de ficheiros Azure, como no local ou numa região de Azure diferente, não poderá aceder à partilha de ficheiros.

Recomendamos vivamente garantir que a encriptação dos dados em trânsito está ativada.

Para obter mais informações sobre encriptação em trânsito, consulte [a necessidade de transferência segura no armazenamento Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Encriptação inativa
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Proteção de dados
A Azure Files tem uma abordagem em várias camadas para garantir que os seus dados são apoiados, recuperáveis e protegidos contra ameaças à segurança.

### <a name="soft-delete"></a>Eliminação recuperável
A eliminação suave para ações de ficheiros (pré-visualização) é uma definição de nível de conta de armazenamento que lhe permite recuperar a sua parte do ficheiro quando esta é acidentalmente eliminada. Quando uma partilha de ficheiros é eliminada, passa para um estado apagado suave em vez de ser permanentemente apagada. Pode configurar a quantidade de tempo que os dados suaves eliminados são recuperáveis antes de serem permanentemente eliminados, e desembolsar a parte a qualquer momento durante este período de retenção. 

Recomendamos que se apale a exclusão suave para a maioria das ações de ficheiros. Se tiver um fluxo de trabalho em que a eliminação de ações é comum e esperada, pode decidir ter um curto período de retenção ou não ter uma eliminação suave ativada.

Para obter mais informações sobre a eliminação suave, consulte [Prevenir a eliminação acidental de dados](./storage-files-prevent-file-share-deletion.md).

### <a name="backup"></a>Backup
Pode fazer cópias do seu ficheiro Azure através [de imagens de partilha,](./storage-snapshots-files.md)que são cópias pontuais e pontuais da sua parte. Os instantâneos são incrementais, o que significa que só contêm tantos dados como mudou desde o instantâneo anterior. Pode ter até 200 instantâneos por ação de ficheiro e retê-los até 10 anos. Pode tirar manualmente estas fotos no portal Azure, via PowerShell, ou interface de linha de comando (CLI), ou pode utilizar [a Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). As imagens instantâneas são armazenadas dentro da sua parte do ficheiro, o que significa que se eliminar a sua parte do ficheiro, as suas imagens também serão eliminadas. Para proteger as cópias de segurança instantâneas da eliminação acidental, certifique-se de que a eliminação suave está ativada para a sua parte.

[A Azure Backup para ações de ficheiros Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) lida com o agendamento e retenção de instantâneos. As suas capacidades de avô-pai-filho (GFS) significam que você pode tomar fotos diárias, semanais, mensais e anualmente, cada uma com o seu próprio período de retenção distinto. O Azure Backup também orquestra a ativação de eliminação suave e recebe um bloqueio de eliminação numa conta de armazenamento assim que qualquer partilha de ficheiros dentro dela estiver configurada para cópia de segurança. Por último, o Azure Backup fornece determinadas capacidades de monitorização e alerta chave que permitem aos clientes ter uma visão consolidada do seu espólio de backup.

Pode executar restauros de nível de item e de nível de partilha no portal Azure utilizando a Cópia de Segurança Azure. Tudo o que precisa de fazer é escolher o ponto de restauro (uma determinada imagem instantânea), o ficheiro ou diretório em particular, se relevante, e, em seguida, a localização (original ou alternativa) a que deseja restaurar. O serviço de cópias de segurança e mostra o progresso da restauração no portal.

Para obter mais informações sobre a cópia de segurança, consulte [a cópia de segurança do ficheiro Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-defender-for-azure-files"></a>Azure Defender para Ficheiros Azure 
O Azure Defender for Azure Storage (anteriormente Advanced Threat Protection for Azure Storage) fornece uma camada adicional de inteligência de segurança que fornece alertas quando deteta atividade anómala na sua conta de armazenamento, por exemplo tentativas de acesso incomuns. Também executa análises de reputação de malware e irá alertar sobre malware conhecido. Pode configurar o Azure Defender num nível de subscrição ou conta de armazenamento através do Azure Security Center. 

Para obter mais informações, consulte [Introdução ao Azure Defender para armazenamento](../../security-center/defender-for-storage-introduction.md).

## <a name="storage-tiers"></a>Camadas de armazenamento
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Permitir que as ações de ficheiros padrão se estendem até 100 TiB
Por padrão, as ações de ficheiros padrão podem abranger apenas até 5 TiB, mas pode aumentar o limite de ações para 100 TiB. Para aprender a aumentar o seu limite de ações, consulte [Ativar e criar grandes ações de ficheiros](storage-files-how-to-create-large-file-share.md).


#### <a name="limitations"></a>Limitações
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundância
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migração
Em muitos casos, não irá estabelecer uma nova partilha de ficheiros net para a sua organização, mas sim migrar uma parte de ficheiro existente de um servidor de ficheiros no local ou dispositivo NAS para Azure Files. Escolher a estratégia e ferramenta de migração certas para o seu cenário é importante para o sucesso da sua migração. 

O [artigo de visão geral da migração](storage-files-migration-overview.md) cobre brevemente o básico e contém uma tabela que o leva a guias de migração que provavelmente cobrem o seu cenário.

## <a name="next-steps"></a>Passos seguintes
* [Planeamento para uma implementação de sincronização de ficheiros Azure](storage-sync-files-planning.md)
* [Implantação de ficheiros Azure](./storage-how-to-create-file-share.md)
* [Implementação de sincronização de ficheiros Azure](storage-sync-files-deployment-guide.md)
* [Confira o artigo visão geral da migração para encontrar o guia de migração para o seu cenário](storage-files-migration-overview.md)