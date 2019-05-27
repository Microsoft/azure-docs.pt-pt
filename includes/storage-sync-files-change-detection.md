---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: beb08c29587e4ce522131142fd61925b5af45fa9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114571"
---
As alterações feitas à partilha de ficheiros do Azure com o portal do Azure ou SMB não imediatamente são detetadas e replicadas como alterações para o ponto final do servidor. Os ficheiros do Azure ainda não tem notificações de alteração ou do Registro no diário, portanto, não há nenhuma forma de iniciar automaticamente uma sessão de sincronização quando os ficheiros são alterados. No Windows Server, utiliza o Azure File Sync [diário USN do Windows](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) automaticamente iniciar uma sessão de sincronização quando os ficheiros alterados.<br /><br /> Para detectar alterações para a partilha de ficheiros do Azure, Azure File Sync tem uma tarefa agendada denominada uma *altere a tarefa de deteção*. Uma tarefa de deteção de alteração enumera todos os ficheiros na partilha de ficheiros e, em seguida, compara-o para a versão de sincronização para esse ficheiro. Quando a tarefa de deteção de alteração determina que ficheiros foram alteradas, o Azure File Sync inicia uma sessão de sincronização. A tarefa de deteção de alteração é iniciada a cada 24 horas. Uma vez que a tarefa de deteção de alteração funciona através da enumeração de todos os ficheiros na partilha de ficheiros do Azure, deteção de alteração demora mais tempo nos namespaces maiores do que em espaços de nomes mais pequenos. Para espaços de nomes grandes, poderá demorar mais tempo do que uma vez a cada 24 horas para determinar quais arquivos foram alterados.<br /><br />
Tenha em atenção que as alterações feitas para uma partilha de ficheiros do Azure com REST não não o SMB hora da última modificação de atualização e não irá ser vista como uma alteração por sincronização. <br /><br />
Estamos explorando a adição de deteção de alteração para uma partilha de ficheiros do Azure semelhante ao USN para volumes no Windows Server. Ajude-na priorizar esta funcionalidade para futuro desenvolvimento por votar na-la em [UserVoice de ficheiros do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
