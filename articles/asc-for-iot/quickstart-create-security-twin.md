---
title: Criar um duplo do módulo de segurança para ASC para pré-visualização do IoT | Documentos da Microsoft
description: Saiba como criar um ASC para duplo do módulo de IoT para utilização com o ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 89802a638944ec220186e943d5fdc33524b2d4e9
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541675"
---
# <a name="quickstart-create-an-asc-for-iot-module-twin"></a>Início rápido: Criar um ASC para duplo do módulo de IoT

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este início rápido explicações sobre como criar individual ASC duplos de módulo de IoT para novos dispositivos ou do batch criar duplos de módulo para todos os dispositivos num IoT Hub.  

## <a name="understanding-asc-for-iot-module-twins"></a>Compreender o ASC duplos de módulo de IoT 

Para soluções de IoT criadas no Azure, os dispositivos duplos desempenham um papel fundamental na gestão de dispositivos e automatização de processos. 

ASC para IoT oferece integração completa com a sua plataforma gestão de dispositivos da IoT existente, permitindo-lhe gerir o seu estado de segurança do dispositivo, bem como a marca utilizar capacidades de controle de dispositivo existentes. ASC para integração de IoT é conseguido fazendo uso do IoT Hub duplo mecanismo.  

Ver [duplos de módulo do IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito geral de duplos de módulo no IoT Hub do Azure. 
 
ASC para IoT facilita usar o mecanismo de duplo do módulo e mantém um duplo do módulo de segurança para cada um dos seus dispositivos. O duplo do módulo de segurança armazena todas as informações relevantes para a segurança do dispositivo para cada um dos seus dispositivos. 
 
Para tirar o máximo proveito do ASC para recursos de IoT, terá de criar, configurar e utilizar estes duplos de módulo de segurança para todos os dispositivos no serviço.  

## <a name="create-asc-for-iot-module-twin"></a>Criar o ASC para duplo do módulo de IoT 

ASC duplos de módulo de IoT pode ser criada no modo de batch com a configuração predefinida ou individualmente com as configurações específicas para cada dispositivo. Para o batch criar para novos dispositivos ou dispositivos sem um duplo do módulo, utilize o [script de comandos do módulo](https://aka.ms/iot-security-github-create-module). 

>[!NOTE] 
> Usando o método de lote não substituirá duplos de módulo existente. APENAS usando o método de batch cria o novo duplos de módulo para dispositivos que ainda não tiver um duplo do módulo. 

Ver [modificar um duplo do módulo de segurança](how-to-modify-security-module-twin.md) para saber como modificar ou alterar a configuração de um módulo duplo existente. 

Para criar um novo ASC para duplo do módulo de IoT para um dispositivo, utilize as instruções seguintes: 

1. No seu IoT Hub, localize e selecione o dispositivo que pretende criar um duplo do módulo de segurança para no seu IoT Hub. 
1. Na **nome de identidade do Microsoft** , insira **ascforiotsecurity**.
1. Clique em **Guardar**. 

## <a name="verify-creation-of-a-module-twin"></a>Certifique-se a criação de um módulo duplo

Para verificar se um duplo do módulo de segurança existe para um dispositivo específico:

1. No seu IoT Hub do Azure, selecione **dispositivos IoT** partir a **exploradores** menu.    
1. Introduza o ID de dispositivo ou selecione uma opção no **campo de dispositivo de consulta** e clique em **consultar dispositivos**. 
    ![Dispositivos de consulta](./media/quickstart/verify-security-module-twin.png)
1. Selecione o dispositivo ou faça duplo clique para abrir a página de detalhes do dispositivo. 
1. Selecione o **identidades de módulo** menu e confirme a existência da **ascforiotsecurity** módulo e um **estado da ligação** de **ligado**na lista de identidades de módulo associados ao dispositivo. 
    ![Módulos associados a um dispositivo](./media/quickstart/verify-security-module-twin-2.png)


Para saber mais sobre como personalizar as propriedades do ASC duplos de módulo do IoT, veja [configuração do agente](concept-agent-configuration.md).

## <a name="next-steps"></a>Passos Seguintes

Avance para o artigo seguinte para saber como configurar alertas personalizados...

> [!div class="nextstepaction"]
> [Configurar alertas personalizados](quickstart-create-custom-alerts.md)
