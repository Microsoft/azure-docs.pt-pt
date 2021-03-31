---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67184863"
---
Se python encontrar um erro ao iniciar a sua aplicação, apenas uma simples página de erro será devolvida (por exemplo, "A página não pode ser exibida porque ocorreu um erro interno do servidor.").

Para capturar erros de aplicação Python:

1. No portal Azure, na sua aplicação web, selecione **Definições**.
2. No **separador Definições,** selecione **definições de aplicação**.
3. Nas **definições da App, insira** o seguinte par de teclas/valor:
    * Chave : WSGI_LOG
    * Valor : D:\home\site\wwwroot\logs.txt (insira a sua escolha de nome de ficheiro)

Deve agora ver erros no ficheiro logs.txt na pasta wwwroot.
