---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 55456a6be938411d3c08a0eaa8fdbfb0844e7129
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72391751"
---
As alterações efetuadas na partilha de ficheiros Azure utilizando o portal Azure ou SMB não são imediatamente detetadas e replicadas como alterações ao ponto final do servidor. O Azure Files ainda não tem notificações de alteração ou diário, pelo que não há forma de iniciar automaticamente uma sessão de sincronização quando os ficheiros são alterados. No Windows Server, o Azure File Sync utiliza [o diário Windows USN](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) para iniciar automaticamente uma sessão de sincronização quando os ficheiros mudam.

Para detetar alterações na partilha de ficheiros Azure, o Azure File Sync tem um trabalho programado chamado trabalho de deteção de *alterações*. Um trabalho de deteção de alterações enumera todos os ficheiros da partilha de ficheiros e, em seguida, compara-o com a versão sincronizada desse ficheiro. Quando o trabalho de deteção de alterações determina que os ficheiros mudaram, o Azure File Sync inicia uma sessão de sincronização. O trabalho de deteção de mudanças é iniciado a cada 24 horas. Como o trabalho de deteção de alterações funciona enumerando cada ficheiro na partilha de ficheiros Azure, a deteção de alterações demora mais tempo em espaços de nome maiores do que em espaços de nomes mais pequenos. Para grandes espaços de nome, pode demorar mais do que uma vez a cada 24 horas para determinar quais os ficheiros que mudaram.

Para sincronizar imediatamente os ficheiros que são alterados na partilha de ficheiros Azure, o cmdlet **DeDetecção De Deteção De Deteção De Deteção De Deteção Invocpor-AzStorageSyncChange** Pode ser utilizado para iniciar manualmente a deteção de alterações na partilha de ficheiros Azure. Este cmdlet destina-se a cenários em que algum tipo de processo automatizado esteja a fazer alterações na partilha de ficheiros Azure ou as alterações são feitas por um administrador (como transferir ficheiros e diretórios para a partilha). Para alterações finais do utilizador, a recomendação é instalar o agente Dessincronização de ficheiros Azure num VM IaaS e ter os utilizadores finais a aceder à partilha de ficheiros através do VM IaaS. Desta forma, todas as alterações sincronizar-se-ão rapidamente com outros agentes sem a necessidade de utilizar o cmdlet Invoke-AzStorageSyncChangeDetection. Para saber mais, consulte a documentação [Invoke-AzStorageSyncChangeDetection.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)

>[!NOTE]
>As alterações efetuadas a uma partilha de ficheiros Azure utilizando o REST não atualizam o SMB na última hora modificada e não serão vistas como uma alteração por sincronização.

Estamos a explorar a adição de deteção de alterações para uma partilha de ficheiros Azure semelhante à USN para volumes no Windows Server. Ajude-nos a priorizar esta funcionalidade para o desenvolvimento futuro votando a favor dela no [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
