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
ms.openlocfilehash: f02fa49b62a2e3d617617a20518810209d3879b7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95564342"
---
Foi utilizada a seguinte configuração para os passos abaixo:

- Computador: Ubuntu Server 18.04
- Dependências: strongSwan


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

[Instruções adicionais sobre como instalar o Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)