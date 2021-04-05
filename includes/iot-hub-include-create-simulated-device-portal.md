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
ms.openlocfilehash: 91b6f1ed06fbf1f5575650f96f4622b3df9ca083
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98187425"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Depois, crie uma identidade do dispositivo e guarde a chave para utilização posterior. Esta identidade do dispositivo é utilizada pela aplicação de simulação para enviar mensagens para o hub IoT. Esta capacidade não está disponível no PowerShell ou quando utiliza um modelo de Gestor de Recursos Azure. Os seguintes passos dizem-lhe como criar o dispositivo simulado utilizando o [portal Azure](https://portal.azure.com).

1. Abra o [portal do Azure](https://portal.azure.com) e inicie sessão na sua conta do Azure.

2. Selecione **grupos de recursos** e, em seguida, escolha o seu grupo de recursos. Este tutorial utiliza **ContosoResources**.

3. Na lista de recursos, selecione o seu hub IoT. Este tutorial utiliza **ContosoTestHub**. Selecione **Dispositivos IoT** no painel Hub.

4. Selecione **+ Novo**. No painel Adicionar Dispositivo, preencha o ID do dispositivo. Este tutorial utiliza **Contoso-Test-Device**. Deixe as chaves em branco e marque **Chaves de Geração Automática**. Confirme que a opção **Ligar dispositivo ao hub IoT** está ativada. Selecione **Guardar**.

   ![O ecrã do dispositivo de adicionação](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Agora que foi criado, selecione o dispositivo para ver as teclas geradas. Selecione o ícone Copiar na tecla Principal e guarde-o em algum lugar como o Bloco de Notas para a fase de teste deste tutorial.

   ![Os detalhes do dispositivo, incluindo as chaves](./media/iot-hub-include-create-simulated-device-portal/device-details.png)