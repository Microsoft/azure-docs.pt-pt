---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543567"
---
| Propriedade | Descrição | Exemplo | Necessário |
|---|---|---|---|
| `app_location` | Localização do seu código de inscrição. | Insira `/` se o código fonte da sua aplicação estiver na raiz do repositório, ou se o seu código de `/app` aplicação estiver num diretório chamado `app` . | Yes |
| `api_location` | Localização do seu código Azure Functions. | Introduza `/api` se o seu código de aplicação estiver numa pasta chamada `api` . Se nenhuma aplicação Azure Functions for detetada na pasta, a construção não falha, o fluxo de trabalho assume que não quer uma API. | No |
| `output_location` | Localização do diretório de saída de construção relativamente ao `app_location` . | Se o código fonte da sua aplicação estiver localizado em `/app` , e o script de construção de saídas de saídas para a `/app/build` pasta, então definido `build` como o `output_location` valor. | Não |