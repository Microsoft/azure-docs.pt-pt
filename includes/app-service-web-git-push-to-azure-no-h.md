---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8539696f4521a1b4a2f56fe7d2936b45dec26ec9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998054"
---
Regresse à janela de terminal local e adicione um remoto do Azure ao seu repositório Git local. *\<deploymentLocalGitUrl-from-create-step>* Substitua-o pelo URL do comando Git que guardou de [criar uma aplicação web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando o Git Credential Manager lhe pedir credenciais, certifique-se de que introduz as credenciais criadas em **Configurar um utilizador de implementação**, e não as credenciais que utiliza para iniciar sessão no portal Azure.

```bash
git push azure master
```

Este comando pode demorar alguns minutos a ser executado. Ao executar, apresenta informações semelhantes ao exemplo seguinte:
