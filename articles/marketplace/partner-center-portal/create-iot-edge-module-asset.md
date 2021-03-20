---
title: Prepare os seus ativos técnicos do módulo IoT Edge - Azure Marketplace
description: Conheça os requisitos técnicos e de configuração que os seus ativos técnicos do módulo Internet of Things (IoT) Edge devem reunir antes de poder publicá-los no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/03/2020
ms.openlocfilehash: aca27b89a3b92b410fa560c8b4bd7eb3d4e0a935
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93346779"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Prepare os seus ativos técnicos do módulo IoT Edge

Este artigo descreve os requisitos que os seus ativos técnicos do módulo Internet of Things (IoT) Edge devem satisfazer antes de serem publicados no Azure Marketplace.

## <a name="get-started"></a>Introdução

Um módulo IoT Edge é um recipiente compatível com Docker que funciona num dispositivo IoT Edge.

- Para saber mais sobre os módulos IoT Edge, consulte [os módulos Understand Azure IoT Edge](../../iot-edge/iot-edge-modules.md).
- Para começar com o desenvolvimento do módulo IoT Edge, consulte [Desenvolver os seus próprios módulos IoT Edge.](../../iot-edge/module-development.md)

## <a name="technical-requirements"></a>Requisitos técnicos

O seu módulo IoT Edge deve satisfazer os seguintes requisitos técnicos para ser certificado e publicado no Azure Marketplace.

### <a name="platform-support"></a>Suporte da plataforma

O seu módulo IoT Edge deve suportar uma das seguintes opções de plataforma:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Plataformas de nível 1 suportadas pela IoT Edge

O seu módulo deve suportar todas as plataformas tier 1 suportadas pelo IoT Edge (tal como registado no [suporte Azure IoT Edge).](../../iot-edge/support.md) Recomendamos esta opção porque proporciona uma melhor experiência ao cliente. Serão apresentados os módulos que satisfaçam estes critérios. Um módulo que utilize esta opção de plataforma deve:

