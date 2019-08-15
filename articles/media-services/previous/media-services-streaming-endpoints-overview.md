---
title: Visão geral do ponto de extremidade de streaming do Azure Media Services | Microsoft Docs
description: Este tópico fornece uma visão geral dos pontos de extremidade de streaming dos serviços de mídia do Azure.
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
ms.openlocfilehash: ac9c9a73e52c678c8a6d9b1e1779d9ec75cab2c8
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "69016454"
---
# <a name="streaming-endpoints-overview"></a>Visão geral dos pontos de extremidade de streaming  

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

No Serviços de Mídia do Microsoft Azure (AMS), um **ponto de extremidade de streaming** representa um serviço de streaming que pode entregar conteúdo diretamente a um aplicativo de player de cliente ou a uma CDN (rede de distribuição de conteúdo) para distribuição posterior. Os serviços de mídia também fornecem integração direta da CDN do Azure. O fluxo de saída de um serviço StreamingEndpoint pode ser uma transmissão ao vivo, um vídeo sob demanda ou um download progressivo de seu ativo na sua conta de serviços de mídia. Cada conta dos serviços de mídia do Azure inclui um StreamingEndpoint padrão. StreamingEndpoints adicionais podem ser criados na conta. Há duas versões de StreamingEndpoints, 1,0 e 2,0. A partir de 10 de janeiro de 2017, todas as contas AMS recém-criadas incluirão a versão 2,0 **padrão** StreamingEndpoint. Os pontos de extremidade de streaming adicionais que você adicionar a essa conta também serão da versão 2,0. Essa alteração não afetará as contas existentes; os StreamingEndpoints existentes serão da versão 1,0 e poderão ser atualizados para a versão 2,0. Com essa alteração, haverá alterações de comportamento, cobrança e recursos (para obter mais informações, consulte a seção **tipos e versões de streaming** documentadas abaixo).

