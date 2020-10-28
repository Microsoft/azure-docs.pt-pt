---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 84aa07ef282dd7100043f7e9bfcc97cb3b3673a5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755519"
---
> [!NOTE]
> Para recursos que não são fixados, abra um bilhete de apoio para pedir um aumento das quotas. Não crie contas adicionais da Azure Media Services numa tentativa de obter limites mais elevados.

### <a name="account-limits"></a>Limites de conta

| Recurso | Limite Predefinido |
| --- | --- |
| Contas de Serviços de Mídia numa única subscrição | 100 (fixo) |

### <a name="asset-limits"></a>Limites de ativos

| Recurso | Limite Predefinido |
| --- | --- |
| Ativos por conta de Serviços de Media | 1 000 000|

### <a name="storage-media-limits"></a>Limites de armazenamento (meios)

| Recurso | Limite Predefinido |
| --- | --- |
| Tamanho dos ficheiros| Em alguns cenários, existe um limite para o tamanho máximo do ficheiro suportado para o processamento em Serviços de Media. <sup>(1)</sup> |
| Contas de armazenamento | 100<sup>(2)</sup> (fixo) |

<sup>1</sup> O tamanho máximo suportado para uma única bolha é atualmente até 5 TB no Azure Blob Storage. São aplicados limites adicionais nos Serviços de Mídia com base nos tamanhos VM utilizados pelo serviço. O limite de tamanho aplica-se aos ficheiros que carrega e também aos ficheiros que são gerados como resultado do processamento dos Serviços de Comunicação Social (codificação ou análise). Se o seu ficheiro de origem for superior a 260 GB, o seu Trabalho provavelmente falhará.

A tabela seguinte mostra os limites das unidades reservadas para os meios de comunicação S1, S2 e S3. Se o seu ficheiro de origem for maior do que os limites definidos na tabela, o seu trabalho de codificação falha. Se codificar fontes de resolução 4K de longa duração, é-lhe exigido que utilize unidades reservadas para obter o desempenho necessário. Se tiver um conteúdo 4K superior ao limite de 260 GB nas unidades reservadas aos meios S3, abra um bilhete de apoio.

|Tipo de unidade reservada aos meios de comunicação|Tamanho máximo de entrada (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> As contas de armazenamento devem ser da mesma subscrição do Azure.

### <a name="jobs-encoding--analyzing-limits"></a>Limites de empregos (codificação & análise)

| Recurso | Limite Predefinido |
| --- | --- |
| Empregos por Conta de Serviços de Media | 500.000 <sup>(3)</sup> (fixo)|
| Entradas de emprego por trabalho | 50 (fixo)|
| Saídas de emprego por Trabalho | 20 (fixo) |
| Transforma por conta de Serviços de Media | 100 (fixo)|
| Transformar saídas numa Transformação | 20 (fixo) |
| Ficheiros por entrada de trabalho|10 (fixo)|

<sup>3</sup> Este número inclui trabalhos de fila, acabados, ativos e cancelados. Não inclui empregos apagados. 

Qualquer registo de Trabalho na sua conta com mais de 90 dias será automaticamente eliminado, mesmo que o número total de registos seja inferior à quota máxima. 

### <a name="live-streaming-limits"></a>Limites de streaming ao vivo

| Recurso | Limite Predefinido |
| --- | --- |
| Eventos ao Vivo <sup>(4)</sup> por conta de Serviços de Mídia |5|
| Saídas ao vivo por Evento Ao Vivo |3 <sup>(5)</sup> |
| Duração da saída ao vivo max | [Tamanho da janela DVR](../articles/media-services/latest/live-event-cloud-dvr.md) |

<sup>4</sup> Para obter informações detalhadas sobre as limitações do Evento Ao Vivo, consulte [a comparação e limitações dos tipos de Eventos Ao Vivo.](../articles/media-services/latest/live-event-types-comparison.md)

<sup>5</sup> As saídas ao vivo começam na criação e param quando apagadas.

### <a name="packaging--delivery-limits"></a>Limites de entrega de & de embalagem

| Recurso | Limite Predefinido |
| --- | --- |
| Streaming Endpoints (parado ou em execução) por conta de Serviços de Mídia| 2 |
| Filtros do Manifesto Dinâmico|100|
| Políticas de Transmissão em Fluxo | 100 <sup>(6)</sup> |
| Localizadores de streaming exclusivos associados a um Ativo de uma só vez | 100<sup>(7)</sup> (fixo) |

<sup>6</sup> Ao utilizar uma [Política de Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizada, deverá conceber um conjunto limitado de tais políticas para a sua conta de Media Service e reutilizá-las para os seus StreamingLocators sempre que forem necessárias as mesmas opções e protocolos de encriptação. Não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

<sup>7</sup> Os localizadores de streaming não foram concebidos para gerir o controlo de acesso por utilizador. Para conceder direitos de acesso diferentes a utilizadores individuais, utilize soluções de Gestão de Direitos Digitais (Digital Rights Management, DRM).

### <a name="protection-limits"></a>Limites de proteção

| Recurso | Limite Predefinido |
| --- | --- |
| Opções por Política chave de conteúdo | 30 |
| Licenças por mês para cada um dos tipos de DRM no serviço de entrega de chaves de Serviços de Mídia por conta|1 000 000|

### <a name="support-ticket"></a>Bilhete de apoio

Para os recursos que não são fixados, pode solicitar o auser das quotas, abrindo um [bilhete de apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Incluir informações pormenorizadas no pedido sobre as alterações de quota desejadas, cenários de casos de utilização e regiões necessárias. <br/>**Não** crie contas dos Serviços de Multimédia do Azure adicionais numa tentativa de obter limites superiores.