- Forneça uma etiqueta mais recente e uma etiqueta de versão (por exemplo, 1.0.1) que sejam marcas manifestas construídas com a [ferramenta GitHub Manifest](https://github.com/estesp/manifest-tool).

- Utilize o separador de listagem de ofertas no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) para adicionar um link na secção **links Úteis** ao catálogo de [dispositivos certificados Azure IoT Edge](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Um subconjunto de plataformas de Nível 1 suportado pela IoT Edge

O seu módulo deve suportar um subconjunto (pelo menos um) de plataformas de Nível 1 suportadas pelo IoT Edge (tal como registado no [suporte Azure IoT Edge).](../../iot-edge/support.md) Um módulo que utilize esta opção de plataforma deve:

- Forneça uma etiqueta mais recente e uma etiqueta de versão (por exemplo, 1.0.1) que sejam marcas manifestas construídas com a [ferramenta manifesto](https://github.com/estesp/manifest-tool) GitHub se mais de uma plataforma for suportada. As etiquetas manifesto só são opcionais quando uma plataforma é suportada.
- Utilize o separador de listagem de oferta no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) para adicionar um link na secção **links Úteis** a pelo menos um dispositivo IoT Edge do catálogo de [dispositivos Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Esta é uma imagem da secção de Listagem de Ofertas dentro do Centro de Parceiros":::

### <a name="device-dimensions"></a>Dimensões do dispositivo

As dimensões dos módulos IoT Edge (tais como CPU, RAM, armazenamento e GPU) em dispositivos IoT Edge direcionados devem satisfazer os seguintes requisitos:

- O módulo deve funcionar com pelo menos um dispositivo IoT Edge do catálogo de [dispositivos Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/).

- Os requisitos mínimos de hardware devem ser documentados como o último parágrafo na descrição da oferta (no separador de listagem de oferta no [Partner Center).](https://partner.microsoft.com/dashboard/commercial-marketplace) Opcionalmente, também pode listar os requisitos de hardware recomendados se diferirem significativamente. Por exemplo, adicione a seguinte secção no final da descrição da sua oferta:

Copie este texto HTML ou utilize as funções de texto rico correspondentes na janela de edição.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Configuração

O seu módulo deve incluir definições de configuração predefinidos para tornar a implementação num dispositivo IoT Edge o mais simples possível. Esta informação pode ser fornecida na página **de configuração técnica** para o plano no [Centro parceiro.](https://partner.microsoft.com/dashboard/commercial-marketplace) O recipiente também pode incluir o Módulo de Borda IoT SDK para permitir a comunicação com o hub de borda e o hub IoT.

#### <a name="default-configuration"></a>Configuração predefinida

Os módulos IoT Edge devem poder começar com as definições predefinidos fornecidas na página **de configuração técnica** para o plano no [Centro parceiro](https://partner.microsoft.com/dashboard/commercial-marketplace). Estão disponíveis as seguintes definições predefinidas:

- Rotas **predefinidos**
- Propriedades **desejadas por twin módulo** predefinido
- **Variáveis ambientais** padrão
- Recipiente predefinido **cria opções**

Num cenário em que um parâmetro que é necessário para um valor predefinido não faz sentido (por exemplo, o endereço IP do servidor de um cliente), adicione um parâmetro como o valor padrão. Este valor é maiústário e incluído em parênteses. Para este exemplo, configuraria a seguinte variável ambiente padrão:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Documentação de configuração

Todas as definições de configuração de um módulo IoT Edge devem estar claramente documentadas. Por exemplo, deve documentar como usar as suas rotas, propriedades duplas desejadas, variáveis ambientais, criar Opções, e assim por diante. Deve fornecer um link para a sua documentação ou torná-la parte da sua oferta ou descrição do plano. Pode fornecer estas informações na página de listagem de **Ofertas** e **Plano** no [Centro de Parceiros.](https://partner.microsoft.com/dashboard/commercial-marketplace)

#### <a name="tags-and-versioning"></a>Etiquetas e versões

Os clientes devem ser capazes de implementar facilmente um módulo e obter automaticamente atualizações do mercado (num cenário de desenvolvimento). Também devem poder usar e congelar uma versão exata que testaram (num cenário de produção).

Para satisfazer estas expectativas dos clientes e ser publicados no mercado, os módulos IoT Edge devem satisfazer os seguintes requisitos

- Inclua uma marca mais recente manifesta que aponta para a versão mais recente em todas as plataformas suportadas.
- Faça tags de versão no formulário X.Y.Z, onde X, Y e Z são inteiros.
- Inclua uma etiqueta "versão", como o 1.0.1, que aponta para uma versão específica em todas as plataformas suportadas.
- Não atualize tags de "versão", como 1.0.1, porque não devem ser alteradas.

> [!NOTE]
> Opcionalmente, a versão pode incluir tags de "versão rolante", como 2.0 e 1.0. Isto suporta a manutenção de várias versões principais em paralelo.

### <a name="telemetry"></a>Telemetria

Os módulos que utilizam o Módulo IoT SDK devem definir o identificador de módulos único para PublisherId.OfferId.SkuId para fins de telemetria. Um identificador único ajuda o Azure Marketplace a identificar o número de exemplos de módulos que estão a ser executadas.

Utilize um dos seguintes métodos dos SDKs do módulo IoT para definir o ProdutoInfo a este identificador:

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo)

Para módulos que não utilizam o Módulo IoT SDK, insights menos precisos estão disponíveis através do Partner Center, como o número de downloads.

### <a name="security"></a>Segurança

Os módulos IoT Edge devem evitar [módulos privilegiados](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities). Em vez disso, peça o acesso menos privilegiado ao hospedeiro o mais possível.

### <a name="module-iot-sdk"></a>Módulo IoT SDK

Incluir o Módulo IoT SDK não é um pré-requisito para a certificação. No entanto, incluindo o Módulo IoT SDK pode proporcionar uma melhor experiência do utilizador. Por exemplo, para apoiar o encaminhamento ou o envio de mensagens para a Cloud.

O Módulo IoT SDK é necessário para obter dados de telemetria sobre o número de instâncias do módulo que estão em execução.

## <a name="recertification-process"></a>Processo de recertificação

Os parceiros são notificados sempre que há uma mudança de rutura que afeta os seus módulos, tais como:

- Matriz de suporte de nível 1 OS/arco suportada por IoT Edge
- Módulo IoT SDK
- Runtime do IoT Edge
- Diretrizes de certificação do módulo IoT Edge

Os parceiros devem atualizar e retificar as suas ofertas reendo-as no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace).

A sua oferta também será recertificada se a atualizar, como por exemplo adicionar novas etiquetas de imagem.

## <a name="host-module-in-azure-container-registry"></a>Módulo de anfitrião no Registo do Contentor de Azure

Para fazer o upload do seu módulo IoT Edge para o Azure Marketplace, primeiro tem de o hospedar num [Registo de Contentores Azure](https://azure.microsoft.com/services/container-registry/) (ACR). O módulo deve incluir todas as etiquetas que pretende publicar, incluindo as etiquetas de imagem que são referenciadas por uma etiqueta manifesto. Para obter mais informações, consulte o tutorial [Criar um registo de contentores Azure e empurrar uma imagem do recipiente](../../container-instances/container-instances-tutorial-prepare-acr.md).

## <a name="next-steps"></a>Passos seguintes

- [Criar uma oferta de módulo do IoT Edge](azure-iot-edge-module-creation.md)