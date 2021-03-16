---
title: 'Quickstart: Criar um módulo de segurança twin'
description: Neste arranque rápido, aprenda a criar um Duplo Desíduo para IoT para utilização com Azure Defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/21/2021
ms.author: shhazam
ms.openlocfilehash: cfd5192a78c34caf5acbe4576f5a00ab314acb61
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489901"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Quickstart: Criar um módulo de azureiotsecurity twin

Este quickstart explica como criar gémeos individuais de módulos _de azureiotsecurity_ para novos dispositivos, ou criar módulos gémeos para todos os dispositivos num Hub IoT.

## <a name="prerequisites"></a>Pré-requisitos

Nenhum

## <a name="understanding-azureiotsecurity-module-twins"></a>Compreender gémeos módulos de azureiotsegurança

Para as soluções IoT construídas em Azure, os gémeos de dispositivo desempenham um papel fundamental tanto na gestão do dispositivo como na automatização de processos.

O Defender for IoT oferece uma integração completa com a sua plataforma de gestão de dispositivos IoT existente, permitindo-lhe gerir o estado de segurança do dispositivo e utilizar as capacidades de controlo do dispositivo existentes.
O Defender para a integração IoT é conseguido utilizando o mecanismo gémeo IoT Hub.

Consulte [os gémeos módulos IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md) para saber mais sobre o conceito geral de gémeos módulos no Azure IoT Hub.

O Defender for IoT utiliza o mecanismo twin do módulo e mantém um módulo de segurança twin nomeado _azureiotsecurity_ para cada um dos seus dispositivos.

O Gémeo Defender-IoT-micro-agente contém todas as informações relevantes para a segurança do dispositivo para cada um dos seus dispositivos.

Para utilizar plenamente as funcionalidades do Defender para ioT, terá de criar, configurar e utilizar estes gémeos Defender-IoT-micro-agente para todos os dispositivos do serviço.

## <a name="create-azureiotsecurity-module-twin"></a>Criar módulo de azureiotsecurity twin

os gémeos _módulos de azureiotsecurity_ podem ser criados de duas formas:

1. [Script de lote de módulo](https://aka.ms/iot-security-github-create-module) - cria automaticamente o módulo twin para novos dispositivos ou dispositivos sem um módulo gémeo utilizando a configuração padrão.
1. Editar manualmente cada módulo gémeo individualmente com configurações específicas para cada dispositivo.

>[!NOTE]
> A utilização do método do lote não substituirá os gémeos módulos de azureiotsegurança existentes. A utilização do método do lote APENAs cria novos módulos gémeos para dispositivos que ainda não têm um módulo de segurança gémeo.

Consulte a [configuração do agente](how-to-agent-configuration.md) para aprender a modificar ou alterar a configuração de um módulo gémeo existente.

Para criar manualmente um novo módulo _de insegurança azureiot para_ um dispositivo:

1. No seu Hub IoT, localize e selecione o dispositivo para o quais deseja criar um módulo de segurança twin.

1. Selecione no seu dispositivo e, em seguida, em **Adicionar identidade do módulo**.

1. No campo Nome de Identidade do **Módulo,** insira **a azureiotsecurity**.

1. Selecione **Guardar**.

## <a name="verify-creation-of-a-module-twin"></a>Verifique a criação de um módulo gémeo

Para verificar se existe um módulo de segurança gémeo para um dispositivo específico:

1. No seu Azure IoT Hub, selecione **dispositivos IoT** do menu **Explorers.**

1. Introduza o ID do dispositivo ou selecione uma opção no campo do **dispositivo de consulta** e selecione **dispositivos de consulta**.

    :::image type="content" source="./media/quickstart/verify-security-module-twin.png" alt-text="Dispositivos de consulta":::

1. Selecione o dispositivo ou selecione-o duas vezes para abrir a página de detalhes do Dispositivo.

1. Selecione o menu **de identidades do Módulo** e confirme a existência do módulo de insegurança **azureiot** na lista de identidades do módulo associadas ao dispositivo.

    :::image type="content" source="./media/quickstart/verify-security-module-twin-3.png" alt-text="Módulos associados a um dispositivo":::

Para saber mais sobre a personalização das propriedades do Defender para gémeos módulos IoT, consulte [a configuração do Agente](how-to-agent-configuration.md).

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a investigar recomendações de segurança...

> [!div class="nextstepaction"]
> [Investigar recomendações de segurança](quickstart-investigate-security-recommendations.md)