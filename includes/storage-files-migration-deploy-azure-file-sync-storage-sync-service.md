---
title: Implementar um serviço de sincronização de armazenamento
description: Implantação do recurso cloud Azure File Sync. Um serviço de sincronização de armazenamento. Um bloco de texto comum, partilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209457"
---
Neste passo, precisa das suas credenciais de subscrição Azure. A subscrição Azure que utiliza pode ser diferente da que utiliza para o StorSimple.

O recurso principal para configurar o Azure File Sync chama-se "Serviço de Sincronização de Armazenamento".
Recomendamos que implemente apenas um para todos os servidores da empresa que sincronizam o mesmo conjunto de ficheiros agora ou no futuro. Se tiver mais de um aparelho StorSimple, pode considerar a criação de um recurso de Serviço de Sincronização de Armazenamento para cada um deles. No entanto, só deve criar vários Serviços de Sincronização de Armazenamento se tiver conjuntos distintos de servidores que nunca devem trocar dados. Caso contrário, um único Serviço de Sincronização de Armazenamento é a melhor prática.

Escolha uma região Azure para o seu Serviço de Sincronização de Armazenamento que esteja perto da localização do seu escritório. Todos os outros recursos em nuvem devem ser implantados na mesma região.
A melhor prática é criar um novo grupo de recursos na sua subscrição de recursos de sincronização e armazenamento de casas para uma gestão mais fácil.

O seguinte artigo descreve como implementar um Serviço de Sincronização de Armazenamento. Siga minuciosamente esta parte do médico. Haverá ligações a outras subsecções deste doc em etapas posteriores.

[Aprenda a implementar um Serviço de Sincronização de Armazenamento.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
