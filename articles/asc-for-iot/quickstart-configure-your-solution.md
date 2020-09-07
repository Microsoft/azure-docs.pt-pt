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
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: e6ab713715cacc799d2b980c2bce2a2a15b76887
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505459"
---
# <a name="quickstart-configure-your-iot-solution"></a>Quickstart: Configure a sua solução IoT

Este artigo fornece uma explicação de como executar a configuração inicial da sua solução de segurança IoT usando o Azure Security Center para IoT.

## <a name="azure-security-center-for-iot"></a>Centro de Segurança do Azure para IoT

O Azure Security Center for IoT fornece uma segurança abrangente de ponta a ponta para soluções IoT baseadas em Azure.

Com o Azure Security Center for IoT, pode monitorizar toda a sua solução IoT num só dashboard, superando todos os seus dispositivos IoT, plataformas IoT e recursos back-end em Azure.

Uma vez ativado no seu IoT Hub, o Azure Security Center for IoT identifica automaticamente outros serviços Azure, também ligados ao seu Hub IoT e relacionados com a sua solução IoT.

Além da deteção automática de relacionamentos, também pode escolher quais outros grupos de recursos Azure para etiquetar como parte da sua solução IoT.

As suas seleções permitem-lhe adicionar subscrições inteiras, grupos de recursos ou recursos únicos.

Depois de definir todas as relações de recursos, o Azure Security Center for IoT aproveita o Azure Security Center para fornecer-lhe recomendações de segurança e alertas para estes recursos.

## <a name="add-azure-resources-to-your-iot-solution"></a>Adicione recursos Azure à sua solução IoT

Para adicionar novos recursos à sua solução IoT, faça o seguinte:

1. Abra o seu **Hub IoT** no portal Azure.
1. Selecione e abra **as definições** da secção **de Segurança** no menu esquerdo e, em seguida, selecione **Recursos Monitorizados**.
1. **Selecione Editar** e selecione os recursos monitorizados que pertencem à sua solução IoT.
1. Clique em **Adicionar**.

Parabéns! Adicionou um novo grupo de recursos à sua solução IoT.

O Azure Security Center for IoT agora monitoriza os seus novos grupos de recursos, e surgem recomendações e alertas de segurança relevantes como parte da sua solução IoT.

## <a name="next-steps"></a>Próximos passos

Avance para o próximo artigo para aprender a criar módulos de segurança...

> [!div class="nextstepaction"]
> [Criar módulos de segurança](quickstart-create-security-twin.md)
