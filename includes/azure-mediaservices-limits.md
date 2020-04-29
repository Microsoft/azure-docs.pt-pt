---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334857"
---
>[!NOTE]
>Para recursos que não estão fixos, abra um bilhete de apoio para pedir um aumento das quotas. Não crie contas adicionais da Azure Media Services numa tentativa de obter limites mais elevados.

| Recurso | Limite | 
| --- | --- | 
| Contas azure Media Services numa única subscrição | 25 (fixo) |
| Unidades reservadas de mídia por conta de Media Services |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Empregos por conta de Serviços de Media | 50.000<sup>2</sup> |
| Tarefas em cadeia por trabalho | 30 (fixo) |
| Ativos por conta de Serviços de Media | 1 000 000|
| Elementos por tarefa | 50 |
| Elementos por trabalho | 100 |
| Localizadores exclusivos associados a um elemento ao mesmo tempo | 5<sup>4</sup> |
| Canais ao vivo por conta de Media Services |5|
| Programas no estado de paragem por canal |50|
| Programas no estado parado por canal |3|
| Pontos finais de streaming que são parados ou em funcionamento por conta de Media Services|2|
| Unidades de transmissão em fluxo por ponto final de transmissão em fluxo |10 |
| Contas de armazenamento | 1.000<sup>5</sup> (fixo) |
| Políticas | 1.000.000<sup>6</sup> |
| Tamanho dos ficheiros| Em alguns cenários, há um limite para o tamanho máximo do ficheiro suportado para processamento em Serviços de Media. <sup>7</sup> |

<sup>1</sup> Se alterar o tipo, por exemplo, de S2 para S1, os limites máximos reservados são redefinidos.

<sup>2</sup> Este número inclui trabalhos em fila, acabados, ativos e cancelados. Não inclui trabalhos apagados. Pode eliminar trabalhos antigos utilizando **o IJob.Delete** ou o pedido **DELETE** HTTP.

A partir de 1 de abril de 2017, qualquer registo de emprego na sua conta com mais de 90 dias é automaticamente apagado, juntamente com os seus registos de tarefas associados. A eliminação automática ocorre mesmo que o número total de registos esteja abaixo do contingente máximo. Para arquivar as informações de trabalho e tarefas, utilize o código descrito na Gestão de [ativos com os Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup> Quando faz um pedido para listar entidades de emprego, um máximo de 1.000 postos de trabalho é devolvido por pedido. Para acompanhar todos os trabalhos submetidos, use as consultas de topo ou ignore as consultas descritas nas opções de consulta do [sistema OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup> Os localizadores não são projetados para gerir o controlo de acesso por utilizador. Para dar diferentes direitos de acesso a utilizadores individuais, utilize soluções de gestão de direitos digitais (DRM). Para mais informações, consulte Proteja o seu conteúdo com a [Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup> As contas de armazenamento devem ser da mesma subscrição do Azure.

<sup>6</sup> Há um limite de 1.000.000 políticas para diferentes políticas de Serviços de Media. Um exemplo é para a política de localização ou ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Se utilizar sempre os mesmos dias e permissões de acesso, utilize a mesma identificação política. Para obter informações e um exemplo, consulte [Gerir ativos com os Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup> O tamanho máximo suportado para uma única bolha é atualmente de até 5 TB no Armazenamento De Blob Azure. Limites adicionais aplicam-se nos Serviços de Media com base nos tamanhos vm que são utilizados pelo serviço. O limite de tamanho aplica-se aos ficheiros que envia e também aos ficheiros que são gerados em resultado do processamento de Serviços de Media (codificação ou análise). Se o seu ficheiro de origem for superior a 260 GB, o seu Trabalho provavelmente falhará. 

A tabela seguinte mostra os limites das unidades reservadas aos meios de comunicação S1, S2 e S3. Se o seu ficheiro fonte for maior do que os limites definidos na tabela, o seu trabalho de codificação falha. Se codificar fontes de resolução 4K de longa duração, é obrigado a utilizar unidades reservadas aos meios de comunicação S3 para obter o desempenho necessário. Se tiver um conteúdo 4K maior do que o limite de 260 GB nas unidades reservadas aos meios de comunicação S3, abra um bilhete de apoio.

|Tipo de unidade reservado aos meios de comunicação    |Tamanho máximo de entrada (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
