---
title: Criar ativos técnicos do módulo Azure IoT Edge [ Mercado Azure
description: Crie os ativos técnicos para um módulo IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: cd9cd9f3986ef1944d9f9119296af5512b352d1d
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744315"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Prepare os seus ativos técnicos do módulo IoT Edge

>[!Important]
>A partir de 30 de março de 2020, começaremos a movimentar a gestão do seu módulo IoT Edge oferece ao Partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Criar uma oferta de módulo IoT Edge](https://aka.ms/AzureIoTTechAsset) para gerir as suas ofertas migratórias.

Este artigo descreve os requisitos que os seus ativos técnicos do módulo IoT Edge precisam de cumprir antes de serem publicados no Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Compreender os módulos IoT Edge e começar

Um módulo IoT Edge é um recipiente compatível com o Docker que é feito para funcionar num dispositivo IoT Edge.

- Para saber mais sobre os módulos IoT Edge, consulte os [módulos Understand Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Para começar com o desenvolvimento do módulo IoT Edge, consulte [requisitos e ferramentas para desenvolver módulos IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Requisitos técnicos

Os seguintes requisitos técnicos devem ser cumpridos para que o seu módulo IoT Edge seja certificado e publicado no Azure Marketplace.

### <a name="platform-support"></a>Suporte da plataforma

O módulo IoT Edge deve suportar uma das seguintes opções da plataforma.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Plataformas de nível 1 suportadas por IoT Edge

Apoie todas as plataformas de Nível 1 suportadas por IoT Edge (conforme registado no [suporte Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Recomendamos esta opção porque proporciona uma melhor experiência ao cliente. Serão apresentados módulos que satisfaçam estes critérios. Um módulo que utilize esta opção de plataforma deve:

- Forneça `latest` uma etiqueta e uma etiqueta `1.0.1`de versão (por exemplo, que sejam marcas de manifesto construídas com a [ferramenta de manifesto](https://github.com/estesp/manifest-tool)GitHub .
- Utilize [o separador Marketplace](./cpp-marketplace-tab.md) para adicionar um link para [dispositivos certificados IoT Edge compatíveis](https://aka.ms/iot-edge-certified). Este link `https://aka.ms/iot-edge-certified`resolve- um site onde os clientes podem navegar ou procurar por dispositivos certificados. Este site também é conhecido como o catálogo de dispositivos [Azure IoT Edge Certified.](https://catalog.azureiotsolutions.com/)

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Um subconjunto de plataformas de Nível 1 suportadas por IoT Edge
  
Suporte um subconjunto (pelo menos um) das plataformas de Nível 1 suportadas por IoT Edge (conforme registado no [suporte Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Um módulo que utilize esta opção de plataforma deve:

- Forneça `latest` uma etiqueta e uma etiqueta `1.0.1`de versão (por exemplo, ) que sejam marcas de manifesto construídas com a [ferramenta manifesto](https://github.com/estesp/manifest-tool) GitHub se mais do que uma plataforma for suportada. As etiquetas de manifesto só são opcionais quando uma plataforma é suportada.
- Utilize o [separador Marketplace](./cpp-marketplace-tab.md) para fornecer um link para pelo menos um dispositivo IoT Edge no catálogo de dispositivos [Azure IoT Edge Certified.](https://catalog.azureiotsolutions.com/)

### <a name="device-dimensions"></a>Dimensões do dispositivo

As dimensões do módulo IoT Edge (CPU/RAM/Storage/GPU/etc.) em dispositivos IoT Edge direcionados devem satisfazer os seguintes requisitos:

- O módulo deve funcionar com pelo menos um dispositivo **certificado IoT Edge** no catálogo de dispositivos [Azure IoT Edge Certified.](https://catalog.azureiotsolutions.com/)
- Os **requisitos mínimos** de hardware devem ser documentados como o último parágrafo na descrição da oferta (ao abrigo do [separador Marketplace).](./cpp-marketplace-tab.md) Opcionalmente, também pode enumerar os requisitos de hardware recomendados se diferirsignificativamente. Por exemplo, adicione a seguinte secção no final da descrição da sua oferta:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Configuração

Também inclui configurações de configuração padrão para tornar a implementação para um dispositivo IoT Edge o mais simples possível. O recipiente também pode incluir o Módulo IoT Edge SDK para permitir a comunicação com o edgeHub e o Hub IoT.

#### <a name="default-configuration"></a>Configuração predefinida

Os módulos IoT Edge devem poder começar com as definições predefinidas fornecidas no [separador SKU do portal Cloud Partner](./cpp-skus-tab.md). Estão disponíveis as seguintes definições predefinidas:

- Rotas **routes** padrão
- **Propriedades desejadas por** padrão
- Variáveis **ambientais** padrão
- Definição de **definição DeOpÇões**

Num cenário em que um parâmetro necessário para um valor predefinido não faz sentido (por exemplo, o endereço IP do servidor de um cliente), adiciona-se um parâmetro como valor predefinido. Este valor é incluído nos parênteses e na maiúscula. Para este exemplo, configurava a seguinte variável de ambiente padrão:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Documentação de configuração

Todas as configurações de configuração de um módulo IoT Edge devem ser claramente documentadas (como utilizar as suas rotas, propriedades duplas desejadas, variáveis ambientais, criações DeOpções, e assim por diante.) Forneça um link para a sua documentação, ou a documentação deve fazer parte da sua descrição oferta/sku.

### <a name="tags-and-versioning"></a>Tags e versão

Os clientes devem ser capazes de implementar facilmente um módulo e receber automaticamente atualizações do mercado (num cenário de desenvolvimento.) Também devem poder usar e congelar uma versão exata que testaram (num cenário de produção.)

Para satisfazer as expectativas destecliente e ser publicado no mercado, os módulos IoT Edge devem satisfazer os seguintes requisitos:

- Inclua `latest` uma etiqueta de manifesto, que aponta a versão mais recente em todas as plataformas suportadas.
- As etiquetas de versão devem ser do formulário X.Y.Z, onde X, Y e Z são inteiros.
- Inclua uma etiqueta de `1.0.1`"versão", como, que aponta para uma versão específica em todas as plataformas suportadas.
- Não atualize as etiquetas de `1.0.1`"versão", tipo, porque devem ser imutáveis.

>[!Note]
>Opcionalmente, a versão pode incluir tags de `2.0` `1.0`"versão rolante", como e . Isto suporta a manutenção de múltiplas versões principais em paralelo.

### <a name="telemetry"></a>Telemetria

Os módulos que utilizam o Módulo IoT SDK devem definir o identificador de módulo único para `PublisherId.OfferId.SkuId` efeitos de telemetria. Um identificador único permite ao Azure Marketplace identificar o número de casos de módulos que estão em execução.

 Utilize os seguintes métodos dos SDKs `ProductInfo` do Módulo IoT para definir o identificador:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Para módulos que não utilizam o Módulo IoT SDK, insights menos precisos estão disponíveis através do Portal do Parceiro cloud, como o número de downloads.

### <a name="security"></a>Segurança

Os módulos IoT Edge devem pedir o acesso menos privilegiado possível ao hospedeiro. Devem ser [evitados módulos privilegiados.](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)

### <a name="module-iot-sdk"></a>Módulo IoT SDK

Incluir o Módulo IoT SDK não é um pré-requisito para a certificação. No entanto, incluindo o Módulo IoT SDK pode proporcionar uma melhor experiência do utilizador. Por exemplo, para apoiar o encaminhamento ou o envio de mensagens para a Cloud.

O Módulo IoT SDK é necessário para obter dados de telemetria sobre o número de instâncias de módulos em execução.


## <a name="recertification-process"></a>Processo de recertificação

<!-- Add legal time windows-->
Os parceiros serão notificados sempre que houver uma alteração de rutura que afete os seus módulos, tais como:

- Matriz de suporte de nível 1/arco suportada por IoT Edge
- Módulo IoT SDK
- Tempo de execução da borda iot
- As diretrizes de certificação do módulo IoT Edge

Os parceiros terão de atualizar os seus módulos e reativá-los utilizando a ferramenta Cloud Partner Portal.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Hospedar o seu módulo IoT Edge num Registo de Contentores Azure

Para fazer o upload do módulo IoT Edge para o Portal do Parceiro cloud, primeiro é necessário acolhê-lo num Registo de [Contentores Azure](https://azure.microsoft.com/services/container-registry/) (ACR). O módulo deve incluir todas as etiquetas que pretende publicar, incluindo as etiquetas de imagem referenciadas por uma etiqueta de manifesto.


## <a name="next-steps"></a>Passos seguintes

- [Crie a sua oferta de móduloS IoT Edge](./cpp-create-offer.md)
