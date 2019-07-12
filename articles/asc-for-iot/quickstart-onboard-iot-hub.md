---
title: Ative o Centro de segurança do Azure para o serviço de IoT em pré-visualização do IoT Hub | Documentos da Microsoft
description: Saiba como ativar o Centro de segurança do Azure para o serviço de IoT no seu IoT Hub.
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
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: f81fb7aeed1b704ebdd82c1f5b83c33a4b05e9ca
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618011"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Início rápido: Ativar o serviço no IoT Hub

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação de como ativar o Centro de segurança do Azure (ASC) para o serviço de pré-visualização do IoT no seu IoT Hub.  

> [!NOTE]
> Centro de segurança do Azure para IoT atualmente suporta apenas os Hubs IoT do escalão standard.
> Centro de segurança do Azure para IoT é uma solução de hub único. Se necessitar de vários hubs, várias soluções são necessárias. 

## <a name="prerequisites-for-enabling-the-service"></a>Pré-requisitos para ativar o serviço

- Área de trabalho do log Analytics
  - Dois tipos de informações são armazenados por padrão na sua área de trabalho do Log Analytics pelo ASC do IoT; **alertas de segurança** e **recomendações**. 
  - Pode optar por adicionar armazenamento de um tipo de informações adicionais **eventos não processados**. Tenha em atenção que armazenar **eventos não processados** no Log Analytics acarreta custos de armazenamento adicionais. 
- IoT Hub (escalão standard)
- Atender a todas as [pré-requisitos de serviço](service-prerequisites.md) 
- Regiões de serviço com suporte
  - EUA Central
  - Europa do Norte
  - Sudeste Asiático

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
