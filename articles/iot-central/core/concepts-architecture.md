---
title: Conceitos arquitetônicos em Azure IoT Central | Microsoft Docs
description: Este artigo introduz conceitos-chave relacionados com a arquitetura da Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: bcda4ca252101ed1505f71a1b5f9fe9a0d8d16b9
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728397"
---
# <a name="azure-iot-central-architecture"></a>Arquitetura do Azure IoT Central

Este artigo fornece uma visão geral dos conceitos-chave na arquitetura Azure IoT Central.

## <a name="devices"></a>Dispositivos

Os dispositivos trocam dados com a sua aplicação Azure IoT Central. Um dispositivo pode:

- Envie medições como telemetria.
- Sincronizar as definições com a sua aplicação.

No Azure IoT Central, os dados que um dispositivo pode trocar com a aplicação são especificados num modelo de dispositivo. Para obter mais informações sobre os modelos do dispositivo, consulte [os modelos do dispositivo](concepts-device-templates.md).

Para saber mais sobre como os dispositivos se ligam à sua aplicação Azure IoT Central, consulte [a conectividade do dispositivo](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Dispositivos Azure IoT Edge

Além de dispositivos criados com os [SDKs Azure IoT,](https://github.com/Azure/azure-iot-sdks)também pode ligar [dispositivos Azure IoT Edge](../../iot-edge/about-iot-edge.md) a uma aplicação IoT Central. O IoT Edge permite-lhe executar a inteligência em nuvem e a lógica personalizada diretamente em dispositivos IoT geridos pela IoT Central. O tempo de execução IoT Edge permite-lhe:

- Instale e atualize cargas de trabalho no dispositivo.
- Mantenha os padrões de segurança IoT Edge no dispositivo.
- Certifique-se de que os módulos IoT Edge estão sempre a funcionar.
- Informe a saúde do módulo na nuvem para monitorização remota.
- Gerencie a comunicação entre dispositivos de folha a jusante e um dispositivo IoT Edge, entre módulos num dispositivo IoT Edge e entre um dispositivo IoT Edge e a nuvem.

![Azure IoT Central com Azure IoT Edge](./media/concepts-architecture/iotedge.png)

A IoT Central permite as seguintes capacidades para dispositivos IoT Edge:

- Modelos de dispositivo para descrever as capacidades de um dispositivo IoT Edge, tais como:
  - Capacidade de upload manifesto de implementação, que o ajuda a gerir um manifesto para uma frota de dispositivos.
  - Módulos que funcionam no dispositivo IoT Edge.
  - A telemetria que cada módulo envia.
  - As propriedades que cada módulo reporta.
  - Os comandos a que cada módulo responde.
  - As relações entre um dispositivo de gateway IoT Edge e dispositivo a jusante.
  - Propriedades em nuvem que não estão armazenadas no dispositivo IoT Edge.
  - Personalizações, dashboards e formulários que fazem parte da sua aplicação IoT Central.

  Para obter mais informações, consulte os [dispositivos Connect Azure IoT Edge num artigo de aplicação Azure IoT Central.](./concepts-iot-edge.md)

- A capacidade de fornecimento de dispositivos IoT Edge em escala utilizando o serviço de fornecimento de dispositivos Azure IoT
- Regras e ações.
- Dashboards personalizados e análise.
- Exportação contínua de dados de telemetria a partir de dispositivos IoT Edge.

### <a name="iot-edge-device-types"></a>Tipos de dispositivos IoT Edge

A IoT Central classifica os tipos de dispositivos IoT Edge da seguinte forma:

- Dispositivos de folha. Um dispositivo IoT Edge pode ter dispositivos de folha a jusante, mas estes dispositivos não são a provisionados na IoT Central.
- Dispositivos gateway com dispositivos a jusante. Tanto o dispositivo de gateway como os dispositivos a jusante são a provisionados na IoT Central

![IoT Central com visão geral da borda IoT](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>Padrões IoT Edge

A IoT Central suporta os seguintes padrões de dispositivo IoT Edge:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge como dispositivo de folha

![IoT Edge como dispositivo de folha](./media/concepts-architecture/edgeasleafdevice.png)

O dispositivo IoT Edge está previsto na IoT Central e quaisquer dispositivos a jusante e a sua telemetria são representadas como provenientes do dispositivo IoT Edge. Os dispositivos a jusante ligados ao dispositivo IoT Edge não são a provisionados na IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>Dispositivo de gateway IoT Edge ligado a dispositivos a jusante com identidade

![IoT Edge com identidade do dispositivo a jusante](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

O dispositivo IoT Edge é a provisionado na IoT Central juntamente com os dispositivos a jusante ligados ao dispositivo IoT Edge. O suporte de tempo de execução para o fornecimento de dispositivos a jusante através do gateway não é suportado atualmente.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>Dispositivo de gateway IoT Edge ligado a dispositivos a jusante com identidade fornecida pelo gateway IoT Edge

![IoT Edge com dispositivo a jusante sem identidade](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

O dispositivo IoT Edge é a provisionado na IoT Central juntamente com os dispositivos a jusante ligados ao dispositivo IoT Edge. O suporte de tempo de execução do gateway que fornece identidade a dispositivos a jusante e o fornecimento de dispositivos a jusante não é suportado atualmente. Se você trouxer o seu próprio módulo de tradução de identidade, a IoT Central pode suportar este padrão.

## <a name="cloud-gateway"></a>Gateway de nuvens

A Azure IoT Central utiliza o Azure IoT Hub como porta de entrada em nuvem que permite a conectividade do dispositivo. O IoT Hub permite:

- Ingestão de dados em escala na nuvem.
- Gestão de dispositivos.
- Conectividade segura do dispositivo.

Para saber mais sobre o IoT Hub, consulte [o Azure IoT Hub.](../../iot-hub/index.yml)

Para saber mais sobre a conectividade do dispositivo no Azure IoT Central, consulte [a conectividade do dispositivo](concepts-get-connected.md).

## <a name="data-stores"></a>Arquivos de dados

Azure IoT Central armazena dados de aplicações na nuvem. Os dados da aplicação armazenados incluem:

- Modelos de dispositivo.
- Identidades do dispositivo.
- Metadados do dispositivo.
- Dados de utilizador e função.

A Azure IoT Central utiliza uma loja de séries de tempo para os dados de medição enviados dos seus dispositivos. Dados da série de tempo de dispositivos utilizados pelo serviço de análise.

## <a name="data-export"></a>Exportação de dados

Numa aplicação Azure IoT Central, pode [exportar continuamente os seus dados](howto-export-data.md) para os seus próprios centros de eventos Azure e para os casos de Autocarros Azure Service. Também pode exportar periodicamente os seus dados para a sua conta de armazenamento Azure Blob. O IoT Central pode exportar medições, dispositivos e modelos de dispositivos.

## <a name="batch-device-updates"></a>Atualizações de dispositivos de lote

Numa aplicação Azure IoT Central, pode [criar e executar empregos](howto-run-a-job.md) para gerir dispositivos conectados. Estes trabalhos permitem-lhe fazer atualizações a granel para propriedades ou configurações do dispositivo, ou executar comandos. Por exemplo, pode criar um trabalho para aumentar a velocidade do ventilador para várias máquinas de venda automática refrigeradas.

## <a name="role-based-access-control-rbac"></a>Controlo de acesso baseado em funções (RBAC)

Cada aplicação IoT Central tem o seu próprio sistema RBAC incorporado. Um [administrador pode definir regras](howto-manage-users-roles.md) de acesso para uma aplicação Azure IoT Central usando uma das funções predefinidas ou criando uma função personalizada. As funções determinam quais as áreas da aplicação a que um utilizador tem acesso e que ações pode realizar.

## <a name="security"></a>Segurança

As funcionalidades de segurança dentro da Azure IoT Central incluem:

- Os dados são encriptados em trânsito e em repouso.
- A autenticação é fornecida pelo Azure Ative Directory ou pela Microsoft Account. A autenticação de dois fatores é suportada.
- Isolamento total do inquilino.
- Segurança ao nível do dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre a arquitetura da Azure IoT Central, o próximo passo sugerido é aprender sobre a [conectividade do dispositivo](concepts-get-connected.md) no Azure IoT Central.