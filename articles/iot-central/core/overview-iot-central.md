---
title: O que é o Azure IoT Central | Microsoft Docs
description: A Azure IoT Central é uma plataforma de aplicação IoT que simplifica a criação de soluções IoT e ajuda a reduzir os encargos e os custos das operações de gestão de IoT, e desenvolvimento. Este artigo disponibiliza uma descrição geral do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: ffaab22efb2f6969f03720abba4a7afc6387021a
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758218"
---
# <a name="what-is-azure-iot-central"></a>O que é o Azure IoT Central?

A IoT Central é uma plataforma de aplicação IoT que reduz os encargos e os custos de desenvolvimento, gestão e manutenção de soluções IoT de nível empresarial. Optar por construir com a IoT Central dá-lhe a oportunidade de concentrar o tempo, o dinheiro e a energia na transformação do seu negócio com dados IoT, em vez de apenas manter e atualizar uma infraestrutura ioT complexa e em constante evolução.

A Web UI permite monitorizar as condições do dispositivo, criar regras e gerir milhões de dispositivos e seus dados ao longo do seu ciclo de vida. Além disso, permite-lhe agir sobre as informações do dispositivo, alargando a inteligência IoT a aplicações de linha de negócio.

Este artigo descreve, para a IoT Central:

- Pessoas fictícias típicas associadas um projeto.
- Como criar a sua aplicação.
- Como ligar os seus dispositivos à aplicação
- Como gerir a sua aplicação.
- Capacidades azure IoT Edge na IoT Central.
- Como ligar os seus dispositivos de funcionamento Azure IoT Edge à sua aplicação.

## <a name="known-issues"></a>Problemas conhecidos

- A exportação contínua de dados não suporta o formato Avro (incompatibilidade).
- A GeoJSON não é atualmente apoiada.
- O azulejo do mapa não é suportado atualmente.
- Os empregos não suportam tipos complexos.
- Os tipos de esquemas matrizes não são suportados.
- Apenas o dispositivo C SDK e o dispositivo Node.js e os SDKs de serviço são suportados.
- A IoT Central está atualmente disponível nas localizações dos Estados Unidos, Europa, Ásia-Pacífico, Austrália, Reino Unido e Japão.
- Não é possível utilizar o modelo de **aplicação personalizado (legado)** nas localizações do Reino Unido e do Japão.
- Os modelos de capacidade do dispositivo devem ter todas as interfaces definidas inline no mesmo ficheiro.
- O suporte para [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) está em pré-visualização e só é suportado em regiões selecionadas.

## <a name="personas"></a>Pessoas fictícias

A documentação IoT Central refere-se a quatro personalidades que interagem com uma aplicação IoT Central:

- Um construtor de _soluções_ é responsável por definir os tipos de dispositivos que se ligam à aplicação e personalizar a aplicação para o operador.
- Um _operador_ gere os dispositivos ligados à aplicação.
- Um _administrador_ é responsável por tarefas administrativas como a gestão das [funções e permissões](howto-administer.md) do utilizador no âmbito da aplicação.
- Um desenvolvedor de _dispositivos_ cria o código que funciona num dispositivo ou módulo IoT Edge ligado à sua aplicação.

## <a name="create-your-iot-central-application"></a>Crie a sua aplicação IoT Central

Como construtor de soluções, você usa a IoT Central para criar uma solução IoT personalizada e hospedada em nuvem para a sua organização. Normalmente, uma solução de IoT personalizada consiste em:

- Uma aplicação baseada na cloud que recebe a telemetria dos seus dispositivos e permite-lhe gerir esses dispositivos.
- Vários dispositivos que executam o código personalizado ligado à sua aplicação baseada na cloud.

Pode implementar rapidamente uma nova aplicação IoT Central e depois personalizá-la para os seus requisitos específicos no seu navegador. Pode começar com um modelo de _aplicação_ genérico ou com um dos modelos de aplicação focados na indústria para [retalho,](../retail/overview-iot-central-retail-pnp.md) [energia,](../energy/overview-iot-central-energy.md) [governo](../government/overview-iot-central-government.md)ou cuidados de [saúde.](../healthcare/overview-iot-central-healthcare.md)

