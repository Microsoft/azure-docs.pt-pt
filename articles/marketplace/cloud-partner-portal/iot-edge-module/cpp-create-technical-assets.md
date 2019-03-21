---
title: Criar o módulo do Azure IoT Edge ativos técnicos | Documentos da Microsoft
description: Crie os recursos técnicos para o módulo do IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 2ed4826eb47ab2fb13d312860475f9ec9b323bf7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884159"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Preparar seu módulo do IoT Edge ativos técnicos

Este artigo descreve os requisitos que seus ativos técnicos de módulo do IoT Edge têm de cumprir antes de serem publicados no Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Compreender os módulos do IoT Edge e introdução

Módulo do IoT Edge é um contentor de compatível com o Docker tornou-se para ser executada num dispositivo IoT Edge.

- Para saber mais sobre os módulos do IoT Edge, veja [módulos de compreender o Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Para começar a utilizar com o desenvolvimento de módulo do IoT Edge, veja [os requisitos e as ferramentas para desenvolver módulos do IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Requisitos técnicos

Têm de ser cumpridos os seguintes requisitos técnicos para que o módulo do IoT Edge para serem certificados e publicar no Azure Marketplace.

### <a name="platform-support"></a>Suporte da plataforma

O módulo do IoT Edge tem de suportar uma das seguintes opções de plataforma.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Plataformas de camada 1 suportadas pelo IoT Edge

Suporte todas as plataformas de camada 1 suportadas pelo IoT Edge (como registadas no [suporte do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Recomendamos esta opção, porque fornece uma melhor experiência de cliente. Módulos este critério de reunião serão seja apresentados. Tem de um módulo com esta opção de plataforma:

- Fornecer um `latest` etiqueta e uma etiqueta de versão (por exemplo, `1.0.1`) que são criadas com o GitHub de etiquetas de manifestos [ferramenta manifesto](https://github.com/estesp/manifest-tool).
- Utilize o [a guia Marketplace](./cpp-marketplace-tab.md) para adicionar uma ligação para [compatível do IoT Edge, dispositivos certificados](https://aka.ms/iot-edge-certified). Esta ligação é resolvido para `https://aka.ms/iot-edge-certified`, dispositivos certificados para um Web site em que os clientes podem navegue ou procure. Este Web site também é conhecido como o [certificação do Azure IoT Edge](https://catalog.azureiotsolutions.com/) catálogo de dispositivos.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Um subconjunto de plataformas de camada 1 suportadas pelo IoT Edge
  
Suporta um subconjunto de plataformas de camada 1 suportadas pelo IoT Edge (pelo menos um) (como registadas no [suporte do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Tem de um módulo com esta opção de plataforma:

- Fornecer um `latest` etiqueta e uma etiqueta de versão (por exemplo, `1.0.1`) que são criadas com o GitHub de etiquetas de manifestos [ferramenta manifesto](https://github.com/estesp/manifest-tool) se mais do que uma plataforma é suportada. Etiquetas de manifestos são opcionais, apenas se for suportada uma única plataforma.
- Utilize o [separador Marketplace](./cpp-marketplace-tab.md) para fornecer uma ligação para, pelo menos, um dispositivo IoT Edge no [certificação do Azure IoT Edge](https://catalog.azureiotsolutions.com/) catálogo de dispositivos.

### <a name="device-dimensions"></a>Dimensões de dispositivo

Dimensões de módulo do IoT Edge (CPU/RAM/armazenamento/GPU/etc.) nos dispositivos visados do IoT Edge tem de cumprir os seguintes requisitos:

- O módulo deve **profissional com, pelo menos, um certificado do IoT Edge** dispositivo na [certificação do Azure IoT Edge](https://catalog.azureiotsolutions.com/) catálogo de dispositivos.
- O **requisitos de hardware mínimo** deve ser documentada como o último parágrafo na descrição da oferta (sob a [separador Marketplace](./cpp-marketplace-tab.md)). Opcionalmente, também pode listar os requisitos de hardware recomendado se eles forem significativamente diferem. Por exemplo, adicione a secção seguinte no final da sua descrição da oferta:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Configuração

Ele também inclui definições de configuração padrão para fazer a implantação para um dispositivo IoT Edge como direta possível. O contentor pode também incluir o SDK de módulo do IoT Edge para permitir a comunicação com o edgeHub e o IoT Hub.

#### <a name="default-configuration"></a>Configuração predefinida

Módulos do IoT Edge tem de conseguir iniciar com as predefinições fornecidas [separador SKU do portal do Cloud Partner](./cpp-skus-tab.md). As seguintes predefinições estão disponíveis:

- Predefinido **rotas**
- Predefinido **propriedades pretendidas do duplo**
- Predefinido **variáveis de ambiente**
- Predefinido **createOptions**

Num cenário em que um parâmetro necessário para um valor padrão não faz sentido (por exemplo, o endereço IP do servidor de um cliente), adicione um parâmetro como o valor predefinido. Este valor está entre parênteses Retos e em maiúsculas. Neste exemplo, poderia configurar a variável de ambiente de predefinição seguintes:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Documentação de configuração

Todas as definições de configuração do módulo do IoT Edge devem ser documentadas claramente (como utilizar as respetivas rotas, duplo as propriedades pretendidas, as variáveis de ambiente, createOptions e assim por diante.) Forneça uma ligação para a documentação ou a documentação deve fazer parte da sua descrição da oferta/sku.

### <a name="tags-and-versioning"></a>As etiquetas e controle de versão

Os clientes tem de ser capazes de implantar facilmente um módulo e obter automaticamente as atualizações do marketplace (num cenário de desenvolvedor.) Eles também devem ser capazes de usar e fixar uma versão exata que tem testado (num cenário de produção.)

Para atender às expectativas do cliente, estes e publicadas no marketplace, módulos do IoT Edge tem de cumprir os seguintes requisitos:

- Incluir um manifesto `latest` etiqueta, que aponta a versão mais recente nas plataformas suportadas.
- Etiquetas de versão tem de ser do formulário x.y. z, em que X, Y e Z são números inteiros.
- Incluir uma marca de "versão", como `1.0.1`, que aponta para uma versão específica nas plataformas suportadas.
- Não atualizar etiquetas "versão", como `1.0.1`, porque eles devem ser imutáveis.

>[!Note]
>Opcionalmente, controle de versão pode incluir "implementar a versão" marcas, como `2.0` e `1.0`. Isto suporta a manutenção de diversas versões principais em paralelo.

### <a name="telemetry"></a>Telemetria

Módulos com o SDK do módulo do IoT tem de definir o identificador exclusivo do módulo `PublisherId.OfferId.SkuId` para fins de telemetria. Permite a um identificador exclusivo no Azure Marketplace para identificar o número de instâncias de módulo que estejam a executar.

 Utilize os seguintes métodos de SDKs de módulo de IoT para definir o `ProductInfo` para este identificador:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Para módulos que não utilizam o SDK de módulo do IoT, informações menos precisas estão disponíveis através do Portal de parceiro de Cloud como o número de downloads.

### <a name="security"></a>Segurança

Módulos do IoT Edge tem de pedir para o acesso com privilégios mínimos para o host quanto possível. [Privilegiado módulos](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) deve ser evitado.

### <a name="module-iot-sdk"></a>SDK do módulo do IoT

Incluindo o SDK de módulo do IoT, não é um pré-requisito para a certificação. No entanto, incluindo o SDK do módulo de IoT pode fornecer uma melhor experiência de utilizador. Por exemplo, para oferecer suporte a roteamento ou enviar mensagens para a Cloud.

O SDK do módulo de IoT é necessário para dados de telemetria sobre o número de instâncias de módulo em execução.


## <a name="recertification-process"></a>Processo de recertification

<!-- Add legal time windows-->
Parceiros serão notificados sempre que houver uma alteração de última hora que afeta os seus módulos, tais como:

- Matriz de suporte do escalão 1 sistema operacional/arquitetura suportada pelo IoT Edge
- Módulo de IoT SDK
- Runtime do IoT Edge
- As diretrizes de certificação de módulo do IoT Edge

Parceiros vão ter de atualizar seus módulos e voltar a certificá-los usando a ferramenta de Portal de parceiros da Cloud.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Alojar o seu módulo do IoT Edge no Azure Container Registry

Para carregar o módulo do IoT Edge para o Portal de parceiros da Cloud, tem primeiro de hospedá-lo num [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). O módulo tem de incluir todas as marcas que deseja publicar, incluindo as marcas de imagem referenciadas por uma marca de manifesto.


## <a name="next-steps"></a>Passos Seguintes

- [Criar a sua oferta de módulo do IoT Edge](./cpp-create-offer.md)
