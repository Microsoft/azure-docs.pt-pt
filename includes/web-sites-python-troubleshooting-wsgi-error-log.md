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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67184863"
---
Se python encontrar um erro durante o início da sua aplicação, apenas uma simples página de erro será devolvida (por exemplo, "A página não pode ser exibida porque ocorreu um erro interno do servidor.").

Para capturar erros de aplicação python:

1. No portal Azure, na sua aplicação web, selecione **Definições**.
2. No separador **Definições,** selecione **definições de aplicação**.
3. Nas definições da **App,** introduza o seguinte par chave/valor:
    * Chave : WSGI_LOG
    * Valor : D:\home\site\wwwroot\logs.txt (insira a sua escolha de nome de ficheiro)

Deverá agora ver erros no ficheiro logs.txt na pasta wwwroot.
