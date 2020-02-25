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
ms.openlocfilehash: 849d1187d6b854d48ad75ab1e55f600407420346
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562365"
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
A faturação é proclamada| Diárias|Diárias
Encriptação dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Automaticamente escala até a entrada direcionada.|SUs adicional
Filtragem IP/G20/Hospedeiro personalizado <sup>1</sup>|Sim|Sim
Download progressivo|Sim|Sim
Utilização recomendada |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional.

<sup>1</sup> Utilizado apenas diretamente no ponto final de streaming quando o CDN não está ativado no ponto final.<br/>

## <a name="streaming-endpoint-properties"></a>Propriedades de Streaming Endpoint

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

## <a name="why-use-multiple-streaming-endpoints"></a>Porquê usar vários pontos finais de streaming?

Um único ponto final de streaming pode transmitir vídeos ao vivo e a pedido e a maioria dos clientes usa apenas um ponto final de streaming. Esta secção dá alguns exemplos do porquê de você precisar de usar vários pontos finais de streaming.

* Cada unidade reservada permite 200 Mbps de largura de banda. Se necessitar de mais de 2.000 Mbps (2 Gbps) de largura de banda, pode utilizar o segundo ponto final de streaming e o equilíbrio de carga para lhe dar largura de banda adicional.

    No entanto, o CDN é a melhor maneira de obter escala para o streaming de conteúdos, mas se estiver a fornecer tanto conteúdo que o CDN está a puxar mais de 2 Gbps, então pode adicionar pontos finais de streaming adicionais (origens). Neste caso, você precisaria distribuir URLs de conteúdo que são equilibrados em todos os dois pontos finais de streaming. Esta abordagem dá melhor cache do que tentar enviar pedidos para cada origem aleatoriamente (por exemplo, através de um gestor de tráfego). 
    
    > [!TIP]
    > Normalmente, se o CDN estiver a puxar mais de 2 Gbps, então algo pode estar mal configurado (por exemplo, sem proteção de origem).
    
* Carregar equilibrando diferentes fornecedores de CDN. Por exemplo, pode configurar o ponto final de streaming padrão para utilizar o Verizon CDN e criar um segundo para utilizar o Akamai. Em seguida, adicione um pouco de equilíbrio de carga entre os dois para obter o equilíbrio multi-CDN. 

    No entanto, o cliente muitas vezes faz o equilíbrio de carga em vários fornecedores de CDN usando uma única origem.
* Streaming de conteúdo misto: Ao vivo e vídeo a pedido. 

    Os padrões de acesso aos conteúdos ao vivo e a pedido são muito diferentes. O conteúdo ao vivo tende a receber muita procura pelo mesmo conteúdo ao mesmo tempo. O conteúdo a pedido de vídeo (conteúdo de arquivo de cauda longa, por exemplo) tem uma baixa utilização no mesmo conteúdo. Assim, o cache funciona muito bem no conteúdo ao vivo, mas não tão bem no conteúdo da cauda longa.

    Considere um cenário em que os seus clientes estão principalmente a ver conteúdos ao vivo, mas apenas ocasionalmente estão a ver conteúdos on-demand e é servido a partir do mesmo Streaming Endpoint. O baixo uso de conteúdo sonoro ocuparia um espaço de cache que seria melhor guardado para o conteúdo ao vivo. Neste cenário, recomendamos servir os conteúdos ao vivo a partir de um Streaming Endpoint e o conteúdo de cauda longa de outro Streaming Endpoint. Isto melhorará o desempenho do conteúdo do evento ao vivo.
    
## <a name="scaling-streaming-with-cdn"></a>Streaming de escala com CDN

Consulte os seguintes artigos:

- [Visão geral do CDN](../../cdn/cdn-overview.md)
- [Streaming de escala com CDN](scale-streaming-cdn.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

A amostra [deste repositório](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) mostra como iniciar o ponto final de streaming predefinido com .NET.
