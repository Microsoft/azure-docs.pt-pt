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
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: a794ccea13323f38b20906458e216f85652bfc3e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596959"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Início rápido: Integração da central de segurança do Azure para o serviço de IoT no Hub IoT

Este artigo fornece uma explicação de como habilitar a central de segurança do Azure para o serviço de IoT em seu hub IoT existente. Se você não tiver um hub IoT no momento, consulte [criar um hub IOT usando o portal do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) para começar. 

> [!NOTE]
> A central de segurança do Azure para IoT atualmente só dá suporte a hubs IoT da camada Standard.
> A central de segurança do Azure para IoT é uma solução de Hub único. Se você precisar de vários hubs, várias soluções da central de segurança do Azure para IoT serão necessárias. 

## <a name="prerequisites-for-enabling-the-service"></a>Pré-requisitos para habilitar o serviço

- Área de trabalho do Log Analytics
  - Dois tipos de informações são armazenados por padrão em seu espaço de trabalho Log Analytics pela central de segurança do Azure para IoT; **alertas de segurança** e **recomendações**. 
  - Você pode optar por adicionar armazenamento de um tipo de informações adicional, **eventos brutos**. Observe que o armazenamento de **eventos brutos** no log Analytics transporta custos de armazenamento adicionais. 
- Hub IoT (camada Standard)
- Atender a todos os [pré-requisitos de serviço](service-prerequisites.md) 

|Regiões de serviço do Azure com suporte | ||
|---|---|---|
| EUA Central |East US |EUA Leste 2 |
| EUA Centro-Oeste |EUA Oeste |E.U.A. Oeste 2 |
| EUA Central Sul|EUA Centro-Norte | Canadá Central|
| Leste do Canadá| Europa do Norte|Sul do Brasil|
| França Central| Reino Unido Oeste|Reino Unido Sul|
|Europa Ocidental|Norte da Europa| Oeste do Japão|
|Leste do Japão | Sudeste da Austrália|Leste da Austrália|
|Ásia Oriental| Sudeste Asiático| Coreia do Sul Central|
|Coreia do Sul| Índia Central| Sul da Índia|
|

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Habilitar a central de segurança do Azure para IoT em seu hub IoT 

Para habilitar a segurança em seu hub IoT, faça o seguinte: 

1. Abra o **Hub IOT** no portal do Azure. 
1. No menu **segurança** , clique em **proteger sua solução IOT**
1. Deixe **habilitar** selecionado como padrão. 
1. Selecione seu espaço de trabalho do log Analytics.
1. Forneça os detalhes do seu Log Analytics espaço de trabalho. 
   - Escolha habilitar a **coleta** de entrelaçamento deixando a opção de **Ativar/desativar** **coleta** .
   - Escolha armazenar **eventos brutos** além dos tipos de informações padrão de armazenamento selecionando os **eventos de segurança de dispositivo brutos de armazenamento** em log Analytics. Deixe a opção de ativar **/desativar** **evento bruto** . 
    
1. Clique em **Guardar**. 

Parabéns! Você concluiu a habilitação da central de segurança do Azure para IoT em seu hub IoT. 

## <a name="next-steps"></a>Passos Seguintes

Avance para o próximo artigo para configurar sua solução...

> [!div class="nextstepaction"]
> [Configurar sua solução](quickstart-configure-your-solution.md)


