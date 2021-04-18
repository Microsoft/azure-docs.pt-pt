---
title: O que é o Azure IoT Central | Microsoft Docs
description: O IoT Central é uma plataforma de aplicações IoT hospedada que é segura, escala consigo à medida que o seu negócio cresce, e integra-se com as suas aplicações de negócio existentes. Este artigo disponibiliza uma descrição geral do Azure IoT Central.
author: vishwam
ms.author: vishwams
ms.date: 04/16/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: 1ff8243f9f4d070592ae6dc99e7c33472a6a90de
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600447"
---
# <a name="what-is-azure-iot-central"></a>O que é o Azure IoT Central?

O IoT Central é uma plataforma de aplicações IoT hospedada que é segura, escala consigo à medida que o seu negócio cresce, e integra-se com as suas aplicações de negócio existentes. Optar por construir com a IoT Central dá-lhe a oportunidade de concentrar tempo, dinheiro e energia na transformação do seu negócio com dados IoT, em vez de apenas manter e atualizar uma infraestrutura IoT complexa e em constante evolução.

O IoT Central permite-lhe ligar rapidamente dispositivos, monitorizar as condições do dispositivo, criar regras e gerir milhões de dispositivos e os seus dados ao longo do seu ciclo de vida. Além disso, permite-lhe agir em informações sobre dispositivos, alargando a inteligência IoT a aplicações de linha de negócio.

## <a name="create-your-iot-central-application"></a>Crie a sua aplicação IoT Central

Pode criar rapidamente uma nova aplicação IoT Central e depois personalizá-la de acordo com os seus requisitos únicos. Pode começar com um modelo de _aplicação_ genérico ou com um dos modelos de aplicação focados na indústria para [retalho,](../retail/overview-iot-central-retail.md) [energia,](../energy/overview-iot-central-energy.md) [governo](../government/overview-iot-central-government.md)ou [cuidados de saúde.](../healthcare/overview-iot-central-healthcare.md)

Veja a [criação de um novo](quick-deploy-iot-central.md) quickstart de aplicação para um walk-through de como criar a sua primeira aplicação.

## <a name="connect-your-devices"></a>Ligar os dispositivos
Depois de criar a sua aplicação, o primeiro passo é ligar os seus dispositivos. Consulte a [visão geral](./overview-iot-central-developer.md) do desenvolvimento do dispositivo para obter uma introdução aos dispositivos de ligação à sua aplicação IoT Central.

### <a name="device-templates"></a>Modelos de dispositivo

Os dispositivos no IoT Central estão associados a um _modelo de dispositivo_. Um modelo de dispositivo é como uma planta: define as características e comportamentos dos seus dispositivos, tais como:

- Telemetrias, que representam medições a partir de sensores, por exemplo, temperatura ou humidade.
- Propriedades, que representam o estado durável de um dispositivo. Exemplos incluem o estado de uma bomba de refrigeração ou a temperatura-alvo para um dispositivo. Pode declarar propriedades apenas para leitura ou legitável. Apenas os dispositivos podem atualizar o valor de uma propriedade apenas de leitura. Um operador pode definir o valor de uma propriedade writable para enviar para um dispositivo.
- Comandos, operações que podem ser acionadas num dispositivo, por exemplo, um comando para reiniciar remotamente um dispositivo.
- Propriedades em nuvem, que são metadados de dispositivo para armazenar na aplicação IoT Central, por exemplo, endereço do cliente ou data de serviço final.

Consulte a [criação de um](howto-set-up-template.md) artigo de modelo de dispositivo para saber mais.

### <a name="customize-the-ui"></a>Personalizar a IU

Pode personalizar a aplicação IoT Central para os operadores responsáveis pela utilização diária da aplicação, por exemplo:

- Configurar dashboards personalizados para ajudar os operadores a detetar informações e resolver os problemas mais rapidamente.


## <a name="manage-your-devices"></a>Faça a gestão dos seus dispositivos


Com qualquer solução IoT projetada para funcionar em escala, é importante uma abordagem estruturada para a gestão do dispositivo. Não basta apenas ligar os seus dispositivos à nuvem, é preciso manter os seus dispositivos ligados e saudáveis.

Pode [gerir os dispositivos](howto-manage-devices.md) utilizando a sua aplicação IoT Central para fazer tarefas como:

