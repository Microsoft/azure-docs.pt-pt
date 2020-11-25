---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: bcc55156ca1d03614a4ff9767d6cf3f2603c06ca
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026476"
---
Adicione suporte no ambiente de desenvolvimento preferido utilizando os seguintes métodos.

| Ambiente de programação  | Tipo de aplicação      | Para adicionar suporte |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Biblioteca de classes C#      | [Instale o pacote NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Baseado em [ferramentas centrais](../articles/azure-functions/functions-run-local.md) | [Registar o pacote de extensão](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Recomenda-se a instalação da [extensão Azure Tools.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) |
| Qualquer outro editor/IDE     | Baseado em [ferramentas centrais](../articles/azure-functions/functions-run-local.md) | [Registar o pacote de extensão](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Portal do Azure             | Online apenas no portal | Instala ao adicionar uma encadernação<br /><br /> Consulte [Update your extensions](../articles/azure-functions/functions-bindings-register.md) to updateing existentes binding extensions without ter to republicar a sua app de função. |