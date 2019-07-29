---
title: Criar um módulo de segurança para a central de segurança do Azure para IoT | Microsoft Docs
description: Saiba como criar uma central de segurança do Azure para o módulo de IoT/r para uso com a central de segurança do Azure para IoT.
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
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 92bf79aa5ae55bad16d68a26dc13d292285a4d46
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597075"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Início rápido: Criar um módulo de azureiotsecurity

Este guia de início rápido explica como criar gêmeos de módulo _azureiotsecurity_ individuais para novos dispositivos ou criar lote de gêmeos de módulo para todos os dispositivos em um hub IOT.  

## <a name="understanding-azureiotsecurity-module-twins"></a>Entendendo o módulo azureiotsecurity gêmeos 

Para soluções de IoT criadas no Azure, o dispositivo gêmeos desempenha um papel fundamental no gerenciamento de dispositivos e na automação de processos. 

A central de segurança do Azure para IoT oferece integração total com sua plataforma de gerenciamento de dispositivo IoT existente, permitindo que você gerencie o status de segurança do dispositivo e use os recursos de controle de dispositivo existentes.
A central de segurança do Azure para integração de IoT é obtida com o uso do mecanismo de entrelaçamento do Hub IoT.  

Consulte [módulo do Hub IOT gêmeos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito geral do módulo gêmeos no Hub IOT do Azure. 
 
A central de segurança do Azure para IoT usa o mecanismo de entrelaçamento do módulo e mantém um módulo de segurança com o nome _azureiotsecurity_ para cada um dos seus dispositivos.

O módulo de segurança de tudo contém todas as informações relevantes à segurança do dispositivo para cada um de seus dispositivos. 
 
Para fazer uso completo da central de segurança do Azure para recursos de IoT, você precisará criar, configurar e usar esses gêmeos de módulo de segurança para cada dispositivo no serviço.  

## <a name="create-azureiotsecurity-module-twin"></a>Criar o módulo azureiotsecurity. 

o módulo _azureiotsecurity_ gêmeos pode ser criado de duas maneiras:
1. [Script em lote de módulo](https://aka.ms/iot-security-github-create-module) -cria automaticamente o módulo "r" para novos dispositivos ou dispositivos sem um módulo "r" usando a configuração padrão.
2. Editando manualmente cada módulo para cima com configurações específicas para cada dispositivo.

>[!NOTE] 
> O uso do método batch não substituirá o gêmeos do módulo azureiotsecurity existente. O uso do método batch cria apenas um novo módulo gêmeos para dispositivos que ainda não têm um módulo de segurança. 

Consulte [configuração do agente](how-to-agent-configuration.md) para saber como modificar ou alterar a configuração de um módulo atual. 

Para criar manualmente um novo módulo _azureiotsecurity_ , use as seguintes instruções: 

1. No Hub IoT, localize e selecione o dispositivo para o qual você deseja criar um módulo de segurança.
1. Clique em seu dispositivo e, em seguida, em **Adicionar identidade do módulo**.
1. No campo **nome da identidade do módulo** , insira **azureiotsecurity**.

1. Clique em **Guardar**. 

## <a name="verify-creation-of-a-module-twin"></a>Verificar a criação de um módulo "r"

Para verificar se existe um módulo de segurança para um dispositivo específico:

1. No Hub IoT do Azure, selecione **dispositivos IOT** no menu **Gerenciador** .    
1. Insira a ID do dispositivo ou selecione uma opção no **campo do dispositivo de consulta** e clique em **consultar dispositivos**. 
    ![Dispositivos de consulta](./media/quickstart/verify-security-module-twin.png)
1. Selecione o dispositivo ou clique duas vezes nele para abrir a página de detalhes do dispositivo. 
1. Selecione o menu identidades do **módulo** e confirme a existência do módulo **azureiotsecurity** na lista de identidades de módulo associadas ao dispositivo. 
    ![Módulos associados a um dispositivo](./media/quickstart/verify-security-module-twin-3.png)


Para saber mais sobre como personalizar as propriedades da central de segurança do Azure para o módulo IoT gêmeos, consulte [configuração do agente](how-to-agent-configuration.md).

## <a name="next-steps"></a>Passos Seguintes

Avance para o próximo artigo para saber como configurar alertas personalizados...

> [!div class="nextstepaction"]
> [Configurar alertas personalizados](quickstart-create-custom-alerts.md)
