---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "83343359"
---
Utilize o seguinte procedimento para visualizar e atualizar a versão de tempo de execução atualmente utilizada por uma aplicação de função.

1. No [portal Azure,](https://portal.azure.com)navegue pela sua aplicação de função.

1. Em **Definições**, escolha **Configuração**. No separador **'Definições de tempo de execução' da função,** localiza a **versão Runtime**. Note a versão específica do tempo de execução. No exemplo abaixo, a versão está definida para `~3` .

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="Ver a versão do tempo de execução." border="true":::

1. Para fixar a sua aplicação de função na versão 1.x runtime, escolha **~1** na **versão Runtime**. Este interruptor é desativado quando tem funções na sua aplicação.

1. Quando alterar a versão de tempo de execução, volte ao **separador 'Vista Geral'** e escolha **Reiniciar** para reiniciar a aplicação.  A aplicação de função reinicia a execução na versão 1.x e os modelos da versão 1.x são utilizados quando cria funções.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="Reinicie a aplicação de função." border="true":::
