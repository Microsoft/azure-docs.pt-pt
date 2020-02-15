---
title: Pontos finais de streaming (Origem)
titleSuffix: Azure Media Services
description: Saiba mais sobre o Streaming Endpoints (Origin), um serviço dinâmico de embalagem e streaming que entrega conteúdo diretamente a uma aplicação de jogador de cliente ou a uma Rede de Entrega de Conteúdos (CDN).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: c1e9be605a6f01695f2472ae76a9e5a786388aa0
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206111"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Pontos Finais de Streaming (Origem) nos Serviços De Mídia Azure

No Microsoft Azure Media Services, um [Streaming Endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) representa um serviço dinâmico de embalagem e origem (just-in-time) que pode entregar os seus conteúdos ao vivo e a pedido diretamente a uma aplicação de jogador de clientes utilizando um dos protocolos comuns de streaming de mídia (HLS ou DASH). Além disso, o **Streaming Endpoint** fornece encriptação dinâmica (just-in-time) às DRMs líderes da indústria.

Ao criar uma conta de Media Services, é criado um Ponto Final de Streaming **por defeito** para si num estado de paragem. Não é possível eliminar o ponto final de streaming **predefinido.** Mais pontos finais de streaming podem ser criados sob a conta (ver [Quotas e limitações).](limits-quotas-constraints.md)

> [!NOTE]
> Para começar a transmitir vídeos, precisa de iniciar o **Streaming Endpoint** a partir do qual pretende transmitir o vídeo.
>
> Só tens faturado quando o teu Streaming Endpoint está em estado de corrida.

## <a name="naming-convention"></a>Convenção de nomeação

O formato de nome do anfitrião do URL de streaming é: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, onde `servicename` = o nome final de streaming ou o nome do evento ao vivo.

Ao utilizar o ponto final de transmissão predefinido, `servicename` é omitida para que o URL seja: `{accountname}-{regionname}.streaming.azure.net`.

### <a name="limitations"></a>Limitações

