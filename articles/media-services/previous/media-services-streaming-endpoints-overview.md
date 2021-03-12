---
title: Visão geral do ponto final dos serviços de comunicação social Azure | Microsoft Docs
description: Este artigo apresenta uma visão geral dos pontos finais de streaming da Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0961b52ebc7271fabf4cc05ed99eea23d911a2d4
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009115"
---
# <a name="streaming-endpoints-overview"></a>Visão geral dos pontos finais do streaming  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

No Microsoft Azure Media Services (AMS), um **Streaming Endpoint** representa um serviço de streaming que pode entregar conteúdo diretamente a uma aplicação de cliente, ou a uma Rede de Entrega de Conteúdos (CDN) para posterior distribuição. Os Media Services também proporcionam uma integração perfeita do Azure CDN. O stream de saída de um serviço StreamingEndpoint pode ser um stream ao vivo, um vídeo a pedido ou um download progressivo do seu ativo na sua conta de Media Services. Cada conta Azure Media Services inclui um StreamingEndpoint predefinido. Os StreamingEndpoints adicionais podem ser criados na conta. Existem duas versões de StreamingEndpoints, 1.0 e 2.0. A partir de 10 de janeiro de 2017, quaisquer contas AMS recém-criadas incluirão a versão 2.0 **default** StreamingEndpoint. Os pontos finais de streaming adicionais que adicionar a esta conta também serão a versão 2.0. Esta alteração não terá impacto nas contas existentes; Os StreamingEndpoints existentes serão a versão 1.0 e podem ser atualizados para a versão 2.0. Com esta alteração haverá mudanças de comportamento, faturação e funcionalidade (para mais informações, consulte os tipos de Streaming e a secção **de versões** documentadas abaixo).

A Azure Media Services adicionou as seguintes propriedades à entidade streaming Endpoint: **CdnProvider,** **CdnProfile,** **StreamingEndpointVersion**. Para uma visão geral detalhada destas propriedades, consulte [isto.](/rest/api/media/operations/streamingendpoint) 

Quando cria uma conta Azure Media Services é criado um ponto final de streaming padrão padrão para si no estado **Stop.** Não é possível eliminar o ponto final de streaming predefinido. Dependendo da disponibilidade do Azure CDN na região visada, por padrão o ponto final de streaming padrão recentemente criado também inclui a integração do fornecedor CDN "StandardVerizon". 
                
> [!NOTE]
> A integração do Azure CDN pode ser desativada antes de iniciar o ponto final de streaming. O `hostname` URL de streaming e o URL de streaming permanecem os mesmos quer ativem ou não a CDN.

Este tópico dá uma visão geral das principais funcionalidades que são fornecidas através do streaming de pontos finais.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Para o ponto final predefinido: `{AccountName}.streaming.mediaservices.windows.net`

Para quaisquer pontos finais adicionais: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Tipos e versões de streaming

### <a name="standardpremium-types-version-20"></a>Tipos Standard/Premium (versão 2.0)

A partir do lançamento de janeiro de 2017 dos Media Services, tem dois tipos de streaming: **Standard** (pré-visualização) e **Premium**. Estes tipos fazem parte da versão "2.0" do ponto final de streaming.


|Tipo|Description|
|--------|--------|  
|**Standard**|O ponto final de streaming predefinido é um tipo **Standard,** pode ser alterado para o tipo Premium ajustando as unidades de streaming.|
|**Premium** |Esta opção é adequada para cenários profissionais que requerem maior escala ou controlo. Muda-se para um tipo **Premium** ajustando unidades de streaming.<br/>Os Endpoints de streaming dedicados vivem em ambientes isolados e não competem por recursos.|

Para clientes que procuram entregar conteúdo a grandes audiências de internet, recomendamos que você ative a CDN no Streaming Endpoint.

