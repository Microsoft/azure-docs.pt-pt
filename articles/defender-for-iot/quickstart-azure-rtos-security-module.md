---
title: 'Quickstart: Configurar e ativar o Módulo de Segurança para Azure RTOS'
description: Aprenda a bordo e ative o Módulo de Segurança para o serviço Azure RTOS no seu Azure IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2020
ms.author: rkarlin
ms.openlocfilehash: 321c8d2b9e58aba943c5bf19adf54d6359c5be96
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351781"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Quickstart: Módulo de segurança para Azure RTOS (pré-visualização)

Este artigo fornece uma explicação dos pré-requisitos antes de começar e explica como ativar o módulo de segurança para o serviço Azure RTOS num Hub IoT. Se não tiver atualmente um Hub IoT, consulte [Criar um Hub IoT utilizando o portal Azure](../iot-hub/iot-hub-create-through-portal.md) para começar.

> [!NOTE]
> O Módulo de Segurança para Azure RTOS só é suportado em hubs IoT de nível padrão.

## <a name="prerequisites"></a>Pré-requisitos 

### <a name="supported-devices"></a>Dispositivos suportados

- Kit de descoberta STM32F746G
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

Faça o download, compile e execute um dos ficheiros .zip para o quadro e ferramenta específicos (IAR, semi's IDE ou PC) da sua escolha a partir do Módulo de [Segurança para O recurso Azure RTOS GitHub](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Recursos do Azure

A próxima fase para começar é preparar os seus recursos Azure. Você vai precisar de um hub IoT e sugerimos um espaço de trabalho Log Analytics. Para o IoT Hub, você precisará da sua cadeia de ligação IoT Hub para ligar ao seu dispositivo. 
  
### <a name="iot-hub-connection"></a>Conexão IoT Hub

É necessária uma ligação IoT Hub para começar. 

1. Abra o seu **Hub IoT** no portal Azure.
1. Copie o fio de ligação IoT para o [ficheiro de configuração](how-to-azure-rtos-security-module.md).


As credenciais de ligação são retiradas da configuração da aplicação do utilizador **HOST_NAME**, **DEVICE_ID** e **DEVICE_SYMMETRIC_KEY**.

O Módulo de Segurança para Azure RTOS utiliza ligações de middleware Azure IoT com base no protocolo **MQTT.**


### <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics

Log Analytics ingestão no IoT Hub está desligado por padrão Defender para solução IoT. Para o habilitar para trabalhar com o Módulo de Segurança para Azure RTOS, faça o seguinte: 
1. No portal Azure, vá ao seu hub IoT.
1. Selecione **Definições** no menu **Segurança.**
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="Opção de recolha de dados de acesso para Azure RTOS"::: 
1. Selecione **a recolha de dados.** 
1. A partir da opção **de configuração do Espaço de Trabalho,** mude o toggle para **On**. 
1. Crie um novo espaço de trabalho Log Analytics ou anexe um existente. Certifique-se de que a opção **De Acesso a dados de segurança bruta** está selecionada. 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="Configuração Azure RTOS mostrando opção de recolha de dados e opções de dados de segurança bruta ambos selecionados":::
1. Selecione **Guardar**
1. Volte à sua lista de recursos Azure e confirme que vê o espaço de trabalho log Analytics que criou ou anexo está ativado para o Hub IoT.
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="Consulte a sua lista de recursos Azure para confirmar a adição do espaço de trabalho de Log Analytics correto adicionado para um Hub IoT"::: 

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para terminar a configuração e personalização da sua solução.

> [!div class="nextstepaction"]
> [Configurar Módulo de Segurança para Azure RTOS](how-to-azure-rtos-security-module.md)