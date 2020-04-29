---
title: Partilha de recursos de origem cruzada (CORS) em Azure Cosmos DB
description: Este artigo descreve como configurar a Partilha de Recursos De Origem Cruzada (CORS) no Azure Cosmos DB utilizando modelos de portal Azure e Azure Resource Manager.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: 7a487cb10965a379a0a418efaa061be88c5d10dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77082992"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Configure partilha de recursos de origem cruzada (CORS)

Cross-Origin Resource Sharing (CORS) é uma funcionalidade HTTP que permite que uma aplicação web que funciona sob um domínio aceda a recursos noutro domínio. Os navegadores da Web implementam uma restrição de segurança conhecida como política de origem mesma que impede uma página web de chamar APIs num domínio diferente. No entanto, o CORS fornece uma forma segura de permitir que o domínio de origem chame APIs em outro domínio. O Core (SQL) API em Azure Cosmos DB suporta agora a Partilha de Recursos Cross-Origin (CORS) utilizando o cabeçalho "allowedOrigins". Depois de ativar o suporte cors para a sua conta Azure Cosmos, apenas são avaliados pedidos autenticados para determinar se são permitidos de acordo com as regras que especificou.

Pode configurar a definição de partilha de recursos de origem cruzada (CORS) a partir do portal Azure ou a partir de um modelo de Gestor de Recursos Azure. Para contas cosmos usando o Core (SQL) API, o Azure Cosmos DB suporta uma biblioteca JavaScript que funciona tanto em ambientes nonómicos como em ambientes baseados em navegador. Esta biblioteca pode agora tirar partido do suporte cors ao utilizar o modo Gateway. Não é necessária nenhuma configuração do lado do cliente para utilizar esta funcionalidade. Com suporte cors, os recursos de um navegador podem aceder diretamente ao Azure Cosmos DB através da [biblioteca JavaScript](https://www.npmjs.com/package/@azure/cosmos) ou diretamente da [API REST](https://docs.microsoft.com/rest/api/cosmos-db/) para operações simples.

> [!NOTE]
> O suporte cors é apenas aplicável e suportado para a API Azure Cosmos DB Core (SQL). Não é aplicável às APIs DB Azure Cosmos para Cassandra, Gremlin ou MongoDB, uma vez que estes protocolos não utilizam http para comunicação cliente-servidor.

## <a name="enable-cors-support-from-azure-portal"></a>Ativar o suporte cors do portal Azure

Utilize os seguintes passos para permitir a partilha de recursos de origem cruzada utilizando o portal Azure:

1. Navegue para a sua conta Azure cosmos DB. Abra a lâmina **CORS.**

2. Especifique uma lista separada de origens que possa fazer chamadas de origem cruzada para a sua conta Azure Cosmos DB. Por `https://www.mydomain.com`exemplo, `https://mydomain.com` `https://api.mydomain.com`. . Também pode utilizar um\*wildcard " para permitir todas as origens e selecionar **Submeter**. 

   > [!NOTE]
   > Atualmente, não pode usar wildcards como parte do nome de domínio. Por `https://*.mydomain.net` exemplo, o formato ainda não é suportado. 

   ![Permitir a partilha de recursos de origem cruzada utilizando o portal Azure](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)

## <a name="enable-cors-support-from-resource-manager-template"></a>Ativar suporte CORS a partir do modelo de Gestor de Recursos

Para ativar o CORS utilizando um modelo de Gestor de Recursos, adicione a secção "cors" com a propriedade "allowedOrigins" a qualquer modelo existente. O JSON seguinte é um exemplo de um modelo que cria uma nova conta Azure Cosmos com CORS ativado.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Usando a biblioteca Azure Cosmos DB JavaScript a partir de um navegador

Hoje, a biblioteca Azure Cosmos DB JavaScript só tem a versão CommonJS da biblioteca enviada com o seu pacote. Para utilizar esta biblioteca a partir do navegador, tem de utilizar uma ferramenta como rollup ou Webpack para criar uma biblioteca compatível com navegador. Algumas bibliotecas nonóios devem ter simulações de navegador para eles. Segue-se um exemplo de um ficheiro webpack config que tem as definições de simulação necessárias.

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
 
Aqui está uma amostra de [código](https://github.com/christopheranderson/cosmos-browser-sample) que usa TypeScript e Webpack com a biblioteca Azure Cosmos DB JavaScript SDK para construir uma aplicação Todo que envia atualizações em tempo real quando novos itens são criados.
Como uma boa prática, não utilize a chave principal para comunicar com o Azure Cosmos DB a partir do navegador. Em vez disso, use fichas de recursos para comunicar. Para obter mais informações sobre fichas de recursos, consulte garantir o acesso ao artigo da [Azure Cosmos DB.](secure-access-to-data.md#resource-tokens)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre outras formas de garantir a sua conta Azure Cosmos, consulte os seguintes artigos:

* [Configure uma firewall para o artigo da Azure Cosmos DB.](how-to-configure-firewall.md)

* [Configure a rede virtual e o acesso baseado em subnet para a sua conta Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
