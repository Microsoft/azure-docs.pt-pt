---
title: Acesso multiprotocolo ao Armazenamento de Lagos De Dados Azure [ Microsoft Docs
description: Utilize APIs blob e aplicações que usam APIs Blob com Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77914863"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Acesso multiprotocolo ao Armazenamento de Lagos De Dados Azure

As APIs blob agora trabalham com contas que têm um espaço hierárquico. Isto desbloqueia o ecossistema de ferramentas, aplicações e serviços, bem como várias funcionalidades de armazenamento Blob para contas que têm um espaço de nome hierárquico.

Até recentemente, poderia ter mantido soluções de armazenamento separadas para armazenamento de objetos e armazenamento de análise. Isto porque o Azure Data Lake Storage Gen2 tinha um apoio limitado ao ecossistema. Também tinha acesso limitado a funcionalidades de serviço Blob, como a exploração de diagnóstico. Uma solução de armazenamento fragmentada é difícil de manter porque é preciso mover dados entre contas para realizar vários cenários. Já não tens de fazer isso.

Com acesso multiprotocolo no Armazenamento de Data Lake, pode trabalhar com os seus dados utilizando o ecossistema de ferramentas, aplicações e serviços. Isto também inclui ferramentas e aplicações de terceiros. Pode apontá-las para contas que têm um espaço de nome hierárquico sem ter que modificá-las. Estas aplicações funcionam *como é* mesmo se chamarem AFS Blob, porque as APIs blob podem agora operar em dados em contas que têm um espaço de nome hierárquico.

As funcionalidades de armazenamento blob, tais como [a exploração madeireira de diagnóstico,](../common/storage-analytics-logging.md)os níveis de [acesso,](storage-blob-storage-tiers.md)e as políticas de gestão do ciclo de vida de [armazenamento blob](storage-lifecycle-management-concepts.md) funcionam agora com contas que têm um espaço de nome hierárquico. Por isso, pode ativar espaços hierárquicos nas suas contas de armazenamento blob sem perder acesso a estas características importantes. 

> [!NOTE]
> O acesso multi-protocolo ao Armazenamento de Data Lake está geralmente disponível e está disponível em todas as regiões. Alguns serviços Azure ou funcionalidades de armazenamento Blob habilitadas pelo acesso multiprotocolo permanecem em pré-visualização.  Estes artigos resumem o suporte atual para as funcionalidades de armazenamento Blob e integrações de serviços Azure. 
>
> [Funcionalidades de armazenamento blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Serviços Azure que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Como funciona o acesso multi-protocolo ao armazenamento de dados

As APIs blob e data Lake Storage Gen2 APIs podem operar os mesmos dados em contas de armazenamento que têm um espaço de nome hierárquico. Data Lake Storage Gen2 percorre As APIs blob através do espaço hierárquico de nomepara que você possa obter os benefícios de operações de diretório de primeira classe e listas de controlo de acesso compatível com POSIX (ACLs). 

![Acesso multi-protocolo ao conceito de armazenamento de data lake](./media/data-lake-storage-interop/interop-concept.png) 

As ferramentas e aplicações existentes que utilizam a API Blob ganham automaticamente estes benefícios. Os desenvolvedores não terão que modificá-los. Data Lake Storage Gen2 aplica consistentemente os ACLs de diretório e de nível de ficheiro, independentemente do protocolo que as ferramentas e aplicações usam para aceder aos dados. 

## <a name="see-also"></a>Consulte também

- [Funcionalidades de armazenamento blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Serviços Azure que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas de código aberto que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Questões conhecidas com Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




