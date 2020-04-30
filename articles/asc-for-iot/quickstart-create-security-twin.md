---
title: 'Quickstart: Criar um módulo de segurança twin'
description: Neste arranque rápido, aprenda a criar um Azure Security Center para o módulo IoT twin para utilização com o Azure Security Center para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: e9c4470ea16f1840274a6fa8613822b20d6772b3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310869"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Quickstart: Criar um módulo de segurança azul twin

Este quickstart explica como criar gémeos individuais de módulos _de azureiotsecurity_ para novos dispositivos, ou criar gémeos de módulos para todos os dispositivos num Hub IoT.

## <a name="understanding-azureiotsecurity-module-twins"></a>Compreender os gémeos do módulo de segurança azureiot

Para soluções IoT construídas em Azure, os gémeos dispositivos desempenham um papel fundamental tanto na gestão de dispositivos como na automatização de processos.

O Azure Security Center for IoT oferece total integração com a sua plataforma de gestão de dispositivos IoT existente, permitindo-lhe gerir o estado de segurança do seu dispositivo, bem como utilizar as capacidades de controlo de dispositivos existentes.
O Azure Security Center for IoT integration é conseguido através da utilização do mecanismo de gémeos IoT Hub.

Consulte os [gémeos do módulo IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito geral de gémeos módulo sinos no Hub Azure IoT.

O Azure Security Center for IoT utiliza o mecanismo de módulo sino gémeo e mantém um módulo de segurança twin chamado _azureiotsecurity_ para cada um dos seus dispositivos.

O módulo de segurança twin detém todas as informações relevantes para a segurança do dispositivo para cada um dos seus dispositivos.

Para utilizar plenamente o Azure Security Center para funcionalidades IoT, terá de criar, configurar e utilizar estes gémeos de módulos de segurança para todos os dispositivos do serviço.

## <a name="create-azureiotsecurity-module-twin"></a>Criar módulo de segurança azul twin

Os gémeos do módulo _de segurança azul_ podem ser criados de duas formas:

1. [Script de lote](https://aka.ms/iot-security-github-create-module) de módulo - cria automaticamente o módulo twin para novos dispositivos ou dispositivos sem um módulo twin usando a configuração padrão.
1. Editar manualmente cada módulo gémeo individualmente com configurações específicas para cada dispositivo.

>[!NOTE]
> A utilização do método do lote não substituirá os gémeos existentes do módulo de azureiotsecurity. Utilização do método do lote APENAS cria novos módulos gémeos para dispositivos que ainda não têm um módulo de segurança twin.

Consulte a [configuração](how-to-agent-configuration.md) do agente para aprender a modificar ou alterar a configuração de um módulo gémeo existente.

Para criar manualmente um novo módulo de _segurança azul_ para um dispositivo, utilize as seguintes instruções:

1. No seu IoT Hub, localize e selecione o dispositivo para o quais pretende criar um módulo de segurança twin.
1. Clique no seu dispositivo e, em seguida, em **Adicionar identidade**do módulo .
1. No campo Nome de Identidade do **Módulo,** **introduza a segurança em azureiotsecurity**.

1. Clique em **Guardar**.

## <a name="verify-creation-of-a-module-twin"></a>Verifique a criação de um módulo twin

Para verificar se existe um módulo de segurança twin para um dispositivo específico:

1. No seu Hub Azure IoT, selecione **dispositivos IoT** do menu **Explorers.**
1. Introduza o ID do dispositivo ou selecione uma opção no campo do **dispositivo Consulta** e clique em **dispositivos De consulta**.
    ![Dispositivos de consulta](./media/quickstart/verify-security-module-twin.png)
1. Selecione o dispositivo ou clique duas vezes nele para abrir a página de detalhes do Dispositivo.
1. Selecione o menu de **identidades do Módulo** e confirme a existência do módulo de **segurança azureiotna** na lista de identidades do módulo associadas ao dispositivo.
    ![Módulos associados a um dispositivo](./media/quickstart/verify-security-module-twin-3.png)

Para saber mais sobre personalizar propriedades do Azure Security Center para gémeos móduloio IoT, consulte a [configuração do Agente](how-to-agent-configuration.md).

## <a name="next-steps"></a>Passos seguintes

Avançar para o próximo artigo para aprender a configurar alertas personalizados...

> [!div class="nextstepaction"]
> [Configurar alertas personalizados](quickstart-create-custom-alerts.md)
