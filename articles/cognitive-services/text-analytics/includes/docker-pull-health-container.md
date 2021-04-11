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
ms.date: 03/25/2021
ms.author: aahi
ms.openlocfilehash: 8595af7f46b63f9991d6a02279ccad76afb38311
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089724"
---
Preencha e envie o formulário de [pedido de Serviços Cognitivos](https://aka.ms/csgate) para solicitar o acesso ao Texto Analytics para pré-visualização do público em saúde.  Esta aplicação aplica-se tanto ao contentor como à pré-visualização pública da API web acolhida.
O formulário solicita informações sobre si, sobre a sua empresa e sobre o cenário de utilizador para o qual utilizará o recipiente. Depois de ter apresentado o formulário, a equipa dos Serviços Cognitivos do Azure revê-o para garantir que cumpre os critérios de acesso ao registo privado de contentores.

> [!IMPORTANT]
> * No formulário, deve utilizar um endereço de e-mail associado a um ID de assinatura Azure.
> * O recurso Text Analytics (ponto final de faturação e apikey) que utiliza para executar o contentor deve ter sido criado com o ID de assinatura Azure aprovado. 
> * Consulte o seu e-mail (pastas de caixa de entrada e lixo) para obter atualizações sobre o estado da sua aplicação da Microsoft.
> * Este URL do contentor mudou, veja os exemplos abaixo. O contentor não estará disponível para download a partir de `containerpreview.azurecr.io` 26 de abril de 2021.


Uma vez aprovado, utilize o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para descarregar esta imagem do contentor do registo público do contentor da Microsoft.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```
