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
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83343359"
---
Utilize o seguinte procedimento para visualizar e atualizar a versão runtime atualmente utilizada por uma aplicação de função.

1. No [portal Azure,](https://portal.azure.com)navegue para a sua aplicação de funções.

1. Em **Definições,** escolha **Configuração**. No separador **função** de definições de tempo de funcionamento, localize a **versão Runtime**. Note a versão específica do tempo de execução. No exemplo abaixo, a versão está definida para `~3` .

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="Veja a versão runtime." border="true":::

1. Para fixar a sua aplicação de função na versão 1.x runtime, escolha **~1** na **versão Runtime**. Este interruptor é desativado quando tiver funções na sua aplicação.

1. Quando alterar a versão 'tempo de execução', volte ao separador **Overview** e escolha **reiniciar** a aplicação.  A aplicação de funções reinicia a execução na versão 1.x e os modelos da versão 1.x são usados quando cria funções.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="Reinicie a sua aplicação de funções." border="true":::
