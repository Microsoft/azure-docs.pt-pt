---
title: Visão geral do Ponto final do Azure Media Services Microsoft Docs
description: Este artigo dá uma visão geral dos pontos finais de streaming da Azure Media Services.
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
ms.openlocfilehash: 95d8d819aa1b418b4a7ec736cef64cb989f7e37b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74885641"
---
# <a name="streaming-endpoints-overview"></a>Visão geral dos pontos finais de streaming  

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

No Microsoft Azure Media Services (AMS), um **Streaming Endpoint** representa um serviço de streaming que pode entregar conteúdo diretamente a uma aplicação de jogador de clientes, ou a uma Rede de Entrega de Conteúdos (CDN) para posterior distribuição. A Media Services também fornece uma integração cdn azure perfeita. O fluxo de saída de um serviço StreamingEndpoint pode ser um live stream, um vídeo a pedido ou transferência progressiva do seu ativo na sua conta de Media Services. Cada conta azure Media Services inclui um StreamingEndpoint padrão. Os pontos De StreamingEnd adicionais podem ser criados sob a conta. Existem duas versões de StreamingEndpoints, 1.0 e 2.0. A partir de 10 de janeiro de 2017, quaisquer contas AMS recém-criadas incluirão a versão 2.0 **padrão** StreamingEndpoint. Os pontos finais de streaming adicionais que adicionar a esta conta também serão a versão 2.0. Esta alteração não terá impacto nas contas existentes; os streamingendpoints existentes serão a versão 1.0 e podem ser atualizados para a versão 2.0. Com esta alteração haverá mudanças de comportamento, faturação e funcionalidade (para mais informações, consulte os tipos de Streaming e a secção de **versões** documentada abaixo).

A Azure Media Services adicionou as seguintes propriedades à entidade streaming Endpoint: **CdnProvider,** **CdnProfile,** **StreamingEndpointVersion**. Para uma visão detalhada destas propriedades, consulte [isto.](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint) 

When you create an Azure Media Services account a default standard streaming endpoint is created for you in the **Stopped** state. Não é possível eliminar o ponto final de transmissão predefinido. Dependendo da disponibilidade do Azure CDN na região alvo, por padrão, o ponto final de streaming de padrão recém-criado inclui também a integração do fornecedor CDN "StandardVerizon". 
                
> [!NOTE]
> A integração do CDN Azure pode ser desativada antes de iniciar o ponto final de streaming. O `hostname` URL de streaming e o URL de streaming permanecem os mesmos, quer asua ativação ou não da CDN.

Este tópico apresenta uma visão geral das principais funcionalidades que são fornecidas através do streaming de pontos finais.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Para o ponto final predefinido:`{AccountName}.streaming.mediaservices.windows.net`

