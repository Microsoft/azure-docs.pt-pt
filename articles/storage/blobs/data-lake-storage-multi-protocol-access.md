---
title: Acesso de vários protocolos em Azure Data Lake Storage (visualização) | Microsoft Docs
description: Use APIs de BLOB e aplicativos que usam APIs de blob com Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 6e3b368d1f5615c34a49b155d288d3c37c28bc26
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933143"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage-preview"></a>Acesso de vários protocolos em Azure Data Lake Storage (versão prévia)

As APIs de blob agora funcionam com contas que têm um namespace hierárquico. Isso desbloqueia todo o ecossistema de ferramentas, aplicativos e serviços, bem como todos os recursos de armazenamento de BLOBs para contas que têm um namespace hierárquico.

Até recentemente, talvez você precise manter soluções de armazenamento separadas para armazenamento de objetos e armazenamento de análise. Isso ocorre porque Azure Data Lake Storage Gen2 tinha suporte limitado a ecossistema. Ele também tinha acesso limitado aos recursos do serviço BLOB, como o log de diagnóstico. Uma solução de armazenamento fragmentada é difícil de manter, pois você precisa mover dados entre contas para realizar vários cenários. Você não precisa mais fazer isso.

> [!NOTE]
> O acesso de vários protocolos no Data Lake Storage está em visualização pública e está disponível em todas as regiões. Você não precisa se registrar na visualização pública porque ela está disponível automaticamente para todas as contas que têm um namespace hierárquico. Para revisar as limitações, consulte o artigo [problemas conhecidos](data-lake-storage-known-issues.md) .

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>Use todo o ecossistema de aplicativos, ferramentas e serviços

Com o acesso de vários protocolos no Data Lake Storage, você pode trabalhar com todos os seus dados usando todo o ecossistema de ferramentas, aplicativos e serviços. Isso inclui serviços do Azure, como [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), [Hub IOT](https://docs.microsoft.com/azure/iot-hub/), [Power bi](https://docs.microsoft.com/power-bi/desktop-data-sources)e muitos outros. Para obter uma lista completa, consulte [integrar Azure data Lake Storage com os serviços do Azure](data-lake-store-integrate-with-azure-services.md).

Isso também inclui ferramentas e aplicativos de terceiros. Você pode apontar para as contas que têm um namespace hierárquico sem precisar modificá-las. Esses aplicativos funcionam *como estão* , mesmo se chamarem APIs de BLOB, porque as APIs de blob podem agora operar em dados em contas que têm um namespace hierárquico.

> [!NOTE]
> Para revisar as limitações, consulte o artigo [problemas conhecidos](data-lake-storage-known-issues.md) .

## <a name="use-all-blob-storage-features"></a>Usar todos os recursos de armazenamento de BLOBs

Os recursos de armazenamento de BLOBs, como [log de diagnóstico](../common/storage-analytics-logging.md), [camadas de acesso](storage-blob-storage-tiers.md)e [políticas de gerenciamento do ciclo de vida do armazenamento de BLOBs](storage-lifecycle-management-concepts.md) , agora funcionam com contas que têm um namespace hierárquico. Portanto, você pode habilitar namespaces hierárquicos em suas contas de armazenamento de BLOBs sem perder o acesso a esses recursos importantes. 

> [!NOTE]
> Para revisar as limitações, consulte o artigo [problemas conhecidos](data-lake-storage-known-issues.md) .

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Como funciona o acesso de vários protocolos no armazenamento do data Lake

APIs de BLOB e APIs de Data Lake Storage Gen2 podem operar nos mesmos dados em contas de armazenamento que têm um namespace hierárquico. Data Lake Storage Gen2 roteia as APIs de blob por meio do namespace hierárquico para que você possa obter os benefícios das operações de diretório de primeira classe e das ACLs (listas de controle de acesso) compatíveis com POSIX. 

![Acesso de vários protocolos em Data Lake Storage conceitual](./media/data-lake-storage-interop/interop-concept.png) 

As ferramentas e os aplicativos existentes que usam a API de blob obterão esses benefícios automaticamente. Os desenvolvedores não precisarão modificá-los. Data Lake Storage Gen2 aplica de forma consistente ACLs de nível de arquivo e de diretório, independentemente do protocolo que as ferramentas e os aplicativos usam para acessar os dados. 

## <a name="next-steps"></a>Passos seguintes

Consulte [problemas conhecidos](data-lake-storage-known-issues.md)




