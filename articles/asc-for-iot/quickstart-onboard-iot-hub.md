---
title: Habilitar a central de segurança do Azure para o serviço de IoT no Hub IoT | Microsoft Docs
description: Saiba como habilitar a central de segurança do Azure para o serviço de IoT em seu hub IoT.
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
ms.date: 10/17/2019
ms.author: mlottner
ms.openlocfilehash: 7cdf9f61c88d93f0cbf0b80576aa30dff5ce9cab
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551591"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Início rápido: integração da central de segurança do Azure para o serviço de IoT no Hub IoT

Este artigo fornece uma explicação de como habilitar a central de segurança do Azure para o serviço de IoT em seu hub IoT existente. Se você não tiver um hub IoT no momento, consulte [criar um hub IOT usando o portal do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) para começar. 

> [!NOTE]
> A central de segurança do Azure para IoT atualmente só dá suporte a hubs IoT da camada Standard.


## <a name="prerequisites-for-enabling-the-service"></a>Pré-requisitos para habilitar o serviço

- Espaço de trabalho Log Analytics
  - Dois tipos de informações são armazenados por padrão em seu espaço de trabalho Log Analytics pela central de segurança do Azure para IoT; **alertas de segurança** e **recomendações**. 
  - Você pode optar por adicionar armazenamento de um tipo de informações adicional, **eventos brutos**. Observe que o armazenamento de **eventos brutos** no log Analytics transporta custos de armazenamento adicionais. 
- Hub IoT (camada Standard)
- Atender a todos os [pré-requisitos de serviço](service-prerequisites.md) 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Habilitar a central de segurança do Azure para IoT em seu hub IoT 

Para habilitar a segurança em seu hub IoT: 

1. Abra o **Hub IOT** no portal do Azure. 
1. No menu **segurança** , clique em **proteger sua solução de IOT**.
1. Deixe **habilitar** selecionado como padrão. 
1. Selecione seu espaço de trabalho do log Analytics.
1. Forneça os detalhes do seu Log Analytics espaço de trabalho. 
   - Escolha habilitar a **coleta de entrelaçamento** deixando a **opção de ativar/desativar** **coleta** .
   - Escolha armazenar **eventos brutos** além dos tipos de informações padrão de armazenamento selecionando os **eventos de segurança de dispositivo brutos de armazenamento** em log Analytics. Deixe a **opção de ativar/desativar** **evento bruto** . 
    
1. Clique em **Guardar**. 

Parabéns! Você concluiu a habilitação da central de segurança do Azure para IoT em seu hub IoT. 

### <a name="geolocation-and-ip-address-handling"></a>Localização geográfica e manipulação de endereço IP

Para proteger sua solução de IoT, os endereços IP de conexões de entrada e saída de e para seus dispositivos IoT, IoT Edge e Hub IoT são coletados e armazenados por padrão. Essas informações são essenciais para detectar a conectividade anormal de fontes de IP suspeitas. Por exemplo, quando são feitas tentativas de estabelecer conexões de uma fonte IP de uma botnet conhecida ou de uma fonte IP fora de sua localização geográfica. A central de segurança do Azure para o serviço de IoT oferece a flexibilidade para habilitar e desabilitar a coleta de dados de endereço IP a qualquer momento. 

Para habilitar ou desabilitar a coleta de dados de endereço IP: 

1. Abra o Hub IoT e, em seguida, selecione **visão geral** no menu **segurança** . 
2. Escolha a tela **configurações** e modifique a localização geográfica e/ou as configurações de manipulação de IP como desejar.

### <a name="log-analytics-creation"></a>Criação de Log Analytics

Quando a central de segurança do Azure para IoT é ativada, um espaço de trabalho padrão do Azure Log Analytics é criado para armazenar eventos de segurança brutos, alertas e recomendações para seus dispositivos IoT, IoT Edge e Hub IoT. Todos os meses, os cinco primeiros (5) GB de dados ingeridos por cliente para o serviço de Log Analytics do Azure são gratuitos. Cada GB de dados ingeridos na área de trabalho do Azure Log Analytics é mantido gratuitamente durante os primeiros 31 dias. Saiba mais sobre preços de [log Analytics](https://azure.microsoft.com/pricing/details/monitor/) .

Para alterar a configuração do espaço de trabalho de Log Analytics:

1. Abra o Hub IoT e, em seguida, selecione **visão geral** no menu **segurança** . 
2. Escolha a tela **configurações** e modifique a configuração do espaço de trabalho das configurações de log Analytics como desejar.

### <a name="customize-your-iot-security-solution"></a>Personalizar sua solução de segurança de IoT
Por padrão, ativar a central de segurança do Azure para a solução de IoT protege automaticamente todos os hubs IoT em sua assinatura do Azure. 

Para ativar ou desativar a central de segurança do Azure para o serviço IoT em um hub IoT específico: 

1. Abra o Hub IoT e, em seguida, selecione **visão geral** no menu **segurança** . 
2. Escolha a tela **configurações** e modifique as configurações de segurança de qualquer Hub IOT na sua assinatura do Azure como desejar.


## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para configurar sua solução...

> [!div class="nextstepaction"]
> [Configurar sua solução](quickstart-configure-your-solution.md)


