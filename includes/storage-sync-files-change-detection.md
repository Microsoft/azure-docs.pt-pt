---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 55456a6be938411d3c08a0eaa8fdbfb0844e7129
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391751"
---
As alterações feitas no compartilhamento de arquivos do Azure usando o portal do Azure ou SMB não são detectadas e replicadas imediatamente, como as alterações no ponto de extremidade do servidor. Os arquivos do Azure ainda não têm notificações de alteração ou registro em log, portanto, não há como iniciar automaticamente uma sessão de sincronização quando os arquivos são alterados. No Windows Server, Sincronização de Arquivos do Azure usa o [diário de USN do Windows](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) para iniciar automaticamente uma sessão de sincronização quando os arquivos são alterados.

Para detectar alterações no compartilhamento de arquivos do Azure, Sincronização de Arquivos do Azure tem um trabalho agendado chamado *trabalho de detecção de alteração*. Um trabalho de detecção de alteração enumera todos os arquivos no compartilhamento de arquivos e os compara com a versão de sincronização para esse arquivo. Quando o trabalho de detecção de alteração determina que os arquivos foram alterados, Sincronização de Arquivos do Azure inicia uma sessão de sincronização. O trabalho de detecção de alteração é iniciado a cada 24 horas. Como o trabalho de detecção de alteração funciona enumerando cada arquivo no compartilhamento de arquivos do Azure, a detecção de alteração leva mais tempo em namespaces maiores do que em namespaces menores. Para namespaces grandes, pode levar mais de uma vez a cada 24 horas para determinar quais arquivos foram alterados.

Para sincronizar imediatamente os arquivos que são alterados no compartilhamento de arquivos do Azure, o cmdlet **Invoke-AzStorageSyncChangeDetection** do PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivos do Azure. Esse cmdlet destina-se a cenários em que algum tipo de processo automatizado está fazendo alterações no compartilhamento de arquivos do Azure ou as alterações são feitas por um administrador (como mover arquivos e diretórios para o compartilhamento). Para alterações do usuário final, a recomendação é instalar o agente de Sincronização de Arquivos do Azure em uma VM IaaS e fazer com que os usuários finais acessem o compartilhamento de arquivos por meio da VM IaaS. Dessa forma, todas as alterações serão rapidamente sincronizadas com outros agentes sem a necessidade de usar o cmdlet Invoke-AzStorageSyncChangeDetection. Para saber mais, confira a documentação do [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .

>[!NOTE]
>As alterações feitas em um compartilhamento de arquivos do Azure usando REST não atualizam a hora da última modificação do SMB e não serão vistas como uma alteração por sincronização.

Estamos explorando adicionar a detecção de alterações para um compartilhamento de arquivos do Azure semelhante ao USN para volumes no Windows Server. Ajude-nos a priorizar esse recurso para desenvolvimento futuro, votando-o em [UserVoice de arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
