---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597234"
---
#### <a name="local-proxy"></a>Procuração local

Pode configurar um proxy para a extensão do Código do Estúdio Visual do Servidor Ao Vivo que encaminha todos os pedidos `/api` para o ponto final da API em `http://127.0.0.1:7071/api` ...

1. Abra o ficheiro _.vscode/settings.json._

1. Adicione as seguintes definições para especificar um proxy para o Live Server.

   ```json
   "liveServer.settings.proxy": {
      "enable": true,
      "baseUri": "/api",
      "proxyUri": "http://127.0.0.1:7071/api"
   }
   ```

   Esta configuração é melhor guardada no ficheiro de definições do projeto, ao contrário do que acontece no ficheiro de definições do utilizador.

   A utilização de configurações de projeto garante que o proxy não é aplicado a todos os outros projetos abertos no Código do Estúdio Visual.
