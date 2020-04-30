---
title: Implementar um serviço de sincronização de armazenamento
description: Implementação do recurso cloud Azure File Sync, um Serviço de Sincronização de Armazenamento. Um bloco de texto comum, partilhado em todos os documentos migratórios.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 248b61530b80a00aa10272dcb3d28b85c6ee04e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143538"
---
Neste passo, precisa das suas credenciais de subscrição Azure.

O recurso principal para configurar para O Sincronizado de Ficheiros Azure chama-se *Serviço de Sincronização*de Armazenamento . Recomendamos que utilize apenas um para todos os servidores que estejam a sincronizar o mesmo conjunto de ficheiros agora ou no futuro. Crie múltiplos Serviços de Sincronização de Armazenamento apenas se tiver conjuntos distintos de servidores que nunca devem trocar dados (por exemplo: sincronizar a mesma partilha de ficheiros Azure). Caso contrário, um único Serviço de Sincronização de Armazenamento é a melhor prática.

Escolha uma região Azure para o seu Serviço de Sincronização de Armazenamento que esteja perto da sua localização. Todos os outros recursos em nuvem devem ser implantados na mesma região.
Para simplificar a gestão, crie um novo grupo de recursos na sua subscrição que abriga recursos de sincronização e armazenamento.

Para mais informações, consulte a [secção sobre a implementação do Serviço](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) de Sincronização de Armazenamento no artigo sobre a implementação do Azure File Sync. Siga apenas esta parte do artigo. Haverá ligações a outras secções do artigo em etapas posteriores.