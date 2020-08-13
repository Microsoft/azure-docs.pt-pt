---
title: 'Quickstart: Criar um módulo de segurança twin'
description: Neste quickstart, aprenda a criar um Centro de Segurança Azure para módulo IoT twin para uso com O Centro de Segurança Azure para IoT.
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
ms.openlocfilehash: 7b867f5bcbbdc5deab2d67f356df69ee870c21f3
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142252"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Quickstart: Criar um módulo de azureiotsecurity twin

Este quickstart explica como criar gémeos individuais de módulos _de azureiotsecurity_ para novos dispositivos, ou criar módulos gémeos para todos os dispositivos num Hub IoT.

## <a name="understanding-azureiotsecurity-module-twins"></a>Compreender gémeos módulos de azureiotsegurança

Para as soluções IoT construídas em Azure, os gémeos de dispositivo desempenham um papel fundamental tanto na gestão do dispositivo como na automatização de processos.

O Azure Security Center for IoT oferece uma integração completa com a sua plataforma de gestão de dispositivos IoT existente, permitindo-lhe gerir o estado de segurança do dispositivo, bem como utilizar as capacidades de controlo de dispositivos existentes.
O Centro de Segurança Azure para a integração IoT é alcançado utilizando o mecanismo gémeo IoT Hub.

Consulte [os gémeos módulos IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito geral de gémeos módulos no Azure IoT Hub.

O Azure Security Center for IoT utiliza o mecanismo twin do módulo e mantém um módulo de segurança twin chamado _azureiotsecurity_ para cada um dos seus dispositivos.

O módulo de segurança twin contém todas as informações relevantes para a segurança do dispositivo para cada um dos seus dispositivos.

Para utilizar plenamente o Azure Security Center para funcionalidades IoT, terá de criar, configurar e utilizar estes módulos de segurança gémeos para todos os dispositivos do serviço.

## <a name="create-azureiotsecurity-module-twin"></a>Criar módulo de azureiotsecurity twin

os gémeos _módulos de azureiotsecurity_ podem ser criados de duas formas:

1. [Script de lote de módulo](https://aka.ms/iot-security-github-create-module) - cria automaticamente o módulo twin para novos dispositivos ou dispositivos sem um módulo gémeo utilizando a configuração padrão.
1. Editar manualmente cada módulo gémeo individualmente com configurações específicas para cada dispositivo.

>[!NOTE]
> A utilização do método do lote não substituirá os gémeos módulos de azureiotsegurança existentes. A utilização do método do lote APENAs cria novos módulos gémeos para dispositivos que ainda não têm um módulo de segurança gémeo.

Consulte a [configuração do agente](how-to-agent-configuration.md) para aprender a modificar ou alterar a configuração de um módulo gémeo existente.

Para criar manualmente um novo módulo _de insegurança azureiot para_ um dispositivo, utilize as seguintes instruções:

1. No seu Hub IoT, localize e selecione o dispositivo para o quais deseja criar um módulo de segurança twin.
1. Clique no seu dispositivo e, em seguida, em **Adicionar identidade do módulo**.
1. No campo Nome de Identidade do **Módulo,** insira **a azureiotsecurity**.

1. Clique em **Guardar**.

## <a name="verify-creation-of-a-module-twin"></a>Verifique a criação de um módulo gémeo

Para verificar se existe um módulo de segurança gémeo para um dispositivo específico:

1. No seu Azure IoT Hub, selecione **dispositivos IoT** do menu **Explorers.**
1. Introduza o ID do dispositivo ou selecione uma opção no campo do **dispositivo de consulta** e clique em **dispositivos de consulta**.
    ![Dispositivos de consulta](./media/quickstart/verify-security-module-twin.png)
1. Selecione o dispositivo ou clique duas vezes nele para abrir a página de detalhes do Dispositivo.
1. Selecione o menu **de identidades do Módulo** e confirme a existência do módulo de insegurança **azureiot** na lista de identidades do módulo associadas ao dispositivo.
    ![Módulos associados a um dispositivo](./media/quickstart/verify-security-module-twin-3.png)

Para saber mais sobre a personalização das propriedades do Azure Security Center para gémeos módulos IoT, consulte [a configuração do Agente](how-to-agent-configuration.md).

## <a name="next-steps"></a>Próximos passos

Avance para o próximo artigo para aprender a investigar recomendações de segurança...

> [!div class="nextstepaction"]
> [Investigar recomendações de segurança](quickstart-investigate-security-recommendations.md)
