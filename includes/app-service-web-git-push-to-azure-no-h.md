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
ms.openlocfilehash: 08f4e76869f124d946566f64da394c895d0af308
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102205994"
---
Regresse à janela de terminal local e adicione um remoto do Azure ao seu repositório Git local. *\<deploymentLocalGitUrl-from-create-step>* Substitua-o pelo URL do comando Git que guardou de [criar uma aplicação web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando o Git Credential Manager lhe pedir credenciais, certifique-se de que introduz as credenciais criadas em **Configurar um utilizador de implementação**, e não as credenciais que utiliza para iniciar sessão no portal Azure.

```bash
git push azure main
```

Este comando pode demorar alguns minutos a ser executado. Ao executar, apresenta informações semelhantes ao exemplo seguinte:
