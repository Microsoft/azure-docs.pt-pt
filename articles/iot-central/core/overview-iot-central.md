---
title: O que é o Azure IoT Central | Microsoft Docs
description: A Azure IoT Central é uma plataforma de aplicação IoT que simplifica a criação de soluções IoT e ajuda a reduzir os encargos e custos das operações de gestão de IoT, e desenvolvimento. Este artigo disponibiliza uma descrição geral do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 55f187781a0f11417b0c918034dcfe3362be4468
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457670"
---
# <a name="what-is-azure-iot-central"></a>O que é o Azure IoT Central?

O IoT Central é uma plataforma de aplicação IoT que reduz a carga e o custo de desenvolvimento, gestão e manutenção de soluções IoT de nível empresarial. Optar por construir com a IoT Central dá-lhe a oportunidade de concentrar tempo, dinheiro e energia na transformação do seu negócio com dados IoT, em vez de apenas manter e atualizar uma infraestrutura IoT complexa e em constante evolução.

A UI web permite monitorizar as condições do dispositivo, criar regras e gerir milhões de dispositivos e seus dados ao longo do seu ciclo de vida. Além disso, permite-lhe agir em informações sobre dispositivos, alargando a inteligência IoT a aplicações de linha de negócio.

Este artigo descreve, para a IoT Central:

- Pessoas fictícias típicas associadas um projeto.
- Como criar a sua aplicação.
- Como ligar os seus dispositivos à aplicação
- Como gerir a sua aplicação.
- Capacidades Azure IoT Edge na IoT Central.
- Como ligar os seus dispositivos azure IoT Edge alimentados com o tempo de funcionamento à sua aplicação.

## <a name="personas"></a>Pessoas fictícias

A documentação da IoT Central refere-se a quatro personalidades que interagem com uma aplicação IoT Central:

- Um _construtor de soluções_ é responsável por [definir os tipos de dispositivos](howto-set-up-template.md) que se ligam à aplicação e personalizar a aplicação para o operador.
- Um _operador_ [gere os dispositivos ligados](howto-manage-devices.md) à aplicação.
- Um _administrador_ é responsável por tarefas administrativas como gerir [funções de utilizador e permissões](howto-administer.md) dentro da aplicação.
- Um _desenvolvedor de dispositivos_ [cria o código que funciona num dispositivo](concepts-telemetry-properties-commands.md) ou [módulo IoT Edge](concepts-iot-edge.md) ligado à sua aplicação.

## <a name="create-your-iot-central-application"></a>Crie a sua aplicação IoT Central

Como construtor de soluções, você usa a IoT Central para criar uma solução IoT personalizada e hospedada em nuvem para a sua organização. Normalmente, uma solução de IoT personalizada consiste em:

- Uma aplicação baseada na cloud que recebe a telemetria dos seus dispositivos e permite-lhe gerir esses dispositivos.
- Vários dispositivos que executam o código personalizado ligado à sua aplicação baseada na cloud.

Pode implementar rapidamente uma nova aplicação IoT Central e, em seguida, personalizá-la aos seus requisitos específicos no seu navegador. Pode começar com um modelo de _aplicação_ genérica ou com um dos modelos de aplicação focados na indústria para [retalho,](../retail/overview-iot-central-retail.md) [energia,](../energy/overview-iot-central-energy.md) [governo](../government/overview-iot-central-government.md)ou [cuidados de saúde.](../healthcare/overview-iot-central-healthcare.md)

Como construtor de soluções, utiliza as ferramentas baseadas na web para criar um modelo de _dispositivo_ para os dispositivos que se ligam à sua aplicação. Um modelo de dispositivo é a planta que define as características e o comportamento de um tipo de dispositivo como:

