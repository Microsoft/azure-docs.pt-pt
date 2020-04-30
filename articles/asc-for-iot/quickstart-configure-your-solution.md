---
title: 'Quickstart: Configure a sua solução'
description: Neste arranque rápido, aprenda a configurar a sua solução IoT de ponta a ponta utilizando o Azure Security Center para IoT.
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
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 04505527428a6bb312c6fb7a12c116947fbd7cf6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310847"
---
# <a name="quickstart-configure-your-iot-solution"></a>Quickstart: Configure a sua solução IoT

Este artigo fornece uma explicação de como executar a configuração inicial da sua solução de segurança IoT usando o Azure Security Center para IoT.

## <a name="azure-security-center-for-iot"></a>Centro de Segurança do Azure para IoT

O Azure Security Center for IoT oferece uma segurança abrangente de ponta a ponta para soluções IoT baseadas em Azure.

Com o Azure Security Center for IoT, pode monitorizar toda a sua solução IoT num dashboard, superando todos os seus dispositivos IoT, plataformas IoT e recursos back-end em Azure.

Uma vez ativado no seu Hub IoT, o Azure Security Center for IoT identifica automaticamente outros serviços Azure, também ligados ao seu Hub IoT e relacionados com a sua solução IoT.

Além da deteção automática de relacionamentos, também pode escolher quais os outros grupos de recursos Azure a marcar como parte da sua solução IoT.

As suas seleções permitem-lhe adicionar subscrições inteiras, grupos de recursos ou recursos únicos.

Depois de definir todas as relações de recursos, o Azure Security Center for IoT aproveita o Azure Security Center para lhe fornecer recomendações de segurança e alertas para estes recursos.

## <a name="add-azure-resources-to-your-iot-solution"></a>Adicione recursos Azure à sua solução IoT

Para adicionar novo recurso à sua solução IoT, faça o seguinte:

1. Abra o seu **IoT Hub** no portal Azure.
1. Selecione e abra **recursos** de baixo da **Segurança** no menu esquerdo.
1. Selecione **Editar** e escolha os grupos de recursos que pertencem à sua solução IoT.
1. Clique em **Adicionar**.

Parabéns! Adicionou um novo grupo de recursos à sua solução IoT.

O Azure Security Center for IoT monitoriza agora os novos grupos de recursos adicionados, e asuperfície recomendações e alertas de segurança relevantes como parte da sua solução IoT.

## <a name="next-steps"></a>Passos seguintes

Avançar para o próximo artigo para aprender a criar módulos de segurança...

> [!div class="nextstepaction"]
> [Criar módulos de segurança](quickstart-create-security-twin.md)
