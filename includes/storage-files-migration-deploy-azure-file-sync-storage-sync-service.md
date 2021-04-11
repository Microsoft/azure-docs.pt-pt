---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 1142082caef06dcc36dfd9b0aaddc44d13cf6cc8
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075600"
---
Neste passo, precisa das suas credenciais de subscrição Azure.

O recurso principal para configurar para Azure File Sync é chamado de *Serviço de Sincronização de Armazenamento*. Recomendamos que implemente apenas um para todos os servidores que estejam a sincronizar o mesmo conjunto de ficheiros agora ou no futuro. Crie vários Serviços de Sincronização de Armazenamento apenas se tiver conjuntos distintos de servidores que nunca devem trocar dados. Por exemplo, pode ter servidores que nunca devem sincronizar a mesma partilha de ficheiros Azure. Caso contrário, um único Serviço de Sincronização de Armazenamento é a melhor prática.

Escolha uma região Azure para o seu Serviço de Sincronização de Armazenamento que esteja perto da sua localização. Todos os outros recursos em nuvem devem ser implantados na mesma região. Para simplificar a gestão, crie um novo grupo de recursos na sua subscrição que aloja recursos de sincronização e armazenamento.

Para obter mais informações, consulte a [secção sobre a implementação do Serviço de Sincronização de Armazenamento](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) no artigo sobre a implementação do Azure File Sync. Siga apenas esta parte do artigo. Haverá ligações a outras secções do artigo em etapas posteriores.