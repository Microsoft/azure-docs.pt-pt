---
title: CORS (compartilhamento de recursos entre origens) no Azure Cosmos DB
description: Este artigo descreve como configurar o CORS (compartilhamento de recursos entre origens) no Azure Cosmos DB usando os modelos portal do Azure e Azure Resource Manager.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: 82c49854611e6c425b75f0830a1402c8f5a4694e
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299169"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Configurar o CORS (compartilhamento de recursos entre origens) 

O CORS (compartilhamento de recursos entre origens) é um recurso HTTP que permite que um aplicativo Web em execução em um domínio acesse recursos em outro domínio. Os navegadores da Web implementam uma restrição de segurança conhecida como política de mesma origem que impede que uma página da Web chame APIs em um domínio diferente. No entanto, o CORS fornece uma maneira segura de permitir que o domínio de origem chame APIs em outro domínio. A API básica (SQL) no Azure Cosmos DB agora dá suporte ao compartilhamento de recursos entre origens (CORS) usando o cabeçalho "allowedOrigins". Depois de habilitar o suporte a CORS para sua conta do Azure Cosmos, somente as solicitações autenticadas serão avaliadas para determinar se são permitidas de acordo com as regras que você especificou.

Você pode configurar a configuração de compartilhamento de recursos entre origens (CORS) do portal do Azure ou de um modelo de Azure Resource Manager. Para contas de Cosmos usando a API básica (SQL), o Azure Cosmos DB dá suporte a uma biblioteca JavaScript que funciona em ambientes de Node. js e baseados em navegador. Essa biblioteca agora pode aproveitar o suporte a CORS ao usar o modo de gateway. Não há nenhuma configuração do lado do cliente necessária para usar esse recurso. Com o suporte a CORS, os recursos de um navegador podem acessar diretamente Azure Cosmos DB por meio da [biblioteca JavaScript](https://www.npmjs.com/package/@azure/cosmos) ou diretamente da [API REST](https://docs.microsoft.com/rest/api/cosmos-db/) para operações simples. 

> [!NOTE]
> O suporte a CORS só é aplicável e tem suporte para a API do Azure Cosmos DB Core (SQL). Ele não é aplicável às APIs de Azure Cosmos DB para Cassandra, Gremlin ou MongoDB, pois esses protocolos não usam HTTP para comunicação de cliente-servidor. 

## <a name="enable-cors-support-from-azure-portal"></a>Habilitar suporte a CORS de portal do Azure

Use as etapas a seguir para habilitar o compartilhamento de recursos entre origens usando portal do Azure:

1. Navegue até sua conta do Azure Cosmos DB. Abra a folha **CORS** .

2. Especifique uma lista separada por vírgulas de origens que podem fazer chamadas entre origens para sua conta de Azure Cosmos DB. Por exemplo, `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. Você também pode usar um curinga "\*" para permitir todas as origens e selecionar **Enviar**. 

   > [!NOTE]
   > No momento, não é possível usar curingas como parte do nome de domínio. Por exemplo, o formato `https://*.mydomain.net` ainda não tem suporte. 
   
   ![Habilitar compartilhamento de recursos entre origens usando o portal do Azure](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)
 
## <a name="enable-cors-support-from-resource-manager-template"></a>Habilitar suporte a CORS no modelo do Resource Manager

Para habilitar o CORS usando um modelo do Resource Manager, adicione a seção "CORS" com a propriedade "allowedOrigins" a qualquer modelo existente. O JSON a seguir é um exemplo de um modelo que cria uma nova conta do Azure cosmos com CORS habilitado.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "name": "test",
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2015-04-08",
            "location": "East US 2",
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "test-eastus2",
                        "failoverPriority": 0,
                        "locationName": "East US 2"
                    }
                ],
                "cors": [
                    {
                        "allowedOrigins": "*"
                    }
                ]
            },
            "dependsOn": [
            ]
        }
    ]
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Usando a biblioteca Azure Cosmos DB JavaScript de um navegador

Hoje, a Azure Cosmos DB biblioteca JavaScript tem apenas a versão CommonJS da biblioteca fornecida com seu pacote. Para usar essa biblioteca do navegador, você precisa usar uma ferramenta como ROLLUP ou webpack para criar uma biblioteca compatível com o navegador. Determinadas bibliotecas do node. js devem ter simulações de navegador para elas. Veja a seguir um exemplo de um arquivo de configuração do webpack que tem as configurações de simulação necessárias.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Aqui está um [exemplo de código](https://github.com/christopheranderson/cosmos-browser-sample) que usa TypeScript e webpack com a Azure Cosmos DB biblioteca SDK do JavaScript para criar um aplicativo de tarefas pendentes que envia atualizações em tempo real quando novos itens são criados.
Como prática recomendada, não use a chave primária para se comunicar com Azure Cosmos DB no navegador. Em vez disso, use tokens de recurso para se comunicar. Para obter mais informações sobre tokens de recurso, consulte [protegendo o acesso ao Azure Cosmos DB](secure-access-to-data.md#resource-tokens) artigo.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre outras maneiras de proteger sua conta do Azure Cosmos, consulte os seguintes artigos:

* [Configurar um firewall para Azure Cosmos DB](how-to-configure-firewall.md) artigo.

* [Configurar a rede virtual e o acesso baseado em sub-rede para sua conta de Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
    