* O nome final de streaming tem um valor máximo de 24 caracteres.
* O nome deve seguir este padrão [regex:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Tipos

Existem dois tipos **de Streaming Endpoint:** **Standard** (pré-visualização) e **Premium**. O tipo é definido pelo número de unidades de escala (`scaleUnits`) que atribui para o ponto final de streaming.

A tabela descreve os tipos:

|Tipo|Unidades de escala|Descrição|
|--------|--------|--------|  
|**Standard**|0|O ponto final de streaming predefinido é um tipo **Standard** — pode ser alterado para o tipo Premium ajustando `scaleUnits`.|
|**Premium**|>0|**Premium** Os pontos finais de streaming são adequados para cargas de trabalho avançadas e proporcionam uma capacidade de largura de banda dedicada e escalável. Muda-se para um tipo **Premium** ajustando `scaleUnits` (unidades de streaming). `scaleUnits` fornecer-lhe uma capacidade de egress dedicada que pode ser comprada em incrementos de 200 Mbps. Ao utilizar o tipo **Premium,** cada unidade ativada proporciona uma capacidade adicional de largura de banda à aplicação. |

> [!NOTE]
> Para clientes que procuram entregar conteúdo a grandes audiências da Internet, recomendamos que ative a CDN no Streaming Endpoint.

Para obter informações sobre SLA, consulte [Preços e SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Comparar tipos de streaming

Funcionalidade|Standard|Premium
---|---|---
Débito |Até 600 Mbps e pode fornecer uma entrada muito mais eficaz quando um CDN é usado.|200 Mbps por unidade de streaming (SU). Pode fornecer uma entrada muito mais eficaz quando um CDN é usado.
CDN|Azure CDN, CDN de terceiros, ou sem CDN.|Azure CDN, CDN de terceiros, ou sem CDN.
A faturação é proclamada| Diariamente|Diariamente
Encriptação dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Automaticamente escala até a entrada direcionada.|SUs adicional
Filtragem IP/G20/Hospedeiro personalizado <sup>1</sup>|Sim|Sim
Download progressivo|Sim|Sim
Utilização recomendada |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional.

<sup>1</sup> Utilizado apenas diretamente no ponto final de streaming quando o CDN não está ativado no ponto final.<br/>

## <a name="properties"></a>Propriedades

Esta secção fornece detalhes sobre algumas das propriedades do Streaming Endpoint. Por exemplo, como criar um novo ponto final de streaming e descrições de todas as propriedades, consulte [streaming Endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create).

- `accessControl`: Utilizado para configurar as seguintes definições de segurança para este ponto final de streaming: Chaves de autenticação do cabeçalho de assinatura Akamai e endereços IP que são autorizados a ligar-se a este ponto final. Esta propriedade só pode ser definida quando `cdnEnabled` é definido como falso.

- `cdnEnabled`: Indica se a integração do CDN Azure para este ponto final de streaming está ativada (desativada por padrão). Se for `cdnEnabled` verdadeiramente, as seguintes configurações são desativadas: `customHostNames` e `accessControl`.

    Nem todos os centros de dados suportam a integração do CDN Azure. Para verificar se o seu centro de dados tem a integração Azure CDN disponível, faça os seguintes passos:

  - Tente tornar o `cdnEnabled` verdadeiro.
  - Verifique o resultado devolvido por um `HTTP Error Code 412` (Pré-condiçãoFailed) com uma mensagem de "Streaming endpoint CdnEnabled property não pode ser definido como verdadeiro, uma vez que a capacidade de CDN não está disponível na região atual."

    Se tiver este erro, o centro de dados não o suporta. Tente outro centro de dados.

- `cdnProfile`: Quando `cdnEnabled` for verdadeira, também pode passar `cdnProfile` valores. `cdnProfile` é o nome do perfil CDN onde será criado o ponto final da CDN. Pode fornecer um cdnProfile existente ou utilizar um novo. Se o valor for NULO e `cdnEnabled` for verdadeiro, o valor padrão "AzureMediaStreamingPlatformCdnProfile" é utilizado. Se o `cdnProfile` já existir, é criado um ponto final sob o mesmo. Se o perfil não existir, um novo perfil é automaticamente criado.
- `cdnProvider`: Quando o CDN estiver ativado, também pode passar `cdnProvider` valores. `cdnProvider` controlos que o fornecedor irá utilizar. Atualmente, são apoiados três valores: "StandardVerizon", "PremiumVerizon" e "StandardAkamai". Se não for fornecido qualquer valor e `cdnEnabled` for verdade, "StandardVerizon" é usado (é esse o valor padrão).
- `crossSiteAccessPolicies`: Utilizado para especificar políticas de acesso ao cross site para vários clientes. Para mais informações, consulte a [especificação de ficheiros de política de domínio transversal](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) e [disponibilize um serviço através dos limites](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)do domínio . As definições aplicam-se apenas ao Smooth Streaming.
- `customHostNames`: Usado para configurar um Ponto Final de Streaming para aceitar o tráfego direcionado para um nome de anfitrião personalizado. Esta propriedade é válida para Pontos Finais de Streaming Standard e Premium e pode ser definida quando `cdnEnabled`: falso.

    A propriedade do nome de domínio deve ser confirmada pela Media Services. A Media Services verifica a propriedade do nome de domínio exigindo um registo `CName` contendo o ID da conta dos Serviços de Media como componente a adicionar ao domínio em uso. Como exemplo, para que "sports.contoso.com" seja usado como nome de anfitrião personalizado para o ponto final de streaming, um registo para `<accountId>.contoso.com` deve ser configurado para apontar um dos nomes de anfitriões de verificação de Media Services. O nome do anfitrião de verificação é composto por verificadns.\<mediaservices-dns-zone>.

    Seguem-se as zonas dNS esperadas a utilizar no registo de verificação das diferentes regiões de Azure.
  
  - América do Norte, Europa, Singapura, Hong Kong SAR, Japão:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - China:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Por exemplo, um registo `CName` que mapeia "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" para "verifydns.media.azure.net" prova que o ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad tem a propriedade do domínio contoso.com, permitindo assim que qualquer nome sob contoso.com seja usado como um nome de anfitrião personalizado para um ponto final de streaming nessa conta. Para encontrar o valor de ID do Serviço de Media, vá ao [portal Azure](https://portal.azure.com/) e selecione a sua conta de Media Service. O ID da **conta** aparece no topo direito da página.

    Se houver uma tentativa de definir um nome de anfitrião personalizado sem uma verificação adequada do registo `CName`, a resposta do DNS falhará e será em cache por algum tempo. Uma vez registado de forma adequada, pode demorar algum tempo até que a resposta em cache seja revalidada. Dependendo do fornecedor DNS para o domínio personalizado, leva de alguns minutos a uma hora para revalidar o disco.

    Para além da `CName` que os mapas `<accountId>.<parent domain>` para `verifydns.<mediaservices-dns-zone>`, é preciso criar outra `CName` que mapeie o nome de anfitrião personalizado (por exemplo, `sports.contoso.com`) para o nome de anfitrião do seu Media Services Streaming Endpoint (por exemplo, `amstest-usea.streaming.media.azure.net`).

    > [!NOTE]
    > O streaming Endpoints localizado no mesmo centro de dados não pode partilhar o mesmo nome de anfitrião personalizado.

    Atualmente, a Media Services não suporta a SSL com domínios personalizados.

- `maxCacheAge` - Sobrepõe-se ao cabeçalho de controlo de cache HTTP de idade máxima definido pelo ponto final de streaming em fragmentos de mídia e manifestos a pedido. O valor é definido em segundos.
- `resourceState` -

    - Parado: o estado inicial de um Ponto Final de Streaming após a criação
    - Começando: está em transição para o estado de corrida
    - Execução: é capaz de transmitir conteúdo para os clientes
    - Escala: as unidades de escala estão a ser aumentadas ou diminuídas
    - Paragem: está em transição para o estado deparado
    - Eliminação: está a ser eliminada

- `scaleUnits`: Forneça-lhe uma capacidade de saída dedicada que pode ser adquirida em incrementos de 200 Mbps. Se precisar de passar para um tipo **Premium,** ajuste `scaleUnits`.

## <a name="working-with-cdn"></a>Trabalhar com a CDN

Na maioria dos casos, deve ter a CDN ativada. No entanto, se está a antecipar a moeda máxima inferior a 500 espectadores, então é recomendado desativar o CDN uma vez que a CDN melhor se qualifica com a conmoeda.

### <a name="considerations"></a>Considerações

* O `hostname` de streaming Endpoint e o URL de streaming permanecem os mesmos, quer ative ou não o CDN.
* Se precisar da capacidade de testar o seu conteúdo com ou sem CDN, crie outro Ponto final de streaming que não esteja ativado pela CDN.

### <a name="detailed-explanation-of-how-caching-works"></a>Explicação detalhada de como o cache funciona

Não há um valor específico de largura de banda ao adicionar o CDN porque a quantidade de largura de banda necessária para um ponto final de streaming ativado por CDN varia. Muito depende do tipo de conteúdo, do quão popular é, dos bitrates e dos protocolos. O CDN só está a apertar o que está a ser pedido. Isto significa que os conteúdos populares serão servidos diretamente a partir do CDN- desde que o fragmento de vídeo esteja em cache. É provável que o conteúdo ao vivo seja emcache porque normalmente tem muitas pessoas a ver exatamente a mesma coisa. O conteúdo a pedido pode ser um pouco mais complicado porque pode ter algum conteúdo que seja popular e outros que não são. Se você tem milhões de ativos de vídeo onde nenhum deles é popular (apenas um ou dois espectadores por semana) mas você tem milhares de pessoas assistindo todos os vídeos diferentes, o CDN torna-se muito menos eficaz. Com esta cache falha, aumenta-se a carga no ponto final de streaming.

Também precisa de considerar como funciona o streaming adaptativo. Cada fragmento de vídeo individual é cached como sua própria entidade. Por exemplo, imagine a primeira vez que um determinado vídeo é visto. Se o espectador saltar por aí assistindo apenas alguns segundos aqui e ali, apenas os fragmentos de vídeo associados ao que a pessoa viu ficam em cache no CDN. Com o streaming adaptativo, você normalmente tem 5 a 7 bitrates diferentes de vídeo. Se uma pessoa está a ver um bitrate e outra pessoa está a ver um bitrate diferente, então cada um está em cache separadamente no CDN. Mesmo que duas pessoas estejam a ver o mesmo bitrate, podem estar a transmitir protocolos diferentes. Cada protocolo (HLS, MPEG-DASH, Smooth Streaming) é cached separadamente. Assim, cada bitrate e protocolo são cached separadamente e apenas os fragmentos de vídeo que foram solicitados são cached.

### <a name="enable-azure-cdn-integration"></a>Permitir a integração do CDN azure

> [!IMPORTANT]
> Não é possível permitir a CDN para julgamento ou contas de estudante sinuoso Azure.
>
> A integração do CDN é ativada em todos os centros de dados azure, exceto governo federal e regiões da China.

Depois de um Streaming Endpoint ser aprovisionado com CDN ativado, há um tempo de espera definido nos Serviços de Media antes que a atualização do DNS seja feita para mapear o ponto final de streaming para o ponto final da CDN.

Se mais tarde pretender desativar/ativar o CDN, o seu ponto final de streaming deve estar no estado **de paragem.** Pode levar até duas horas para que a integração do CDN Azure seja ativada e que as alterações sejam ativas em todos os POPs da CDN. No entanto, pode iniciar o seu ponto final de streaming e transmitir sem interrupções do ponto final de streaming e uma vez concluída a integração, o fluxo é entregue a partir do CDN. Durante o período de provisionamento, o seu ponto final de streaming estará no estado **de partida** e poderá observar um desempenho degradado.

Quando o ponto final de streaming Standard é criado, é configurado por padrão com standard Verizon. Pode configurar fornecedores Premium Verizon ou Standard Akamai utilizando APIs REST.

A integração da Azure Media Services com o Azure CDN é implementada no **Azure CDN da Verizon** para pontos finais de streaming padrão. Os pontos finais de streaming premium podem ser configurados utilizando todos os níveis e fornecedores de **preços Do CDN do Azure.** 

> [!NOTE]
> Para mais detalhes sobre o Azure CDN, consulte a visão geral do [CDN](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-was-made"></a>Determine se a alteração do DNS foi feita

Pode determinar se a alteração do DNS foi feita num Ponto Final de Streaming (o tráfego está a ser direcionado para o Azure CDN) utilizando https://www.digwebinterface.com. Se os resultados azureedge.net nomes de domínio nos resultados, o tráfego está agora a ser apontado para o CDN.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="see-also"></a>Veja também

[Visão geral do CDN](../../cdn/cdn-overview.md)

## <a name="next-steps"></a>Passos seguintes

A amostra [deste repositório](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) mostra como iniciar o ponto final de streaming predefinido com .NET.
