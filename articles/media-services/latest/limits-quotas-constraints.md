---
title: Cotas e limitações nos serviços de mídia do Azure v3 | Microsoft Docs
description: Este tópico descreve cotas e limitações nos serviços de mídia do Azure v3
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
ms.openlocfilehash: 2a530d6a1a447ccde448259623da6faeaa6a4d72
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598368"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Cotas e limitações nos serviços de mídia do Azure v3

Este artigo descreve cotas e limitações nos serviços de mídia do Azure v3.

| Recurso | Limite Predefinido | 
| --- | --- | 
| Recursos por conta dos Serviços de Multimédia do Azure | 1 000 000|
| Filtros do Manifesto Dinâmico|100|
| JobInputs por trabalho | 50 (fixo)|
| JobOutputs por trabalho | 20 (fixo) |
| TransformOutputs em uma transformação | 20 (fixo) |
| Arquivos por JobInput|10 (fixo)|
| Tamanho dos ficheiros| Em alguns cenários, há um limite no tamanho máximo de arquivo com suporte para processamento nos serviços de mídia. <sup>uma</sup> |
| Trabalhos por conta de serviços de mídia | 500.000 <sup>(2)</sup> (fixo)|
| Eventos em Direto por conta dos Serviços de Multimédia |5|
| Contas de serviços de mídia em uma única assinatura | 25 (fixo) |
| Saídas ao vivo por evento ao vivo |3 <sup>(3)</sup> |
| Duração máxima de saída ao vivo | 25 horas |
| Contas de armazenamento | 100<sup>(4)</sup> (fixo) |
| Pontos de extremidade de streaming (interrompidos ou em execução) por conta de serviços de mídia|2 (fixo)|
| Políticas de Transmissão em Fluxo | 100 <sup>(5)</sup> |
| Transformações por conta de serviços de mídia | 100 (fixo)|
| Localizadores de streaming exclusivos associados a um ativo ao mesmo tempo | 100<sup>(6)</sup> (fixo) |
| Opções por política de chave de conteúdo |30 | 
| Licenças por mês para cada um dos tipos de DRM no serviço de entrega de chave dos serviços de mídia por conta|1 000 000|

<sup>1</sup> o tamanho máximo com suporte para um único blob atualmente é de até 5 TB no armazenamento de BLOBs do Azure. Limites adicionais se aplicam aos serviços de mídia com base nos tamanhos de VM que são usados pelo serviço. O limite de tamanho se aplica aos arquivos que você carrega e também aos arquivos que são gerados como resultado do processamento de serviços de mídia (codificação ou análise). Se o arquivo de origem for maior do que 260 GB, provavelmente haverá falha no trabalho. 

A tabela a seguir mostra os limites nas unidades reservadas de mídia S1, S2 e S3. Se o arquivo de origem for maior do que os limites definidos na tabela, o trabalho de codificação falhará. Se você codificar fontes de resolução de 4K de longa duração, será necessário usar unidades reservadas de mídia S3 para atingir o desempenho necessário. Se você tiver um conteúdo de 4K maior do que o limite de 260 GB nas unidades reservadas de mídia S3, abra um tíquete de suporte.

|Tipo de unidade reservada de mídia   |Tamanho máximo de entrada (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> esse número inclui trabalhos enfileirados, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. 

Qualquer registro de trabalho em sua conta com mais de 90 dias será excluído automaticamente, mesmo que o número total de registros esteja abaixo da cota máxima. 

<sup>3</sup> saídas ao vivo começam na criação e param quando excluídas.

<sup>4</sup> as contas de armazenamento devem ser da mesma assinatura do Azure.

<sup>5</sup> ao usar uma [política de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)personalizada, você deve criar um conjunto limitado de políticas para sua conta de serviço de mídia e reutilizá-las para o StreamingLocators sempre que forem necessárias as mesmas opções de criptografia e protocolos. Você não deve criar uma nova política de streaming para cada localizador de streaming.

<sup>6</sup> os localizadores de streaming não foram projetados para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes a utilizadores individuais, utilize soluções de Gestão de Direitos Digitais (Digital Rights Management, DRM).

## <a name="support-ticket"></a>Tíquete de suporte

Para recursos que não são corrigidos, você pode solicitar que as cotas sejam geradas, abrindo um [tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Inclua informações detalhadas na solicitação sobre as alterações de cota desejadas, cenários de caso de uso e regiões necessárias. <br/>**Não** crie contas dos Serviços de Multimédia do Azure adicionais numa tentativa de obter limites superiores.

## <a name="next-steps"></a>Passos seguintes

[Descrição geral](media-services-overview.md)
