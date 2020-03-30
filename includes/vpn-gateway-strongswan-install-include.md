---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "69520871"
---
A seguinte configuração foi utilizada para os passos abaixo:

  | | |
  |---|---|
  |Computador| Ubuntu Server 18.04|
  |Dependências| forteCisne |


Utilize os seguintes comandos para instalar a configuração strongSwan necessária:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Utilize o seguinte comando para instalar a interface da linha de comando Azure:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Instruções adicionais sobre como instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
