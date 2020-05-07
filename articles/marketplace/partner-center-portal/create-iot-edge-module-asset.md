---
title: Prepare os seus ativos técnicos do módulo IoT Edge - Azure Marketplace
description: Conheça os requisitos técnicos e de configuração que os seus ativos técnicos do módulo Internet of Things (IoT) Edge devem cumprir antes de os poder publicar no Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: fc35602b55e79f3351da0def800d2a2b2698e250
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856722"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Prepare os seus ativos técnicos do módulo IoT Edge

Este artigo descreve os requisitos que os seus ativos técnicos do módulo Internet of Things (IoT) Edge devem cumprir antes de serem publicados no Azure Marketplace.

## <a name="get-started"></a>Introdução

Um módulo IoT Edge é um recipiente compatível com o Docker que funciona num dispositivo IoT Edge.

- Para saber mais sobre os módulos IoT Edge, consulte os [módulos Understand Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Para começar com o desenvolvimento do módulo IoT Edge, consulte [Desenvolver os seus próprios módulos IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Requisitos técnicos

O módulo IoT Edge deve satisfazer os seguintes requisitos técnicos a certificar e publicar no Azure Marketplace.

### <a name="platform-support"></a>Suporte da plataforma

O módulo IoT Edge deve suportar uma das seguintes opções da plataforma:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Plataformas de nível 1 suportadas por IoT Edge

O seu módulo deve suportar todas as plataformas de Nível 1 suportadas por IoT Edge (conforme registado no [suporte Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Recomendamos esta opção porque proporciona uma melhor experiência ao cliente. Serão apresentados módulos que satisfaçam estes critérios. Um módulo que utilize esta opção de plataforma deve:

- Forneça uma etiqueta mais recente e uma etiqueta de versão (por exemplo, 1.0.1) que são marcas de manifesto construídas com a [ferramenta GitHub Manifesto](https://github.com/estesp/manifest-tool).

- Utilize o separador de listagem de ofertas no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) para adicionar um link sob a secção de **links Úteis** ao catálogo de [dispositivos Certificado si o Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Um subconjunto de plataformas de Nível 1 suportadas por IoT Edge

O seu módulo deve suportar um subconjunto (pelo menos um) das plataformas de Nível 1 suportadas por IoT Edge (conforme registado no [suporte Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Um módulo que utilize esta opção de plataforma deve:

- Forneça uma etiqueta mais recente e uma etiqueta de versão (por exemplo, 1.0.1) que são marcas de manifesto construídas com a [ferramenta de manifesto](https://github.com/estesp/manifest-tool) GitHub se mais do que uma plataforma for suportada. As etiquetas de manifesto só são opcionais quando uma plataforma é suportada.
- Utilize o separador de listagem de ofertas no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) para adicionar um link sob a secção de **links Úteis** a pelo menos um dispositivo IoT Edge do catálogo de [dispositivos Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Esta é uma imagem da secção de Listagem de Ofertadentro do Partner Center":::

### <a name="device-dimensions"></a>Dimensões do dispositivo

As dimensões do módulo IoT Edge (tais como CPU, RAM, armazenamento e GPU) em dispositivos IoT Edge direcionados devem satisfazer os seguintes requisitos:

- O módulo deve funcionar com pelo menos um dispositivo IoT Edge do catálogo de [dispositivos Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/).

- Os requisitos mínimos de hardware devem ser documentados como o último parágrafo na descrição da oferta (ao abrigo do separador de listagem de oferta no [Partner Center).](https://partner.microsoft.com/dashboard/commercial-marketplace) Opcionalmente, também pode enumerar os requisitos de hardware recomendados se diferirsignificativamente. Por exemplo, adicione a seguinte secção no final da descrição da sua oferta:

Copie este texto HTML ou utilize as funções de texto ricas correspondentes na janela de edição.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Configuração

O seu módulo deve incluir definições de configuração predefinidas para tornar a implementação num dispositivo IoT Edge o mais simples possível. Esta informação pode ser fornecida na página de **configuração Técnica** para o plano no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace). O recipiente também pode incluir o Módulo IoT Edge SDK para permitir a comunicação com o Hub de borda e o Hub IoT.

#### <a name="default-configuration"></a>Configuração predefinida

Os módulos IoT Edge devem poder começar com as definições predefinidas fornecidas na página **de configuração Técnica** para o plano no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace). Estão disponíveis as seguintes definições predefinidas:

- Rotas **routes** padrão
- Módulo padrão **propriedades desejadas**
- Variáveis **ambientais** padrão
- Recipiente padrão **criar opções**

Num cenário em que um parâmetro que é necessário para um valor predefinido não faz sentido (por exemplo, o endereço IP do servidor de um cliente), adicione um parâmetro como o valor predefinido. Este valor é maiúsculo e está fechado em parênteses. Para este exemplo, configurava a seguinte variável de ambiente padrão:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Documentação de configuração

Todas as definições de configuração de um módulo IoT Edge devem ser claramente documentadas. Por exemplo, deve documentar como utilizar as suas rotas, propriedades duplas desejadas, variáveis ambientais, criar Opções, e assim por diante. Deve fornecer um link para a sua documentação ou torná-la parte da sua oferta ou descrição do plano. Pode fornecer esta informação na página de listagem e listagem de **planos** de **oferta** no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace).

#### <a name="tags-and-versioning"></a>Tags e versão

Os clientes devem ser capazes de implementar facilmente um módulo e receber automaticamente atualizações do mercado (num cenário de desenvolvimento). Também devem poder usar e congelar uma versão exata que testaram (num cenário de produção).

Para satisfazer estas expectativas dos clientes e ser publicado no mercado, os módulos IoT Edge devem satisfazer os seguintes requisitos

- Inclua uma etiqueta mais recente que aponta para a versão mais recente em todas as plataformas suportadas.
- Faça etiquetas de versão no formato X.Y.Z, onde X, Y e Z são inteiros.
- Inclua uma etiqueta de "versão", como 1.0.1, que aponta para uma versão específica em todas as plataformas suportadas.
- Não atualize as etiquetas de "versão", como 1.0.1, porque não devem ser alteradas.

> [!NOTE]
> Opcionalmente, a versão pode incluir tags de "versão rolante", como 2.0 e 1.0. Isto suporta a manutenção de múltiplas versões principais em paralelo.

### <a name="telemetry"></a>Telemetria

Os módulos que utilizam o Módulo IoT SDK devem definir o identificador de módulo único para PublisherId.OfferId.SkuId para efeitos de telemetria. Um identificador único ajuda o Azure Marketplace a identificar o número de casos de módulos que estão a decorrer.

Utilize um dos seguintes métodos dos SDKs do Módulo IoT para definir o ProductInfo para este identificador:

- [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Para módulos que não utilizam o Módulo IoT SDK, insights menos precisos estão disponíveis através do Partner Center, como o número de downloads.

### <a name="security"></a>Segurança

Os módulos IoT Edge devem evitar [módulos privilegiados](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities). Em vez disso, peça o acesso menos privilegiado possível ao hospedeiro.

### <a name="module-iot-sdk"></a>Módulo IoT SDK

Incluir o Módulo IoT SDK não é um pré-requisito para a certificação. No entanto, incluindo o Módulo IoT SDK pode proporcionar uma melhor experiência do utilizador. Por exemplo, para apoiar o encaminhamento ou o envio de mensagens para a Cloud.

O Módulo IoT SDK é necessário para obter dados de telemetria sobre o número de instâncias de módulos que estão em execução.

## <a name="recertification-process"></a>Processo de recertificação

Os parceiros são notificados sempre que há uma mudança de rutura que afeta os seus módulos, tais como:

- Matriz de suporte de nível 1 OS/arco suportada por IoT Edge
- Módulo IoT SDK
- Runtime do IoT Edge
- Diretrizes de certificação de módulos IoT Edge

Os parceiros devem atualizar e recertificar as suas ofertas reeditando-as no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace).

A sua oferta também será recertificada se a atualizar, como por exemplo, adicionar novas etiquetas de imagem.

## <a name="host-module-in-azure-container-registry"></a>Módulo de acolhimento no Registo de Contentores de Azure

Para fazer o upload do módulo IoT Edge para o Azure Marketplace, é necessário acolhê-lo primeiro num Registo de [Contentores Azure](https://azure.microsoft.com/services/container-registry/) (ACR). O módulo deve incluir todas as etiquetas que pretende publicar, incluindo as etiquetas de imagem que são referenciadas por uma etiqueta de manifesto. Para mais informações, consulte o tutorial Criar um registo de [contentores Azure e empurre uma imagem de recipiente](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr).

## <a name="next-steps"></a>Passos seguintes

- [Criar uma oferta de módulo do IoT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)