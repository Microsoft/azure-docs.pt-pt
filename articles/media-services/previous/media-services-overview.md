---
title: Descrição geral dos Serviços de Multimédia do Azure | Microsoft Docs
description: Os Media Services do Microsoft Azure são uma plataforma baseado na nuvem extensível que permite aos programadores compilar uma gestão de multimédia dimensionável e aplicações de entrega. Este artigo apresenta uma visão geral da Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6e68e53387aa50b99bab8ed4cdba7f1e97fc48c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008242"
---
# <a name="azure-media-services-overview"></a>Descrição geral dos Serviços de Multimédia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecione a versão dos Serviços de Media que está a utilizar:"]
> * [Versão 3](../latest/media-services-overview.md)
> * [Versão 2](media-services-overview.md)

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Os Serviços de Multimédia do Microsoft Azure (AMS) são uma plataforma baseado na nuvem extensível que permite aos programadores compilar uma gestão de multimédia dimensionável e aplicações de entrega. Os Serviços de Multimédia são baseados nas APIs REST, que permitem carregar, armazenar, codificar e empacotar de forma segura conteúdos de vídeo ou áudio, para o envio de transmissão em fluxo, tanto a pedido como em direto, para vários clientes (por exemplo, TV, PC e dispositivos móveis).

Pode compilar fluxos de trabalho ponto-a-ponto utilizando inteiramente os Media Services. Pode também optar por utilizar componentes de terceiros para algumas partes do seu fluxo de trabalho. Por exemplo, codificar utilizando um codificador de terceiros. Em seguida, carregue, proteja, empacote e entregue através dos Media Services. Pode também optar por transmitir os seus conteúdos em direto ou enviar conteúdos a pedido. 


## <a name="compliance-privacy-and-security"></a>Conformidade, Privacidade e Segurança

Como um importante lembrete, deve cumprir todas as leis aplicáveis na sua utilização da Azure Media Services, e não poderá utilizar os Serviços de Comunicação Social ou qualquer serviço Azure de forma a violar os direitos de outros, ou que possam ser prejudiciais para os outros.

Antes de enviar qualquer vídeo/imagem para os Serviços de Comunicação, deve ter todos os direitos adequados de utilização do vídeo/imagem, incluindo, sempre que tal seja exigido por lei, todos os consentimentos necessários de indivíduos (se houver) no vídeo/imagem, para a utilização, processamento e armazenamento dos seus dados nos Serviços de Comunicação Social e Azure. Algumas jurisdições podem impor requisitos legais especiais para a recolha, tratamento e armazenamento online de determinadas categorias de dados, tais como dados biométricos. Antes de utilizar os Media Services e a Azure para o processamento e armazenamento de quaisquer dados sujeitos a requisitos legais especiais, deve garantir o cumprimento de quaisquer requisitos legais que possam ser aplicáveis a Si.

Para saber mais sobre a conformidade, privacidade e segurança nos Serviços de Media, visite o Microsoft [Trust Center.](https://www.microsoft.com/trust-center/?rtc=1) Para as obrigações de privacidade da Microsoft, práticas de tratamento e retenção de dados, incluindo como eliminar os seus dados, por favor reveja a Declaração de [Privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, [os Termos dos Serviços Online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") e a [adenda de processamento de dados](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Ao utilizar os Serviços de Comunicação Social, concorda em ficar vinculado pela OST, DPA e pela Declaração de Privacidade.
 
## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar os Media Services do Azure, deve ter o seguinte:

* Uma conta do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com).
* Uma conta de Media Services do Azure Para obter mais informações, consulte [Criar Conta](media-services-portal-create-account.md).
* (Opcional) Configurar o ambiente de desenvolvimento. Escolha .NET ou a API REST para o ambiente de desenvolvimento. Para obter mais informações, consulte [Configurar o ambiente](media-services-dotnet-how-to-use.md).

    Além disso, saiba como [estabelecer ligação através de programação à API AMS](media-services-use-aad-auth-to-access-ams-api.md).
* Um ponto final de transmissão em fluxo standard ou premium no estado iniciado.  Para obter mais informações, consulte [Gerir os pontos finais de streaming](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>SDKs e ferramentas

Para compilar soluções de Media Services, pode utilizar:

* [API REST dos Serviços de Multimédia](/rest/api/media/operations/azure-media-services-rest-api-reference)
* Um dos SDKs do Cliente disponíveis:
    * Azure Media Services SDK para .NET
    
        * [Pacote NuGet](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [Código fonte GitHub](https://github.com/Azure/azure-sdk-for-media-services)
    * [Azure SDK para Java,](https://github.com/Azure/azure-sdk-for-java)
    * [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php),
    * [Media Services do Azure para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Esta é uma versão não Microsoft de um SDK Node.js. Esta é mantida por uma comunidade e não tem atualmente uma cobertura de 100% das APIs do AMS).
* Ferramentas existentes:
    * [Portal do Azure](https://portal.azure.com/)
    * [Explorador de Media Services do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE – Azure Media Services Explorer, uma aplicação Winforms/C# para Windows)

> [!NOTE]
> Para obter a versão mais recente do Java SDK e começar a programar com o Java, veja [Introdução ao SDK do cliente de Java dos Serviços de Multimédia](./media-services-java-how-to-use.md). <br/>
> Para transferir o SDK mais recente do PHP para os Serviços de Multimédia, procure a versão 0.5.7 do pacote Microsoft/WindowAzure no [repositório Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Exemplos de código

Localize vários exemplos de código na galeria **Exemplos de Código do Azure**: [exemplos de código dos Serviços de Multimédia do Azure](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Conceitos

Para saber mais sobre os conceitos de Media Services do Azure, consulte [Conceitos](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Cenários suportados e disponibilidade dos Serviços de Multimédia em datacenters

Para obter mais informações sobre cenários comuns do Azure, consulte [os cenários da AMS](scenarios-and-availability.md).
Para obter informações sobre a disponibilidade regional, consulte [a disponibilidade do serviço de mídia.](availability-regions-v-2.md)

## <a name="service-level-agreement-sla"></a>Contrato de Nível de Serviço (SLA)

Para obter mais informações, consulte [SLA do Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

## <a name="support"></a>Suporte

O [Suporte do Azure](https://azure.microsoft.com/support/options/) fornece opções de suporte para o Azure, incluindo os Media Services.

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
