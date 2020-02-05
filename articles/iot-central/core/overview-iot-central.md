---
title: O que é o Azure IoT Central | Microsoft Docs
description: O Azure IoT Central é uma plataforma de aplicações IoT que simplifica a criação de soluções de IoT. Este artigo disponibiliza uma descrição geral do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 6c62b247da9447138bd7e5d84dfe49fdf75a06fd
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990764"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>O que é o Azure IoT Central?

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

O Azure IoT Central é uma plataforma de aplicativo IoT que reduz a carga e o custo associados ao desenvolvimento, gerenciamento e manutenção de soluções de IoT de nível empresarial. Optar por criar com o Azure IoT Central oferece a oportunidade de concentrar seu tempo, dinheiro e energia ao transformar seus negócios com dados de IoT, em vez de apenas manter e atualizar uma infraestrutura de IoT complexa e continuamente em constante evolução.

A interface fácil de usar simplifica o monitoramento de condições de dispositivo, a criação de regras e o gerenciamento de milhões de dispositivos e seus dados ao longo de seu ciclo de vida. Além disso, ele permite que você atue em insights do dispositivo estendendo o IoT Intelligence em aplicativos de linha de negócios.

Este artigo descreve os IoT Central do Azure:

- Pessoas fictícias típicas associadas um projeto.
- Como criar a sua aplicação.
- Como ligar os seus dispositivos à aplicação
- Como gerir a sua aplicação.

## <a name="personas"></a>Pessoas fictícias

A documentação do IoT Central do Azure refere-se a quatro pessoas que interagem com um aplicativo de IoT Central do Azure:

- Um _construtor_ é responsável por definir os tipos de dispositivos que ligam à aplicação e por personalizar a aplicação para o operador.
- Um _operador_ gere os dispositivos ligados à aplicação.
- Um _administrador_ é responsável por tarefas como o gerenciamento de [funções de usuário e permissões](howto-administer.md) no aplicativo.
- Um_programador de dispositivos_ cria o código que é executado num dispositivo ligado à sua aplicação.

## <a name="create-your-azure-iot-central-application"></a>Criar a sua aplicação do Azure IoT Central

Como construtor, utilize o Azure IoT Central para criar uma solução de IoT personalizada e alojada na cloud para a sua organização. Normalmente, uma solução de IoT personalizada consiste em:

- Uma aplicação baseada na cloud que recebe a telemetria dos seus dispositivos e permite-lhe gerir esses dispositivos.
- Vários dispositivos que executam o código personalizado ligado à sua aplicação baseada na cloud.

Você pode implantar rapidamente um novo aplicativo de IoT Central do Azure e, em seguida, personalizá-lo para seus requisitos específicos em seu navegador. Como um construtor, você usa as ferramentas baseadas na Web para criar um _modelo de dispositivo_ para os dispositivos que se conectam ao seu aplicativo. Um modelo de dispositivo é o plano gráfico que define as características e o comportamento de um tipo de dispositivo, como:

- Telemetria que envia.
- As propriedades de negócio que um operador pode modificar.
- As propriedades dos dispositivos que são definidas por um dispositivo e são só de leitura na aplicação.
- Limites aos quais o aplicativo responde.
- As definições que determinam o comportamento do dispositivo.

Pode testar imediatamente os modelos de dispositivos e a aplicação, com dados simulados que o Azure IoT Central gera por si.

Como construtor, também pode personalizar a IU da aplicação do Azure IoT Central para os operadores que são responsáveis pela utilização diária da aplicação. As personalizações que um construtor pode fazer incluem:

- Definir o esquema de propriedades e definições num modelo de dispositivo.
- Configurar dashboards personalizados para ajudar os operadores a detetar informações e resolver os problemas mais rapidamente.
- Configurar uma análise personalizada para explorar os dados de séries de tempo dos seus dispositivos ligados.

## <a name="pricing"></a>Preços

Você pode criar aplicação IoT Central usando um teste gratuito de 7 dias, ou usar um plano de preços padrão.

- As aplicações que cria utilizando o plano *gratuito* são gratuitas durante sete dias e suportam até cinco dispositivos. Pode convertê-los para usar um plano de preços padrão a qualquer momento antes de expirarem.
- As aplicações que cria utilizando o plano *padrão* são faturadas por dispositivo, pode escolher o plano de preços **Standard 1** ou **Standard 2,** com os dois primeiros dispositivos gratuitos. Saiba mais sobre [preços de IOT central](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Ligue os seus dispositivos

Depois de o construtor definir os tipos de dispositivos que podem ligar à aplicação, um programador de dispositivos cria o código que irá ser executado nos dispositivos. Como programador de dispositivos, utilize os [SDKs open source do Azure IoT](https://github.com/Azure/azure-iot-sdks) da Microsoft para criar o código do dispositivo. Estes SDKs incluem um suporte abrangente de linguagem, plataforma e protocolo para satisfazer as suas necessidades de ligar os dispositivos à sua aplicação do Azure IoT Central. Os SDKs ajudam a implementar os seguintes recursos de dispositivo:

- Criar uma ligação segura.
- Enviar telemetria.
- Comunicar o estado.
- Receber atualizações de configuração.

Para obter mais informações, veja a mensagem de blogue [Vantagens de utilizar os SDKs do Azure IoT e problemas a evitar caso não o faça](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Gerir a aplicação

As aplicações do Azure IoT Central são totalmente alojadas pela Microsoft, o que reduz a sobrecarga de gestão das suas aplicações.

Como operador, pode utilizar a aplicação do Azure IoT Central para gerir os dispositivos na sua solução do Azure IoT Central. Os operadores executam tarefas como:

- Monitorizar os dispositivos ligados à aplicação.
- Resolver e remediar problemas dos dispositivos.
- Aprovisionar novos dispositivos.

Como um construtor, você pode definir regras e ações personalizadas que operam sobre o streaming de dados de dispositivos conectados. Um operador pode ativar ou desativar estas regras ao nível do dispositivo, para controlar e automatizar tarefas na aplicação.

Os administradores gerenciam o acesso ao seu aplicativo com [funções e permissões de usuário](howto-administer.md).

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma descrição geral do Azure IoT Central, eis os passos sugeridos:

- Entenda as [tecnologias e os serviços do Azure disponíveis para a criação de soluções de IOT](../../iot-fundamentals/iot-services-and-technologies.md).
- Familiarizar-se com a [IU do Azure IoT Central](overview-iot-central-tour.md).
- Começar por [criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).
- Siga uma sequência de tutoriais que mostram como:
  - [Como construtor, criar um modelo de dispositivo](tutorial-define-device-type.md)
  - [Como construtor, adicionar regras para automatizar a solução](tutorial-configure-rules.md)
  - [Como construtor, personalizar a aplicação para os operadores](tutorial-customize-operator.md)
  - [Como operador, monitorizar os dispositivos](tutorial-monitor-devices.md)
  - [Como operador, adicionar um dispositivo real à solução](tutorial-add-device.md)
  - [Como um programador de dispositivos, criar o código para os dispositivos](tutorial-add-device.md#prepare-the-client-code)
