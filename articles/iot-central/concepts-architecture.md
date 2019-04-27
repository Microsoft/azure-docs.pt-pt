---
title: Conceitos de arquitetura no Azure IoT Central | Documentos da Microsoft
description: Este artigo apresenta os principais conceitos relacionados a arquitetura do Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 4f4b917808f4973dc83294391f58d7e0e2d01c4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60887423"
---
# <a name="azure-iot-central-architecture"></a>Arquitetura do Centro de IoT do Azure

Este artigo fornece uma descrição geral da arquitetura do Microsoft Azure IoT Central.

![Arquitetura de nível superior](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Dispositivos

Dispositivos trocam dados com a sua aplicação do Azure IoT Central. Um dispositivo pode:

- Envie medidas como telemetria.
- Sincronize as definições com a sua aplicação.

No Azure IoT Central, os dados que um dispositivo pode trocar com a sua aplicação são especificados num modelo de dispositivo. Para obter mais informações sobre modelos de dispositivos, consulte [gestão de metadados](#metadata-management).

Para saber mais sobre como os dispositivos se ligam à sua aplicação do Azure IoT Central, consulte [conectividade do dispositivo](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Gateway de nuvem

Centro de IoT do Azure utiliza o IoT Hub do Azure como um gateway de cloud que permite a conectividade do dispositivo. O IoT Hub permite:

- Ingestão de dados à escala na cloud.
- Gestão de dispositivos.
- Proteja a conectividade do dispositivo.

Para saber mais sobre o IoT Hub, veja [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/).

Para saber mais sobre a conectividade do dispositivo no Azure IoT Central, consulte [conectividade do dispositivo](concepts-connectivity.md).

## <a name="data-stores"></a>Arquivos de dados

Centro de IoT do Azure armazena dados da aplicação na cloud. Dados armazenados do aplicativo incluem:

- Modelos de dispositivos.
- Identidades do dispositivo.
- Metadados do dispositivo.
- Dados de utilizador e da função.

Centro de IoT do Azure utiliza um tempo série armazenar para os dados de medição enviados a partir dos seus dispositivos. Dados de séries de tempo de dispositivos utilizados pelo serviço de análise.

## <a name="analytics"></a>Análise

O serviço de análise é responsável por gerar os dados de relatório personalizados que exibe o aplicativo. Um operador pode [personalizar a análise](howto-create-analytics.md) apresentada na aplicação. O serviço de análise é embutido no início da [do Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) e processa os dados de medição enviados a partir dos seus dispositivos.

## <a name="rules-and-actions"></a>Regras e ações

[Regras e ações](howto-create-telemetry-rules.md) trabalho estrita cooperação para automatizar tarefas no aplicativo. Um construtor pode definir regras com base na telemetria do dispositivo, como a temperatura exceder um limiar definido. Centro de IoT do Azure utiliza um processador de fluxo para determinar quando estiverem reunidas as condições de regra. Quando for cumprida uma condição de regra, aciona uma ação definida pelo construtor. Por exemplo, uma ação pode enviar um e-mail para notificar um engenheiro de que a temperatura num dispositivo é demasiado elevada.

## <a name="metadata-management"></a>Gestão de metadados

Num aplicativo do Azure IoT Central, os modelos de dispositivo definem o comportamento e a capacidade dos tipos de dispositivo. Por exemplo, um modelo de dispositivo refrigerator (refrigerador) Especifica a telemetria de que um refrigerador envia para a sua aplicação.

![Arquitetura do modelo](media/concepts-architecture/template_architecture.png)

Num modelo de dispositivo:

- **Medidas** especificar a telemetria do dispositivo envia para a aplicação.
- **Definições** especificar as configurações que pode definir um operador.
- **Propriedades** especificar metadados que pode definir um operador.
- **Regras** automatizar o comportamento do aplicativo com base nos dados enviados a partir de um dispositivo.
- **Dashboards** são personalizáveis modos de exibição de um dispositivo na aplicação.

Um aplicativo pode ter um ou mais dispositivos simulados e real com base em cada modelo de dispositivo.

## <a name="role-based-access-control-rbac"></a>Controlo de acesso baseado em funções (RBAC)

Uma [administrador pode definir regras de acesso](howto-administer.md) para uma aplicação do Azure IoT Central usando as funções predefinidas. Um administrador pode atribuir utilizadores a funções que determinam quais áreas do aplicativo o utilizador tem acesso.

## <a name="security"></a>Segurança

Funcionalidades de segurança no Azure IoT Central incluem:

- Dados são encriptados em trânsito e em inatividade.
- Autenticação é fornecida ao Azure Active Directory ou Account Microsoft. Autenticação de dois fatores é suportada.
- Isolamento de inquilino completo.
- Segurança ao nível do dispositivo.

## <a name="ui-shell"></a>Shell de interface do Usuário

O shell de interface do Usuário é um moderno, capacidade de resposta, HTML5 baseada no browser e aplicativo.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu sobre a arquitetura do Azure IoT Central, a próxima etapa sugerida é saber mais sobre [conectividade do dispositivo](concepts-connectivity.md) no Azure IoT Central.