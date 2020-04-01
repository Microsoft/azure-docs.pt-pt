---
title: Quotas e limitações nos Serviços de Media Azure v3 [ Microsoft Docs
description: Este tópico descreve quotas e limitações no Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 514c1466bc1a686adfc3e07e1f19bd566e979dc1
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420882"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quotas e limitações nos Serviços de Mídia Azure v3

Este artigo descreve quotas e limitações no Azure Media Services v3.

| Recurso | Limite Predefinido | 
| --- | --- | 
| Recursos por conta dos Serviços de Multimédia do Azure | 1 000 000|
| Filtros do Manifesto Dinâmico|100|
| Inputs de emprego por Job | 50 (fixo)|
| Saídas de emprego por Job | 20 (fixo) |
| Transforme as saídas numa Transformação | 20 (fixo) |
| Ficheiros por entrada de trabalho|10 (fixo)|
| Tamanho dos ficheiros| Em alguns cenários, existe um limite para o tamanho máximo dos ficheiros suportados para o processamento em Serviços de Media. <sup>(1)</sup> |
| Empregos por conta de Serviços de Media | 500.000 <sup>(2)</sup> (fixo)|
| Eventos em Direto por conta dos Serviços de Multimédia |5|
| Contas de Serviços de Media numa única subscrição | 25 (fixo) |
| Saídas ao vivo por evento ao vivo |3 <sup>(3)</sup> |
| Duração da saída de Max Live | 25 horas |
| Contas de armazenamento | 100<sup>(4)</sup> (fixo) |
| Pontos Finais de Streaming (parados ou em execução) por conta de Media Services|2 (fixo)|
| Políticas de Transmissão em Fluxo | 100 <sup>(5)</sup> |
| Transforma-se por conta de Serviços de Media | 100 (fixo)|
| Localizadores de streaming exclusivos associados a um Ativo ao mesmo tempo | 100<sup>(6)</sup> (fixo) |
| Opções por Política chave de conteúdo |30 | 
| Licenças por mês para cada um dos tipos de DRM no serviço de entrega chave de Serviços de Media por conta|1 000 000|

<sup>1</sup> O tamanho máximo suportado para uma única bolha é atualmente de até 5 TB no Armazenamento De Blob Azure. Limites adicionais aplicam-se nos Serviços de Media com base nos tamanhos vm que são utilizados pelo serviço. O limite de tamanho aplica-se aos ficheiros que envia e também aos ficheiros que são gerados em resultado do processamento de Serviços de Media (codificação ou análise). Se o seu ficheiro de origem for superior a 260 GB, o seu Trabalho provavelmente falhará. 

A tabela seguinte mostra os limites das unidades reservadas aos meios de comunicação S1, S2 e S3. Se o seu ficheiro fonte for maior do que os limites definidos na tabela, o seu trabalho de codificação falha. Se codificar fontes de resolução 4K de longa duração, é obrigado a utilizar unidades reservadas aos meios de comunicação S3 para obter o desempenho necessário. Se tiver um conteúdo 4K maior do que o limite de 260 GB nas unidades reservadas aos meios de comunicação S3, abra um bilhete de apoio.

|Tipo de unidade reservado aos meios de comunicação   |Tamanho máximo de entrada (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Este número inclui trabalhos em fila, acabados, ativos e cancelados. Não inclui empregos apagados. 

Qualquer registo de emprego na sua conta com mais de 90 dias será automaticamente eliminado, mesmo que o número total de registos esteja abaixo da quota máxima. 

<sup>3</sup> As saídas ao vivo começam na criação e param quando eliminadas.

<sup>4</sup> As contas de armazenamento devem ser da mesma assinatura Azure.

<sup>5</sup> Ao utilizar uma Política de [Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizada, deve conceber um conjunto limitado de tais políticas para a sua conta de Serviço de Media e reutilizá-las para os seus Localizadores de Streaming sempre que forem necessárias as mesmas opções e protocolos de encriptação. Não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

<sup>6</sup> Os localizadores de streaming não foram concebidos para gerir o controlo de acesso por utilizador. Para conceder direitos de acesso diferentes a utilizadores individuais, utilize soluções de Gestão de Direitos Digitais (Digital Rights Management, DRM).

## <a name="support-ticket"></a>Bilhete de apoio

Para recursos que não estão fixos, pode pedir que as quotas sejam aumentadas, abrindo um bilhete de [apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Incluir informações detalhadas no pedido sobre as alterações de quota desejadas, cenários de caso de utilização e regiões necessárias. <br/>**Não** crie contas dos Serviços de Multimédia do Azure adicionais numa tentativa de obter limites superiores.

## <a name="next-steps"></a>Passos seguintes

[Descrição Geral](media-services-overview.md)
