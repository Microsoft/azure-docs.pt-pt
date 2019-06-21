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
ms.date: 05/16/2019
ms.author: juliako
ms.openlocfilehash: 709ed293dbb0550dc1bb43bf1e1e1cc50906cc31
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293449"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quotas e limitações em serviços de multimédia do Azure v3

Este artigo descreve as quotas e limitações em serviços de multimédia do Azure v3.

| Resource | Limite Predefinido | 
| --- | --- | 
| Recursos por conta dos Serviços de Multimédia do Azure | 1 000 000|
| Filtros do Manifesto Dinâmico|100|
| JobInputs por tarefa | 50 (fixa)|
| JobOutputs por tarefa | 20 (fixa) |
| TransformOutputs numa transformação | 20 (fixa) |
| Ficheiros por JobInput|10 (fixa)|
| Tamanho dos ficheiros| Em alguns cenários, existe um limite no tamanho de ficheiro máximo suportado para processamento nos serviços de multimédia. <sup>(1)</sup> |
| Tarefas por conta dos Media Services | 500 000 <sup>(2)</sup> (fixa)|
| Listar Transformações|Paginar a resposta, com 1000 Transformações por página|
| Listar Tarefas|Paginar a resposta, com 500 Tarefas por página|
| Eventos em Direto por conta dos Serviços de Multimédia |5|
| Contas de serviços de multimédia numa única subscrição | 25 (fixo) |
| Saídas em direto por evento em direto |3 <sup>(3)</sup> |
| Duração de saída de Live máx. | 25 horas |
| Contas de armazenamento | 100<sup>(4)</sup> (fixa) |
| Pontos finais de transmissão em fluxo (parada ou em execução) por conta dos Media Services|2 (fixa)|
| Políticas de Transmissão em Fluxo | 100 <sup>(5)</sup> |
| Transformações por conta dos Media Services | 100 (fixa)|
| Localizadores de transmissão em fluxo exclusivos associados um recurso de uma só vez | 100<sup>(6)</sup> (fixa) |
| Opções de acordo com a política de chave de conteúdo |30 | 
| Licenças por mês para cada um dos tipos DRM, serviços de multimédia do serviço de entrega por conta da chave|1 000 000|

<sup>1</sup> o tamanho máximo suportado para um único blob está atualmente até 5 TB de armazenamento de Blobs do Azure. Limites adicionais aplicam-se nos serviços de multimédia com base sobre os tamanhos VM que são utilizados pelo serviço. O limite de tamanho aplica-se para os ficheiros que é carregado e também os ficheiros que obterem gerados como resultado dos serviços de multimédia de processamento (codificação ou analisar). Se o ficheiro de origem for superior a 260 GB, o seu trabalho provavelmente falhará. 

A tabela seguinte mostra os limites no reservadas de multimédia unidades de S1, S2 e S3. Se o ficheiro de origem for maior do que os limites definidos na tabela, falha a tarefa de codificação. Se a codificar fontes de resolução de 4K de longa duração, tem de utilizar unidades reservadas de multimédia S3 para alcançar o desempenho necessário. Se tiver conteúdo de 4K, que é maior do que o limite de 260 GB nas unidades reservadas de multimédia S3, contacte-nos em amshelp@microsoft.com para possíveis atenuações suportar o seu cenário.

|Tipo de unidades reservadas de multimédia   |Tamanho máximo de entrada (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> este número inclui trabalhos em fila, concluídos, Active Directory e foi cancelados. Não inclui trabalhos eliminados. 

Qualquer registo de tarefa na sua conta mais de 90 dias será eliminado automaticamente, mesmo se o número total de registos for inferior à quota máxima. 

<sup>3</sup> live saídas início após a criação e param quando eliminado.

<sup>4</sup> as contas de armazenamento tem de ser da mesma subscrição do Azure.

<sup>5</sup> ao utilizar um personalizado [política de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streamingpolicies), deve criar um conjunto limitado dessas políticas para a sua conta de serviço de suporte de dados e utilizá-los novamente para sua StreamingLocators sempre que a encriptação mesmo opções e protocolos são necessários. Devem não ser a criar uma nova política de transmissão em fluxo para cada localizador de transmissão em fluxo.

<sup>6</sup> localizadores de transmissão em fluxo não foram concebidos para gerir o controlo de acesso por utilizador. Para conceder direitos de acesso diferentes a utilizadores individuais, utilize soluções de Gestão de Direitos Digitais (Digital Rights Management, DRM).

## <a name="support-ticket"></a>Pedido de suporte

Para obter recursos que não estão corrigidos, pode solicitar o aumento das quotas, abrindo uma [pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Inclua informações detalhadas no pedido de alterações de quota pretendida, cenários de casos de utilização e regiões necessários. <br/>**Não** crie contas dos Serviços de Multimédia do Azure adicionais numa tentativa de obter limites superiores.

## <a name="next-steps"></a>Passos Seguintes

[Descrição Geral](media-services-overview.md)
