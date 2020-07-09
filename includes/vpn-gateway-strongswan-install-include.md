---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "69520871"
---
Foi utilizada a seguinte configuração para os passos abaixo:

  | | |
  |---|---|
  |Computador| Ubuntu Server 18.04|
  |Dependências| strongSwan |


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

Utilize o seguinte comando para instalar a interface de linha de comando Azure:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Instruções adicionais sobre como instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