- A monitorizar os dispositivos.
- Resolver e remediar problemas dos dispositivos.
- Execute atualizações a granel nos dispositivos.

### <a name="dashboards"></a>Dashboards

Os [dashboards incorporados](./howto-set-up-template.md#generate-default-views) fornecem uma UI personalizável para monitorizar a saúde e a telemetria do dispositivo. Comece com um dashboard pré-construído num modelo de [aplicação](howto-use-app-templates.md) ou crie os seus próprios dashboards adaptados às necessidades dos seus operadores. Pode partilhar dashboards com todos os utilizadores da sua aplicação ou mantê-los privados.

### <a name="rules-and-actions"></a>Regras e ações

Construa [regras personalizadas](tutorial-create-telemetry-rules.md) com base no estado do dispositivo e telemetria para identificar dispositivos que precisam de atenção. Configure as ações para notificar as pessoas certas e garantir que as medidas corretivas sejam tomadas em tempo útil.

### <a name="jobs"></a>Tarefas

[Os trabalhos](howto-run-a-job.md) permitem aplicar atualizações individuais ou a granel aos dispositivos, definindo propriedades ou chamando comandos.

### <a name="analytics"></a>Análise
[A Analytics](howto-create-analytics.md) expõe capacidades ricas para analisar tendências históricas e correlacionar várias telemetrias dos seus dispositivos.

## <a name="integrate-with-other-services"></a>Integrar noutros serviços

Como plataforma de aplicação, a IoT Central permite transformar os seus dados IoT nos insights de negócio que impulsionam resultados atuais. [Regras](./tutorial-create-telemetry-rules.md), [exportação de dados,](./howto-export-data.md)e a [API rest pública](/learn/modules/manage-iot-central-apps-with-rest-api/) são exemplos de como pode integrar a IoT Central com aplicações de linha de negócio:

![Como a IoT Central pode transformar os seus dados IoT](media/overview-iot-central/transform.png)

Pode gerar insights de negócio, tais como determinar tendências de eficiência da máquina ou prever o uso futuro de energia num piso de fábrica, construindo oleodutos de análise personalizados para processar a telemetria dos seus dispositivos e armazenar os resultados. Configure as exportações de dados na sua aplicação IoT Central para exportar os seus dados para outros serviços onde possa analisar, armazenar e visualizá-los com as suas ferramentas preferidas.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Construa soluções e integrações personalizadas de IoT com as APIs REST

Construa soluções IoT tais como:

- Aplicativos de acompanhante móvel que podem configurar e controlar remotamente dispositivos.
- Integrações personalizadas que permitem que as aplicações existentes da linha de negócio interajam com os seus dispositivos e dados IoT.
- Aplicações de gestão de dispositivos para modelação de dispositivos, embarque, gestão e acesso a dados.

## <a name="administer-your-application"></a>Administrar a sua aplicação

As aplicações IoT Central são totalmente hospedadas pela Microsoft, o que reduz a sobrecarga da administração na gestão das suas aplicações. Os administradores gerem o acesso à sua aplicação com [funções e permissões de utilizador.](howto-administer.md)

## <a name="pricing"></a>Preços

Você pode criar uma aplicação IoT Central usando um teste gratuito de 7 dias, ou usar um plano de preços padrão.

- As aplicações que cria usando o plano *gratuito* são gratuitas durante sete dias e suportam até cinco dispositivos. Pode convertê-los para usar um plano de preços padrão a qualquer momento antes de expirarem.
- As aplicações que cria utilizando o plano *padrão* são faturadas por dispositivo. Pode escolher o plano de preços **Standard 0,** **Standard 1** ou **Standard 2,** com os dois primeiros dispositivos gratuitos. Saiba mais sobre [os preços da IoT Central.](https://aka.ms/iotcentral-pricing)

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão geral da IoT Central, aqui estão alguns passos sugeridos seguintes:

- Começar por [criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).
- Familiarizar-se com a [IU do Azure IoT Central](overview-iot-central-tour.md).
- Se é um desenvolvedor de dispositivos e quer mergulhar em algum código, [crie e conecte uma aplicação do cliente à sua aplicação Azure IoT Central](./tutorial-connect-device.md).
