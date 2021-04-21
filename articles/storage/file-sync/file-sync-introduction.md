---
title: Introdução ao Azure File Sync | Microsoft Docs
description: Uma visão geral do Azure File Sync, um serviço que lhe permite criar e utilizar ações de ficheiros de rede na nuvem utilizando o protocolo SMB padrão da indústria.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da40783e3d299b0edf27c6d045dbc6dcfc5cf964
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797664"
---
# <a name="what-is-azure-file-sync"></a>O que é o Azure File Sync?
O Azure File Sync permite centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros Windows. Embora alguns utilizadores possam optar por manter uma cópia completa dos seus dados localmente, o Azure File Sync tem ainda a capacidade de transformar o Windows Server numa cache rápida da sua partilha de ficheiros Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos dados localmente, incluindo SMB, NFS e FTPS. Podes ter o número de caches que precisares em todo o mundo.   

## <a name="videos"></a>Vídeos
| Apresentando a Azure File Sync | Ficheiros Azure com Sincronização (Ignite 2019)  |
|-|-|
| [![Screencast do vídeo Azure File Sync - clique para reproduzir!](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Screencast dos Ficheiros Azure com apresentação de Sync - clique para reproduzir!](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>Benefícios do Azure File Sync

### <a name="cloud-tiering"></a>Disposição em camadas na cloud
Com o tiering de nuvem ativado, os seus ficheiros mais frequentemente acedidos estão em cache no servidor local e os seus ficheiros com menos acesso são hierárquicos para a nuvem. Pode controlar a quantidade de espaço em disco local utilizado para cache. Os ficheiros hierárquicos podem ser rapidamente recolhidos a pedido, tornando a experiência perfeita, permitindo ao mesmo tempo reduzir os custos, uma vez que apenas precisa de armazenar uma fração dos seus dados no local. Para obter mais informações sobre o tiering da nuvem, consulte [a visão geral do tiering da Cloud](file-sync-cloud-tiering-overview.md). 

### <a name="multi-site-access-and-sync"></a>Acesso a vários locais e sincronização
O Azure File Sync é ideal para cenários de acesso distribuídos. Para cada um dos seus escritórios, pode providenciar um Servidor Windows local como parte da sua implementação de Azure File Sync. As alterações efetuadas num servidor de um escritório sincronizam-se automaticamente com os servidores em todos os outros escritórios.

### <a name="business-continuity-and-disaster-recovery"></a>Continuidade de negócio e recuperação após desastre
O Azure File Sync é apoiado pela Azure Files, que oferece várias opções de redundância para armazenamento altamente disponível. Uma vez que o Azure contém cópias resilientes dos seus dados, o seu servidor local torna-se um dispositivo de caching descartável, e a recuperação de um servidor falhado pode ser feita adicionando um novo servidor à sua implementação de Azure File Sync. Em vez de restaurar a partir de uma cópia de segurança local, você forja outro Servidor do Windows, instale o agente Azure File Sync nele e, em seguida, adicione-o à sua implementação de Azure File Sync. O Azure File Sync descarrega o seu espaço de nome de ficheiro antes de descarregar dados, para que o seu servidor possa estar a funcionar o mais rapidamente possível. Para uma recuperação ainda mais rápida, pode ter um servidor de stand by quente como parte da sua implementação, ou pode utilizar o Azure File Sync com o Windows Clustering.

## <a name="cloud-side-backup"></a>Backup do lado da nuvem
Reduza os gastos de backup no local, tomando backups centralizados na nuvem usando o Azure Backup. As ações do Azure Files SMB têm capacidades de instantâneo nativo, e o processo pode ser automatizado usando o Azure Backup para agendar as suas cópias de segurança e gerir a sua retenção. O Azure Backup também se integra com os seus servidores no local, pelo que quando restaurar a nuvem, estas alterações são automaticamente descarregadas nos seus Servidores windows.  

## <a name="next-steps"></a>Passos Seguintes
* [Planear uma implementação do Azure File Sync](file-sync-planning.md)
* [Visão geral do tiering da nuvem](file-sync-cloud-tiering-overview.md)
* [Monitorizar o Azure File Sync](file-sync-monitoring.md)