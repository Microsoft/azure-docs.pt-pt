---
title: 'Quickstart: Ativar o serviço'
description: Aprenda a bordo e ative o Azure Security Center para o serviço de segurança IoT no seu Hub Azure IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 0d3e4ad63baf6883aa8fadaca5b1f2d28fa14881
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310758"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Quickstart: Centro de Segurança Azure a bordo para serviço IoT no IoT Hub

Este artigo fornece uma explicação de como ativar o Serviço Azure Security Center para ioT no seu hub IoT existente. Se não tiver atualmente um Hub IoT, consulte [Create a IoT Hub utilizando o portal Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) para começar.

> [!NOTE]
> O Azure Security Center for IoT atualmente apenas suporta hubs ioT de nível padrão.

## <a name="prerequisites-for-enabling-the-service"></a>Pré-requisitos para permitir o serviço

- Área de trabalho do Log Analytics
  - Dois tipos de informação são armazenados por padrão no seu espaço de trabalho Log Analytics pelo Azure Security Center for IoT; **alertas** de segurança e **recomendações.**
  - Pode optar por adicionar armazenamento de um tipo adicional de informação, **eventos brutos.** Note que armazenar **eventos brutos** no Log Analytics tem custos adicionais de armazenamento.
- IoT Hub (nível padrão)
- Conheça todos os [pré-requisitos de serviço](service-prerequisites.md)

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Enable Azure Security Center for IoT on your IoT Hub

Para permitir a segurança no seu Hub IoT:

1. Abra o seu **IoT Hub** no portal Azure.
1. No menu **'Segurança',** clique em **Proteger a sua solução IoT**.

Parabéns! Completou a habilitação do Centro de Segurança Azure para ioT no seu Hub IoT.

### <a name="geolocation-and-ip-address-handling"></a>Gestão de endereços de geolocalização e IP

Para proteger a sua solução IoT, os endereços IP das ligações de entrada e saída de e para os seus dispositivos IoT, IoT Edge e IoT Hub(s) são recolhidos e armazenados por padrão. Esta informação é essencial para detetar conectividade anormal de fontes ip suspeitas. Por exemplo, quando são feitas tentativas para estabelecer ligações a partir de uma fonte IP de uma botnet conhecida ou de uma fonte IP fora da sua geolocalização. O Azure Security Center for IoT service oferece a flexibilidade para ativar e desativar a recolha de dados de endereçoip a qualquer momento.

Para permitir ou desativar a recolha de dados de endereços IP:

1. Abra o seu IoT Hub e, em seguida, selecione **a visão geral** do menu De **segurança.**
1. Escolha o ecrã **Definições** e modifique as definições de gelocalização e/ou manuseamento IP como desejar.

### <a name="log-analytics-creation"></a>Criação de Log Analytics

Quando o Azure Security Center for IoT é ligado, é criado um espaço de trabalho padrão do Azure Log Analytics para armazenar eventos de segurança bruta, alertas e recomendações para os seus dispositivos IoT, IoT Edge e IoT Hub. Todos os meses, os primeiros cinco (5) GB de dados ingeridos por cliente para o serviço Azure Log Analytics são gratuitos. Todos os GB de dados ingeridos no seu espaço de trabalho Azure Log Analytics são mantidos gratuitamente nos primeiros 31 dias. Saiba mais sobre os preços do [Log Analytics.](https://azure.microsoft.com/pricing/details/monitor/)

Para alterar a configuração do espaço de trabalho do Log Analytics:

1. Abra o seu IoT Hub e, em seguida, selecione **a visão geral** do menu De **segurança.**
1. Escolha o ecrã **Definições** e modifique a configuração do espaço de trabalho das definições de Log Analytics como desejar.

### <a name="customize-your-iot-security-solution"></a>Personalize a sua solução de segurança IoT

Por predefinição, ligar o Azure Security Center para a solução IoT protege automaticamente todos os Hubs IoT sob a sua subscrição Azure.

Para ligar o Azure Security Center para o serviço IoT num hub ioT específico ligado ou desligado:

1. Abra o seu IoT Hub e, em seguida, selecione **a visão geral** do menu De **segurança.**
1. Escolha o ecrã **Definições** e modifique as definições de segurança de qualquer hub IoT na sua subscrição Azure como desejar.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para configurar a sua solução...

> [!div class="nextstepaction"]
> [Configurar a sua solução](quickstart-configure-your-solution.md)
