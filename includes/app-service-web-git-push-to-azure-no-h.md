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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75681060"
---
Regresse à janela de terminal local e adicione um remoto do Azure ao seu repositório Git local. Substitua * \<a implementaçãoLocalGitUrl-from-create-step>* com o URL do comando Git que guardou de [Criar uma aplicação web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando o Git Credential Manager lhe pedir credenciais, certifique-se de introduzir as credenciais que criou na [Configuração de um utilizador](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user)de implementação , e não as credenciais que utiliza para iniciar sessão no portal Azure.

```bash
git push azure master
```

Este comando pode demorar alguns minutos a ser executado. Ao executar, apresenta informações semelhantes ao exemplo seguinte:
