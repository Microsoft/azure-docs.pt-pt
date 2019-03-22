---
title: O que é o Azure IoT Central | Microsoft Docs
description: Azure IoT Central é uma solução de SaaS ponto-a-ponto, que pode usar para criar e gerir a sua solução de IoT personalizada. Este artigo disponibiliza uma descrição geral do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 9fc565996797c90a6d2ac9b3851ac3408f1842c7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183276"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>O que é o Azure IoT Central?

O Azure IoT Central é uma solução de IoT completamente gerida de software como serviço, que facilita a criação de produtos que ligam aos universos físicos e digitais. Pode dar vida à sua visão de produto ligado, ao:

- Efetuar a derivação de novas informações de dispositivos ligados para oferecer melhores produtos e experiências aos seus clientes.
- Criar novas oportunidades de negócio para a sua organização.

Em comparação com um projeto de IoT típico, o Azure IoT Central elimina todos os problemas associados à gestão de uma solução de IoT, ao:

- Reduzir a carga de gestão.
- Reduzir os custos e as sobrecargas operacionais.
- Facilitar a personalização da sua aplicação, tirando partido de:
  - Tecnologias líderes na indústria, como o [Hub IoT](https://azure.microsoft.com/services/iot-hub/) e o [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/).
  - Funcionalidades de segurança de nível empresarial, como a encriptação ponto a ponto.

O vídeo seguinte apresenta uma descrição geral do Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

O resto deste artigo descreve o Azure IoT Central:

- Pessoas fictícias típicas associadas um projeto.
- Como criar a sua aplicação.
- Como ligar os seus dispositivos à aplicação
- Como gerir a sua aplicação.

## <a name="personas"></a>Pessoas fictícias

A documentação do Azure IoT Central faz referência a quatro pessoas fictícias típicas que interagem com uma aplicação do Azure IoT Central:

- Um _construtor_ é responsável por definir os tipos de dispositivos que ligam à aplicação e por personalizar a aplicação para o operador.
- Um _operador_ gere os dispositivos ligados à aplicação.
- Um _administrador_ é responsável por tarefas administrativas, como a gestão de utilizadores e funções na aplicação.
- Um_programador de dispositivos_ cria o código que é executado num dispositivo ligado à sua aplicação.

## <a name="create-your-azure-iot-central-application"></a>Criar a sua aplicação do Azure IoT Central

Como construtor, utilize o Azure IoT Central para criar uma solução de IoT personalizada e alojada na cloud para a sua organização. Normalmente, uma solução de IoT personalizada consiste em:

- Uma aplicação baseada na cloud que recebe a telemetria dos seus dispositivos e permite-lhe gerir esses dispositivos.
- Vários dispositivos que executam o código personalizado ligado à sua aplicação baseada na cloud.

Pode implementar rapidamente uma nova aplicação do Azure IoT Central e, em seguida, personalizá-la de acordo com os seus requisitos específicos, diretamente no browser. Como construtor do Azure IoT Central, pode utilizar as ferramentas baseadas na Web para criar um _modelo de dispositivo_ para os dispositivos que ligam à sua aplicação. Um modelo de dispositivo é o esquema de um modelo de dispositivo. Todos os dispositivos criados com o mesmo modelo de dispositivo partilham o modelo. Um modelo de dispositivo define as caraterísticas e o comportamento de um tipo de dispositivo, como:

- A telemetria que envia.
- As propriedades de negócio que um operador pode modificar.
- As propriedades dos dispositivos que são definidas por um dispositivo e são só de leitura na aplicação.
- Os limiares aos quais a aplicação responde.
- As definições que determinam o comportamento do dispositivo.

Pode testar imediatamente os modelos de dispositivos e a aplicação, com dados simulados que o Azure IoT Central gera por si.

Como construtor, também pode personalizar a IU da aplicação do Azure IoT Central para os operadores que são responsáveis pela utilização diária da aplicação. As personalizações que um construtor pode fazer incluem:

- Definir o esquema de propriedades e definições num modelo de dispositivo.
- Configurar dashboards personalizados para ajudar os operadores a detetar informações e resolver os problemas mais rapidamente.
- Configurar uma análise personalizada para explorar os dados de séries de tempo dos seus dispositivos ligados.

## <a name="connect-your-devices"></a>Ligar os dispositivos

Depois de o construtor definir os tipos de dispositivos que podem ligar à aplicação, um programador de dispositivos cria o código que irá ser executado nos dispositivos. Como programador de dispositivos, utilize os [SDKs open source do Azure IoT](https://github.com/Azure/azure-iot-sdks) da Microsoft para criar o código do dispositivo. Estes SDKs incluem um suporte abrangente de linguagem, plataforma e protocolo para satisfazer as suas necessidades de ligar os dispositivos à sua aplicação do Azure IoT Central. Os SDKs ajudam-no a realizar as seguintes tarefas no dispositivo ligado ao Azure IoT Central:

- Criar uma ligação segura.
- Enviar telemetria.
- Comunicar o estado.
- Receber atualizações de configuração.

Para obter mais informações, veja a mensagem de blogue [Vantagens de utilizar os SDKs do Azure IoT e problemas a evitar caso não o faça](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Gerir a aplicação

As aplicações do Azure IoT Central são totalmente alojadas pela Microsoft, o que reduz a sobrecarga de gestão das suas aplicações.

Como operador, pode utilizar a aplicação do Azure IoT Central para gerir os dispositivos na sua solução do Azure IoT Central. Os operadores podem realizar tarefas, como:

- Monitorizar os dispositivos ligados à aplicação.
- Resolver e remediar problemas dos dispositivos.
- Aprovisionar novos dispositivos.

Um construtor pode definir regras e ações personalizadas que funcionam através de transmissão em fluxo de dados ao nível do modelo de dispositivo. Um operador pode ativar ou desativar estas regras ao nível do dispositivo, para controlar e automatizar tarefas na aplicação.

Os administradores podem gerir o acesso à sua aplicação com [funções e permissões de utilizador](howto-administer.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que tem uma descrição geral do Azure IoT Central, eis os passos sugeridos:

- Compreender as diferenças entre o [Azure IoT Central e os aceleradores de soluções do Azure IoT](overview-iot-options.md).
- Familiarizar-se com a [IU do Azure IoT Central](overview-iot-central-tour.md).
- Começar por [criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).
- Siga uma sequência de tutoriais que mostram como:
  - [Como construtor, criar um modelo de dispositivo](tutorial-define-device-type.md)
  - [Como construtor, adicionar regras para automatizar a solução](tutorial-configure-rules.md)
  - [Como construtor, personalizar a aplicação para os operadores](tutorial-customize-operator.md)
  - [Como operador, monitorizar os dispositivos](tutorial-monitor-devices.md)
  - [Como operador, adicionar um dispositivo real à solução](tutorial-add-device.md)
  - [Como um programador de dispositivos, criar o código para os dispositivos](tutorial-add-device.md#prepare-the-client-code)
