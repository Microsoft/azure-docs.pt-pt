---
title: Configurar sua central de segurança do Azure para a solução de IoT | Microsoft Docs
description: Saiba como configurar sua solução de IoT de ponta a ponta usando a central de segurança do Azure para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: a546d153c6fe4f14ccc8c21308bd4a33385870c3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299695"
---
# <a name="quickstart-configure-your-iot-solution"></a>Início rápido: Configurar sua solução de IoT

Este artigo fornece uma explicação de como executar a configuração inicial da sua solução de segurança de IoT usando a central de segurança do Azure para IoT. 

## <a name="azure-security-center-for-iot"></a>Centro de Segurança do Azure para IoT

A central de segurança do Azure para IoT fornece segurança completa de ponta a ponta para soluções de IoT baseadas no Azure.

Com a central de segurança do Azure para IoT, você pode monitorar toda a sua solução de IoT em um único painel, identificando todos os seus dispositivos IoT, plataformas IoT e recursos de back-end no Azure.

Uma vez habilitado no Hub IoT, a central de segurança do Azure para IoT identifica automaticamente outros serviços do Azure, também conectados ao seu hub IoT e relacionados à sua solução de IoT.

Além da detecção automática de relações, você também pode escolher quais outros grupos de recursos do Azure marcar como parte de sua solução de IoT. 

Suas seleções permitem que você adicione assinaturas inteiras, grupos de recursos ou recursos individuais. 

Depois de definir todas as relações de recursos, a central de segurança do Azure para IoT aproveita a central de segurança do Azure para fornecer recomendações e alertas de segurança para esses recursos.

## <a name="add-azure-resources-to-your-iot-solution"></a>Adicionar recursos do Azure à sua solução de IoT

Para adicionar um novo recurso à sua solução de IoT, faça o seguinte: 

1. Abra o **Hub IOT** no portal do Azure. 
1. Selecione e abra **recursos** em **segurança** no menu à esquerda. 
1. Selecione **Editar** e escolha os grupos de recursos que pertencem à sua solução de IOT.
1. Clique em **Adicionar**. 

Parabéns! Você adicionou um novo grupo de recursos à sua solução de IoT.

A central de segurança do Azure para IoT agora monitora os grupos de recursos adicionados recentemente e superfícies recomendações de segurança e alertas relevantes como parte de sua solução de IoT.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a criar módulos de segurança...

> [!div class="nextstepaction"]
> [Criar módulos de segurança](quickstart-create-security-twin.md)