Para obter informações mais detalhadas, consulte os [tipos de Streaming compare](#comparing-streaming-types) que se seguem.

### <a name="classic-type-version-10"></a>Tipo clássico (versão 1.0)

Para os utilizadores que criaram contas AMS antes do lançamento de 10 de janeiro de 2017, você tem um tipo **clássico** de um ponto final de streaming. Este tipo faz parte da versão "1.0" do ponto final de streaming.

Se a sua **versão "1.0"** streaming endpoint tiver >=1 unidades de streaming premium (SU), será o ponto final de streaming premium e fornecerá todas as funcionalidades AMS (tal como o tipo **Standard/Premium)** sem quaisquer etapas de configuração adicionais.

>[!NOTE]
>Os pontos finais **clássicos** de streaming (versão "1.0" e 0 SU), fornecem funcionalidades limitadas e não incluem um SLA. Recomenda-se migrar para o tipo **Standard** para obter uma melhor experiência e usar funcionalidades como embalagem dinâmica ou encriptação e outras funcionalidades que vêm com o tipo **Standard.** Para migrar para o tipo **Standard,** vá ao [portal Azure](https://portal.azure.com/) e selecione **Opt-in to Standard**. Para mais informações sobre migração, consulte a secção [de migração.](#migration-between-types)
>
>Tenha em atenção que esta operação não pode ser revertida e tem um impacto nos preços.
>
 
## <a name="comparing-streaming-types"></a>Comparar tipos de streaming

### <a name="versions"></a>Versões

|Tipo|StreamingEndpointVersion|Unidades de escala|CDN|Faturação|
|--------------|----------|-----------------|-----------------|-----------------|
|Clássico|1.0|0|ND|Gratuito|
|Ponto final de streaming padrão (pré-visualização)|2.0|0|Yes|Paga|
|Unidades de streaming premium|1.0|>0|Yes|Paga|
|Unidades de streaming premium|2.0|>0|Yes|Paga|

### <a name="features"></a>Funcionalidades

Funcionalidade|Standard|Premium
---|---|---
Débito |Até 600 Mbps e pode fornecer uma produção eficaz muito maior quando um CDN é usado.|200 Mbps por unidade de streaming (SU). Pode fornecer uma produção eficaz muito maior quando um CDN é usado.
CDN|Azure CDN, CDN de terceiros, ou sem CDN.|Azure CDN, CDN de terceiros, ou sem CDN.
A faturação é prostimada| Diário|Diário
Encriptação dinâmica|Yes|Yes
Empacotamento dinâmico|Yes|Yes
Escala|A escala automática sobe até à produção visada.|Unidades de streaming adicionais.
Filtragem IP/G20/Anfitrião personalizado <sup>1</sup>|Yes|Yes
Download progressivo|Yes|Yes
Uso recomendado |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional. 

<sup>1</sup> Apenas utilizado diretamente no ponto de ponta de streaming quando o CDN não estiver ativado no ponto final.<br/>

Para obter informações sobre SLA, consulte [preços e SLA.](https://azure.microsoft.com/pricing/details/media-services/)

## <a name="migration-between-types"></a>Migração entre tipos

De | Para | Ação
---|---|---
Clássica|Standard|Necessidade de optar
Clássico|Premium| Escala (unidades de streaming adicionais)
Standard/Premium|Clássico|Não disponível (Se a versão de ponto final de streaming for 1.0. É permitido mudar para clássico com escalas de definição para "0")
Padrão (com/sem CDN)|Premium com as mesmas configurações|Permitido no estado **iniciado.** (via portal Azure)
Premium (com/sem CDN)|Standard com as mesmas configurações|Permitido no estado **iniciado** (via portal Azure)
Padrão (com/sem CDN)|Premium com config diferente|Permitido no estado **parado** (via portal Azure). Não é permitido no estado de corrida.
Premium (com/sem CDN)|Padrão com config diferente|Permitido no estado **parado** (via portal Azure). Não é permitido no estado de corrida.
Versão 1.0 com SU >= 1 com CDN|Standard/Premium sem CDN|Permitido no estado **parado.** Não é permitido no estado **iniciado.**
Versão 1.0 com SU >= 1 com CDN|Padrão com/sem CDN|Permitido no estado **parado.** Não é permitido no estado **iniciado.** A versão 1.0 CDN será eliminada e nova criada e iniciada.
Versão 1.0 com SU >= 1 com CDN|Premium com/sem CDN|Permitido no estado **parado.** Não é permitido no estado **iniciado.** O CDN clássico será eliminado e o novo criado e iniciado.

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
