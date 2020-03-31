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
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184908"
---
Utilize o seguinte procedimento para visualizar e atualizar a versão runtime atualmente utilizada por uma aplicação de função.

1. No [portal Azure,](https://portal.azure.com)navegue para a sua aplicação de funções.

1. Em **funções configuradas,** escolha as definições da **aplicação Função**.

    ![Selecione as definições de aplicativo de função](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. No separador de definições da **aplicação Fun,** localize a **versão Runtime**. Note a versão específica do tempo de execução e a versão principal solicitada. No exemplo abaixo, a versão `~2`está definida para .

   ![Selecione as definições de aplicativo de função](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Para fixar a sua aplicação de função na versão 1.x runtime, escolha **~1** na **versão Runtime**. Este interruptor é desativado quando tiver funções na sua aplicação.

1. Quando alterar a versão 'tempo de execução', volte ao separador **Overview** e escolha **reiniciar** a aplicação.  A aplicação de funções reinicia a execução na versão 1.x e os modelos da versão 1.x são usados quando cria funções.