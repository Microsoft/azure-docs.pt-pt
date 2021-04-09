---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: c315c071aeb36eea0bd1af84b1344cf2fef5f703
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91329669"
---
>[!NOTE]
>Para recursos que não são fixados, abra um bilhete de apoio para pedir um aumento das quotas. Não crie contas adicionais da Azure Media Services numa tentativa de obter limites mais elevados.

| Recurso | Limite | 
| --- | --- | 
| Contas Azure Media Services numa única subscrição | 25 (fixo) |
| Unidades reservadas aos meios de comunicação por conta de Media Services |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Empregos por Conta de Serviços de Media | 50.000<sup>2</sup> |
| Tarefas em cadeia por trabalho | 30 (fixo) |
| Ativos por conta de Serviços de Media | 1 000 000|
| Elementos por tarefa | 50 |
| Elementos por trabalho | 100 |
| Localizadores exclusivos associados a um elemento ao mesmo tempo | 5<sup>4</sup> |
| Canais ao vivo por conta de Media Services |5|
| Programas no estado de paragem por canal |50|
| Programas no estado parado por canal |3|
| Pontos finais de streaming que são parados ou em execução por conta de Serviços de Media|2|
| Unidades de transmissão em fluxo por ponto final de transmissão em fluxo |10 |
| Contas de armazenamento | 100<sup>5</sup> (fixo) |
| Políticas | 1.000.000<sup>6</sup> |
| Tamanho dos ficheiros| Em alguns cenários, há um limite no tamanho máximo do ficheiro suportado para o processamento em Media Services. <sup>7</sup> |

<sup>1</sup> Se alterar o tipo, por exemplo, de S2 para S1, os limites máximos de unidade reservados são repostos.

<sup>2</sup> Este número inclui trabalhos em fila, acabados, ativos e cancelados. Não inclui empregos apagados. Pode eliminar trabalhos antigos utilizando **o iJob.Delete** ou o pedido **DELETE** HTTP.

A partir de 1 de abril de 2017, qualquer registo de emprego na sua conta com mais de 90 dias é automaticamente eliminado, juntamente com os seus registos de tarefas associados. A eliminação automática ocorre mesmo que o número total de registos seja inferior ao contingente máximo. Para arquivar as informações de trabalho e de tarefa, utilize o código descrito na [Gestão de ativos com os Serviços de Mídia .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup> Quando se faz um pedido de listar entidades de trabalho, um máximo de 1.000 postos de trabalho é devolvido por pedido. Para acompanhar todos os trabalhos submetidos, utilize as consultas de topo ou de salto, conforme descrito nas [opções de consulta do sistema OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup> Os localizadores não são concebidos para gerir o controlo de acesso por utilizador. Para dar diferentes direitos de acesso a utilizadores individuais, utilize soluções de gestão de direitos digitais (DRM). Para mais informações, consulte [Protect your content with Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup> As contas de armazenamento devem ser da mesma subscrição do Azure.

<sup>6</sup> Há um limite de 1.000.000 políticas para diferentes políticas de Serviços de Comunicação Social. Um exemplo é para a política do Localizador ou ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Se utilizar sempre os mesmos dias e permissões de acesso, utilize o mesmo ID de política. Para obter informações e um exemplo, consulte [Gerir ativos com os Serviços de Mídia .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup> O tamanho máximo suportado para uma única bolha é atualmente até 5 TB no Azure Blob Storage. São aplicados limites adicionais nos Serviços de Mídia com base nos tamanhos VM utilizados pelo serviço. O limite de tamanho aplica-se aos ficheiros que carrega e também aos ficheiros que são gerados como resultado do processamento dos Serviços de Comunicação Social (codificação ou análise). Se o seu ficheiro de origem for superior a 260 GB, o seu Trabalho provavelmente falhará. 

A tabela seguinte mostra os limites das unidades reservadas para os meios de comunicação S1, S2 e S3. Se o seu ficheiro de origem for maior do que os limites definidos na tabela, o seu trabalho de codificação falha. Se codificar fontes de resolução 4K de longa duração, é-lhe exigido que utilize unidades reservadas para obter o desempenho necessário. Se tiver um conteúdo 4K superior ao limite de 260 GB nas unidades reservadas aos meios S3, abra um bilhete de apoio.

|Tipo de unidade reservada aos meios de comunicação    |Tamanho máximo de entrada (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
