---
title: O que é o Azure IoT Central | Microsoft Docs
description: O Azure IoT Central é uma plataforma de aplicativo IoT que simplifica a criação de soluções de IoT e ajuda a reduzir a carga e o custo das operações de gerenciamento de IoT e do desenvolvimento. Este artigo disponibiliza uma descrição geral do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: a16ed6ac942dd4a9fa521cc813a92e6767a98328
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024131"
---
# <a name="what-is-azure-iot-central"></a>O que é o Azure IoT Central?

IoT Central é uma plataforma de aplicativo IoT que reduz a carga e o custo de desenvolvimento, gerenciamento e manutenção de soluções de IoT de nível empresarial. Optar por criar com o IoT Central oferece a oportunidade de concentrar o tempo, o dinheiro e a energia ao transformar seus negócios com dados de IoT, em vez de apenas manter e atualizar uma infraestrutura de IoT complexa e continuamente em constante evolução.

A interface do usuário da Web permite que você monitore as condições do dispositivo, crie regras e gerencie milhões de dispositivos e seus dados ao longo de seu ciclo de vida. Além disso, ele permite que você atue em insights do dispositivo estendendo o IoT Intelligence em aplicativos de linha de negócios.

Este artigo descreve IoT Central:

- Pessoas fictícias típicas associadas um projeto.
- Como criar a sua aplicação.
- Como ligar os seus dispositivos à aplicação
- Como gerir a sua aplicação.
- Azure IoT Edge recursos no IoT Central.
- Como conectar seus dispositivos com o Azure IoT Edge Runtime ao seu aplicativo.

## <a name="known-issues"></a>Problemas conhecidos

- A exportação de dados contínua não dá suporte ao formato Avro (incompatibilidade).
- Não há suporte para geojson no momento.
- O bloco de mapa não tem suporte no momento.
- Os trabalhos não dão suporte a tipos complexos.
- Não há suporte para tipos de esquema de matriz.
- Somente o SDK do dispositivo C e os SDKs do dispositivo e do serviço node. js têm suporte.
- Ele só está disponível nos locais Estados Unidos e Europa.
- Os modelos de funcionalidade do dispositivo devem ter todas as interfaces definidas embutidas no mesmo arquivo.

## <a name="personas"></a>Pessoas fictícias

A documentação IoT Central refere-se a quatro pessoas que interagem com um aplicativo IoT Central:

- Um _Solution Builder_ é responsável por definir os tipos de dispositivos que se conectam ao aplicativo e personalizando o aplicativo para o operador.
- Um _operador_ gere os dispositivos ligados à aplicação.
- Um _administrador_ é responsável por tarefas administrativas, como gerenciar [funções de usuário e permissões](howto-administer.md) no aplicativo.
- Um _desenvolvedor de dispositivos_ cria o código que é executado em um dispositivo ou IOT Edge módulo conectado ao seu aplicativo.

## <a name="create-your-iot-central-application"></a>Criar seu aplicativo IoT Central

Como um Solution Builder, você usa IoT Central para criar uma solução de IoT personalizada e hospedada na nuvem para sua organização. Normalmente, uma solução de IoT personalizada consiste em:

- Uma aplicação baseada na cloud que recebe a telemetria dos seus dispositivos e permite-lhe gerir esses dispositivos.
- Vários dispositivos que executam o código personalizado ligado à sua aplicação baseada na cloud.

Você pode implantar rapidamente um novo aplicativo IoT Central e, em seguida, personalizá-lo para seus requisitos específicos em seu navegador. Pode começar com um modelo de _aplicação_ genérico ou com um dos modelos de aplicação focados na indústria para [retalho,](../retail/overview-iot-central-retail-pnp.md) [energia,](../energy/overview-iot-central-energy.md) [governo](../government/overview-iot-central-government.md)ou cuidados de [saúde.](../healthcare/overview-iot-central-healthcare.md)

Como um construtor de soluções, você usa as ferramentas baseadas na Web para criar um _modelo de dispositivo_ para os dispositivos que se conectam ao seu aplicativo. Um modelo de dispositivo é o plano gráfico que define as características e o comportamento de um tipo de dispositivo, como:

