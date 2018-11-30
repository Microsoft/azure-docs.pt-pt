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
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52500041"
---
Utilize o procedimento seguinte para ver e atualizar a versão de runtime atualmente a ser utilizada por uma aplicação de funções.

1. Na [portal do Azure](https://portal.azure.com), navegue para a aplicação de funções.

1. Sob **recursos configurado**, escolha **as definições da aplicação de função**.

    ![Selecione as definições de aplicação de função](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. Na **as definições da aplicação de função** separador, localize a **versão de Runtime**. Tenha em atenção que a versão de runtime específica e a versão principal pedida. No exemplo abaixo, a versão é definida como `~2`.

   ![Selecione as definições de aplicação de função](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Para afixar a sua aplicação de funções para o tempo de execução do versão 1.x, escolha **~ 1** sob **versão de Runtime**. Este comutador é desativado quando tem funções na sua aplicação.

1. Quando alterar a versão de runtime, volte para o **descrição geral** separador e escolha **reiniciar** para reiniciar a aplicação.  A aplicação de função reiniciar em execução o tempo de execução do versão 1.x e os modelos da versão 1.x são utilizados quando criar funções.