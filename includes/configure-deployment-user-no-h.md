---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: a972b4738ce5646a1ee9eed6495bdc43a40826fd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219046"
---
A FTP e o Git local podem implementar para uma aplicação web Azure utilizando um *utilizador de implementação.* Uma vez configurar o utilizador de implementação, pode usá-lo para todas as suas implementações Azure. O nome de utilizador e palavra-passe de implementação ao nível da sua conta são diferentes das suas credenciais de subscrição Azure. 

Para configurar o utilizador de implementação, executar o comando [de configuração de implementação az webapp](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) em Azure Cloud Shell. Substitua \<username> e por um nome de utilizador de \<password> implementação e senha. 

- O nome de utilizador deve ser único dentro de Azure, e para os impulsos locais de Git, não deve conter o \@ símbolo ' 
- A palavra-passe deve ter pelo menos oito caracteres, com dois dos seguintes três elementos: letras, números e símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra a palavra-passe como `null` . Se obtiver o erro `'Conflict'. Details: 409`, altere o nome de utilizador. Se obtiver o `'Bad Request'. Details: 400` erro, utilize uma palavra-passe mais forte. 

Grave o seu nome de utilizador e palavra-passe para usar para implementar as suas aplicações web.
