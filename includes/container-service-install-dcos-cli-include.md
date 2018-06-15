---
title: Instalar o DC/SO CLI | Microsoft Docs
description: Instalar o DC/SO CLI.
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Contentores, Microserviços, DC/OS, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2016
ms.author: rogardle
ms.openlocfilehash: a8ea47f158c0d666340815d2e039995c7483257f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
ms.locfileid: "23761969"
---
> [!NOTE]
> Esta ação é para trabalhar com clusters de ACS baseados em DC/SO. Não é necessário efetuar este procedimento para clusters de ACS com base em Swarm.
> 
> 

Primeiro, [ligue-se ao seu cluster de ACS baseado no DC/SO](../articles/container-service/container-service-connect.md). Depois de ter efetuado esta ação, pode instalar o DC/SO CLI no computador do seu cliente com comandos abaixo:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Se estiver a utilizar uma versão antiga do Python, poderá reparar alguns "InsecurePlatformWarnings". Pode ignorar com segurança estes avisos.

Para poder começar a trabalhar sem reiniciar o shell, execute:

```bash
source ~/.bashrc
```

Este passo não será necessário quando iniciar novamente o shell.

Agora, pode confirmar que o CLI está instalado:

```bash
dcos --help
```