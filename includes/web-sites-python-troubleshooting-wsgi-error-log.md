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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66137038"
---
Se o Python encontra um erro ao iniciar a sua aplicação, apenas uma página de erro simples será retornada (por exemplo "A página não pode ser apresentada porque ocorreu um erro de servidor interno.").

Para capturar erros de aplicações Python:

1. No portal do Azure, na sua aplicação web, selecione **definições**.
2. Sobre o **definições** separador, selecione **as definições da aplicação**.
3. Sob **as definições da aplicação**, introduza o seguinte par chave/valor:
    * Chave: WSGI_LOG
    * Valor: D:\home\site\wwwroot\logs.txt (introduzir a sua escolha de nome de ficheiro)

Deverá ver erros no ficheiro na pasta wwwroot logs.txt.
