---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d8f8021925ac9c9e427dd6571ecaa1a30c85497
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162733"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Depois, crie uma identidade do dispositivo e guarde a chave para utilização posterior. Esta identidade do dispositivo é utilizada pela aplicação de simulação para enviar mensagens para o hub IoT. Esta capacidade não está disponível no PowerShell ou ao utilizar um modelo Azure Resource Manager. Os seguintes passos informá-lo a criar o dispositivo simulado utilizando o [portal do Azure](https://portal.azure.com).

1. Abra o [portal do Azure](https://portal.azure.com) e inicie sessão na sua conta do Azure.

2. Selecione **grupos de recursos** e, em seguida, selecione o grupo de recursos. Este tutorial utiliza **ContosoResources**.

3. Na lista de recursos, selecione o seu hub IoT. Este tutorial utiliza **ContosoTestHub**. Selecione **Dispositivos IoT** no painel Hub.

4. Selecione **+ Adicionar**. No painel Adicionar Dispositivo, preencha o ID do dispositivo. Este tutorial utiliza **Contoso-Test-Device**. Deixe as chaves em branco e marque **Chaves de Geração Automática**. Confirme que a opção **Ligar dispositivo ao hub IoT**  está ativada. Selecione **Guardar**.

   ![O ecrã de adicionar o dispositivo](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Agora que foi criado, selecione o dispositivo para ver as teclas geradas. Selecione o ícone de cópia na chave primária e guardá-lo em algum lugar, como o bloco de notas para a fase de teste deste tutorial.

   ![Os detalhes do dispositivo, incluindo as chaves](./media/iot-hub-include-create-simulated-device-portal/device-details.png)