Para quaisquer pontos finais adicionais:`{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Tipos e versões de streaming

### <a name="standardpremium-types-version-20"></a>Tipos standard/Premium (versão 2.0)

A partir do lançamento de janeiro de 2017 dos Media Services, tem dois tipos de streaming: **Standard** (pré-visualização) e **Premium**. Estes tipos fazem parte da versão final do streaming "2.0".


|Tipo|Descrição|
|--------|--------|  
|**Standard**|O ponto final de streaming predefinido é um tipo **Standard,** pode ser alterado para o tipo Premium ajustando as unidades de streaming.|
|**Premium** |Esta opção é adequada para cenários profissionais que requerem maior escala ou controlo. Muda-se para um tipo **Premium** ajustando unidades de streaming.<br/>Os Pontos Finais de Streaming dedicados vivem em ambiente isolado e não competem por recursos.|

Para clientes que procuram entregar conteúdo a grandes audiências da Internet, recomendamos que ative a CDN no Streaming Endpoint.

Para obter informações mais detalhadas, consulte os tipos de [streaming compare seguintes.](#comparing-streaming-types)

### <a name="classic-type-version-10"></a>Tipo clássico (versão 1.0)

Para os utilizadores que criaram contas AMS antes do lançamento de 10 de janeiro de 2017, você tem um tipo **clássico** de um ponto final de streaming. Este tipo faz parte da versão final de streaming "1.0".

Se a sua **versão "1.0"** ponto final de streaming tiver >=1 unidades de streaming premium (SU), será um ponto final de streaming premium e fornecerá todas as funcionalidades AMS (tal como o tipo **Standard/Premium)** sem quaisquer passos de configuração adicionais.

>[!NOTE]
>**Os** pontos finais clássicos de streaming (versão "1.0" e 0 SU), fornecem funcionalidades limitadas e não incluem um SLA. Recomenda-se migrar para o tipo **Standard** para obter uma melhor experiência e utilizar funcionalidades como embalagem dinâmica ou encriptação e outras funcionalidades que vêm com o tipo **Standard.** Para migrar para o tipo **Standard,** vá ao [portal Azure](https://portal.azure.com/) e selecione **Opt-in para Standard**. Para obter mais informações sobre migração, consulte a secção [de migração.](#migration-between-types)
>
>Tenha cuidado com o facto de esta operação não poder ser relançada e ter um impacto nos preços.
>
 
## <a name="comparing-streaming-types"></a>Comparar tipos de streaming

### <a name="versions"></a>Versões

|Tipo|Versão streamingEndpoint|Unidades de Escala|CDN|Faturação|
|--------------|----------|-----------------|-----------------|-----------------|
|Clássica|1.0|0|ND|Gratuito|
|Ponto final de streaming padrão (pré-visualização)|2.0|0|Sim|Pago|
|Unidades de Streaming Premium|1.0|>0|Sim|Pago|
|Unidades de Streaming Premium|2.0|>0|Sim|Pago|

### <a name="features"></a>Funcionalidades

Funcionalidade|Standard|Premium
---|---|---
Débito |Até 600 Mbps e pode fornecer uma entrada muito mais eficaz quando um CDN é usado.|200 Mbps por unidade de streaming (SU). Pode fornecer uma entrada muito mais eficaz quando um CDN é usado.
CDN|Azure CDN, CDN de terceiros, ou sem CDN.|Azure CDN, CDN de terceiros, ou sem CDN.
A faturação é proclamada| Diárias|Diárias
Encriptação dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Automaticamente escala até a entrada direcionada.|Unidades de streaming adicionais.
Filtragem IP/G20/Hospedeiro personalizado <sup>1</sup>|Sim|Sim
Download progressivo|Sim|Sim
Utilização recomendada |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional. 

<sup>1</sup> Apenas utilizado diretamente no ponto final de streaming quando o CDN não está ativado no ponto final.<br/>

Para obter informações sobre SLA, consulte [Preços e SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migração entre tipos

De | Para | Ação
---|---|---
Clássica|Standard|Precisa optar por entrar
Clássica|Premium| Escala (unidades de streaming adicionais)
Standard/Premium|Clássica|Não disponível (Se a versão final do streaming for 1.0. É permitido mudar para clássico com definição de unidades de escala para "0")
Standard (com/without CDN)|Premium com as mesmas configurações|Permitido no estado **de partida.** (via portal Azure)
Premium (com/sem CDN)|Standard com as mesmas configurações|Permitido no estado **iniciado** (via portal Azure)
Standard (com/without CDN)|Premium com config diferente|Permitido no estado **de paragem** (via portal Azure). Não é permitido no estado de corrida.
Premium (com/sem CDN)|Padrão com config diferente|Permitido no estado **de paragem** (via portal Azure). Não é permitido no estado de corrida.
Versão 1.0 com SU >= 1 com CDN|Standard/Premium sem CDN|Permitido no estado **de paragem.** Não é permitido no estado **de partida.**
Versão 1.0 com SU >= 1 com CDN|Standard com/sem CDN|Permitido no estado **de paragem.** Não é permitido no estado **de partida.** A versão 1.0 CDN será eliminada e a nova criada e iniciada.
Versão 1.0 com SU >= 1 com CDN|Premium com/sem CDN|Permitido no estado **de paragem.** Não é permitido no estado **de partida.** O CDN clássico será apagado e o novo criado e iniciado.

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

