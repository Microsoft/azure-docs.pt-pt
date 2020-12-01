---
title: 'Início rápido: Ativar o serviço'
description: Aprenda a bordo e ative o serviço de segurança IoT no seu Azure IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 786fcd1a0c6d7df2c38a086a830a63f7179d7d40
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352512"
---
# <a name="quickstart-onboard-azure-defender-for-iot-service-in-iot-hub"></a>Quickstart: A bordo Azure Defender para serviço IoT no IoT Hub

Este artigo fornece uma explicação de como ativar o serviço Defender for IoT no seu IoT Hub existente. Se não tiver atualmente um Hub IoT, consulte [Criar um Hub IoT utilizando o portal Azure](../iot-hub/iot-hub-create-through-portal.md) para começar.

> [!NOTE]
> Atualmente, o Defender for IoT apenas suporta os hubs IoT de nível padrão.

## <a name="prerequisites-for-enabling-the-service"></a>Pré-requisitos para permitir o serviço

- Área de trabalho do Log Analytics
  - Dois tipos de informação são armazenados por padrão no seu espaço de trabalho Log Analytics pelo Defender para IoT; **alertas** e **recomendações de** segurança.
  - Pode optar por adicionar armazenamento de um tipo de informação adicional, **eventos crus.** Note que armazenar **eventos crus** no Log Analytics tem custos adicionais de armazenamento.
- Hub IoT (nível padrão)
- Conheça todos os [pré-requisitos de serviço](service-prerequisites.md)

## <a name="enable-defender-for-iot-on-your-iot-hub"></a>Ativar o Defender para IoT no seu IoT Hub

Para ativar a segurança no seu Hub IoT:

1. Abra o seu **Hub IoT** no portal Azure.
1. No menu **Segurança,** clique em **Secure your IoT solution**.

Parabéns! Completou a habilitação do Defender para IoT no seu IoT Hub.

### <a name="geolocation-and-ip-address-handling"></a>Geolocalização e tratamento de endereços IP

Para garantir a sua solução IoT, os endereços IP das ligações de entrada e saída de e para os seus dispositivos IoT, IoT Edge e IoT Hub(s) são recolhidos e armazenados por padrão. Esta informação é essencial para detetar conectividade anormal a partir de fontes ip suspeitas. Por exemplo, quando se tentam estabelecer ligações a partir de uma fonte de IP de uma botnet conhecida ou de uma fonte de IP fora da sua geolocalização. O serviço Defender for IoT oferece a flexibilidade para permitir e desativar a recolha de dados de endereços IP a qualquer momento.

Para ativar ou desativar a recolha de dados de endereços IP:

1. Abra o seu Hub IoT e, em seguida, **selecione Definições** do menu **Segurança.**
1. Escolha o ecrã **de Recolha de Dados** e modifique as definições de geolocalização e/ou manuseamento IP conforme desejar.

### <a name="log-analytics-creation"></a>Criação log analytics

Quando o Defender for IoT é ligado, é criado um espaço de trabalho padrão do Azure Log Analytics para armazenar eventos de segurança bruto, alertas e recomendações para os seus dispositivos IoT, IoT Edge e IoT Hub. Todos os meses, os primeiros cinco (5) GB de dados ingeridos por cliente para o serviço Azure Log Analytics são gratuitos. Cada GB de dados ingeridos no seu espaço de trabalho Azure Log Analytics é mantido gratuitamente durante os primeiros 31 dias. Saiba mais sobre os preços [do Log Analytics.](https://azure.microsoft.com/pricing/details/monitor/)

Para alterar a configuração do espaço de trabalho do Log Analytics:

1. Abra o seu Hub IoT e, em seguida, **selecione Definições** do menu **Segurança.**
1. Escolha o ecrã **de Recolha de Dados** e modifique a configuração do espaço de trabalho das definições de Log Analytics como desejar.

### <a name="customize-your-iot-security-solution"></a>Personalize a sua solução de segurança IoT

Por predefinição, ligar a solução Defender para IoT protege automaticamente todos os Hubs IoT sob a sua subscrição Azure.

Para ligar ou desligar o Defender para um serviço IoT específico:

1. Abra o seu Hub IoT e, em seguida, **selecione Definições** do menu **Segurança.**
1. Escolha o ecrã **de Recolha de Dados** e modifique as definições de segurança de qualquer hub IoT na sua subscrição Azure como desejar.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para configurar a sua solução...

> [!div class="nextstepaction"]
> [Configurar a sua solução](quickstart-configure-your-solution.md)