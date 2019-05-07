---
title: Ative o Centro de segurança do Azure para o serviço de IoT em pré-visualização do IoT Hub | Documentos da Microsoft
description: Saiba como ativar o Centro de segurança do Azure para o serviço de IoT no seu IoT Hub.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: mlottner
ms.openlocfilehash: 3ef3891ac2b0423688f49ae034331935b1b0cd6c
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192481"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Início rápido: Ativar o serviço no IoT Hub

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação de como ativar o Centro de segurança do Azure (ASC) para o serviço de pré-visualização do IoT no seu IoT Hub.  

> [!NOTE]
> Centro de segurança do Azure para IoT atualmente suporta apenas o escalão standard e superiores os IoT Hubs.
> Centro de segurança do Azure para IoT é uma solução de hub único. Se necessitar de vários hubs, várias soluções são necessárias. 

## <a name="prerequisites-for-enabling-the-service"></a>Pré-requisitos para ativar o serviço

- Área de trabalho do Log Analytics
  - Dois tipos de informações são armazenados por padrão na sua área de trabalho do Log Analytics pelo ASC do IoT; **alertas de segurança** e **recomendações**. 
  - Pode optar por adicionar armazenamento de um tipo de informações adicionais **eventos não processados**. Tenha em atenção que armazenar **eventos não processados** no Log Analytics acarreta custos de armazenamento adicionais. 
- IoT Hub (escalão standard ou superior)
- Atender a todas as [prerequities de serviço](service-prerequisites.md) 

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Ativar o ASC para IoT no seu Hub IoT 

Para ativar a segurança no seu IoT Hub, faça o seguinte: 

1. Abra sua **IoT Hub** no portal do Azure. 
2. Sob o **segurança** menu, clique em **descrição geral**, em seguida, clique em **pré-visualização do início**. 
3. Escolher **ativar a segurança de IoT**. 
4. Forneça os detalhes da sua área de trabalho do Log Analytics. 
   - Optar por armazenar **eventos não processados** além dos tipos de informações padrão de armazenamento, deixando a **eventos não processados** Ativar/desativar **no**. 
   - Opte por ativar **duplo coleção** deixando a **duplo coleção** Ativar/desativar **no**. 
5. Clique em **Guardar**. 

Parabéns! Concluiu a ativar o ASC para IoT no seu IoT Hub. 

## <a name="next-steps"></a>Passos Seguintes

Avance para o artigo seguinte para aprender a configurar a sua solução...

> [!div class="nextstepaction"]
> [Configurar a sua solução](quickstart-configure-your-solution.md)