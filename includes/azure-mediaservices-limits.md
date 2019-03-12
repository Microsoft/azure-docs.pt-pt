---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: e7243c1f0c0bc26b2e77a060b5def6e204835235
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554235"
---
>[!NOTE]
>Para obter recursos que não estão corrigidos, abra um pedido de suporte para pedir um aumento de quotas. Não crie contas de serviços de multimédia do Azure adicionais numa tentativa de obter limites superiores.

| Recurso | Limite predefinido | 
| --- | --- | 
| Contas de serviços de multimédia do Azure numa única subscrição | 25 (fixo) |
| Unidades por conta dos serviços de multimédia reservadas de multimédia |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Tarefas por conta dos Media Services | 50 000<sup>2</sup> |
| Tarefas em cadeia por trabalho | 30 (fixo) |
| Elementos por conta dos Media Services | 1 000 000|
| Elementos por tarefa | 50 |
| Elementos por trabalho | 100 |
| Localizadores exclusivos associados a um elemento ao mesmo tempo | 5<sup>4</sup> |
| Canais em direto por conta dos Media Services |5|
| Programas no estado de paragem por canal |50|
| Programas no estado parado por canal |3|
| Pontos finais que estão parados de transmissão em fluxo ou em execução por conta dos Media Services|2|
| Unidades de transmissão em fluxo por ponto final de transmissão em fluxo |10 |
| Contas de armazenamento | 1000<sup>5</sup> (fixo) |
| Políticas | 1 000 000<sup>6</sup> |
| Tamanho dos ficheiros| Em alguns cenários, existe um limite no tamanho de ficheiro máximo suportado para processamento nos serviços de multimédia. <sup>7</sup> |

<sup>1</sup>se alterar o tipo, por exemplo, de S2 para S1, os limites de unidade reservada máximo são repostos.

<sup>2</sup>este número inclui trabalhos em fila, concluídos, Active Directory e foi cancelados. Ele não inclui trabalhos eliminados. Pode eliminar trabalhos antigos com **Ijob** ou o **eliminar** pedido HTTP.

A partir de 1 de Abril de 2017, qualquer registo de tarefa na sua conta mais de 90 dias é eliminado automaticamente, juntamente com os seus registos de tarefa associada. Eliminação automática ocorre mesmo se o número total de registos for inferior à quota máxima. Para arquivar as informações de trabalhos e tarefas, utilize o código descrito em [gerir recursos com o SDK .NET dos Media Services](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>quando fizer um pedido para entidades de trabalho de lista, um máximo de 1.000 tarefas é devolvido por pedido. Para manter o controle de todos os trabalhos submetidos, utilize a parte superior ou ignorar a consultas conforme descrito em [opções de consulta do OData system](https://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup>localizadores não são concebidos para gerir o controlo de acesso por utilizador. Para conceder direitos de acesso diferentes para utilizadores individuais, utilize as soluções de gestão (DRM) de direitos digitais. Para obter mais informações, consulte [proteger o seu conteúdo com os serviços de multimédia do Azure](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup>as contas de armazenamento tem de ser da mesma subscrição do Azure.

<sup>6</sup>existe um limite de 1,000,000 políticas para diferentes políticas de serviços de multimédia. Um exemplo é para a política Locator ou ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Se sempre utiliza os mesmos dias e as permissões de acesso, utilize o mesmo ID de política. Para informações e exemplos, consulte [gerir recursos com o SDK .NET dos Media Services](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup>se carregar conteúdo para um elemento nos serviços de multimédia para processá-lo com um dos processadores de multimédia no serviço, tenha em atenção os tamanhos de ficheiro máximo que são suportados. Ativos incluem codificadores como o Media Encoder Standard e o Media Encoder Premium Workflow ou motores de análise, como o detetor de rostos.

O tamanho máximo suportado para um único blob está atualmente até 5 TB de armazenamento de Blobs do Azure. Limites adicionais aplicam-se nos serviços de multimédia com base sobre os tamanhos VM que são utilizados pelo serviço. A tabela seguinte mostra os limites no reservadas de multimédia unidades de S1, S2 e S3. Se o ficheiro de origem for maior do que os limites definidos na tabela, falha a tarefa de codificação. Se a codificar fontes de resolução de 4K de longa duração, tem de utilizar unidades reservadas de multimédia S3 para alcançar o desempenho necessário. Se tiver conteúdo de 4K, que é maior do que o limite de 260 GB nas unidades reservadas de multimédia S3, contacte-nos em amshelp@microsoft.com para possíveis atenuações suportar o seu cenário.

| Tipo de unidades reservadas de multimédia | Tamanho máximo de entrada (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
