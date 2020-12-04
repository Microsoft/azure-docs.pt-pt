---
title: Ligue o modo de pré-visualização para O Azure IoT Hub
description: Aprenda a ligar o modo de pré-visualização para IoT Hub, por que você gostaria, e alguns avisos
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 5299a049ab2cc2246a861dd3fb81f466b56afad3
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603484"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>Ligue o modo de pré-visualização para ioT Hub para experimentar novas funcionalidades

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

As novas funcionalidades estão em pré-visualização pública para o IoT Hub, incluindo:

- MQTT 5
- Certificado de servidor ECC
- Negociação do comprimento do fragmento TLS
- Suporte em cadeia X509 CA para HTTPS/WebSocket

Estas funcionalidades são melhorias no protocolo IoT Hub e camadas de autenticação, pelo que só estão disponíveis para **novos** hubs IoT por enquanto. Ainda *não* estão disponíveis para os centros de IoT existentes. Para pré-visualizar estas funcionalidades, o hub IoT deve ser criado com o modo de pré-visualização ligado.

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>Ligue o modo de pré-visualização para um novo hub IoT

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir da página inicial do Azure, selecione o **botão + Criar um** botão de recurso e, em seguida, introduza o *Hub IoT* no campo **'Pesquisar o Mercado'.**

1. Selecione **IoT Hub** a partir dos resultados da pesquisa e, em seguida, selecione **Criar**.

1. No separador **Básicos,** complete os campos [como normalmente faria,](iot-hub-create-through-portal.md) exceto a **Região.** Selecione uma destas regiões:
    
    - E.U.A. Central
    - Europa Ocidental
    - Ásia Sudeste

1. Selecione o separador **Gestão** e, em seguida, expanda a secção **de definições Avançadas.**

1. Ao lado do **modo pré-visualização**, selecione **On**. Reveja cuidadosamente o texto de aviso.

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="Imagem mostrando onde selecionar a opção de modo de pré-visualização ao criar um novo hub IoT":::

1. Selecione **Seguinte: Rever + criar** e, em seguida, **criar**.

Uma vez criado, um IoT Hub no modo de pré-visualização mostra sempre este banner, informando-o de utilizar este hub IoT apenas para efeitos de pré-visualização: 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="Imagem mostrando banner para o modo de pré-visualização IoT hub":::

## <a name="using-an-iot-hub-in-preview-mode"></a>Usando um hub IoT no modo de pré-visualização

*Não* utilize um hub IoT no modo de pré-visualização para a produção. O modo de pré-visualização destina-se *apenas* a visualizar as funcionalidades selecionadas listadas no topo desta página. Algumas outras limitações ao modo de pré-visualização IoT Hub são

- Algumas funcionalidades existentes do IoT Hub, tais como ligação privada, identidade gerida, streams de dispositivos e falha de falha podem funcionar inesperadamente ou não.
- Um hub IoT no modo de pré-visualização não pode ser alterado ou atualizado para um hub IoT normal.
- Não podemos garantir o [IoT Hub SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) normal - não use para produção.

> [!TIP]
> O modo de pré-visualização não é necessário para [os fluxos de dispositivos](iot-hub-device-streams-overview.md) e [para o rastreio distribuído](iot-hub-distributed-tracing.md). Para utilizar estas funcionalidades de pré-visualização mais antigas, siga a sua documentação normalmente. 

## <a name="next-steps"></a>Próximos passos

- Para visualizar o suporte MQTT 5, consulte [a visão geral de suporte do IoT Hub MQTT 5 (pré-visualização)](iot-hub-mqtt-5.md)
- Para visualizar o certificado do servidor ECC, consulte o [certificado de Eptografia de Eptografia de Curva Elíptica (ECC) (pré-visualização)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)
- Para pré-visualizar a negociação do tamanho do fragmento de TLS, consulte a [negociação do comprimento máximo do fragmento TLS (pré-visualização)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview)