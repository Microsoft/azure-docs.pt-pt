---
title: Implementar um serviço de sincronização de armazenamento
description: Implantação do recurso cloud Azure File Sync. Um serviço de sincronização de armazenamento. Um bloco de texto comum, partilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fe74bae95784dcb63a80ee7e280c02de2eadeb5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124719"
---
Neste passo, precisa das suas credenciais de subscrição Azure.

O recurso principal para configurar o Azure File Sync chama-se "Serviço de Sincronização de Armazenamento".
Recomendamos que implemente apenas um para todos os servidores da empresa que sincronizam o mesmo conjunto de ficheiros agora ou no futuro. Só crie vários Serviços de Sincronização de Armazenamento se tiver conjuntos distintos de servidores que nunca devem trocar dados. (por exemplo: sincronizar a mesma quota de ficheiros Azure). Caso contrário, um único Serviço de Sincronização de Armazenamento é a melhor prática.

Escolha uma região Azure para o seu Serviço de Sincronização de Armazenamento que esteja perto da localização do seu escritório. Todos os outros recursos em nuvem devem ser implantados na mesma região.
Para simplificar a gestão, crie um novo grupo de recursos na sua subscrição que abriga recursos de sincronização e armazenamento.

O seguinte artigo descreve como implementar um Serviço de Sincronização de Armazenamento. Siga minuciosamente esta parte do médico. Haverá ligações a outras subsecções deste doc em etapas posteriores.

[Aprenda a implementar um Serviço de Sincronização de Armazenamento.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
