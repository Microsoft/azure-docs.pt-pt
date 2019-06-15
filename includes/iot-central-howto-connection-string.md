---
title: incluir ficheiro
description: incluir ficheiro
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66151972"
---
1. Utilize o `dps-keygen` utilitário de linha de comandos para gerar uma cadeia de ligação:

    Para instalar o [utilitário do gerador de chaves](https://github.com/Azure/dps-keygen), execute o seguinte comando:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Para gerar uma cadeia de ligação, execute o comando seguinte com os detalhes de ligação que apontou anteriormente:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Copie a cadeia de ligação do `dps-keygen` saída para utilizar no seu código de dispositivo.