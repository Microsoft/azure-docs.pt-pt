---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 03/31/2020
ms.author: juliako
ms.openlocfilehash: 99aeb5384b317d1b4d291c769b5402e829247b30
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656221"
---
> [!NOTE]
> Para recursos que não estão fixos, abra um bilhete de apoio para pedir um aumento das quotas. Não crie contas adicionais da Azure Media Services numa tentativa de obter limites mais elevados.

### <a name="account-limits"></a>Limites de conta

| Recurso | Limite Predefinido | 
| --- | --- | 
| Contas de Serviços de Media numa única subscrição | 25 (fixo) |

### <a name="asset-limits"></a>Limites de ativos

| Recurso | Limite Predefinido | 
| --- | --- | 
| Ativos por conta de Serviços de Media | 1 000 000|

### <a name="storage-media-limits"></a>Limites de armazenamento (meios)

| Recurso | Limite Predefinido | 
| --- | --- | 
| Tamanho dos ficheiros| Em alguns cenários, existe um limite para o tamanho máximo dos ficheiros suportados para o processamento em Serviços de Media. <sup>(1)</sup> |
| Contas de armazenamento | 100<sup>(2)</sup> (fixo) |

<sup>1</sup> O tamanho máximo suportado para uma única bolha é atualmente de até 5 TB no Armazenamento De Blob Azure. Limites adicionais aplicam-se nos Serviços de Media com base nos tamanhos vm que são utilizados pelo serviço. O limite de tamanho aplica-se aos ficheiros que envia e também aos ficheiros que são gerados em resultado do processamento de Serviços de Media (codificação ou análise). Se o seu ficheiro de origem for superior a 260 GB, o seu Trabalho provavelmente falhará. 

A tabela seguinte mostra os limites das unidades reservadas aos meios de comunicação S1, S2 e S3. Se o seu ficheiro fonte for maior do que os limites definidos na tabela, o seu trabalho de codificação falha. Se codificar fontes de resolução 4K de longa duração, é obrigado a utilizar unidades reservadas aos meios de comunicação S3 para obter o desempenho necessário. Se tiver um conteúdo 4K maior do que o limite de 260 GB nas unidades reservadas aos meios de comunicação S3, abra um bilhete de apoio.

|Tipo de unidade reservado aos meios de comunicação|Tamanho máximo de entrada (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> As contas de armazenamento devem ser da mesma assinatura Azure.

### <a name="jobs-encoding--analyzing-limits"></a>Limites de emprego (codificação & análise)

| Recurso | Limite Predefinido | 
| --- | --- | 
| Empregos por conta de Serviços de Media | 500.000 <sup>(3)</sup> (fixo)|
| Inputs de emprego por Job | 50 (fixo)|
| Saídas de emprego por Job | 20 (fixo) |
| Transforma-se por conta de Serviços de Media | 100 (fixo)|
| Transforme as saídas numa Transformação | 20 (fixo) |
| Ficheiros por entrada de trabalho|10 (fixo)|

<sup>3</sup> Este número inclui trabalhos em fila, acabados, ativos e cancelados. Não inclui empregos apagados. 

Qualquer registo de emprego na sua conta com mais de 90 dias será automaticamente eliminado, mesmo que o número total de registos esteja abaixo da quota máxima. 

### <a name="live-streaming-limits"></a>Limites de streaming ao vivo

| Recurso | Limite Predefinido | 
| --- | --- | 
| Eventos ao Vivo <sup>(4)</sup> por conta de Serviços de Media |5|
| Saídas ao vivo por evento ao vivo |3 <sup>(5)</sup> |
| Duração da saída de Max Live | 25 horas |

<sup>4</sup> Para obter informações detalhadas sobre as limitações do Evento Ao Vivo, consulte a [comparação e limitações](../articles/media-services/latest/live-event-types-comparison.md)dos tipos de eventos ao vivo .

<sup>5</sup> Saídas ao vivo começam na criação e param quando eliminados.

### <a name="packaging--delivery-limits"></a>Limites de entrega de & de embalagem

| Recurso | Limite Predefinido | 
| --- | --- | 
| Pontos Finais de Streaming (parados ou em execução) por conta de Media Services|2 (fixo)|
| Filtros do Manifesto Dinâmico|100|
| Políticas de Transmissão em Fluxo | 100 <sup>(6)</sup> |
| Localizadores de streaming exclusivos associados a um Ativo ao mesmo tempo | 100<sup>(7)</sup> (fixo) |

<sup>6</sup> Ao utilizar uma Política de [Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizada, deve conceber um conjunto limitado de tais políticas para a sua conta de Serviço de Media e reutilizá-las para os seus Localizadores de Streaming sempre que forem necessárias as mesmas opções e protocolos de encriptação. Não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

<sup>7</sup> Os localizadores de streaming não foram concebidos para gerir o controlo de acesso por utilizador. Para conceder direitos de acesso diferentes a utilizadores individuais, utilize soluções de Gestão de Direitos Digitais (Digital Rights Management, DRM).

### <a name="protection-limits"></a>Limites de proteção

| Recurso | Limite Predefinido | 
| --- | --- | 
| Opções por Política chave de conteúdo |30 | 
| Licenças por mês para cada um dos tipos de DRM no serviço de entrega chave de Serviços de Media por conta|1 000 000|

### <a name="support-ticket"></a>Bilhete de apoio

Para recursos que não estão fixos, pode pedir que as quotas sejam aumentadas, abrindo um bilhete de [apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Incluir informações detalhadas no pedido sobre as alterações de quota desejadas, cenários de caso de utilização e regiões necessárias. <br/>**Não** crie contas dos Serviços de Multimédia do Azure adicionais numa tentativa de obter limites superiores.
