---
title: Acesso multi-protocolo a Azure Data Lake Storage | Microsoft Docs
description: Utilize APIs Blob e aplicações que usam APIs Blob com Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "77914863"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Acesso multi-protocolo no Azure Data Lake Storage

As APIs blob agora trabalham com contas que têm um espaço hierárquico de nomes. Isto desbloqueia o ecossistema de ferramentas, aplicações e serviços, bem como várias funcionalidades de armazenamento Blob para contas que têm um espaço hierárquico de nomes.

Até recentemente, poderia ter tido de manter soluções de armazenamento separadas para armazenamento de objetos e armazenamento de analíticos. Isto porque o Azure Data Lake Storage Gen2 tinha um apoio limitado ao ecossistema. Também tinha acesso limitado a funcionalidades de serviço Blob, como registo de diagnóstico. Uma solução de armazenamento fragmentada é difícil de manter porque é preciso mover dados entre contas para realizar vários cenários. Já não tens de fazer isso.

Com acesso multi-protocolo no Armazenamento do Lago de Dados, pode trabalhar com os seus dados utilizando o ecossistema de ferramentas, aplicações e serviços. Isto também inclui ferramentas e aplicações de terceiros. Pode apontá-las para contas que têm um espaço hierárquico sem ter que modificá-las. Estas aplicações funcionam *como está* mesmo se chamarem APIs Blob, porque as APIs blob podem agora operar em dados em contas que têm um espaço hierárquico de nomes.

As funcionalidades de armazenamento de blob, tais como [o registo de diagnóstico,](../common/storage-analytics-logging.md) [os níveis de acesso](storage-blob-storage-tiers.md)e as políticas de [gestão do ciclo de vida de armazenamento blob](storage-lifecycle-management-concepts.md) funcionam agora com contas que têm um espaço hierárquico de nomes. Portanto, pode ativar espaços hierárquicos nas suas contas blob Storage sem perder o acesso a estas características importantes. 

> [!NOTE]
> O acesso multi-protocolo ao Armazenamento do Lago de Dados está geralmente disponível e está disponível em todas as regiões. Alguns serviços Azure ou funcionalidades de armazenamento Blob ativados pelo acesso multi-protocolo permanecem em pré-visualização.  Estes artigos resumem o suporte atual para funcionalidades de armazenamento Blob e integrações de serviços Azure. 
>
> [Funcionalidades de Armazenamento blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Serviços Azure que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Como funciona o acesso a vários protocolos no armazenamento de lagos de dados

As APIs blob e as APIs de armazenamento de dados podem operar nos mesmos dados em contas de armazenamento que têm um espaço hierárquico de nomes. Data Lake Storage Gen2 rotas Blob APIs através do espaço hierárquico de nome para que você possa obter os benefícios de operações de diretório de primeira classe e listas de controlo de acesso compatível com POSIX (ACLs). 

![Acesso multi-protocolo ao conceito de armazenamento de data lake](./media/data-lake-storage-interop/interop-concept.png) 

As ferramentas e aplicações existentes que utilizam a API Blob ganham estes benefícios automaticamente. Os desenvolvedores não terão que modificá-los. Data Lake Storage Gen2 aplica consistentemente diretório e ACLs de nível de arquivo, independentemente do protocolo que as ferramentas e aplicações usam para aceder aos dados. 

## <a name="see-also"></a>Ver também

- [Funcionalidades de Armazenamento blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Serviços Azure que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas open source que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Problemas conhecidos com Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




