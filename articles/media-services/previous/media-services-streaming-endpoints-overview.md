---
title: Descrição geral de suporte de dados de ponto de final de transmissão em fluxo ao serviços do Azure | Documentos da Microsoft
description: Este tópico apresenta uma visão geral dos serviços de multimédia do Azure, os pontos finais de transmissão em fluxo.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c5979fa7ff67c5acda9ab653bc4ee52d8b5129a5
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293809"
---
# <a name="streaming-endpoints-overview"></a>Descrição geral de pontos finais de transmissão em fluxo  

## <a name="overview"></a>Descrição geral

No Microsoft Azure Media Services (AMS), um **ponto final de transmissão em fluxo** representa um serviço de transmissão em fluxo que pode entregar conteúdo diretamente a uma aplicação de leitor cliente ou para uma rede de entrega de conteúdos (CDN) para uma maior distribuição. Serviços de multimédia também fornecem uma integração perfeita do CDN do Azure. O fluxo de saída de um serviço de StreamingEndpoint pode ser uma transmissão em direto, um vídeo sob demanda ou transferência progressiva de seus recursos na sua conta de Media Services. Cada conta de Media Services do Azure inclui um padrão StreamingEndpoint. Pontos finais adicionais podem ser criadas sob a conta. Existem duas versões de pontos finais, 1.0 e 2.0. A partir de 10 de Janeiro de 2017, qualquer acabada de criar contas do AMS irão incluir versão 2.0 **predefinição** StreamingEndpoint. Transmissão em fluxo pontos finais adicionais que adicionar a esta conta também será a versão 2.0. Esta alteração não afetará as contas existentes; pontos finais existentes serão a versão 1.0 e podem ser atualizados para a versão 2.0. Com esta alteração vão ocorrer alterações de comportamento, a faturação e a funcionalidade (para obter mais informações, consulte a **transmissão em fluxo de tipos e versões** secção documentados abaixo).

