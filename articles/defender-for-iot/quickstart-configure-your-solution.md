---
title: 'Quickstart: Configure a sua solução'
description: Neste arranque rápido, aprenda a configurar a sua solução IoT de ponta a ponta utilizando o Azure Defender para IoT.
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
ms.openlocfilehash: 3ecb2e2022cab80abf6a8692ac8cc3ba54eff1e4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90948532"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Quickstart: Configuure o seu Azure Defender para a solução IoT

Este artigo fornece uma explicação de como executar a configuração inicial da sua solução de segurança IoT usando o Defender para IoT.

## <a name="what-is-defender-for-iot"></a>O que é Defender para IoT?

O Defender for IoT fornece uma segurança abrangente de ponta a ponta para soluções IoT baseadas em Azure.

Com o Defender para IoT, pode monitorizar toda a sua solução IoT num só dashboard, superando todos os seus dispositivos IoT, plataformas IoT e recursos back-end em Azure.

Uma vez ativado no seu IoT Hub, o Defender for IoT identifica automaticamente outros serviços Azure, também ligados ao seu Hub IoT e relacionados com a sua solução IoT.

Além da deteção automática de relacionamentos, também pode escolher quais outros grupos de recursos Azure para etiquetar como parte da sua solução IoT.

As suas seleções permitem-lhe adicionar subscrições inteiras, grupos de recursos ou recursos únicos.

Depois de definir todas as relações de recursos, o Defender for IoT aproveita o Defender para lhe fornecer recomendações de segurança e alertas para estes recursos.

## <a name="add-azure-resources-to-your-iot-solution"></a>Adicione recursos Azure à sua solução IoT

Para adicionar novos recursos à sua solução IoT, faça o seguinte:

1. Abra o seu **Hub IoT** no portal Azure.
1. Selecione e abra **as definições** da secção **de Segurança** no menu esquerdo e, em seguida, selecione **Recursos Monitorizados**.
1. **Selecione Editar** e selecione os recursos monitorizados que pertencem à sua solução IoT.
1. Clique em **Adicionar**.

Parabéns! Adicionou um novo grupo de recursos à sua solução IoT.

O Defender for IoT agora monitoriza os seus novos grupos de recursos, e surge as recomendações e alertas de segurança relevantes como parte da sua solução IoT.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a criar módulos de segurança...

> [!div class="nextstepaction"]
> [Criar módulos de segurança](quickstart-create-security-twin.md)
