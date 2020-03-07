---
title: Conceitos arquitetónicos em Azure IoT Central [ Central De Azure IoT ] Microsoft Docs
description: Este artigo introduz conceitos-chave relativos à arquitetura da Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 12ad231d81b6c134ebb8d4902b3f95c978e9622d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386223"
---
# <a name="azure-iot-central-architecture"></a>Arquitetura do Azure IoT Central



Este artigo fornece uma visão geral da arquitetura Microsoft Azure IoT Central.

![Arquitetura de alto nível](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Dispositivos

Os dispositivos trocam dados com a sua aplicação Azure IoT Central. Um dispositivo pode:

- Envie medições como telemetria.
- Sincronizar as definições com a sua aplicação.

No Azure IoT Central, os dados que um dispositivo pode trocar com a sua aplicação são especificados num modelo de dispositivo. Para obter mais informações sobre os modelos do dispositivo, consulte [a gestão de Metadados.](#metadata-management)

Para saber mais sobre como os dispositivos se ligam à sua aplicação Central Azure IoT, consulte a [conectividade do Dispositivo](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Dispositivos do IoT Edge do Azure

Além de dispositivos criados com os [SDKs Azure IoT,](https://github.com/Azure/azure-iot-sdks)também pode ligar [os dispositivos Azure IoT Edge](../../iot-edge/about-iot-edge.md) a uma aplicação IoT Central. O IoT Edge permite-lhe executar a inteligência em nuvem e a lógica personalizada diretamente em dispositivos IoT geridos pela IoT Central. O tempo de execução do IoT Edge permite::

- Instale e atualize as cargas de trabalho no dispositivo.
- Mantenha os padrões de segurança ioT Edge no dispositivo.
- Certifique-se de que os módulos IoT Edge estão sempre em funcionamento.
- Informe a saúde do módulo na nuvem para monitorização remota.
- Gerencie a comunicação entre dispositivos de folhas a jusante e um dispositivo IoT Edge, entre módulos num dispositivo IoT Edge, e entre um dispositivo IoT Edge e a nuvem.

![Central Azure IoT com borda azure ioT](./media/concepts-architecture/iotedge.png)

A IoT Central permite as seguintes capacidades para dispositivos IoT Edge:

- Modelos de dispositivo para descrever as capacidades de um dispositivo IoT Edge, tais como:
  - Capacidade de upload manifesto de implantação, o que o ajuda a gerir um manifesto para uma frota de dispositivos.
  - Módulos que funcionam no dispositivo IoT Edge.
  - A telemetria que cada módulo envia.
  - As propriedades que cada módulo relata.
  - Os comandos a que cada módulo responde.
  - As relações entre um modelo de capacidade de dispositivo de gateway IoT Edge e o modelo de capacidade de dispositivo a jusante.
  - Propriedades em nuvem que não estão armazenadas no dispositivo IoT Edge.
  - Personalizações, dashboards e formas que fazem parte da sua aplicação IoT Central.

  Para mais informações, consulte os [dispositivos Connect Azure IoT Edge para um artigo de aplicação Azure IoT Central.](./concepts-iot-edge.md)

- A capacidade de fornecer dispositivos IoT Edge em escala utilizando o serviço de provisionamento de dispositivos Azure IoT
- Regras e ações.
- Dashboards personalizados e análises.
- Exportação contínua de dados de telemetria a partir de dispositivos IoT Edge.

### <a name="iot-edge-device-types"></a>Tipos de dispositivos IoT Edge

IoT Central classifica os tipos de dispositivos IoT Edge da seguinte forma:

- Dispositivos de folhas. Um dispositivo IoT Edge pode ter dispositivos de folhas a jusante, mas estes dispositivos não estão aprovisionados na IoT Central.
- Dispositivos gateway com dispositivos a jusante. Tanto o dispositivo gateway como os dispositivos a jusante são aprovisionados na IoT Central

![IoT Central com visão geral da borda iot](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>Padrões de borda iot

A IoT Central suporta os seguintes padrões de dispositivo IoT Edge:

#### <a name="iot-edge-as-leaf-device"></a>Borda ioT como dispositivo de folha

![Borda ioT como dispositivo de folha](./media/concepts-architecture/edgeasleafdevice.png)

O dispositivo IoT Edge é aprovisionado em IoT Central e quaisquer dispositivos a jusante e a sua telemetria está representada como proveniente do dispositivo IoT Edge. Os dispositivos a jusante ligados ao dispositivo IoT Edge não estão provisionados na IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>Dispositivo de gateway IoT Edge ligado a dispositivos a jusante com identidade

![Borda IoT com identidade de dispositivo a jusante](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

O dispositivo IoT Edge é aprovisionado na IoT Central juntamente com os dispositivos a jusante ligados ao dispositivo IoT Edge. O suporte de tempo de execução para o fornecimento de dispositivos a jusante através do portal não é suportado atualmente.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>Dispositivo de gateway IoT Edge ligado a dispositivos a jusante com identidade fornecida pelo gateway IoT Edge

![IoT Edge com dispositivo a jusante sem identidade](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

O dispositivo IoT Edge é aprovisionado na IoT Central juntamente com os dispositivos a jusante ligados ao dispositivo IoT Edge. O suporte de tempo de execução do gateway que fornece identidade a dispositivos a jusante e o fornecimento de dispositivos a jusante não é atualmente suportado. Se trouxer o seu próprio módulo de tradução de identidade, a IoT Central pode suportar este padrão.

## <a name="cloud-gateway"></a>Porta de entrada de nuvem

O Azure IoT Central usa o Azure IoT Hub como um portal de cloud que permite a conectividade do dispositivo. O IoT Hub permite:

- Ingestão de dados à escala na nuvem.
- Gestão de dispositivos.
- Conectividade segura do dispositivo.

Para saber mais sobre o Hub IoT, consulte [o Hub Azure IoT.](https://docs.microsoft.com/azure/iot-hub/)

Para saber mais sobre a conectividade do dispositivo no Azure IoT Central, consulte [a conectividade do Dispositivo.](concepts-get-connected.md)

## <a name="data-stores"></a>Lojas de dados

A Azure IoT Central armazena dados de aplicações na nuvem. Os dados da aplicação armazenados incluem:

- Modelos de dispositivo.
- Identidades do dispositivo.
- Metadados do dispositivo.
- Dados de utilizador e de função.

A Azure IoT Central utiliza uma loja de séries temporais para os dados de medição enviados dos seus dispositivos. Dados da série time dos dispositivos utilizados pelo serviço de análise.

## <a name="analytics"></a>Análise

O serviço de análise é responsável por gerar os dados de reporte personalizados que a aplicação exibe. Um operador pode [personalizar a análise](howto-create-analytics.md) exibida na aplicação. O serviço de análise é construído em cima de Insights da [Série De Tempo Azure](https://azure.microsoft.com/services/time-series-insights/) e processa os dados de medição enviados dos seus dispositivos.

## <a name="rules-and-actions"></a>Regras e ações

[Regras e ações](tutorial-create-telemetry-rules.md) trabalham em estreita colaboração para automatizar tarefas dentro da aplicação. Um construtor pode definir regras baseadas na telemetria do dispositivo, como a temperatura que excede um limiar definido. A Azure IoT Central utiliza um processador de fluxo para determinar quando as condições de regra são satisfeitas. Quando uma condição de regra é satisfeita, desencadeia uma ação definida pelo construtor. Por exemplo, uma ação pode enviar um e-mail para notificar um engenheiro que a temperatura num dispositivo é muito alta.

## <a name="metadata-management"></a>Gestão de metadados

Numa aplicação Central Azure IoT, os modelos do dispositivo definem o comportamento e a capacidade dos tipos de dispositivo. Por exemplo, um modelo de dispositivo frigorífico especifica a telemetria que um frigorífico envia para a sua aplicação.

![Arquitetura de modelo](media/concepts-architecture/template-architecture.png)

Num modelo de dispositivo de aplicação IoT Central contém:

- **Os modelos** de capacidade do dispositivo especificam as capacidades de um dispositivo como a telemetria que envia, as propriedades que definem o estado do dispositivo, e os comandos a que o dispositivo responde. As capacidades do dispositivo são organizadas em uma ou mais interfaces. Para obter mais informações sobre os modelos de capacidade do dispositivo, consulte a documentação [IoT Plug and Play (pré-visualização).](../../iot-pnp/overview-iot-plug-and-play.md)
- **As propriedades da nuvem** especificam as propriedades IoT Central lojas para um dispositivo. Estas propriedades são armazenadas apenas na IoT Central e nunca são enviadas para um dispositivo.
- **As vistas** especificam os dashboards e formam o construtor que o construtor cria para permitir que o operador monitorize e gere os dispositivos.
- **As personalizações** permitem ao construtor anular algumas das definições no modelo de capacidade do dispositivo para torná-las mais relevantes para a aplicação IoT Central.

Uma aplicação pode ter um ou mais dispositivos simulados e reais baseados em cada modelo de dispositivo.

## <a name="data-export"></a>Exportação de dados

Numa aplicação Azure IoT Central, pode [exportar continuamente os seus dados](howto-export-data.md) para os seus próprios Hubs de Eventos Azure e para as instâncias do Azure Service Bus. Também pode exportar periodicamente os seus dados para a sua conta de armazenamento Azure Blob. A IoT Central pode exportar medições, dispositivos e modelos de dispositivos.

## <a name="batch-device-updates"></a>Atualizações de dispositivos de lote

Numa aplicação Azure IoT Central, pode [criar e executar trabalhos](howto-run-a-job.md) para gerir dispositivos conectados. Estes trabalhos permitem-lhe fazer atualizações a granel para propriedades ou configurações do dispositivo, ou executar comandos. Por exemplo, pode criar um trabalho para aumentar a velocidade do ventilador para várias máquinas de venda automática refrigeradas.

## <a name="role-based-access-control-rbac"></a>Controlo de acesso baseado em funções (RBAC)

Um [administrador pode definir regras](howto-manage-users-roles.md) de acesso para uma aplicação Azure IoT Central utilizando uma das funções predefinidas ou criando uma função personalizada. As funções determinam quais as áreas da aplicação a que um utilizador tem acesso e quais as ações que pode executar.

## <a name="security"></a>Segurança

As funcionalidades de segurança dentro do Azure IoT Central incluem:

- Os dados são encriptados em trânsito e em repouso.
- A autenticação é fornecida quer pelo Diretório Ativo azure quer pela Conta Microsoft. A autenticação de dois fatores é suportada.
- Isolamento total de inquilinos.
- Segurança do nível do dispositivo.

## <a name="ui-shell"></a>Concha ui

A concha UI é uma aplicação moderna, responsiva e baseada no navegador HTML5.
Um administrador pode personalizar o UI da aplicação aplicando temas personalizados e modificando os links de ajuda para apontar para os seus próprios recursos de ajuda personalizada. Para saber mais sobre a personalização da UI, consulte Customize o artigo [Da UI Central Azure IoT.](howto-customize-ui.md)

Um operador pode criar dashboards de aplicação personalizados. Pode ter vários dashboards que exibem diferentes dados e alternam entre eles.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre a arquitetura da Azure IoT Central, o próximo passo sugerido é aprender sobre a conectividade do [dispositivo](concepts-get-connected.md) na Azure IoT Central.
