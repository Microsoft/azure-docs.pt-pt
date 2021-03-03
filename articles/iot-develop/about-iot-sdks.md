---
title: Visão geral das opções SDK do dispositivo Azure IoT
description: Saiba qual o dispositivo Azure IoT SDK para usar com base no seu papel de desenvolvimento e tarefas.
author: elhorton
ms.author: elhorton
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: 4d9546c8a912176117e42f2a6bbef3058331383b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702679"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Visão geral dos SDKs de dispositivoS Azure IoT

Os SDKs de dispositivo Azure IoT são um conjunto de bibliotecas de clientes de dispositivos, guias de desenvolvedores, amostras e documentação. Os SDKs do dispositivo ajudam-no a ligar programáticamente os dispositivos aos serviços Azure IoT.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Diagrama mostrando vários Azure IoT SDKs":::

Como mostra o diagrama, existem vários SDKs de dispositivo disponíveis para se adaptarem às necessidades do seu dispositivo e da linguagem de programação. Orientação para a seleção do dispositivo apropriado SDK está disponível em [que SDK devo usar](#which-sdk-should-i-use). Existem também SDKs de serviço Azure IoT disponíveis para ligar a sua aplicação baseada na nuvem com os serviços Azure IoT no backend. Este artigo centra-se nos SDKs do dispositivo, mas você pode saber mais sobre SDKs de serviço Azure [aqui](#service-sdks).

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Por que devo usar os SDKs de dispositivo IoT Azure?

Para ligar os dispositivos ao Azure IoT, pode construir uma camada de conexão personalizada ou utilizar SDKs de dispositivo Azure IoT. Existem várias vantagens na utilização de SDKs de dispositivoS Azure IoT:

| &nbsp; &nbsp; Custo de &nbsp; desenvolvimento&nbsp; | Camada de conexão personalizada | SDKs de dispositivo Azure IoT |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Suporte | Precisa apoiar e documentar o que construir | Tenha acesso ao suporte da Microsoft (GitHub, Microsoft Q&A, Microsoft Docs, equipas de Apoio ao Cliente) |
| Novas Funcionalidades | Precisa adicionar novas funcionalidades do Azure ao middleware personalizado | Pode imediatamente tirar partido de novas funcionalidades que a Microsoft constantemente adiciona aos SDKs IoT |
| Investimento | Invista centenas de horas de desenvolvimento embutido para projetar, construir, testar e manter uma versão personalizada | Pode aproveitar ferramentas gratuitas e de código aberto. O único custo associado aos SDKs é a curva de aprendizagem. |

## <a name="which-sdk-should-i-use"></a>Que SDK devo usar?

Os SDKs de dispositivos Azure IoT estão disponíveis em linguagens de programação populares, incluindo C, C#, Java, Node.js e Python. Existem duas considerações primárias quando se escolhe um SDK: capacidades de dispositivo, e a familiaridade da sua equipa com a linguagem de programação.

### <a name="device-capabilities"></a>Capacidades do dispositivo

Ao escolher um SDK, terá de considerar os limites dos dispositivos que está a usar. Um dispositivo constrangido é aquele que tem um único microcontrolador (MCU) e memória limitada. Se estiver a utilizar um dispositivo constrangido, recomendamos que utilize o [SDK C Incorporado.](#embedded-c-sdk) Este SDK foi concebido para fornecer o conjunto mínimo de capacidades para ligar ao Azure IoT. Também pode selecionar componentes (cliente MQTT, TLS e bibliotecas de tomadas) que estão mais otimizados para o seu dispositivo incorporado. Se o seu dispositivo constrangido também executa O Azure RTOS, pode utilizar o middleware Azure RTOS para ligar ao Azure IoT. O middleware Azure RTOS envolve o SDK C Incorporado com uma funcionalidade extra para simplificar a ligação do seu dispositivo Azure RTOS à nuvem.

Um dispositivo sem restrições é aquele que tem um CPU mais robusto, que é capaz de executar um sistema operativo para suportar um tempo de execução da linguagem como .NET ou Python. Se estiver a usar um dispositivo sem restrições, a principal consideração é a familiaridade com o idioma.

### <a name="your-teams-familiarity-with-the-programming-language"></a>A familiaridade da sua equipa com a linguagem de programação

Os SDKs de dispositivoS Azure IoT são implementados em vários idiomas para que possa escolher o idioma que prefere. Os SDKs do dispositivo também se integram com outras ferramentas familiares e específicas da linguagem. Ser capaz de trabalhar com uma linguagem e ferramentas de desenvolvimento familiares, permite à sua equipa otimizar o ciclo de desenvolvimento de pesquisa, prototipagem, desenvolvimento de produtos e manutenção contínua.

Sempre que possível, selecione um SDK que se sinta familiar à sua equipa de desenvolvimento. Todos os SDKs Azure IoT são de código aberto e têm várias amostras disponíveis para a sua equipa avaliar e testar antes de se comprometer com um SDK específico.

## <a name="how-can-i-get-started"></a>Como posso começar?

O lugar para começar é explorar os repositórios GitHub dos SDKs de dispositivos Azure. Também pode experimentar um [quickstart](quickstart-send-telemetry-python.md) que mostra como usar rapidamente um SDK para enviar telemetria para Azure IoT.

As suas opções para começar dependem do tipo de dispositivo que tem:
- Para dispositivos constrangidos, utilize o [SDK C Incorporado](#embedded-c-sdk). 
- Para dispositivos que funcionam em Azure RTOS, pode desenvolver-se com o [middleware Azure RTOS](#azure-rtos-middleware). 
- Para dispositivos que não são constrangidos, então pode [escolher um SDK](#unconstrained-device-sdks) num idioma à sua escolha. 

### <a name="constrained-device-sdks"></a>SDKs de dispositivo constrangido
Estes SDKs são especializados para funcionar em dispositivos com recursos de computação ou memória limitados. Para saber mais sobre tipos comuns de dispositivos, consulte [a visão geral dos tipos de dispositivos Azure IoT](concepts-iot-device-types.md).

#### <a name="embedded-c-sdk"></a>Incorporado C SDK
* [Repositório gitHub](https://github.com/Azure/azure-sdk-for-c/tree/1.0.0/sdk/docs/iot)
* [Amostras](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Documentação de Referência](https://azure.github.io/azure-sdk-for-c/)
* [Como construir o C SDK incorporado](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Gráfico de tamanho para dispositivos constrangidos](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTOS Middleware

* [Repositório gitHub](https://github.com/azure-rtos/threadx)
* [Guias de Arranque](https://github.com/azure-rtos/getting-started) e [mais amostras](https://github.com/azure-rtos/samples)
* [Documentação de Referência](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>SDKs de dispositivos não constrangidos
Estes SDKs podem funcionar em qualquer dispositivo que possa suportar um tempo de execução de linguagem de ordem superior. Isto inclui dispositivos como PCs, Raspberry Pis e smartphones. São diferenciados principalmente pela linguagem para que possa escolher qualquer biblioteca que melhor se adapte à sua equipa e cenário.

#### <a name="c-device-sdk"></a>Dispositivo C SDK
* [Repositório gitHub](https://github.com/Azure/azure-iot-sdk-c)
* [Amostras](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Pacote](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Documentação de Referência](/azure/iot-hub/iot-c-sdk-ref/)
* [Documentação de referência do módulo de borda](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Compilar o Dispositivo C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Portando o C SDK para outras plataformas](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentação do programador](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) para informação sobre compilação cruzada e início em diferentes plataformas
* [Informação sobre o consumo de recursos Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>Dispositivo C# SDK

* [Repositório gitHub](https://github.com/Azure/azure-iot-sdk-csharp)
* [Amostras](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Pacote](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Documentação de Referência](/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true)
* [Documentação de referência do módulo de borda](/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet&preserve-view=true)

#### <a name="java-device-sdk"></a>SDK dispositivo java

* [Repositório gitHub](https://github.com/Azure/azure-iot-sdk-java)
* [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Pacote](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Documentação de Referência](/java/api/com.microsoft.azure.sdk.iot.device)
* [Documentação de referência do módulo de borda](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable&preserve-view=true)

#### <a name="nodejs-device-sdk"></a>Node.js Dispositivo SDK

* [Repositório gitHub](https://github.com/Azure/azure-iot-sdk-node)
* [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Pacote](https://www.npmjs.com/package/azure-iot-device)
* [Documentação de Referência](/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest&preserve-view=true)
* [Documentação de referência do módulo de borda](/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest&preserve-view=true)

#### <a name="python-device-sdk"></a>Python Device SDK

* [Repositório gitHub](https://github.com/Azure/azure-iot-sdk-python)
* [Amostras](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Pacote](https://pypi.org/project/azure-iot-device/)
* [Documentação de Referência](/python/api/azure-iot-device)
* [Documentação de referência do módulo de borda](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python&preserve-view=true)

### <a name="service-sdks"></a>SDKs de Serviço
O Azure IoT também oferece SDKs de serviço que lhe permitem construir aplicações do lado da solução para gerir dispositivos, obter insights, visualizar dados e muito mais. Estes SDKs são específicos de cada serviço Azure IoT e estão disponíveis em C#, Java, JavaScript e Python para simplificar a sua experiência de desenvolvimento. 

#### <a name="iot-hub"></a>IoT Hub

Os SDKs de serviço IoT Hub permitem-lhe construir aplicações que interagem facilmente com o seu Hub IoT para gerir dispositivos e segurança. Pode utilizar estes SDKs para enviar mensagens nuvem-a-dispositivo, invocar métodos diretos nos seus dispositivos, atualizar propriedades do dispositivo e muito mais.

[**Saiba mais sobre o IoT Hub**](https://azure.microsoft.com/services/iot-hub/)  |  [ **Tente controlar um dispositivo**](../iot-hub/quickstart-control-device-python.md)

**C# IoT Hub Service SDK**: [GitHub Repository](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)  |  [Package](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [Samples](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [Reference Documentation](/dotnet/api/microsoft.azure.devices)

**Java IoT Hub Service SDK**: [GitHub Repository](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)  |  [Package](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)  |  [Samples](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [Documentação de referência](/java/api/com.microsoft.azure.sdk.iot.service)

**JavaScript IoT Hub Service SDK**: [GitHub Repository](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)  |  [Package](https://www.npmjs.com/package/azure-iothub)  |  [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)  |  [Reference Documentation](/javascript/api/azure-iothub/?view=azure-iot-typescript-latest&preserve-view=true)

**Python IoT Hub Service SDK**: [GitHub Repository](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)  |  [Package](https://pypi.python.org/pypi/azure-iot-hub/)  |  [Samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [Reference Documentation](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Azure Digital Twins

A Azure Digital Twins é uma plataforma como uma oferta de serviço (PaaS) que permite a criação de gráficos de conhecimento baseados em modelos digitais de ambientes inteiros. Estes ambientes poderiam ser edifícios, fábricas, fazendas, redes de energia, caminhos de ferro, estádios e muito mais - até cidades inteiras. Estes modelos digitais podem ser usados para obter insights que impulsionam melhores produtos, operações otimizadas, custos reduzidos e experiências inovadoras do cliente. A Azure IoT oferece SDKs de serviço para facilitar a construção de aplicações que usam o poder da Azure Digital Twins.

[**Saiba mais sobre a Azure Digital Twins**](https://azure.microsoft.com/services/digital-twins/)  |  [ **Código de uma aplicação ADT**](../digital-twins/tutorial-code.md)

**C# Serviço ADT SDK**: Amostras de pacote [de repositório GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)  |  [](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)

**Java ADT Service SDK**: [GitHub Repository](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [Package](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)  |  [Reference Documentation](/java/api/overview/azure/digitaltwins/client?preserve-view=true&view=azure-java-stable)

**Node.js Serviço ADT SDK**: Amostras de pacote [de repositório GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)  |  [](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [](/javascript/api/@azure/digital-twins-core/?branch=master&view=azure-node-latest&preserve-view=true)

**Python ADT Service SDK**: [GitHub Repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [Package](https://pypi.org/project/azure-digitaltwins-core/)  |  [Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)  |  [Reference Documentation](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true)

#### <a name="device-provisioning-service"></a>Serviço de Aprovisionamento do Dispositivos

O IoT Hub Device Provisioning Service (DPS) é um serviço de ajuda para o IoT Hub que permite o fornecimento de zero toques, just-in-time, para o centro IoT certo sem necessidade de intervenção humana. O DPS permite o fornecimento de milhões de dispositivos de forma segura e escalável. Os DPS Service SDKs permitem-lhe construir aplicações que possam gerir de forma segura os seus dispositivos criando grupos de inscrição e fazendo operações a granel.

[**Saiba mais sobre o Serviço de Provisionamento de Dispositivos**](../iot-dps/index.yml)  |  [ **Tente criar uma inscrição em grupo para dispositivos X.509**](../iot-dps/quick-enroll-device-x509-csharp.md)

**C# Serviço de Provisionamento de Dispositivos SDK**: [GitHub Repository](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [Package](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)  |  [Samples](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [Documentação de referência](/dotnet/api/microsoft.azure.devices.provisioning.service?view=azure-dotnet&preserve-view=true)

**Serviço de fornecimento de dispositivos java SDK**: Documentação de referência de [amostras de pacotes de repositório GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [](/java/api/com.microsoft.azure.sdk.iot.provisioning.service?view=azure-java-stable&preserve-view=true)

**serviço de fornecimento de dispositivosNode.js SDK**: Documentação de referência [de amostras de pacotes de repositórios GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [](https://www.npmjs.com/package/azure-iot-provisioning-service)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Ligue um dispositivo à IoT Central (Python)](quickstart-send-telemetry-python.md)
* [Quickstart: Ligue um dispositivo ao IoT Hub (Python)](quickstart-send-telemetry-cli-python.md)
* [Começar com o desenvolvimento embutido](quickstart-device-development.md)
* Saiba mais sobre os [benefícios de desenvolver usando Azure IoT SDKs](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)