Como construtor de soluções, utiliza as ferramentas baseadas na Web para criar um modelo de _dispositivo_ para os dispositivos que se ligam à sua aplicação. Um modelo de dispositivo é a planta que define as características e o comportamento de um tipo de dispositivo como o:

- Telemetria que envia.
- As propriedades de negócio que um operador pode modificar.
- As propriedades dos dispositivos que são definidas por um dispositivo e são só de leitura na aplicação.
- Propriedades, que um operador define, que determinam o comportamento do dispositivo.

Este modelo de dispositivo inclui:

- Um modelo de capacidade de _dispositivo_ que descreve as capacidades que um dispositivo deve implementar, como a telemetria que envia e as propriedades que reporta.
- Propriedades em nuvem que não estão armazenadas no dispositivo.
- Personalizações, dashboards e formas que fazem parte da sua aplicação IoT Central.

### <a name="pricing"></a>Preços

Você pode criar aplicação IoT Central usando um teste gratuito de 7 dias, ou usar um plano de preços padrão.

- As aplicações que cria utilizando o plano *gratuito* são gratuitas durante sete dias e suportam até cinco dispositivos. Pode convertê-los para usar um plano de preços padrão a qualquer momento antes de expirarem.
- As aplicações que cria utilizando um plano *padrão* são faturadas por dispositivo, pode escolher o plano de preços **Standard 1** ou **Standard 2,** com os dois primeiros dispositivos gratuitos. Saiba mais sobre os planos de preços gratuitos e standard na página de [preços Do Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

### <a name="create-device-templates"></a>Criar modelos de dispositivo

[IoT Plug and Play (pré-visualização)](../../iot-pnp/overview-iot-plug-and-play.md) permite ao IoT Central integrar dispositivos sem que escreva qualquer código de dispositivo incorporado. No centro do IoT Plug and Play (pré-visualização), está um esquema de modelo de capacidade de dispositivo que descreve as capacidades do dispositivo. Numa aplicação IoT Central, os modelos do dispositivo utilizam estes modelos de capacidade de dispositivo IoT Plug e Play (pré-visualização).

Como construtor de soluções, tem várias opções para criar modelos de dispositivos:

- Importe um modelo de capacidade de dispositivo do catálogo de [dispositivos Azure Certified para ioT](https://aka.ms/iotdevcat) e, em seguida, adicione quaisquer propriedades na nuvem, personalizações e dashboards que a sua aplicação IoT Central necessita.
- Desenhe o modelo do dispositivo na IoT Central e, em seguida, implemente o seu modelo de capacidade de dispositivo no código do seu dispositivo.
- Crie um modelo de capacidade de dispositivo utilizando o código Visual Studio e publique o modelo num repositório. Implemente o código do seu dispositivo a partir do modelo e ligue o seu dispositivo à sua aplicação IoT Central. A IoT Central encontra o modelo de capacidade do dispositivo a partir do repositório e cria um modelo simples de dispositivo para si.
- Crie um modelo de capacidade de dispositivo utilizando o código Visual Studio. Implemente o código do seu dispositivo a partir do modelo. Importe manualmente o modelo de capacidade do dispositivo para a sua aplicação IoT Central e, em seguida, adicione quaisquer propriedades na nuvem, personalizações e dashboards que a sua aplicação IoT Central necessita.

Como construtor de soluções, pode utilizar a IoT Central para gerar código para dispositivos de teste para validar os modelos do seu dispositivo.

### <a name="customize-the-ui"></a>Personalizar a IU

Como construtor de soluções, também pode personalizar a UI de aplicação IoT Central para os operadores responsáveis pela utilização diária da aplicação. As personalizações que um construtor de soluções pode fazer incluem:

- Definir o esquema de propriedades e definições num modelo de dispositivo.
- Configurar dashboards personalizados para ajudar os operadores a detetar informações e resolver os problemas mais rapidamente.
- Configurar uma análise personalizada para explorar os dados de séries de tempo dos seus dispositivos ligados.

## <a name="pricing"></a>Preços

Você pode criar aplicação IoT Central usando um teste gratuito de 7 dias, ou usar um plano de preços padrão.

- As aplicações que cria utilizando o plano *gratuito* são gratuitas durante sete dias e suportam até cinco dispositivos. Pode convertê-los para usar um plano de preços padrão a qualquer momento antes de expirarem.
- As aplicações que cria utilizando o plano *padrão* são faturadas por dispositivo, pode escolher o plano de preços **Standard 1** ou **Standard 2,** com os dois primeiros dispositivos gratuitos. Saiba mais sobre [os preços da IoT Central.](https://aka.ms/iotcentral-pricing)

## <a name="connect-your-devices"></a>Ligar os dispositivos

A Azure IoT Central utiliza o serviço de provisionamento de [dispositivos Hub Azure IoT (DPS)](../../iot-dps/about-iot-dps.md) para gerir todo o registo e ligação do dispositivo.

A utilização de DPS permite:

- IoT Central para apoiar dispositivos de embarque e ligação em escala.
- Você gera credenciais de dispositivo e configurar os dispositivos offline sem registar os dispositivos através da IoT Central UI.
- Dispositivos para se conectarem utilizando assinaturas de acesso partilhado.
- Dispositivos para se conectarem utilizando certificados X.509 padrão da indústria.
- Você usa as suas próprias iDs de dispositivo para registar dispositivos na IoT Central. A utilização dos seus próprios IDs de dispositivo simplifica a integração com os sistemas de back-office existentes.
- Uma forma única e consistente de ligar os dispositivos à IoT Central.

Para saber mais, consulte [Fique ligado à Central Azure IoT](./concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Dispositivos Azure IoT Edge

Além de dispositivos criados com os [SDKs Azure IoT,](https://github.com/Azure/azure-iot-sdks)também pode ligar [os dispositivos Azure IoT Edge](../../iot-edge/about-iot-edge.md) a uma aplicação IoT Central. O Azure IoT Edge permite-lhe executar a inteligência em nuvem e a lógica personalizada diretamente em dispositivos IoT geridos pela IoT Central. O tempo de execução do IoT Edge permite::

- Instale e atualize as cargas de trabalho no dispositivo.
- Mantenha os padrões de segurança Azure IoT Edge no dispositivo.
- Certifique-se de que os módulos IoT Edge estão sempre em funcionamento.
- Informe a saúde do módulo na nuvem para monitorização remota.
- Gerencie a comunicação entre dispositivos de folhas a jusante e um dispositivo IoT Edge, entre módulos num dispositivo IoT Edge, e entre um dispositivo IoT Edge e a nuvem.

Para mais informações, consulte [os dispositivos Azure IoT Edge e IoT Central](concepts-architecture.md#azure-iot-edge-devices).

## <a name="stay-connected"></a>Manter-se ligado

As aplicações IoT Central são totalmente hospedadas pela Microsoft, o que reduz a administração de gerir as suas aplicações.

Como operador, utiliza a aplicação IoT Central para gerir os dispositivos na sua solução IoT Central. Os operadores fazem tarefas como:

- Monitorizar os dispositivos ligados à aplicação.
- Resolver e remediar problemas dos dispositivos.
- Aprovisionar novos dispositivos.

Como construtor de soluções, pode definir regras e ações personalizadas que operam através do streaming de dados a partir de dispositivos conectados. Um operador pode ativar ou desativar estas regras ao nível do dispositivo, para controlar e automatizar tarefas na aplicação.

Os administradores gerem o acesso à sua aplicação com [funções e permissões](howto-administer.md)do utilizador.

Com qualquer solução IoT projetada para funcionar em escala, é importante uma abordagem estruturada para a gestão do dispositivo. Não basta apenas ligar os seus dispositivos à nuvem, é necessário manter os seus dispositivos ligados e saudáveis. Um operador pode utilizar as seguintes capacidades IoT Central para gerir os seus dispositivos ao longo do ciclo de vida da aplicação:

### <a name="dashboards"></a>Dashboards 

Os [tabliers incorporados](./howto-set-up-template.md#generate-default-views) fornecem um UI personalizável para monitorizar a saúde e a telemetria do dispositivo. Comece com um dashboard pré-construído num modelo de [aplicação](howto-use-app-templates.md) ou crie os seus próprios dashboards adaptados às necessidades dos seus operadores. Pode partilhar dashboards com todos os utilizadores da sua aplicação ou mantê-los privados.

### <a name="rules-and-actions"></a>Regras e ações 

Construa [regras personalizadas](tutorial-create-telemetry-rules.md) baseadas no estado do dispositivo e telemetria para identificar dispositivos que precisam de atenção. Configure as ações para notificar as pessoas certas e garantir que as medidas corretivas são tomadas em tempo útil.

### <a name="jobs"></a>Tarefas 

[Os trabalhos](howto-run-a-job.md) permitem-lhe aplicar atualizações individuais ou a granel aos dispositivos, definindo propriedades ou comandos de chamada. 

### <a name="user-roles-and-permissions"></a>Funções e permissões do utilizador

[Funções e permissões](howto-manage-users-roles.md) permitem que um administrador adapte a experiência de cada utilizador. Um administrador usa a UI web para criar funções e atribuir permissões.

## <a name="transform-your-iot-data"></a>Transforme os seus dados ioT

Como plataforma de aplicação, a IoT Central permite-lhe transformar os seus dados IoT nos insights de negócio que impulsionam resultados atuais. [Regras,](./tutorial-create-telemetry-rules.md) [exportação](./howto-export-data.md)de dados, e a [API do REST público](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) são exemplos de como pode integrar a IoT Central com aplicações de linha de negócio:

![Como a IoT Central pode transformar os seus dados ioT](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>Monitorizar a saúde e as operações dos dispositivos utilizando regras

Quando os seus dispositivos estão conectados e enviam dados, as regras podem identificar dispositivos que sofrem de problemas ou enviar mensagens de erro para que possa corrigi-los com o mínimo de tempo de inatividade. Construa regras na sua aplicação IoT Central para monitorizar a telemetria dos seus dispositivos e alerte um operador quando uma métrica atravessa um limiar ou um dispositivo envia uma mensagem específica. As ações de e-mail e webhooks para as suas regras notificam as pessoas certas e os sistemas a jusante.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Executar análises personalizadas e processamento dos seus dados exportados

Pode gerar insights de negócio, tais como determinar tendências de eficiência da máquina ou prever o uso futuro de energia num piso de fábrica, construindo oleodutos de análise personalizada para processar a telemetria a partir dos seus dispositivos e armazenar os resultados. Configure as exportações de dados na sua aplicação IoT Central para exportar telemetria, alterações na propriedade do dispositivo e alterações no modelo do dispositivo para outros serviços onde pode analisar, armazenar e visualizar os dados com as suas ferramentas preferidas.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Construa soluções e integrações ioT personalizadas com as APIs rest

Construir soluções IoT tais como:

- Aplicativos de acompanhantes móveis que podem configurar e controlar remotamente dispositivos.
- Integrações personalizadas que permitem às aplicações de linha de negócio existentes interagir com os seus dispositivos e dados IoT.
- Aplicações de gestão de dispositivos para modelação de dispositivos, embarque, gestão e acesso a dados.

## <a name="quotas"></a>Quotas

Cada subscrição do Azure tem quotas padrão que podem afetar o âmbito da sua solução IoT. Atualmente, a IoT Central limita o número de aplicações que pode implementar numa subscrição de 10. Se precisar de aumentar este limite, contacte o [suporte da Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão geral da IoT Central, aqui estão alguns passos sugeridos:

- Compreenda as [tecnologias e serviços disponíveis do Azure para a criação de soluções IoT.](../../iot-fundamentals/iot-services-and-technologies.md)
- Familiarizar-se com a [IU do Azure IoT Central](overview-iot-central-tour.md).
- Começar por [criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).
- Saiba mais sobre [ioT Plug e Play (pré-visualização)](../../iot-pnp/overview-iot-plug-and-play.md).
- Saiba como [ligar um dispositivo Azure IoT Edge](./tutorial-add-edge-as-leaf-device.md).
- Saiba mais sobre [tecnologias e serviços Azure IoT.](../../iot-fundamentals/iot-services-and-technologies.md)

Se é um desenvolvedor de dispositivos e quer mergulhar em algum código, o próximo passo sugerido é criar e ligar uma aplicação de [cliente à sua aplicação Azure IoT Central](./tutorial-connect-device-nodejs.md).
