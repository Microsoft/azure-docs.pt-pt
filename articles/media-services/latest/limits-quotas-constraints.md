---
title: Quotas e limites nos Serviços de Mídia Azure
description: Este tópico descreve quotas e limites nos Serviços de Media Microsoft Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/01/2020
ms.author: juliako
ms.openlocfilehash: 2d4f5f83335f6f115362bcf66cf69d7f9de7eaa5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582327"
---
# <a name="azure-media-services-quotas-and-limits"></a>Quotas e limites dos Serviços De Mídia Azure

Este artigo lista alguns dos limites mais comuns do Microsoft Azure Media Services, que também são por vezes chamados de quotas.

> [!NOTE]
> Para recursos que não estão fixos, abra um bilhete de apoio para pedir um aumento das quotas. Não crie contas adicionais da Azure Media Services numa tentativa de obter limites mais elevados.

## <a name="account-limits"></a>Limites de conta

| Recurso | Limite Predefinido | 
| --- | --- | 
| [Contas de Serviços de Media](media-services-account-concept.md) numa única subscrição | 25 (fixo) |

## <a name="asset-limits"></a>Limites de ativos

| Recurso | Limite Predefinido | 
| --- | --- | 
| [Ativos](assets-concept.md) por conta de Serviços de Media | 1 000 000|

## <a name="storage-limits"></a>Limites de armazenamento

| Recurso | Limite Predefinido | 
| --- | --- | 
| Tamanho dos ficheiros| Em alguns cenários, existe um limite para o tamanho máximo dos ficheiros suportados para o processamento em Serviços de Media. <sup>(1)</sup> |
| [Contas de armazenamento](storage-account-concept.md) | 100<sup>(2)</sup> (fixo) |

<sup>1</sup> O tamanho máximo suportado para uma única bolha é atualmente de até 5 TB no Armazenamento De Blob Azure. Limites adicionais aplicam-se nos Serviços de Media com base nos tamanhos vm que são utilizados pelo serviço. O limite de tamanho aplica-se aos ficheiros que envia e também aos ficheiros que são gerados em resultado do processamento de Serviços de Media (codificação ou análise). Se o seu ficheiro de origem for superior a 260 GB, o seu Trabalho provavelmente falhará. 

A tabela seguinte mostra os limites das unidades reservadas aos meios de comunicação S1, S2 e S3. Se o seu ficheiro fonte for maior do que os limites definidos na tabela, o seu trabalho de codificação falha. Se codificar fontes de resolução 4K de longa duração, é obrigado a utilizar unidades reservadas aos meios de comunicação S3 para obter o desempenho necessário. Se tiver um conteúdo 4K maior do que o limite de 260 GB nas unidades reservadas aos meios de comunicação S3, abra um bilhete de apoio.

|Tipo de unidade reservado aos meios de comunicação|Tamanho máximo de entrada (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> As contas de armazenamento devem ser da mesma assinatura Azure.

## <a name="jobs-encoding--analyzing-limits"></a>Limites de emprego (codificação & análise)

| Recurso | Limite Predefinido | 
| --- | --- | 
| [Empregos](transforms-jobs-concept.md) por conta de Serviços de Media | 500.000 <sup>(3)</sup> (fixo)|
| Inputs de emprego por Job | 50 (fixo)|
| Saídas de emprego por Job | 20 (fixo) |
| [Transforma-se](transforms-jobs-concept.md) por conta de Serviços de Media | 100 (fixo)|
| Transforme as saídas numa Transformação | 20 (fixo) |
| Ficheiros por entrada de trabalho|10 (fixo)|

<sup>3</sup> Este número inclui trabalhos em fila, acabados, ativos e cancelados. Não inclui empregos apagados. 

Qualquer registo de emprego na sua conta com mais de 90 dias será automaticamente eliminado, mesmo que o número total de registos esteja abaixo da quota máxima. 

## <a name="live-streaming-limits"></a>Limites de streaming ao vivo

| Recurso | Limite Predefinido | 
| --- | --- | 
| [Eventos ao Vivo](live-events-outputs-concept.md) <sup>(4)</sup> por conta de Serviços de Media |5|
| Saídas ao vivo por evento ao vivo |3 <sup>(5)</sup> |
| Duração da saída de Max Live | 25 horas |

<sup>4</sup> Para obter informações detalhadas sobre os limites do Evento Ao Vivo, consulte a [comparação e os limites](live-event-types-comparison.md)dos tipos de eventos ao vivo.

<sup>5</sup> Saídas ao vivo começam na criação e param quando eliminados.

## <a name="packaging--delivery-limits"></a>Limites de entrega de & de embalagem

| Recurso | Limite Predefinido | 
| --- | --- | 
| [Pontos Finais de Streaming](streaming-endpoint-concept.md) (parados ou em execução) por conta de Media Services|2 |
| [Filtros do Manifesto Dinâmico](filters-dynamic-manifest-overview.md)|100|
| [Políticas de Transmissão em Fluxo](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| [Localizadores de streaming exclusivos associados](streaming-locators-concept.md) a um Ativo ao mesmo tempo | 100<sup>(7)</sup> (fixo) |

<sup>6</sup> Ao utilizar uma Política de [Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizada, deve conceber um conjunto limitado de tais políticas para a sua conta de Serviço de Media e reutilizá-las para os seus Localizadores de Streaming sempre que forem necessárias as mesmas opções e protocolos de encriptação. Não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

<sup>7</sup> Os localizadores de streaming não foram concebidos para gerir o controlo de acesso por utilizador. Para conceder direitos de acesso diferentes a utilizadores individuais, utilize soluções de Gestão de Direitos Digitais (Digital Rights Management, DRM).

## <a name="protection-limits"></a>Limites de proteção

| Recurso | Limite Predefinido | 
| --- | --- | 
| Opções por [Política chave de conteúdo](content-key-policy-concept.md) |30 | 
| Licenças por mês para cada um dos tipos de DRM no serviço de entrega chave de Serviços de Media por conta|1 000 000|

## <a name="support-ticket"></a>Bilhete de apoio

Para recursos que não estão fixos, pode pedir que as quotas sejam aumentadas, abrindo um bilhete de [apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Incluir informações detalhadas no pedido sobre as alterações de quota desejadas, cenários de caso de utilização e regiões necessárias. <br/>**Não** crie contas dos Serviços de Multimédia do Azure adicionais numa tentativa de obter limites superiores.

## <a name="next-steps"></a>Passos seguintes

[Descrição Geral](media-services-overview.md)
