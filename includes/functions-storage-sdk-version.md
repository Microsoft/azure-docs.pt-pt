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
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77198428"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Versão SDK de armazenamento Azure em Funções 1.x

Nas Funções 1.x, os gatilhos e encadernações de armazenamento utilizam a versão 7.2.1 do Pacote Azure Storage SDK (pacote[WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet). Se referenciar uma versão diferente do SDK de armazenamento e ligar-se a um tipo SDK de armazenamento na sua assinatura de função, o tempo de execução de Funções pode relatar que não pode ligar-se a esse tipo. A solução é certificar-se de que o seu projeto faz referência ao [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