- Telemetria que envia.
- As propriedades de negócio que um operador pode modificar.
- As propriedades dos dispositivos que são definidas por um dispositivo e são só de leitura na aplicação.
- Propriedades, que um operador define, que determinam o comportamento do dispositivo.

Este modelo de dispositivo inclui:

- Um _modelo de funcionalidade de dispositivo_ que descreve os recursos que um dispositivo deve implementar, como a telemetria que ele envia e as propriedades que ele relata.
- Propriedades de nuvem que não são armazenadas no dispositivo.
- Personalizações, painéis e formulários que fazem parte do seu aplicativo IoT Central.


### <a name="pricing"></a>Preços

Você pode criar aplicação IoT Central usando um teste gratuito de 7 dias, ou usar um plano de preços padrão.

- As aplicações que cria utilizando o plano *gratuito* são gratuitas durante sete dias e suportam até cinco dispositivos. Pode convertê-los para usar um plano de preços padrão a qualquer momento antes de expirarem.
- As aplicações que cria utilizando um plano *padrão* são faturadas por dispositivo, pode escolher o plano de preços **Standard 1** ou **Standard 2,** com os dois primeiros dispositivos gratuitos. Saiba mais sobre os planos de preços gratuitos e standard na página de [preços Do Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

### <a name="create-device-templates"></a>Criar modelos de dispositivo

[IoT Plug and Play (pré-visualização)](../../iot-pnp/overview-iot-plug-and-play.md) permite ao IoT Central integrar dispositivos sem que escreva qualquer código de dispositivo incorporado. No centro do IoT Plug and Play (pré-visualização), está um esquema de modelo de capacidade de dispositivo que descreve as capacidades do dispositivo. Numa aplicação IoT Central, os modelos do dispositivo utilizam estes modelos de capacidade de dispositivo IoT Plug e Play (pré-visualização).

Como um Solution Builder, você tem várias opções para criar modelos de dispositivo:

- Importe um modelo de capacidade de dispositivo do catálogo de [dispositivos Azure Certified para ioT](https://aka.ms/iotdevcat) e, em seguida, adicione quaisquer propriedades na nuvem, personalizações e dashboards que a sua aplicação IoT Central necessita.
- Crie o modelo de dispositivo no IoT Central e, em seguida, implemente seu modelo de capacidade de dispositivo no código do dispositivo.
- Crie um modelo de capacidade de dispositivo utilizando o código Visual Studio e publique o modelo num repositório. Implemente o código do dispositivo do modelo e conecte seu dispositivo ao seu aplicativo IoT Central. A IoT Central encontra o modelo de capacidade do dispositivo a partir do repositório e cria um modelo simples de dispositivo para si.
- Crie um modelo de capacidade de dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo. Importe manualmente o modelo de capacidade do dispositivo para seu aplicativo IoT Central e, em seguida, adicione quaisquer propriedades de nuvem, personalizações e painéis de que seu aplicativo IoT Central precisa.

Como um Solution Builder, você pode usar IoT Central para gerar código para dispositivos de teste para validar os modelos de dispositivo.

### <a name="customize-the-ui"></a>Personalizar a IU

Como um Solution Builder, você também pode personalizar a interface do usuário do aplicativo IoT Central para os operadores responsáveis pelo uso diário do aplicativo. As personalizações que um Solution Builder pode fazer incluem:

- Definir o esquema de propriedades e definições num modelo de dispositivo.
- Configurar dashboards personalizados para ajudar os operadores a detetar informações e resolver os problemas mais rapidamente.
- Configurar uma análise personalizada para explorar os dados de séries de tempo dos seus dispositivos ligados.

## <a name="pricing"></a>Preços

Você pode criar aplicação IoT Central usando um teste gratuito de 7 dias, ou usar um plano de preços padrão.

- As aplicações que cria utilizando o plano *gratuito* são gratuitas durante sete dias e suportam até cinco dispositivos. Pode convertê-los para usar um plano de preços padrão a qualquer momento antes de expirarem.
- As aplicações que cria utilizando o plano *padrão* são faturadas por dispositivo, pode escolher o plano de preços **Standard 1** ou **Standard 2,** com os dois primeiros dispositivos gratuitos. Saiba mais sobre [preços de IOT central](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Ligue os seus dispositivos

O Azure IoT Central usa o [DPS (serviço de provisionamento de dispositivos) do Hub IOT do Azure](../../iot-dps/about-iot-dps.md) para gerenciar todo o registro e a conexão do dispositivo.

O uso do DPS permite:

- IoT Central para dar suporte à integração e à conexão de dispositivos em escala.
- Você deve gerar credenciais de dispositivo e configurar os dispositivos offline sem registrar os dispositivos por meio da interface do usuário do IoT Central.
- Dispositivos para se conectarem utilizando assinaturas de acesso partilhado.
- Dispositivos para se conectar usando certificados X. 509 padrão do setor.
- Você usa suas próprias IDs de dispositivo para registrar dispositivos no IoT Central. O uso de suas próprias IDs de dispositivo simplifica a integração com sistemas de Back-Office existentes.
- Uma maneira única e consistente de conectar dispositivos a IoT Central.

Para saber mais, consulte [Fique ligado à Central Azure IoT](./concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Dispositivos do IoT Edge do Azure

Além de dispositivos criados usando os [SDKs do IOT do Azure](https://github.com/Azure/azure-iot-sdks), você também pode conectar [Azure IOT Edge dispositivos](../../iot-edge/about-iot-edge.md) a um aplicativo IOT central. Azure IoT Edge permite que você execute a inteligência de nuvem e a lógica personalizada diretamente em dispositivos IoT gerenciados pelo IoT Central. O tempo de execução do IoT Edge permite que você:

- Instalar e atualizar cargas de trabalho no dispositivo.
- Mantenha os padrões de segurança Azure IoT Edge no dispositivo.
- Verifique se os módulos IoT Edge estão sempre em execução.
- Relate a integridade do módulo para a nuvem para monitoramento remoto.
- Gerencie a comunicação entre dispositivos de folha downstream e um dispositivo IoT Edge, entre módulos em um dispositivo IoT Edge e entre um dispositivo IoT Edge e a nuvem.

Para obter mais informações, consulte [Azure IOT Edge dispositivos e IOT central](concepts-architecture.md#azure-iot-edge-devices).

## <a name="stay-connected"></a>Mantenha-se ligado

IoT Central aplicativos são totalmente hospedados pela Microsoft, o que reduz a sobrecarga de administração do gerenciamento de seus aplicativos.

Como um operador, você usa o aplicativo IoT Central para gerenciar os dispositivos em sua solução de IoT Central. Os operadores executam tarefas como:

- Monitorizar os dispositivos ligados à aplicação.
- Resolver e remediar problemas dos dispositivos.
- Aprovisionar novos dispositivos.

Como um construtor de soluções, você pode definir regras e ações personalizadas que operam sobre o streaming de dados de dispositivos conectados. Um operador pode ativar ou desativar estas regras ao nível do dispositivo, para controlar e automatizar tarefas na aplicação.

Os administradores gerenciam o acesso ao seu aplicativo com [funções e permissões de usuário](howto-administer.md).

Com qualquer solução IoT projetada para funcionar em escala, é importante uma abordagem estruturada para a gestão do dispositivo. Não basta apenas ligar os seus dispositivos à nuvem, é necessário manter os seus dispositivos ligados e saudáveis. Um operador pode utilizar as seguintes capacidades IoT Central para gerir os seus dispositivos ao longo do ciclo de vida da aplicação:

### <a name="dashboards"></a>Dashboards 

Os [tabliers incorporados](./howto-set-up-template.md#generate-default-views) fornecem um UI personalizável para monitorizar a saúde e a telemetria do dispositivo. Comece com um dashboard pré-construído num modelo de [aplicação](howto-use-app-templates.md) ou crie os seus próprios dashboards adaptados às necessidades dos seus operadores. Pode partilhar dashboards com todos os utilizadores da sua aplicação ou mantê-los privados.

### <a name="rules-and-actions"></a>Regras e ações 

Construa [regras personalizadas](tutorial-create-telemetry-rules.md) baseadas no estado do dispositivo e telemetria para identificar dispositivos que precisam de atenção. Configure as ações para notificar as pessoas certas e garantir que as medidas corretivas são tomadas em tempo útil.

### <a name="jobs"></a>Tarefas 

[Os trabalhos](howto-run-a-job.md) permitem-lhe aplicar atualizações individuais ou a granel aos dispositivos, definindo propriedades ou comandos de chamada. 

### <a name="user-roles-and-permissions"></a>Funções e permissões de usuário

[Funções e permissões](howto-manage-users-roles.md) permitem que um administrador adapte a experiência de cada utilizador. Um administrador usa a UI web para criar funções e atribuir permissões.

## <a name="transform-your-iot-data"></a>Transforme os seus dados ioT

Como plataforma de aplicação, a IoT Central permite-lhe transformar os seus dados IoT nos insights de negócio que impulsionam resultados atuais. [Regras,](./tutorial-create-telemetry-rules.md) [exportação](./howto-export-data.md)de dados, e a [API do REST público](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) são exemplos de como pode integrar a IoT Central com aplicações de linha de negócio:

![Como a IoT Central pode transformar os seus dados ioT](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>Monitorar a integridade e as operações do dispositivo usando regras

Quando os seus dispositivos estão conectados e enviam dados, as regras podem identificar dispositivos que sofrem de problemas ou enviar mensagens de erro para que possa corrigi-los com o mínimo de tempo de inatividade. Construa regras na sua aplicação IoT Central para monitorizar a telemetria dos seus dispositivos e alerte um operador quando uma métrica atravessa um limiar ou um dispositivo envia uma mensagem específica. As ações de e-mail e webhooks para as suas regras notificam as pessoas certas e os sistemas a jusante.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Executar análise personalizada e processamento em seus dados exportados

Pode gerar insights de negócio, tais como determinar tendências de eficiência da máquina ou prever o uso futuro de energia num piso de fábrica, construindo oleodutos de análise personalizada para processar a telemetria a partir dos seus dispositivos e armazenar os resultados. Configure as exportações de dados na sua aplicação IoT Central para exportar telemetria, alterações na propriedade do dispositivo e alterações no modelo do dispositivo para outros serviços onde pode analisar, armazenar e visualizar os dados com as suas ferramentas preferidas.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Construa soluções e integrações ioT personalizadas com as APIs rest

Construir soluções IoT tais como:

- Aplicativos de acompanhantes móveis que podem configurar e controlar remotamente dispositivos.
- Integrações personalizadas que permitem às aplicações de linha de negócio existentes interagir com os seus dispositivos e dados IoT.
- Aplicações de gestão de dispositivos para modelação de dispositivos, embarque, gestão e acesso a dados.

## <a name="quotas"></a>Quotas

Cada assinatura do Azure tem cotas padrão que podem afetar o escopo da sua solução de IoT. Atualmente, IoT Central limita o número de aplicativos que você pode implantar em uma assinatura para 10. Se você precisar aumentar esse limite, entre em contato com o [suporte da Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passos seguintes

Agora que você tem uma visão geral de IoT Central, aqui estão as próximas etapas sugeridas:

- Entenda as [tecnologias e os serviços do Azure disponíveis para a criação de soluções de IOT](../../iot-fundamentals/iot-services-and-technologies.md).
- Familiarizar-se com a [IU do Azure IoT Central](overview-iot-central-tour.md).
- Começar por [criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).
- Saiba mais sobre [ioT Plug e Play (pré-visualização)](../../iot-pnp/overview-iot-plug-and-play.md).
- Saiba como [ligar um dispositivo Azure IoT Edge](./tutorial-add-edge-as-leaf-device.md).
- Saiba mais sobre [tecnologias e serviços Azure IoT.](../../iot-fundamentals/iot-services-and-technologies.md)
