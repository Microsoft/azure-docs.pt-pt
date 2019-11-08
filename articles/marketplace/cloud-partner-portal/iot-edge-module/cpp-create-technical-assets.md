---
title: Criar Azure IoT Edge ativos técnicos do módulo | Azure Marketplace
description: Crie os ativos técnicos para um módulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 57bc2f789836a7d3453004cdacc59029c4b24129
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827625"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Preparar seus ativos técnicos do módulo IoT Edge

Este artigo descreve os requisitos que seus ativos técnicos do módulo de IoT Edge precisam cumprir antes de serem publicados no Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Noções básicas sobre módulos IoT Edge e introdução

Um módulo IoT Edge é um contêiner compatível com o Docker que é feito para ser executado em um dispositivo IoT Edge.

- Para saber mais sobre os módulos IoT Edge, consulte [entender os módulos Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Para começar a usar seu IoT Edge desenvolvimento de módulo, consulte [requisitos e ferramentas para desenvolver módulos IOT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Requisitos técnicos

Os seguintes requisitos técnicos devem ser atendidos para que seu IoT Edge Module seja certificado e publicado no Azure Marketplace.

### <a name="platform-support"></a>Suporte da plataforma

Seu módulo de IoT Edge deve oferecer suporte a uma das seguintes opções de plataforma.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Plataformas da camada 1 com suporte pelo IoT Edge

Dê suporte a todas as plataformas de camada 1 com suporte pelo IoT Edge (conforme registrado no [suporte Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Recomendamos essa opção porque ela fornece uma melhor experiência do cliente. Os módulos que atendem a esses critérios serão demonstrados. Um módulo que usa essa opção de plataforma deve:

- Forneça uma marca de `latest` e uma marca de versão (por exemplo, `1.0.1`) que são marcas de manifesto criadas com a [ferramenta de manifesto](https://github.com/estesp/manifest-tool)do github.
- Use a [guia Marketplace](./cpp-marketplace-tab.md) para adicionar um link para [dispositivos compatíveis IOT Edge certificados](https://aka.ms/iot-edge-certified). Esse link resolve para `https://aka.ms/iot-edge-certified`, um site em que os clientes podem procurar ou procurar dispositivos certificados. Esse site também é conhecido como o [Azure IOT Edge](https://catalog.azureiotsolutions.com/) catálogo de dispositivos certificado.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Um subconjunto de plataformas de camada 1 com suporte pelo IoT Edge
  
Suporte a um subconjunto (pelo menos um) de plataformas de camada 1 com suporte pelo IoT Edge (conforme registrado no [suporte Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Um módulo que usa essa opção de plataforma deve:

- Forneça uma marca de `latest` e uma marca de versão (por exemplo, `1.0.1`) que são marcas de manifesto criadas com a [ferramenta de manifesto](https://github.com/estesp/manifest-tool) do GitHub se houver suporte para mais de uma plataforma. As marcas de manifesto são opcionais somente quando há suporte para uma plataforma.
- Use a [guia Marketplace](./cpp-marketplace-tab.md) para fornecer um link para pelo menos um dispositivo IOT Edge no catálogo de dispositivos [certificado Azure IOT Edge](https://catalog.azureiotsolutions.com/) .

### <a name="device-dimensions"></a>Dimensões do dispositivo

IoT Edge dimensões de módulo (CPU/RAM/armazenamento/GPU/etc.) nos dispositivos de IoT Edge de destino devem atender aos seguintes requisitos:

- O módulo deve **trabalhar com pelo menos um IOT Edge dispositivo certificado** no catálogo de dispositivos [Azure IOT Edge certificado](https://catalog.azureiotsolutions.com/) .
- Os **requisitos mínimos de hardware** devem ser documentados como o último parágrafo na descrição da oferta (na [guia Marketplace](./cpp-marketplace-tab.md)). Opcionalmente, você também pode listar os requisitos de hardware recomendados se eles diferirem significativamente. Por exemplo, adicione a seguinte seção ao final da descrição da sua oferta:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Configuração

Ele também inclui definições de configuração padrão para tornar a implantação em um dispositivo IoT Edge o mais direto possível. O contêiner também pode incluir o SDK do módulo IoT Edge para habilitar a comunicação com o Hub IoT e edgeHub.

#### <a name="default-configuration"></a>Configuração padrão

Os módulos de IoT Edge devem ser capazes de iniciar com as configurações padrão fornecidas na [guia SKU do portal de parceiros de nuvem](./cpp-skus-tab.md). As seguintes configurações padrão estão disponíveis:

- **Rotas** padrão
- **Propriedades desejadas de entrelaçamento** padrão
- **Variáveis de ambiente** padrão
- **Criaroptions** padrão

Em um cenário em que um parâmetro necessário para um valor padrão não faz sentido (por exemplo, o endereço IP do servidor de um cliente), você adiciona um parâmetro como o valor padrão. Esse valor é colocado entre colchetes e em letras maiúsculas. Para este exemplo, você configuraria a seguinte variável de ambiente padrão:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Documentação de configuração

Todas as definições de configuração de um módulo de IoT Edge devem ser claramente documentadas (como usar suas rotas, propriedades desejadas de entrelaçamento, variáveis de ambiente, criaroptions e assim por diante.) Forneça um link para sua documentação ou a documentação deve fazer parte da sua descrição de oferta/SKU.

### <a name="tags-and-versioning"></a>Marcas e controle de versão

Os clientes devem ser capazes de implantar um módulo com facilidade e obter atualizações automaticamente do Marketplace (em um cenário de desenvolvedor). Eles também devem ser capazes de usar e congelar uma versão exata testada (em um cenário de produção).

Para atender às expectativas do cliente e publicá-las no Marketplace, IoT Edge módulos devem atender aos seguintes requisitos:

- Inclua um manifesto `latest` marca, que aponta a versão mais recente em todas as plataformas com suporte.
- As marcas de versão devem estar no formato X. Y. Z, em que X, Y e Z são inteiros.
- Inclua uma marca "Version", como `1.0.1`, que aponta para uma versão específica em todas as plataformas com suporte.
- Não atualize as marcas "Version", como `1.0.1`, porque elas devem ser imutáveis.

>[!Note]
>Opcionalmente, o controle de versão pode incluir marcas de "versão sem interrupção", como `2.0` e `1.0`. Isso oferece suporte à manutenção de várias versões principais em paralelo.

### <a name="telemetry"></a>Telemetria

Os módulos que usam o SDK do módulo IoT devem definir o identificador de módulo exclusivo para `PublisherId.OfferId.SkuId` para fins de telemetria. Um identificador exclusivo permite que o Azure Marketplace identifique o número de instâncias de módulo em execução.

 Use os seguintes métodos dos SDKs do módulo IoT para definir o `ProductInfo` para esse identificador:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Para módulos que não usam o SDK do módulo IoT, percepções menos precisas estão disponíveis por meio do Portal do Cloud Partner, como o número de downloads.

### <a name="security"></a>Segurança

Os módulos IoT Edge devem solicitar o acesso menos privilegiado ao host o máximo possível. Os [módulos com privilégios](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) devem ser evitados.

### <a name="module-iot-sdk"></a>SDK do módulo IoT

Incluir o SDK do módulo IoT não é um pré-requisito para a certificação. No entanto, a inclusão do SDK do módulo IoT pode fornecer uma melhor experiência do usuário. Por exemplo, para dar suporte ao roteamento ou envio de mensagens para a nuvem.

O SDK do módulo IoT é necessário para obter dados de telemetria sobre o número de instâncias de módulo em execução.


## <a name="recertification-process"></a>Processo de rescertificação

<!-- Add legal time windows-->
Os parceiros serão notificados sempre que houver uma alteração significativa que afete seus módulos, como:

- Matriz de suporte de so/Arch da camada 1 com suporte pelo IoT Edge
- SDK do módulo IoT
- Tempo de execução IoT Edge
- As diretrizes de certificação do módulo IoT Edge

Os parceiros precisarão atualizar seus módulos e certificá-los novamente usando a ferramenta Portal do Cloud Partner.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Hospedar seu módulo de IoT Edge em um registro de contêiner do Azure

Para carregar o módulo IoT Edge no Portal do Cloud Partner, primeiro você precisa hospedá-lo em um ACR ( [registro de contêiner do Azure](https://azure.microsoft.com/services/container-registry/) ). O módulo deve incluir todas as marcas que você deseja publicar, incluindo as marcas de imagem referenciadas por uma marca de manifesto.


## <a name="next-steps"></a>Passos seguintes

- [Criar sua oferta de módulo de IoT Edge](./cpp-create-offer.md)
