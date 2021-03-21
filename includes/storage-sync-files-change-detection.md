---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 1387933dc82c07e73b7715d6593238ea8c993e93
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005341"
---
As alterações efetuadas na partilha de ficheiros Azure utilizando o portal Azure ou SMB não são imediatamente detetadas e replicadas como alterações no ponto final do servidor. O Azure Files ainda não tem notificações ou diários alterados, pelo que não há forma de iniciar automaticamente uma sessão de sincronização quando os ficheiros são alterados. No Windows Server, o Azure File Sync utiliza [o diário USN do Windows](/windows/win32/fileio/change-journals) para iniciar automaticamente uma sessão de sincronização quando os ficheiros mudam.

Para detetar alterações na partilha de ficheiros Azure, o Azure File Sync tem um trabalho programado chamado de *trabalho de deteção de alterações*. Um trabalho de deteção de alterações enumera todos os ficheiros da partilha de ficheiros e, em seguida, compara-o com a versão sincronizada desse ficheiro. Quando o trabalho de deteção de alterações determina que os ficheiros foram alterados, o Azure File Sync inicia uma sessão de sincronização. O trabalho de deteção de alterações é iniciado a cada 24 horas. Como o trabalho de deteção de alterações funciona enumerando todos os ficheiros na partilha de ficheiros Azure, a deteção de alterações demora mais tempo em espaços de nome maiores do que em espaços de nome mais pequenos. Para grandes espaços de nome, pode levar mais do que uma vez a cada 24 horas para determinar quais os ficheiros que mudaram.

Para sincronizar imediatamente ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet Dedlet PowerShell da **Invoke-AzStorageSyncChangeDetection** powerShell pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Este cmdlet destina-se a cenários em que algum tipo de processo automatizado está a fazer alterações na partilha de ficheiros Azure ou as alterações são feitas por um administrador (como mover ficheiros e diretórios para a partilha). Para as alterações do utilizador final, a recomendação é instalar o agente Azure File Sync num IaaS VM e fazer com que os utilizadores finais acedam à partilha de ficheiros através do IaaS VM. Desta forma, todas as alterações irão rapidamente sincronizar-se com outros agentes sem a necessidade de utilizar o Invoke-AzStorageSyncChangeDetection cmdlet. Para saber mais, consulte a documentação [Invoke-AzStorageSyncChangeDetection.](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)

>[!NOTE]
>As alterações efetuadas a uma partilha de ficheiros Azure utilizando o REST não atualizam o último tempo modificado do SMB e não serão vistas como uma alteração por sincronização.

Estamos a explorar a adição de deteção de alterações para uma partilha de ficheiros Azure semelhante à USN para volumes no Windows Server. Ajude-nos a priorizar esta funcionalidade para o desenvolvimento futuro votando a seu favor no [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).