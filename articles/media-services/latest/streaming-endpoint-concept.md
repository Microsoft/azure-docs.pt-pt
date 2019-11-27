---
title: Pontos de extremidade de streaming (origem)
titleSuffix: Azure Media Services
description: Saiba mais sobre os pontos de extremidade de streaming (origem), um serviço de empacotamento dinâmico e streaming que fornece conteúdo diretamente a um aplicativo de player de cliente ou a uma CDN (rede de distribuição de conteúdo).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: c8901dccb67e91c608e999f823cf7d2e757da08b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186007"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Pontos de extremidade de streaming (origem) nos serviços de mídia do Azure

No Serviços de Mídia do Microsoft Azure, um [ponto de extremidade de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) representa um serviço de empacotamento e origem (just-in-time) dinâmico que pode entregar seu conteúdo ao vivo e sob demanda diretamente a um aplicativo de player de cliente usando um dos protocolos de mídia de streaming comuns (HLS ou Dash). Além disso, o **ponto de extremidade de streaming** fornece criptografia dinâmica (just-in-time) para DRMs líderes do setor.

Quando você cria uma conta dos serviços de mídia, um ponto de extremidade de streaming **padrão** é criado para você em um estado parado. Não é possível excluir o ponto de extremidade de streaming **padrão** . Mais pontos de extremidade de streaming podem ser criados na conta (consulte [cotas e limitações](limits-quotas-constraints.md)).

> [!NOTE]
> Para iniciar o streaming de vídeos, você precisa iniciar o **ponto de extremidade de streaming** do qual você deseja transmitir o vídeo.
>
> Você será cobrado somente quando o ponto de extremidade de streaming estiver no estado executando.

## <a name="naming-convention"></a>Convenção de nomenclatura

O formato do nome de host da URL de streaming é: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, em que `servicename` = o nome do ponto de extremidade de streaming ou o nome do evento ao vivo.

Ao usar o ponto de extremidade de streaming padrão, `servicename` é omitido para que a URL seja: `{accountname}-{regionname}.streaming.azure.net`.

### <a name="limitations"></a>Limitações