Serviços de multimédia do Azure adicionadas as seguintes propriedades para a entidade de ponto final de transmissão em fluxo: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Para uma visão geral detalhada destas propriedades, consulte [isso](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Quando cria uma conta de Media Services do Azure um padrão de ponto final de transmissão em fluxo standard é criado por si a **parado** estado. Não é possível eliminar o ponto final de transmissão em fluxo predefinido. Dependendo da disponibilidade da CDN do Azure na região de destino, por predefinição de predefinido criado recentemente ponto final de transmissão em fluxo também inclui "StandardVerizon" CDN integração de fornecedor. 
                
> [!NOTE]
> Integração da CDN do Azure pode ser desativada antes de iniciar o ponto final de transmissão em fluxo. O `hostname` e o URL de transmissão em fluxo permanece o mesmo se ou não ativar a CDN.

Este tópico apresenta uma visão geral das principais funcionalidades que são fornecidos por pontos finais de transmissão em fluxo.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Para o ponto final predefinido: `{AccountName}.streaming.mediaservices.windows.net`

Para pontos finais adicionais: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Tipos de transmissão em fluxo e versões

### <a name="standardpremium-types-version-20"></a>Tipos de Standard/Premium (versão 2.0)

A partir da versão de Janeiro de 2017 dos serviços de multimédia, tem dois tipos de transmissão em fluxo: **Standard** e **Premium**. Esses tipos são parte da versão de ponto final de transmissão em fluxo "2.0".

Type|Descrição
---|---
**Standard** |Esta é a opção predefinida que funcionaria para a maioria dos cenários.<br/>Com esta opção, obtém o SLA/limitado, primeiros 15 dias depois de iniciar o ponto final de transmissão em fluxo é gratuito.<br/>Se criar mais de uma transmissão em fluxo de pontos de extremidade, apenas a primeira é gratuito durante os primeiros 15 dias, os outros são faturados assim que são iniciados. <br/>Tenha em atenção que versão de avaliação gratuita só se aplica às contas de serviços de suporte de dados recém-criado e ponto final de transmissão em fluxo predefinido. Pontos finais de transmissão em fluxo existentes e os pontos finais de transmissão em fluxo criados além disso, não inclui período de avaliação gratuita, mesmo que sejam atualizados para a versão 2.0 ou são criados como a versão 2.0.
**Premium** |Esta opção é adequada para cenários de profissionais que necessitam de maior escala ou controle.<br/>SLA de variável com base na capacidade premium transmissão em fluxo (SU) de unidades adquirida, dedicados pontos finais de transmissão em fluxo em direto em ambiente isolado e não compitam por recursos.

Para obter mais informações, consulte a **tipos de comparar a transmissão em fluxo** secção seguinte.

### <a name="classic-type-version-10"></a>Tipo clássico (versão 1.0)

Para os utilizadores que criou as contas do AMS antes do lançamento de 10 de Janeiro de 2017, tem um **clássico** tipo de um ponto de final de transmissão em fluxo. Este tipo faz parte da versão de ponto final de transmissão em fluxo "1.0".

Se sua **versão "1.0"** ponto final de transmissão em fluxo tem > = 1 premium unidades (SU), transmissão em fluxo que será o ponto final de transmissão em fluxo premium e fornecerá todas as funcionalidades do AMS (tal como o **Standard/Premium** tipo) sem quaisquer passos de configuração adicionais.

>[!NOTE]
>**Clássico** pontos finais de transmissão em fluxo (versão "1.0" e 0 SU), fornece recursos limitados e não inclui um SLA. Recomenda-se para migrar para o **padrão** tipo para obter uma melhor experiência e utilizar funcionalidades como o empacotamento dinâmico ou encriptação e outros recursos que vêm com o **padrão** tipo. Para migrar para o **padrão** tipo, vá para o [portal do Azure](https://portal.azure.com/) e selecione **participação ativa no padrão**. Para obter mais informações sobre a migração, consulte a [migração](#migration-between-types) secção.
>
>Lembre-se de que esta operação não pode ser revertida e afeta o preço.
>
 
## <a name="comparing-streaming-types"></a>Comparar tipos de transmissão em fluxo

### <a name="versions"></a>Versões

|Type|StreamingEndpointVersion|ScaleUnits|CDN|Faturação|SLA| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|Clássica|1.0|0|ND|Gratuito|ND|
|Ponto Final de Transmissão em Fluxo Standard|2.0|0|Sim|Pago|Sim|
|Unidades de Transmissão em Fluxo Premium|1.0|>0|Sim|Pago|Sim|
|Unidades de Transmissão em Fluxo Premium|2.0|>0|Sim|Pago|Sim|

### <a name="features"></a>Funcionalidades

Funcionalidade|Standard|Premium
---|---|---
Gratuitos primeiros 15 dias| Sim |Não
Débito |Até 600 Mbps quando não for utilizada o CDN do Azure. Dimensiona com CDN.|200 Mbps por unidade (SU) de transmissão em fluxo. Dimensiona com CDN.
SLA | 99.9|99,9 (200 Mbps por SU).
CDN|A CDN do Azure, terceiros CDN ou não CDN.|A CDN do Azure, terceiros CDN ou não CDN.
A faturação é calculada| Diariamente|Diariamente
Encriptação dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Automática se aumentar verticalmente para a taxa de transferência de destino.|Unidades de transmissão em fluxo adicionais
Anfitrião de filtragem/G20/personalizado IP|Sim|Sim
Transferência progressiva|Sim|Sim
Uso recomendado |Recomendado para a grande maioria dos cenários de transmissão em fluxo.|Utilização profissional.<br/>Se acha que podem ter necessidades além padrão. Contacte-nos (amsstreaming@microsoft.com) se espera que um tamanho de audiência em simultâneo maior do que 50.000 visualizadores.


## <a name="migration-between-types"></a>Migração entre tipos

A partir de | Para | Ação
---|---|---
Clássica|Standard|Tem de participar
Clássica|Premium| Dimensionamento (unidades de transmissão em fluxo adicional)
Standard/Premium|Clássica|Não está disponível (se a versão do ponto final de transmissão em fluxo é 1.0. Ele tem permissão para alterar para clássica com a definição scaleunits como "0")
Standard (com/sem CDN)|Premium com as mesmas configurações|Permitido no **iniciado** estado. (através do portal do Azure)
Premium (com/sem CDN)|Standard com as mesmas configurações|Permitido no **iniciado** Estado (através do portal do Azure)
Standard (com/sem CDN)|Premium com configuração diferente|Permitido no **parado** Estado (através do portal do Azure). Não é permitida no Estado em execução.
Premium (com/sem CDN)|Standard com configuração diferente|Permitido no **parado** Estado (através do portal do Azure). Não é permitida no Estado em execução.
A versão 1.0 com SU > = 1 com a CDN|Standard/Premium não CDN|Permitido no **parado** estado. Não é permitida no **iniciado** estado.
A versão 1.0 com SU > = 1 com a CDN|Standard com/sem CDN|Permitido no **parado** estado. Não é permitida no **iniciado** estado. Versão 1.0 CDN vai ser eliminados e nova criada e iniciada.
A versão 1.0 com SU > = 1 com a CDN|Premium com/sem CDN|Permitido no **parado** estado. Não é permitida no **iniciado** estado. CDN clássica irá ser eliminado e nova criada e iniciada.

## <a name="next-steps"></a>Passos Seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

