---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77205732"
---
Adicione suporte no ambiente de desenvolvimento preferido utilizando os seguintes métodos.

| Ambiente de programação  | Tipo de aplicação      | Para adicionar suporte |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Biblioteca de classes C#      | [Instale o pacote NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Baseado em [ferramentas centrais](../articles/azure-functions/functions-run-local.md) | [Registar o pacote de extensão](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Recomenda-se a instalação da [extensão Azure Tools.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) |
| Qualquer outro editor/IDE     | Baseado em [ferramentas centrais](../articles/azure-functions/functions-run-local.md) | [Registar o pacote de extensão](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Portal do Azure             | Online apenas no portal | Instala ao adicionar uma encadernação<br /><br /> Consulte [Update your extensions](../articles/azure-functions/install-update-binding-extensions-manual.md) to updateing existentes binding extensions without ter to republicar a sua app de função. |
