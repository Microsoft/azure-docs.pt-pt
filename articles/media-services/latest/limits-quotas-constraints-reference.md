---
title: Quotas e limites nos Serviços de Media Azure
description: Este tópico descreve quotas e limites nos Serviços de Media microsoft Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: 9b1e43968569fb7c185043e1dd249c65fcadddfb
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278665"
---
<!-- If you update limits in this topic, make sure to also update https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#media-services-limits -->
# <a name="azure-media-services-quotas-and-limits"></a>Quotas e limites da Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo lista alguns dos limites mais comuns da Microsoft Azure Media Services, que também são por vezes chamados de quotas.

> [!NOTE]
> Para recursos que não são fixados, abra um bilhete de apoio para pedir um aumento das quotas. Não crie contas adicionais da Azure Media Services numa tentativa de obter limites mais elevados.

## <a name="account-limits"></a>Limites de conta

| Recurso | Limite Predefinido |
| --- | --- |
| [Contas de Serviços de Mídia](account-move-account-how-to.md) numa única subscrição | 100 (fixo) |

## <a name="asset-limits"></a>Limites de ativos

| Recurso | Limite Predefinido |
| --- | --- |
| [Ativos](assets-concept.md) por conta de Serviços de Media | 1 000 000|

## <a name="storage-limits"></a>Limites de armazenamento

| Recurso | Limite Predefinido | 
| --- | --- | 
| Tamanho dos ficheiros| Em alguns cenários, existe um limite para o tamanho máximo do ficheiro suportado para o processamento em Serviços de Media. <sup>(1)</sup> |
| [Contas de armazenamento](storage-account-concept.md) | 100<sup>(2)</sup> (fixo) |

<sup>1</sup> O tamanho máximo suportado para uma única bolha é atualmente até 5 TB no Azure Blob Storage. São aplicados limites adicionais nos Serviços de Mídia com base nos tamanhos VM utilizados pelo serviço. O limite de tamanho aplica-se aos ficheiros que carrega e também aos ficheiros que são gerados como resultado do processamento dos Serviços de Comunicação Social (codificação ou análise). Se o seu ficheiro de origem for superior a 260 GB, o seu Trabalho provavelmente falhará. 

A tabela seguinte mostra os limites das unidades reservadas para os meios de comunicação S1, S2 e S3. Se o seu ficheiro de origem for maior do que os limites definidos na tabela, o seu trabalho de codificação falha. Se codificar fontes de resolução 4K de longa duração, é-lhe exigido que utilize unidades reservadas para obter o desempenho necessário. Se tiver um conteúdo 4K superior ao limite de 260 GB nas unidades reservadas aos meios S3, abra um bilhete de apoio.

|Tipo de unidade reservada aos meios de comunicação|Tamanho máximo de entrada (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> As contas de armazenamento devem ser da mesma subscrição do Azure.

## <a name="jobs-encoding--analyzing-limits"></a>Limites de empregos (codificação & análise)

| Recurso | Limite Predefinido | 
| --- | --- | 
| [Empregos](transform-jobs-concept.md) por Conta de Serviços de Media | 500.000 <sup>(3)</sup> (fixo)|
| Entradas de emprego por trabalho | 50 (fixo)|
| Saídas de emprego por Trabalho | 20 (fixo) |
| Transforma por conta de [Serviços](transform-jobs-concept.md) de Media | 100 (fixo)|
| Transformar saídas numa Transformação | 20 (fixo) |
| Ficheiros por entrada de trabalho|10 (fixo)|

<sup>3</sup> Este número inclui trabalhos de fila, acabados, ativos e cancelados. Não inclui empregos apagados. 

Qualquer registo de Trabalho na sua conta com mais de 90 dias será automaticamente eliminado, mesmo que o número total de registos seja inferior à quota máxima. 

## <a name="live-streaming-limits"></a>Limites de streaming ao vivo

| Recurso | Limite Predefinido | 
| --- | --- | 
| [Eventos ao Vivo](live-event-outputs-concept.md) <sup>(4)</sup> por conta de Serviços de Mídia |5|
| Saídas ao vivo por Evento Ao Vivo |3 <sup>(5)</sup> |
| Duração da saída ao vivo max | [Tamanho da janela DVR](live-event-cloud-dvr-time-how-to.md) |

<sup>4</sup> Para obter informações detalhadas sobre os limites do Evento Ao Vivo, consulte [a comparação e os limites dos tipos de Eventos Ao Vivo](live-event-types-comparison-reference.md).

<sup>5</sup> As saídas ao vivo começam na criação e param quando apagadas.

## <a name="packaging--delivery-limits"></a>Limites de entrega de & de embalagem

| Recurso | Limite Predefinido |
| --- | --- |
| [Streaming Endpoints](stream-streaming-endpoint-concept.md) (parado ou em execução) por conta de Serviços de Mídia | 2 |
| Unidades de streaming premium | 10 |
| [Filtros do Manifesto Dinâmico](filters-dynamic-manifest-concept.md)|100|
| [Políticas de streaming](stream-streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| [Localizadores de streaming exclusivos associados](stream-streaming-locators-concept.md) a um Ativo de uma só vez | 100<sup>(7)</sup> (fixo) |

<sup>6</sup> Ao utilizar uma [Política de Streaming](/rest/api/media/streamingpolicies)personalizada, deverá conceber um conjunto limitado de tais políticas para a sua conta de Media Service e reutilizá-las para os seus StreamingLocators sempre que forem necessárias as mesmas opções e protocolos de encriptação. Não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

<sup>7</sup> Os localizadores de streaming não foram concebidos para gerir o controlo de acesso por utilizador. Para conceder direitos de acesso diferentes a utilizadores individuais, utilize soluções de Gestão de Direitos Digitais (Digital Rights Management, DRM).

## <a name="protection-limits"></a>Limites de proteção

| Recurso | Limite Predefinido |
| --- | --- |
| Opções por [Política chave de conteúdo](drm-content-key-policy-concept.md) |30 |
| Licenças por mês para cada um dos tipos de DRM no serviço de entrega de chaves de Serviços de Mídia por conta|1 000 000|

## <a name="support-ticket"></a>Bilhete de apoio

Para os recursos que não são fixados, pode solicitar o auser das quotas, abrindo um [bilhete de apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Incluir informações pormenorizadas no pedido sobre as alterações de quota desejadas, cenários de casos de utilização e regiões necessárias. <br/>**Não** crie contas dos Serviços de Multimédia do Azure adicionais numa tentativa de obter limites superiores.

## <a name="next-steps"></a>Passos seguintes

[Descrição geral](media-services-overview.md)
