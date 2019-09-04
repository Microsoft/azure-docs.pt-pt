---
title: Acesso de vários protocolos em Azure Data Lake Storage (visualização) | Microsoft Docs
description: Use APIs de BLOB e aplicativos que usam APIs de blob com Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 471e3008c25ddef83dd9b502dd8f677ae31cc71b
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259380"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage-preview"></a>Acesso de vários protocolos em Azure Data Lake Storage (versão prévia)

As APIs de blob agora funcionam com contas que têm um namespace hierárquico. Isso desbloqueia todo o ecossistema de ferramentas, aplicativos e serviços, bem como todos os recursos de armazenamento de BLOBs para contas que têm um namespace hierárquico.

Até recentemente, talvez você precise manter soluções de armazenamento separadas para armazenamento de objetos e armazenamento de análise. Isso ocorre porque Azure Data Lake Storage Gen2 tinha suporte limitado a ecossistema. Ele também tinha acesso limitado aos recursos do serviço BLOB, como o log de diagnóstico. Uma solução de armazenamento fragmentada é difícil de manter, pois você precisa mover dados entre contas para realizar vários cenários. Você não precisa mais fazer isso.

> [!NOTE]
> O acesso de vários protocolos no Data Lake Storage está em visualização pública e está disponível nessas [regiões](#region-availability). Para revisar as limitações, consulte o artigo [problemas conhecidos](data-lake-storage-known-issues.md) . Para se registrar na versão prévia, consulte [esta página](https://aka.ms/blobinteropsignup).

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>Use todo o ecossistema de aplicativos, ferramentas e serviços

Se você se registrar na versão prévia do acesso multiprotocolo no Data Lake Storage, poderá trabalhar com todos os seus dados usando todo o ecossistema de ferramentas, aplicativos e serviços. Isso inclui serviços do Azure, como [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), [Hub IOT](https://docs.microsoft.com/azure/iot-hub/), [Power bi](https://docs.microsoft.com/power-bi/desktop-data-sources)e muitos outros. 

Isso também inclui ferramentas e aplicativos de terceiros. Você pode apontar para as contas que têm um namespace hierárquico sem precisar modificá-las. Esses aplicativos funcionam *como estão* , mesmo se chamarem APIs de BLOB, porque as APIs de blob podem agora operar em dados em contas que têm um namespace hierárquico.

> [!NOTE]
> Para revisar as limitações, consulte o artigo [problemas conhecidos](data-lake-storage-known-issues.md) .

## <a name="use-all-blob-storage-features"></a>Usar todos os recursos de armazenamento de BLOBs

Os recursos de armazenamento de BLOBs, como [log de diagnóstico](../common/storage-analytics-logging.md), [camadas de acesso](storage-blob-storage-tiers.md)e [políticas de gerenciamento do ciclo de vida do armazenamento](storage-lifecycle-management-concepts.md) de BLOBs, agora funcionam com contas que têm um namespace hierárquico. Portanto, você pode habilitar namespaces hierárquicos em suas contas de armazenamento de BLOBs sem acesso perder a esses recursos importantes. 

> [!NOTE]
> Para revisar as limitações, consulte o artigo [problemas conhecidos](data-lake-storage-known-issues.md) .

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Como funciona o acesso de vários protocolos no armazenamento do data Lake

APIs de BLOB e APIs de Data Lake Storage Gen2 podem operar nos mesmos dados em contas de armazenamento que têm um namespace hierárquico. Data Lake Storage Gen2 roteia as APIs de blob por meio do namespace hierárquico para que você possa obter os benefícios das operações de diretório de primeira classe e das ACLs (listas de controle de acesso) compatíveis com POSIX. 

![Acesso de vários protocolos em Data Lake Storage conceitual](./media/data-lake-storage-interop/interop-concept.png) 

As ferramentas e os aplicativos existentes que usam a API de blob obterão esses benefícios automaticamente. Os desenvolvedores não precisarão modificá-los. Data Lake Storage Gen2 aplica de forma consistente ACLs de nível de arquivo e de diretório, independentemente do protocolo que as ferramentas e os aplicativos usam para acessar os dados. 

<a id="region-availability" />

## <a name="region-availability"></a>Disponibilidade de região

O acesso de vários protocolos no Azure Data Lake Storage (versão prévia) está disponível nas seguintes regiões:

|||||
|-|-|-|-|
|EUA Central|EUA Centro-Oeste|Canadá Central|
|East US|Ásia Oriental|Europa do Norte|
|EUA Leste 2|Sudeste Asiático|Europa Ocidental|
|EUA Oeste|Leste da Austrália|Leste do Japão|
|EUA Oeste 2|Sul do Brasil||

## <a name="next-steps"></a>Passos seguintes

Consulte [problemas conhecidos](data-lake-storage-known-issues.md)