* O nome do ponto de extremidade de streaming tem um valor máximo de 24 caracteres.
* O nome deve seguir este padrão de [Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Tipos

Há dois tipos de **ponto de extremidade de streaming** : **Standard** (visualização) e **Premium**. O tipo é definido pelo número de unidades de escala (`scaleUnits`) que você aloca para o ponto de extremidade de streaming.

A tabela descreve os tipos:

|Tipo|Unidades de escala|Descrição|
|--------|--------|--------|  
|**Standard**|0|O ponto de extremidade de streaming padrão é um tipo **padrão** — ele pode ser alterado para o tipo Premium ajustando `scaleUnits`.|
|**Premium**|>0|**Premium** Os pontos de extremidade de streaming são adequados para cargas de trabalho avançadas e para fornecer capacidade de largura de banda dedicada e escalonável. Você passa para um tipo **Premium** ajustando `scaleUnits` (unidades de streaming). `scaleUnits` fornecem uma capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps. Ao usar o tipo **Premium** , cada unidade habilitada fornece capacidade de largura de banda adicional para o aplicativo. |

> [!NOTE]
> Para clientes que procuram fornecer conteúdo a grandes públicos da Internet, recomendamos que você habilite a CDN no ponto de extremidade de streaming.

Para obter informações de SLA, consulte [preços e SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Comparando tipos de streaming

Funcionalidade|Standard|Premium
---|---|---
Débito |Até 600 Mbps e pode fornecer uma taxa de transferência muito mais eficiente quando uma CDN é usada.|200 Mbps por SU (unidade de streaming). Pode fornecer uma taxa de transferência muito mais eficiente quando uma CDN é usada.
CDN|CDN do Azure, CDN de terceiros ou nenhuma CDN.|CDN do Azure, CDN de terceiros ou nenhuma CDN.
A cobrança é rateada| Diariamente|Diariamente
Encriptação dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Dimensionamento|Escala verticalmente para a taxa de transferência de destino.|SUs adicional
Filtragem de IP/G20/host personalizado <sup>1</sup>|Sim|Sim
Download progressivo|Sim|Sim
Uso recomendado |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional.

<sup>1</sup> é usado somente diretamente no ponto de extremidade de streaming quando a CDN não está habilitada no ponto de extremidade.<br/>

## <a name="properties"></a>Propriedades

Esta seção fornece detalhes sobre algumas das propriedades do ponto de extremidade de streaming. Para obter exemplos de como criar um novo ponto de extremidade de streaming e descrições de todas as propriedades, consulte [ponto de extremidade](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)de streaming.

- `accessControl`: usado para definir as seguintes configurações de segurança para este ponto de extremidade de streaming: chaves de autenticação de cabeçalho de assinatura Akamai e endereços IP que têm permissão para se conectar a esse ponto de extremidade. Essa propriedade só pode ser definida quando `cdnEnabled` é definida como false.

- `cdnEnabled`: indica se a integração da CDN do Azure para este ponto de extremidade de streaming está habilitada (desabilitada por padrão). Se você definir `cdnEnabled` como true, as configurações a seguir serão desabilitadas: `customHostNames` e `accessControl`.

    Nem todos os data centers dão suporte à integração da CDN do Azure. Para verificar se o data center tem a integração da CDN do Azure disponível, execute as seguintes etapas:

  - Tente definir a `cdnEnabled` como true.
  - Verifique o resultado retornado para um `HTTP Error Code 412` (PreconditionFailed) com uma mensagem de "a propriedade CdnEnabled do ponto de extremidade de streaming não pode ser definida como true, pois a funcionalidade CDN não está disponível na região atual".

    Se você receber esse erro, o data center não oferece suporte a ele. Tente outra data center.

- `cdnProfile`: quando `cdnEnabled` é definido como true, você também pode passar valores `cdnProfile`. `cdnProfile` é o nome do perfil CDN em que o ponto de extremidade CDN será criado. Você pode fornecer um cdnProfile existente ou usar um novo. Se o valor for nulo e `cdnEnabled` for true, o valor padrão "AzureMediaStreamingPlatformCdnProfile" será usado. Se o `cdnProfile` fornecido já existir, um ponto de extremidade será criado sob ele. Se o perfil não existir, um novo perfil será criado automaticamente.
- `cdnProvider`: quando a CDN está habilitada, você também pode passar valores de `cdnProvider`. `cdnProvider` controla qual provedor será usado. Atualmente, há suporte para três valores: "StandardVerizon", "PremiumVerizon" e "StandardAkamai". Se nenhum valor for fornecido e `cdnEnabled` for true, "StandardVerizon" será usado (esse é o valor padrão).
- `crossSiteAccessPolicies`: usado para especificar políticas de acesso entre sites para vários clientes. Para obter mais informações, consulte [especificação de arquivo de política entre domínios](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) e [disponibilizando um serviço entre limites de domínio](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx). As configurações se aplicam somente a Smooth Streaming.
- `customHostNames`: usado para configurar um ponto de extremidade de streaming para aceitar o tráfego direcionado para um nome de host personalizado. Essa propriedade é válida para pontos de extremidade de streaming Standard e Premium e pode ser definida quando `cdnEnabled`: false.

    A propriedade do nome de domínio deve ser confirmada pelos serviços de mídia. Os serviços de mídia verificam a propriedade do nome de domínio exigindo um registro de `CName` que contém a ID da conta dos serviços de mídia como um componente a ser adicionado ao domínio em uso. Por exemplo, para "sports.contoso.com" ser usado como um nome de host personalizado para o ponto de extremidade de streaming, um registro para `<accountId>.contoso.com` deve ser configurado para apontar para um dos nomes de host de verificação dos serviços de mídia. O nome do host de verificação é composto por verifydns.\<mediaservices-DNS-> de zona.

    A seguir estão as zonas DNS esperadas a serem usadas no registro de verificação para diferentes regiões do Azure.
  
  - América do Norte, Europa, Cingapura, RAE de Hong Kong, Japão:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - China:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Por exemplo, um registro de `CName` que mapeia "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" para "verifydns.media.azure.net" comprova que a ID de serviços de mídia 945a4c4e-28ea-45cd-8ccb-a519f6b700ad tem a propriedade do domínio contoso.com, permitindo assim que qualquer nome em contoso.com seja usado como um nome de host personalizado para um ponto de extremidade de streaming nessa conta. Para localizar o valor da ID do serviço de mídia, vá para a [portal do Azure](https://portal.azure.com/) e selecione sua conta de serviço de mídia. A **ID da conta** aparece no canto superior direito da página.

    Se houver uma tentativa de definir um nome de host personalizado sem uma verificação adequada do registro de `CName`, a resposta DNS falhará e será armazenada em cache por algum tempo. Quando um registro adequado estiver em vigor, pode levar algum tempo até que a resposta armazenada em cache seja revalidada. Dependendo do provedor DNS para o domínio personalizado, levará de alguns minutos a uma hora para revalidar o registro.

    Além do `CName` que mapeia `<accountId>.<parent domain>` para `verifydns.<mediaservices-dns-zone>`, você deve criar outro `CName` que mapeie o nome de host personalizado (por exemplo, `sports.contoso.com`) para o nome de host do ponto de extremidade de streaming dos serviços de mídia (por exemplo, `amstest-usea.streaming.media.azure.net`).

    > [!NOTE]
    > Os pontos de extremidade de streaming localizados no mesmo data center não podem compartilhar o mesmo nome de host personalizado.

    Atualmente, os serviços de mídia não dão suporte a SSL com domínios personalizados.

- `maxCacheAge`-substitui o cabeçalho de controle de cache HTTP Max-age padrão definido pelo ponto de extremidade de streaming em fragmentos de mídia e manifestos sob demanda. O valor é definido em segundos.
- `resourceState` -

    - Stopped: o estado inicial de um ponto de extremidade de streaming após a criação
    - Iniciando: está fazendo a transição para o estado de execução
    - Em execução: é capaz de transmitir conteúdo para clientes
    - Dimensionamento: as unidades de escala estão sendo aumentadas ou diminuídas
    - Parando: está fazendo a transição para o estado parado
    - Excluindo: está sendo excluído

- `scaleUnits`: forneça a capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps. Se você precisar mover para um tipo **Premium** , ajuste `scaleUnits`.

## <a name="working-with-cdn"></a>Trabalhando com CDN

Na maioria dos casos, deve ter a CDN ativada. No entanto, se você estiver prevendo a simultaneidade máxima inferior a 500 visualizadores, é recomendável desabilitar a CDN, uma vez que a CDN é dimensionada melhor com simultaneidade.

### <a name="considerations"></a>Considerações

* O ponto de extremidade de streaming `hostname` e a URL de streaming permanecem os mesmos, independentemente de você habilitar ou não a CDN.
* Se você precisar da capacidade de testar seu conteúdo com ou sem CDN, crie outro ponto de extremidade de streaming que não esteja habilitado para CDN.

### <a name="detailed-explanation-of-how-caching-works"></a>Explicação detalhada de como funciona o Caching

Não há nenhum valor de largura de banda específico ao adicionar a CDN porque a quantidade de largura de banda necessária para um ponto de extremidade de streaming habilitado para CDN varia. Muito depende do tipo de conteúdo, do quão popular é, das taxas de bits e dos protocolos. A CDN está armazenando em cache apenas o que está sendo solicitado. Isso significa que o conteúdo popular será servido diretamente da CDN, desde que o fragmento de vídeo seja armazenado em cache. É provável que o conteúdo ao vivo seja armazenado em cache porque você normalmente tem muitas pessoas assistindo exatamente à mesma coisa. O conteúdo sob demanda pode ser um pouco mais complicado porque você pode ter algum conteúdo que seja popular e outros não. Se você tem milhões de ativos de vídeo em que nenhum deles é popular (apenas um ou dois visualizadores por semana), mas você tem milhares de pessoas assistindo a todos os vídeos diferentes, a CDN se torna muito menos eficiente. Com esses erros de cache, você aumenta a carga no ponto de extremidade de streaming.

Você também precisa considerar como funciona a transmissão adaptável. Cada fragmento de vídeo individual é armazenado em cache como sua própria entidade. Por exemplo, imagine a primeira vez que um determinado vídeo é observado. Se o visualizador ignorar apenas alguns segundos aqui e houver, somente os fragmentos de vídeo associados ao que a pessoa observou serão armazenados em cache na CDN. Com o streaming adaptável, normalmente você tem de 5 a 7 taxas de bits diferentes de vídeo. Se uma pessoa estiver assistindo a uma taxa de bits e outra pessoa estiver assistindo a uma taxa de bits diferente, ela será cada uma armazenada em cache separadamente na CDN. Mesmo que duas pessoas estejam assistindo a mesma taxa de bits, elas podem ser transmitidas por protocolos diferentes. Cada protocolo (HLS, MPEG-DASH, Smooth Streaming) é armazenado em cache separadamente. Portanto, cada taxa de bits e cada protocolo são armazenados em cache separadamente e somente os fragmentos de vídeo que foram solicitados são armazenados em cache.

### <a name="enable-azure-cdn-integration"></a>Habilitar a integração da CDN do Azure

Depois que um ponto de extremidade de streaming é provisionado com a CDN habilitada, há um tempo de espera definido nos serviços de mídia antes que a atualização de DNS seja feita para mapear o ponto de extremidade de streaming para o ponto de extremidade CDN.

Se posteriormente você quiser desabilitar/habilitar a CDN, o ponto de extremidade de streaming deverá estar no estado **parado** . Pode levar até duas horas para que a integração da CDN do Azure seja habilitada e que as alterações estejam ativas em todos os POPs da CDN. No entanto, você pode iniciar seu ponto de extremidade de streaming e transmitir sem interrupções do ponto de extremidade de streaming e depois que a integração for concluída, o fluxo será entregue da CDN. Durante o período de provisionamento, o ponto de extremidade de streaming estará no estado **inicial** e você poderá observar o desempenho degradado.

Quando o ponto de extremidade de streaming padrão é criado, ele é configurado por padrão com a Verizon padrão. Você pode configurar provedores da Verizon Premium ou Standard Akamai usando APIs REST.

A integração da CDN é habilitada em todos os data centers do Azure, exceto nas regiões da China e do governo federal.

> [!IMPORTANT]
> A integração dos serviços de mídia do Azure com a CDN do Azure é implementada na **CDN do Azure da Verizon** para pontos de extremidade de streaming padrão. Os pontos de extremidade de streaming Premium podem ser configurados usando todos os **tipos de preços e provedores da CDN do Azure**. Para obter mais informações sobre os recursos da CDN do Azure, consulte a [visão geral da CDN](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-was-made"></a>Determinar se a alteração de DNS foi feita

Você pode determinar se a alteração de DNS foi feita em um ponto de extremidade de streaming (o tráfego está sendo direcionado para a CDN do Azure) usando https://www.digwebinterface.com. Se os resultados tiverem nomes de domínio azureedge.net nos resultados, o tráfego agora estará sendo apontado para a CDN.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos seguintes

O exemplo [neste repositório](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) mostra como iniciar o ponto de extremidade de streaming padrão com o .net.
