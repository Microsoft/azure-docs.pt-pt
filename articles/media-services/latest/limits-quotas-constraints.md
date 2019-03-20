---
title: Quotas e limitações da v3 de serviços de multimédia do Azure | Documentos da Microsoft
description: Este tópico descreve as quotas e limitações em serviços de multimédia do Azure v3
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: 694c56cf52cb7a15230b9a2cdd34642c7820b407
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897570"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quotas e limitações em serviços de multimédia do Azure v3

Este artigo descreve as quotas e limitações em serviços de multimédia do Azure v3.

| Recurso | Limite Predefinido | 
| --- | --- | 
| Elementos por conta de Media Services do Azure | 1 000 000|
| Filtros do manifesto dinâmico|100|
| JobInputs por tarefa | 50 (fixa)|
| JobOutputs por tarefa/TransformOutputs numa transformação | 20 (fixa) |
| Ficheiros por JobInput|10 (fixa)|
| Tamanho dos ficheiros| Em alguns cenários, existe um limite no tamanho de ficheiro máximo suportado para processamento nos serviços de multimédia. <sup>(1)</sup> |
| Tarefas por conta dos Media Services | 500 000 <sup>(2)</sup> (fixa)|
| Listagem transformações|Paginar a resposta, com 1000 transformações por página|
| A listagem de trabalhos|Paginar a resposta, com 500 tarefas por página|
| Eventos em direto por conta dos Media Services |5|
| Contas de serviços de multimédia numa única subscrição | 25 (fixo) |
| Saídas em direto no estado parado por LiveEvent |3|
| Contas de armazenamento | 100<sup>(4)</sup> (fixa) |
| Pontos finais de transmissão em fluxo (parada ou em execução) por conta dos Media Services|2 (fixa)|
| Políticas de Transmissão em Fluxo | 100 <sup>(3)</sup> |
| Transformações por conta dos Media Services | 100 (fixa)|
| Localizadores de transmissão em fluxo exclusivos associados um recurso de uma só vez | 100<sup>(5)</sup> (fixa) |
| Política de chave de conteúdo |30 | 

<sup>1</sup> o tamanho máximo suportado para um único blob está atualmente até 5 TB de armazenamento de Blobs do Azure. No entanto, os limites adicionais aplicam-se nos serviços de multimédia do Azure com a base sobre os tamanhos VM que são utilizados pelo serviço. Se o ficheiro de origem for superior a 260 GB, o seu trabalho provavelmente falhará. Se tiver conteúdo de 4K, que é maior do que o limite de 260 GB, contacte-nos em amshelp@microsoft.com para possíveis atenuações suportar o seu cenário.

<sup>2</sup> este número inclui trabalhos em fila, concluídos, Active Directory e foi cancelados. Não inclui trabalhos eliminados. 

Qualquer registo de tarefa na sua conta mais de 90 dias será eliminado automaticamente, mesmo se o número total de registos for inferior à quota máxima. 

<sup>3</sup> ao utilizar um personalizado [política de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streamingpolicies), deve criar um conjunto limitado dessas políticas para a sua conta de serviço de suporte de dados e utilizá-los novamente para sua StreamingLocators sempre que a encriptação mesmo opções e protocolos são necessários. Devem não ser a criar uma nova política de transmissão em fluxo para cada localizador de transmissão em fluxo.

<sup>4</sup> as contas de armazenamento tem de ser da mesma subscrição do Azure.

<sup>5</sup> localizadores de transmissão em fluxo não foram concebidos para gerir o controlo de acesso por utilizador. Para conceder direitos de acesso diferentes a utilizadores individuais, utilize soluções de Gestão de Direitos Digitais (Digital Rights Management, DRM).

## <a name="support-ticket"></a>Pedido de suporte

Para obter recursos que não estão corrigidos, pode solicitar o aumento das quotas, abrindo uma [pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Inclua informações detalhadas no pedido de alterações de quota pretendida, cenários de casos de utilização e regiões necessários. <br/>**Não** crie contas dos Serviços de Multimédia do Azure adicionais numa tentativa de obter limites superiores.

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral](media-services-overview.md)