- Telemetria que envia. Exemplos incluem temperatura e humidade. A telemetria está a transmitir dados.
- As propriedades de negócio que um operador pode modificar. Exemplos incluem um endereço de cliente e uma última data servida.
- As propriedades dos dispositivos que são definidas por um dispositivo e são só de leitura na aplicação. Por exemplo, o estado de uma válvula como aberto ou fechado.
- Propriedades que um operador define, que determinam o comportamento do dispositivo. Por exemplo, uma temperatura-alvo para o dispositivo.
- Comandos, que um operador pode ligar, que funcionam num dispositivo. Por exemplo, um comando para reiniciar remotamente um dispositivo.

Este [modelo de dispositivo](howto-set-up-template.md) inclui:

- Um _modelo de dispositivo_ que descreva as capacidades que um dispositivo deve implementar. As capacidades do dispositivo incluem:

  - A telemetria transmite para a IoT Central.
  - As propriedades só de leitura que usa para reportar o estado à IoT Central.
  - As propriedades writable que recebe da IoT Central para definir o estado do dispositivo.
  - Os comandos ligaram da IoT Central.

- Propriedades em nuvem que não são armazenadas no dispositivo.
- Personalizações, dashboards e formulários que fazem parte da sua aplicação IoT Central.

### <a name="create-device-templates"></a>Criar modelos de dispositivo

Como construtor de soluções, tem várias opções para criar modelos de dispositivos:

- Desenhe o modelo do dispositivo na IoT Central e, em seguida, implemente o seu modelo de dispositivo no código do dispositivo.
- Crie um modelo de dispositivo utilizando o código Visual Studio e publique o modelo num repositório. Implemente o código do dispositivo a partir do modelo e ligue o seu dispositivo à sua aplicação IoT Central. A IoT Central encontra o modelo do dispositivo a partir do repositório e cria um modelo de dispositivo simples para si.
- Crie um modelo de dispositivo utilizando o código Visual Studio. Implemente o código do dispositivo a partir do modelo. Importe manualmente o modelo do dispositivo na sua aplicação IoT Central e adicione, em seguida, quaisquer propriedades na nuvem, personalizações e dashboards que a sua aplicação IoT Central necessita.

Como construtor de soluções, pode utilizar o IoT Central para gerar código para dispositivos de teste para validar os modelos do seu dispositivo.

Se for um desenvolvedor de dispositivos, consulte a [visão geral do desenvolvimento do dispositivo IoT Central](./overview-iot-central-developer.md) para uma introdução aos dispositivos de implementação que utilizam estes modelos de dispositivos.

### <a name="customize-the-ui"></a>Personalizar a IU

Como construtor de soluções, também pode personalizar a UI de aplicação IoT Central para os operadores responsáveis pelo uso diário da aplicação. As personalizações que um construtor de soluções pode fazer incluem:

- Definir o esquema de propriedades e definições num modelo de dispositivo.
- Configurar dashboards personalizados para ajudar os operadores a detetar informações e resolver os problemas mais rapidamente.
- Configurar uma análise personalizada para explorar os dados de séries de tempo dos seus dispositivos ligados.

## <a name="manage-your-devices"></a>Faça a gestão dos seus dispositivos

Como operador, utiliza a aplicação IoT Central para [gerir os dispositivos](howto-manage-devices.md) na sua solução IoT Central. Os operadores fazem tarefas como:

- Monitorizar os dispositivos ligados à aplicação.
- Resolver e remediar problemas dos dispositivos.
- Aprovisionar novos dispositivos.

Como construtor de soluções, pode [definir regras e ações personalizadas](howto-configure-rules.md) que operam sobre o streaming de dados a partir de dispositivos conectados. Um operador pode ativar ou desativar estas regras ao nível do dispositivo, para controlar e automatizar tarefas na aplicação.

Com qualquer solução IoT projetada para funcionar em escala, é importante uma abordagem estruturada para a gestão do dispositivo. Não basta apenas ligar os seus dispositivos à nuvem, é preciso manter os seus dispositivos ligados e saudáveis. Um operador pode utilizar as seguintes capacidades IoT Central para gerir os seus dispositivos durante todo o ciclo de vida da aplicação:

### <a name="dashboards"></a>Dashboards

