---
title: Docker puxa para o recipiente de saúde
titleSuffix: Azure Cognitive Services
description: Docker puxa comando para Text Analytics para recipiente de saúde
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 519625f6468372ec7ace523dae7648212f4f3203
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779161"
---
Preencha e envie o formulário de [pedido de solicitação dos recipientes dos Serviços Cognitivos](https://aka.ms/csgate) para solicitar o acesso ao contentor.
O formulário solicita informações sobre si, sobre a sua empresa e sobre o cenário de utilizador para o qual utilizará o recipiente. Depois de ter apresentado o formulário, a equipa dos Serviços Cognitivos do Azure revê-o para garantir que cumpre os critérios de acesso ao registo privado de contentores.

> [!IMPORTANT]
> * No formulário, deve utilizar um endereço de e-mail associado a um ID de assinatura Azure.
> * O recurso Azure que utiliza para executar o contentor deve ter sido criado com o ID de assinatura Azure aprovado. 
> * Consulte o seu e-mail (pastas de caixa de entrada e lixo) para obter atualizações sobre o estado da sua aplicação da Microsoft.

Utilize o comando de login do estivador com credenciais fornecidas no seu email de embarque para ligar ao nosso registo privado de contentores para recipientes de Serviços Cognitivos.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para descarregar esta imagem do contentor do nosso registo privado de contentores.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
