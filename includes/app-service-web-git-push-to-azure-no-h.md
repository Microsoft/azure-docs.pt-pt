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
ms.openlocfilehash: e5ba08da5c58ff486bc26f2c771dfef55452629a
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681060"
---
De volta à janela do terminal local, adicione um remoto do Azure ao repositório git local. Substitua *\<deploymentLocalGitUrl-from-create-step>* pelo URL do Git remoto que guardou em [Criar uma aplicação Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando o Git Credential Manager solicitar credenciais, certifique-se de inserir as credenciais criadas em [configurar um usuário de implantação](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user), não as credenciais usadas para entrar no portal do Azure.

```bash
git push azure master
```

Este comando pode demorar alguns minutos a ser executado. Ao executar, apresenta informações semelhantes ao exemplo seguinte:
