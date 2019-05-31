---
title: Recursos de várias origens (CORS) de partilha no Azure Cosmos DB
description: Este artigo descreve como configurar a Cross-Origin Resource Sharing (CORS) no Azure Cosmos DB com o portal do Azure e os modelos Azure Resource Manager.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 1269c4c2405e9b906b63c8a29c0de1ac217da1d7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241898"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Configurar recursos de várias origens (CORS) de partilha 

Cross-Origin Resource Sharing (CORS) é uma funcionalidade HTTP que permite que uma aplicação web em execução num domínio aceder a recursos em outro domínio. Navegadores da Web implementam uma restrição de segurança, conhecida como diretiva de mesma origem impede que uma página da web de chamar APIs num domínio diferente. No entanto, o CORS fornece uma forma segura para permitir que o domínio de origem chamar as APIs em outro domínio. API de SQL Core no Azure Cosmos DB suporta agora Cross-Origin Resource Sharing (CORS) com o cabeçalho "allowedOrigins". Depois de ativar o suporte do CORS para a sua conta do Cosmos do Azure, apenas pedidos autenticados são avaliados para determinar se são permitidos, de acordo com as regras que especificou.

Pode configurar o definição de (CORS) do portal do Azure ou a partir de um modelo Azure Resource Manager de partilha de recursos de várias origens. API de SQL Core no Azure Cosmos DB oferece suporte a uma biblioteca de JavaScript que funcione em node. js e ambientes baseados no browser. Esta biblioteca pode agora tirar partido do suporte CORS ao utilizar o modo de Gateway. Não existe nenhuma configuração do lado do cliente necessária para utilizar esta funcionalidade. Com suporte para CORS, recursos a partir de um navegador podem aceder diretamente ao Azure Cosmos DB através do [biblioteca JavaScript](https://www.npmjs.com/package/@azure/cosmos) ou diretamente a partir do [REST API](https://docs.microsoft.com/rest/api/cosmos-db/) para operações simples. 

## <a name="enable-cors-support-from-azure-portal"></a>Ativar o suporte CORS no portal do Azure

Utilize os seguintes passos para ativar a partilha de recursos de várias origens de mensagens em fila com o portal do Azure:

1. Navegue até à sua conta do Azure cosmos DB. Abra o **CORS** painel.

2. Especifica uma lista separada por vírgulas de origens que pode fazer chamadas de várias origens para a sua conta do Azure Cosmos DB. Por exemplo, `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. Também pode utilizar um caráter universal "\*" para permitir todas as origens e selecione **submeter**. 

   > [!NOTE]
   > Atualmente, não é possível utilizar caracteres curinga como parte do nome do domínio. Por exemplo `https://*.mydomain.net` formato ainda não é suportado. 
   
   ![Ativar a partilha de recursos de transversais através do portal do Azure](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)
 
## <a name="enable-cors-support-from-resource-manager-template"></a>Ativar o suporte CORS do modelo do Resource Manager

Para ativar a CORS utilizando um modelo do Resource Manager, adicione a secção de "cors" com a propriedade de "allowedOrigins" para qualquer modelo existente. O JSON seguinte é um exemplo de um modelo que cria uma nova conta do Cosmos do Azure com a CORS ativada.

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

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Usando a biblioteca de JavaScript do Azure Cosmos DB a partir de um browser

Hoje em dia, a biblioteca JavaScript do Azure Cosmos DB tem apenas a versão de CommonJS da biblioteca fornecida com o seu pacote. Para utilizar esta biblioteca a partir do browser, terá de utilizar uma ferramenta como o Rollup ou Webpack para criar uma biblioteca compatível do navegador. Determinados bibliotecas de node. js devem ter as simulações de navegador para eles. Segue-se um exemplo de um ficheiro de configuração de webpack que tem as definições necessárias de simulação.

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
 
Aqui está uma [exemplo de código](https://github.com/christopheranderson/cosmos-browser-sample) que utiliza o TypeScript e Webpack com a biblioteca de JavaScript SDK do Azure Cosmos DB para criar uma aplicação de tarefas que envia atualizações em tempo real, quando são criados novos itens.
Como melhor prática, não utilize a chave primária para comunicar com o Azure Cosmos DB a partir do browser. Em vez disso, utilize tokens de recursos para se comunicar. Para obter mais informações sobre os tokens de recursos, consulte [proteger o acesso ao Azure Cosmos DB](secure-access-to-data.md#resource-tokens) artigo.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre outras formas de proteger a sua conta do Cosmos do Azure, veja os artigos seguintes:

* [Configurar uma firewall para o Azure Cosmos DB](how-to-configure-firewall.md) artigo.

* [Configurar a rede virtual e o acesso com base na sub-rede para a sua conta do Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
    

