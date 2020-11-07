---
title: Acerca do Azure Kinect DK
description: Visão geral das ferramentas e serviços integrados do kit de desenvolvimento Azure Kinect (DK).
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: overview
ms.date: 06/26/2019
keywords: azul, kinect, visão geral, dev kit, DK, dispositivo, profundidade, body tracking, fala, serviços cognitivos, SDKs, SDK, firmware
ms.openlocfilehash: d188d37c0247aebb16f51b4404da81d469136468
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359658"
---
# <a name="about-azure-kinect-dk"></a>Acerca do Azure Kinect DK

 ![Azure Kinect DK](./media/index/device-image.jpg)

O Azure Kinect DK é um kit de programador com sensores de IA avançados que fornece modelos de voz e de imagem digitalizada sofisticados.  O Kinect contém um sensor de profundidade, uma matriz de microfones espacial com uma câmara de vídeo e um sensor de orientação sob a forma de um pequeno dispositivo tudo em um com vários modos, opções e Software Development Kits (SDKs). Está disponível para compra na [loja online da Microsoft.](https://www.microsoft.com/p/azure-kinect-dk/8pp5vxmd9nhq)

O ambiente de desenvolvimento do Azure Kinect DK consiste nos seguintes múltiplos SDKs:

- Sensor SDK para sensor de baixo nível e acesso ao dispositivo.
- SDK de rastreio de corpos de rastreamento em 3D.
- Serviços Cognitivos de Fala SDK para permitir o acesso ao microfone e serviços de fala baseados na nuvem Azure.

Além disso, os serviços de Visão Cognitiva podem ser utilizados com a câmara RGB do dispositivo.

   ![Diagrama Azure Kinect SDKs](./media/quickstarts/sdk-diagram.jpg)

## <a name="azure-kinect-sensor-sdk"></a>Azure Kinect Sensor SDK

O Azure Kinect Sensor SDK fornece acesso a sensores de baixo nível para sensores de hardware DK Azure Kinect e configuração do dispositivo.

Para saber mais sobre o Sensor Kinect Azure SDK, consulte [utilizar o Sensor SDK](about-sensor-sdk.md).

### <a name="azure-kinect-sensor-sdk-features"></a>Funcionalidades SDK do sensor Azure Kinect

O Sensor SDK tem as seguintes funcionalidades que funcionam uma vez instaladas e executadas no Azure Kinect DK:

- Profundidade do acesso à câmara e do controlo do modo (um modo IR passivo, mais modos de profundidade de campo de visão largo e estreito) 
- Acesso e controlo da câmara RGB (por exemplo, exposição e equilíbrio branco) 
- Acesso ao sensor de movimento (giroscópio e acelerómetro) 
- Streaming de câmara sincronizada Depth-RGB com atraso configurável entre câmaras 
- Controlo de sincronização de dispositivos externos com compensação de atraso configurável entre dispositivos 
- Acesso de meta-dados de quadro de câmara para resolução de imagem, hora de fixação, etc. 
- Acesso de dados de calibração do dispositivo 

### <a name="azure-kinect-sensor-sdk-tools"></a>Ferramentas SDK sensor kinect Azure

As seguintes ferramentas estão disponíveis no Sensor SDK:

- Uma ferramenta de visualização para monitorizar os fluxos de dados do dispositivo e configurar diferentes modos.
- Uma ferramenta de gravação de sensores e leitor de reprodução API que utiliza o formato do recipiente Matroska.
- Uma ferramenta de atualização de firmware Azure Kinect DK.

## <a name="azure-kinect-body-tracking-sdk"></a>Azure Kinect Body Tracking SDK

O Body Tracking SDK inclui uma biblioteca windows e tempo de execução para rastrear os corpos em 3D quando usado com o hardware DK Azure Kinect.

### <a name="azure-kinect-body-tracking-features"></a>Características de rastreio do corpo de Azure Kinect

As seguintes funcionalidades de rastreio corporal estão disponíveis no SDK que o acompanha:

- Fornece segmentação corporal.
- Contém um esqueleto anatomicamente correto para cada corpo parcial ou completo em FOV.
- Oferece uma identidade única para cada corpo.
- Pode rastrear corpos ao longo do tempo.

### <a name="azure-kinect-body-tracking-tools"></a>Ferramentas de rastreio do corpo de Azure Kinect

- Body Tracker tem uma ferramenta de espectador para rastrear corpos em 3D.

## <a name="speech-cognitive-services-sdk"></a>Serviços cognitivos da fala SDK

O Speech SDK permite serviços de fala ligados ao Azure.

### <a name="speech-services"></a>Serviços de voz

- Conversão de voz em texto
- Tradução de voz
- Conversão de Texto em Voz

>[!NOTE]
>O Azure Kinect DK não tem altifalantes.

Para mais detalhes e informações, visite [a documentação do Serviço de Discurso.](../cognitive-services/speech-service/index.yml)

## <a name="vision-services"></a>Serviços de visão

Os [seguintes Serviços de Visão Cognitiva Azure](https://azure.microsoft.com/services/cognitive-services/directory/vision/) fornecem serviços Azure que podem identificar e analisar conteúdos dentro de imagens e vídeos.

- [Visão computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
- [Face](https://azure.microsoft.com/services/cognitive-services/face/)
- [Indexador de vídeo](https://azure.microsoft.com/services/media-services/video-indexer/)
- [Moderador de conteúdo](https://azure.microsoft.com/services/cognitive-services/content-moderator/)
- [Visão personalizada](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)

Os serviços evoluem e melhoram constantemente, por isso lembre-se de verificar regularmente se há novos ou adicionais [serviços cognitivos](https://azure.microsoft.com/services/cognitive-services/) para melhorar a sua aplicação. Para ver mais cedo novos serviços emergentes, consulte os laboratórios de [serviços cognitivos.](https://labs.cognitive.microsoft.com/)

## <a name="azure-kinect-hardware-requirements"></a>Requisitos de hardware Azure Kinect

O Azure Kinect DK integra a mais recente tecnologia de sensores da Microsoft num único acessório USB ligado. Para mais informações, consulte a especificação de [hardware DK da Azure Kinect](hardware-specification.md).

## <a name="next-steps"></a>Passos seguintes

Agora tem uma visão geral de Azure Kinect DK. O próximo passo é mergulhar e preparar!

> [!div class="nextstepaction"]
>[Quickstart: Configurar Azure Kinect DK](set-up-azure-kinect-dk.md)