Os [dashboards incorporados](./howto-set-up-template.md#generate-default-views) fornecem uma UI personalizável para monitorizar a saúde e a telemetria do dispositivo. Comece com um dashboard pré-construído num modelo de [aplicação](howto-use-app-templates.md) ou crie os seus próprios dashboards adaptados às necessidades dos seus operadores. Pode partilhar dashboards com todos os utilizadores da sua aplicação ou mantê-los privados.

### <a name="rules-and-actions"></a>Regras e ações

Construa [regras personalizadas](tutorial-create-telemetry-rules.md) com base no estado do dispositivo e telemetria para identificar dispositivos que precisam de atenção. Configure as ações para notificar as pessoas certas e garantir que as medidas corretivas sejam tomadas em tempo útil.

### <a name="jobs"></a>Tarefas

[Os trabalhos](howto-run-a-job.md) permitem aplicar atualizações individuais ou a granel aos dispositivos, definindo propriedades ou chamando comandos.

## <a name="integrate-with-other-services"></a>Integrar noutros serviços

Como plataforma de aplicação, a IoT Central permite transformar os seus dados IoT nos insights de negócio que impulsionam resultados atuais. [Regras](./tutorial-create-telemetry-rules.md), [exportação de dados,](./howto-export-data.md)e a [API rest pública](/learn/modules/manage-iot-central-apps-with-rest-api/) são exemplos de como pode integrar a IoT Central com aplicações de linha de negócio:

![Como a IoT Central pode transformar os seus dados IoT](media/overview-iot-central/transform.png)

Pode gerar insights de negócio, tais como determinar tendências de eficiência da máquina ou prever o uso futuro de energia num piso de fábrica, construindo oleodutos de análise personalizados para processar a telemetria dos seus dispositivos e armazenar os resultados. Configure as exportações de dados na sua aplicação IoT Central para exportar telemetria, alterações de propriedade do dispositivo e alterações no modelo do dispositivo para outros serviços onde pode analisar, armazenar e visualizar os dados com as suas ferramentas preferidas.

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
- As aplicações que cria utilizando o plano *padrão* são faturadas por dispositivo, pode escolher o plano de preços **Standard 0,** **Standard 1** ou **Standard 2,** com os dois primeiros dispositivos a serem gratuitos. Saiba mais sobre [os preços da IoT Central.](https://aka.ms/iotcentral-pricing)

## <a name="quotas"></a>Quotas

Cada subscrição do Azure tem quotas padrão que podem afetar o âmbito da sua solução IoT. Atualmente, a IoT Central limita o número de aplicações que pode implementar numa subscrição a 10. Se precisar de aumentar este limite, contacte o [suporte da Microsoft](https://azure.microsoft.com/support/options/).

## <a name="known-issues"></a>Problemas conhecidos

- A exportação contínua de dados não suporta o formato Avro (incompatibilidade).
- GeoJSON não é apoiado.
- O azulejo do mapa não é suportado atualmente.
- Os tipos de esquemas de matriz não são suportados.
- Apenas o dispositivo C SDK e o Node.js dispositivo e serviço SDKs são suportados.
- A IoT Central está atualmente disponível nos Estados Unidos, Europa, Ásia-Pacífico, Austrália, Reino Unido e Japão.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão geral da IoT Central, aqui estão alguns passos sugeridos seguintes:

- Compreenda as tecnologias e serviços disponíveis [da Azure para a criação de soluções IoT.](../../iot-fundamentals/iot-services-and-technologies.md)
- Se é um desenvolvedor de dispositivos e quer mergulhar em algum código, o próximo passo sugerido é [criar e ligar uma aplicação do cliente à sua aplicação Azure IoT Central](./tutorial-connect-device.md).
- Familiarizar-se com a [IU do Azure IoT Central](overview-iot-central-tour.md).
- Começar por [criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).
- Saiba como [ligar um dispositivo Azure IoT Edge](./tutorial-add-edge-as-leaf-device.md).
- Saiba mais sobre [as tecnologias e serviços Azure IoT.](../../iot-fundamentals/iot-services-and-technologies.md)