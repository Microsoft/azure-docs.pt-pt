---
title: Conceitos de arquitetura no Azure IoT Central | Microsoft Docs
description: Este artigo apresenta os principais conceitos relacionados à arquitetura do Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3dacea5c1d77bfead85e42ae312bfafbe70d8ca1
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990384"
---
# <a name="azure-iot-central-architecture-preview-features"></a>Arquitetura de IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este artigo fornece uma visão geral da arquitetura do Microsoft Azure IoT Central.

![Arquitetura de nível superior](media/concepts-architecture-pnp/architecture.png)

## <a name="devices"></a>Dispositivos

Os dispositivos trocam dados com o aplicativo IoT Central do Azure. Um dispositivo pode:

- Envie medidas como telemetria.
- Sincronize as configurações com seu aplicativo.

No Azure IoT Central, os dados que um dispositivo pode trocar com seu aplicativo são especificados em um modelo de dispositivo. Para obter mais informações sobre modelos de dispositivo, consulte [Gerenciamento de metadados](#metadata-management).

Para saber mais sobre como os dispositivos se conectam ao aplicativo IoT Central do Azure, confira [conectividade do dispositivo](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="cloud-gateway"></a>Gateway de nuvem

O Azure IoT Central usa o Hub IoT do Azure como um gateway de nuvem que habilita a conectividade do dispositivo. O Hub IoT permite:

- Ingestão de dados em escala na nuvem.
- Gerenciamento de dispositivo.
- Conectividade de dispositivo segura.

Para saber mais sobre o Hub IoT, confira [Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub/).

Para saber mais sobre a conectividade do dispositivo no IoT Central do Azure, confira [conectividade do dispositivo](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="data-stores"></a>Armazenamentos de dados

O Azure IoT Central armazena dados de aplicativo na nuvem. Os dados de aplicativo armazenados incluem:

- Modelos de dispositivo.
- Identidades de dispositivo.
- Metadados do dispositivo.
- Dados de usuário e função.

O Azure IoT Central usa um repositório de série temporal para os dados de medição enviados de seus dispositivos. Dados de série temporal de dispositivos usados pelo serviço de análise.

## <a name="analytics"></a>Análise

O serviço de análise é responsável por gerar os dados de relatório personalizados que o aplicativo exibe. Um operador pode [Personalizar a análise](howto-create-analytics.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) exibida no aplicativo. O serviço de análise é criado com base em [Azure Time Series insights](https://azure.microsoft.com/services/time-series-insights/) e processa os dados de medição enviados de seus dispositivos.

## <a name="rules-and-actions"></a>Regras e ações

[Regras e ações](howto-create-telemetry-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) trabalham juntas para automatizar tarefas dentro do aplicativo. Um construtor pode definir regras com base na telemetria do dispositivo, como a temperatura que excede um limite definido. O Azure IoT Central usa um processador de fluxo para determinar quando as condições de regra são atendidas. Quando uma condição de regra é atendida, ela dispara uma ação definida pelo construtor. Por exemplo, uma ação pode enviar um email para notificar um engenheiro de que a temperatura em um dispositivo é muito alta.

## <a name="metadata-management"></a>Gerenciamento de metadados

Em um aplicativo IoT Central do Azure, os modelos de dispositivo definem o comportamento e a capacidade de tipos de dispositivo. Por exemplo, um modelo de dispositivo refrigerador especifica a telemetria que um refrigerador envia para seu aplicativo.

![Arquitetura do modelo](media/concepts-architecture-pnp/template-architecture.png)

Em um modelo de dispositivo de aplicativo IoT Central Preview:

- Os **modelos de capacidade de dispositivo** especificam os recursos de um dispositivo, como a telemetria que ele envia, as propriedades que definem o estado do dispositivo e os comandos que o dispositivo responde. Os recursos do dispositivo são organizados em uma ou mais interfaces. Para obter mais informações sobre modelos de capacidade de dispositivo, consulte a documentação do [plug and Play IOT](../../iot-pnp/overview-iot-plug-and-play.md) .
- **Propriedades de nuvem** especifique as propriedades IOT central repositórios para um dispositivo. Essas propriedades são armazenadas somente em IoT Central e nunca são enviadas a um dispositivo.
- As **exibições** especificam os painéis e formulários criados pelo construtor para permitir que o operador monitore e gerencie os dispositivos.
- As **personalizações** permitem que o Construtor substitua algumas das definições no modelo de funcionalidade do dispositivo para torná-las mais relevantes para o aplicativo IOT central.

Um aplicativo pode ter um ou mais dispositivos simulados e reais com base em cada modelo de dispositivo.

## <a name="data-export"></a>Exportação de dados

Em um aplicativo IoT Central do Azure, você pode [Exportar continuamente seus dados](howto-export-data-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) para seus próprios hubs de eventos do Azure e instâncias do barramento de serviço do Azure. Você também pode exportar periodicamente seus dados para sua conta de armazenamento de BLOBs do Azure. IoT Central pode exportar medidas, dispositivos e modelos de dispositivo.

## <a name="batch-device-updates"></a>Atualizações de dispositivo do lote

Em um aplicativo IoT Central do Azure, você pode [criar e executar trabalhos](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) para gerenciar dispositivos conectados. Esses trabalhos permitem que você faça atualizações em massa nas propriedades ou configurações do dispositivo ou execute comandos. Por exemplo, você pode criar um trabalho para aumentar a velocidade do ventilador para várias máquinas de venda com refrigeração.

## <a name="role-based-access-control-rbac"></a>Controlo de acesso baseado em funções (RBAC)

Um [administrador pode definir regras de acesso](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) para um aplicativo de IOT central do Azure usando as funções predefinidas. Um administrador pode atribuir usuários a funções que determinam quais áreas do aplicativo o usuário tem acesso.

## <a name="security"></a>Segurança

Os recursos de segurança do Azure IoT Central incluem:

- Os dados são criptografados em trânsito e em repouso.
- A autenticação é fornecida por Azure Active Directory ou por conta da Microsoft. Há suporte para a autenticação de dois fatores.
- Isolamento de locatário completo.
- Segurança no nível do dispositivo.

## <a name="ui-shell"></a>Shell de interface do usuário

O Shell de interface do usuário é um aplicativo moderno, responsivo e baseado em navegador HTML5.
Um administrador pode personalizar a interface do usuário do aplicativo aplicando temas personalizados e modificando os links da ajuda para apontar para seus próprios recursos de ajuda personalizados. Para saber mais sobre a personalização da interface do usuário, consulte [o artigo personalizar a interface do usuário IOT central do Azure](howto-customize-ui.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

Um operador pode criar painéis de aplicativo personalizados. Você pode ter vários painéis que exibem dados diferentes e alternam entre eles.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre a arquitetura do Azure IoT Central, a próxima etapa sugerida é saber mais sobre a [conectividade do dispositivo](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) na IOT central do Azure.