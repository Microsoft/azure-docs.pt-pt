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
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77198428"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Versão SDK de Armazenamento Azure nas Funções 1.x

Nas funções 1.x, os gatilhos de armazenamento e encadernações utilizam a versão 7.2.1 do pacote Azure Storage SDK[(WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet). Se se referir a uma versão diferente do SDK de armazenamento e se ligar a um tipo SDK de armazenamento na sua assinatura de função, o tempo de funcionamento das Funções pode relatar que não pode ligar-se a esse tipo. A solução é certificar-se de que o seu projeto faz referência ao [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
