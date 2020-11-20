---
title: incluir ficheiro
description: incluir ficheiro
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987774"
---
Quando executar a aplicação do dispositivo de amostra mais tarde neste tutorial, precisa dos seguintes valores de configuração:

* Âmbito de identificação: Na sua aplicação IoT Central, navegue para **a Administração > Ligação do Dispositivo**. Tome nota do valor do âmbito de **identificação.**
* Chave primária do grupo: Na sua aplicação IoT Central, navegue para **a Administração > a Ligação do Dispositivo > dispositivos SAS-IoT**. Tome nota do valor da chave **principal de** acesso partilhado.

Utilize a Cloud Shell para gerar uma chave de dispositivo a partir da chave SAS do grupo que acabou de recuperar:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

Tome nota da chave do dispositivo gerado, use-a mais tarde neste tutorial.