Os serviços de mídia do Azure adicionaram as seguintes propriedades à entidade de ponto de extremidade de streaming: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Para obter uma visão geral detalhada dessas propriedades, consulte [isso](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Quando você cria uma conta dos serviços de mídia do Azure, um ponto de extremidade de streaming padrão é criado para você no estado **parado** . Não é possível excluir o ponto de extremidade de streaming padrão. Dependendo da disponibilidade da CDN do Azure na região de destino, por padrão, o ponto de extremidade de streaming padrão recém-criado também inclui a integração do provedor de CDN "StandardVerizon". 
                
> [!NOTE]
> A integração da CDN do Azure pode ser desabilitada antes de iniciar o ponto de extremidade de streaming. O `hostname` e a URL de streaming permanecem os mesmos, independentemente de você habilitar ou não a CDN.

Este tópico fornece uma visão geral das principais funcionalidades fornecidas por pontos de extremidade de streaming.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Para o ponto de extremidade padrão:`{AccountName}.streaming.mediaservices.windows.net`

Para quaisquer pontos de extremidade adicionais:`{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Tipos e versões de streaming

### <a name="standardpremium-types-version-20"></a>Tipos Standard/Premium (versão 2,0)

A partir da versão de janeiro de 2017 dos serviços de mídia, você tem dois tipos de streaming: **Padrão** (visualização) e **Premium**. Esses tipos fazem parte da versão do ponto de extremidade de streaming "2,0".


|Type|Descrição|
|--------|--------|  
|**Standard**|O ponto de extremidade de streaming padrão é um tipo **padrão** , pode ser alterado para o tipo Premium, ajustando as unidades de streaming.|
|**Premium** |Essa opção é adequada para cenários profissionais que exigem maior escala ou controle. Você passa para um tipo **Premium** ajustando unidades de streaming.<br/>Os pontos de extremidade de streaming dedicados residem em um ambiente isolado e não conpetem por recursos.|

Para clientes que procuram fornecer conteúdo a grandes públicos da Internet, recomendamos que você habilite a CDN no ponto de extremidade de streaming.

Para obter informações mais detalhadas, consulte a seção [comparar tipos de streaming](#comparing-streaming-types) a seguir.

### <a name="classic-type-version-10"></a>Tipo clássico (versão 1,0)

Para usuários que criaram contas do AMS antes da versão de janeiro de 10 2017, você tem um tipo **clássico** de ponto de extremidade de streaming. Esse tipo faz parte da versão do ponto de extremidade de streaming "1,0".

Se o ponto de extremidade de streaming da **versão "1,0"** tiver > = 1 Su (unidades de streaming Premium), ele será um ponto de extremidade de streaming Premium e fornecerá todos os recursos do AMS (assim como o tipo **Standard/Premium** ) sem nenhuma etapa de configuração adicional.

>[!NOTE]
>Os pontos de extremidade de streaming **clássicos** (versão "1,0" e 0 Su) fornecem recursos limitados e não incluem um SLA. É recomendável migrar para o tipo **padrão** para obter uma experiência melhor e usar recursos como empacotamento dinâmico ou criptografia e outros recursos que acompanham o tipo **padrão** . Para migrar para o tipo **padrão** , vá para o [portal do Azure](https://portal.azure.com/) e selecione **aceitar para Standard**. Para obter mais informações sobre migração, consulte a seção [migração](#migration-between-types) .
>
>Lembre-se de que essa operação não pode ser revertida e tem um impacto no preço.
>
 
## <a name="comparing-streaming-types"></a>Comparando tipos de streaming

### <a name="versions"></a>Versões

|Type|StreamingEndpointVersion|ScaleUnits|CDN|Faturação|
|--------------|----------|-----------------|-----------------|-----------------|
|Clássica|1.0|0|ND|Livre|
|Ponto de extremidade de streaming padrão (visualização)|2.0|0|Sim|Pago|
|Unidades de Transmissão em Fluxo Premium|1.0|>0|Sim|Pago|
|Unidades de Transmissão em Fluxo Premium|2.0|>0|Sim|Pago|

### <a name="features"></a>Funcionalidades

Funcionalidade|Standard|Premium
---|---|---
Débito |Até 600 Mbps e pode fornecer uma taxa de transferência muito mais eficiente quando uma CDN é usada.|200 Mbps por SU (unidade de streaming). Pode fornecer uma taxa de transferência muito mais eficiente quando uma CDN é usada.
CDN|CDN do Azure, CDN de terceiros ou nenhuma CDN.|CDN do Azure, CDN de terceiros ou nenhuma CDN.
A cobrança é rateada| Diariamente|Diariamente
Encriptação dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Escala verticalmente para a taxa de transferência de destino.|Unidades de streaming adicionais.
Filtragem de IP/G20/host personalizado <sup>1</sup>|Sim|Sim
Download progressivo|Sim|Sim
Uso recomendado |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional. 

<sup>1</sup> é usado somente diretamente no ponto de extremidade de streaming quando a CDN não está habilitada no ponto de extremidade.<br/>

Para obter informações de SLA, consulte [preços e SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migração entre tipos

De | Para | Action
---|---|---
Clássica|Standard|Precisa aceitar
Clássica|Premium| Escala (unidades de streaming adicionais)
Standard/Premium|Clássica|Não disponível (se a versão do ponto de extremidade de streaming for 1,0. É permitido alterar para clássico com a configuração de ScaleUnits como "0")
Standard (com/sem CDN)|Premium com as mesmas configurações|Permitido no estado **iniciado** . (via portal do Azure)
Premium (com/sem CDN)|Standard com as mesmas configurações|Permitido no estado **iniciado** (via portal do Azure)
Standard (com/sem CDN)|Premium com configuração diferente|Permitido no estado **parado** (via portal do Azure). Não permitido no estado de execução.
Premium (com/sem CDN)|Padrão com configuração diferente|Permitido no estado **parado** (via portal do Azure). Não permitido no estado de execução.
Versão 1,0 com SU > = 1 com CDN|Standard/Premium sem CDN|Permitido no estado **parado** . Não permitido no estado **iniciado** .
Versão 1,0 com SU > = 1 com CDN|Standard com/sem CDN|Permitido no estado **parado** . Não permitido no estado **iniciado** . A CDN da versão 1,0 será excluída e nova criada e iniciada.
Versão 1,0 com SU > = 1 com CDN|Premium com/sem CDN|Permitido no estado **parado** . Não permitido no estado **iniciado** . A CDN clássica será excluída e nova criada e iniciada.

## <a name="next-steps"></a>Passos Seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

