---
title: Partilha de Recursos de Origem Cruzada (CORS) em Azure Cosmos DB
description: Este artigo descreve como configurar a Partilha de Recursos de Origem Cruzada (CORS) em Azure Cosmos DB utilizando modelos do portal Azure e do Gestor de Recursos Azure.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: 863c31ecac13337ea3f91d7a7ced49b0f7141e58
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086419"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Configure a partilha de recursos de origem cruzada (CORS)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Cross-Origin Resource Sharing (CORS) é uma funcionalidade HTTP que permite a uma aplicação web em execução sob um domínio aceder a recursos em outro domínio. Os navegadores da Web implementam uma restrição de segurança conhecida como política de mesma origem que impede uma página web de chamar APIs em um domínio diferente. No entanto, o CORS fornece uma forma segura de permitir que o domínio de origem chame APIs em outro domínio. A API Core (SQL) em Azure Cosmos DB suporta agora a Partilha de Recursos de Origem Cruzada (CORS) utilizando o cabeçalho "AllowedOrigins". Depois de ativar o suporte DO CORS para a sua conta Azure Cosmos, apenas são avaliados pedidos autenticados para determinar se são permitidos de acordo com as regras especificadas.

Pode configurar a definição de partilha de recursos de origem cruzada (CORS) a partir do portal Azure ou a partir de um modelo de Gestor de Recursos Azure. Para contas cosmos usando a API Core (SQL), a Azure Cosmos DB suporta uma biblioteca JavaScript que funciona em ambientes Node.js e baseados no navegador. Esta biblioteca pode agora tirar partido do suporte DO CORS ao utilizar o modo Gateway. Não é necessária nenhuma configuração do lado do cliente para utilizar esta funcionalidade. Com o suporte do CORS, os recursos de um navegador podem aceder diretamente ao Azure Cosmos DB através da [biblioteca JavaScript](https://www.npmjs.com/package/@azure/cosmos) ou diretamente da [API REST](/rest/api/cosmos-db/) para operações simples.

> [!NOTE]
> O suporte do CORS só é aplicável e suportado para a API AZure Cosmos DB Core (SQL). Não é aplicável às APIs DB do Azure Cosmos para Cassandra, Gremlin ou MongoDB, uma vez que estes protocolos não utilizam HTTP para comunicação com servidores de clientes.

## <a name="enable-cors-support-from-azure-portal"></a>Ativar o suporte do CORS a partir do portal Azure

Utilize os seguintes passos para permitir a partilha de recursos de origem cruzada utilizando o portal Azure:

1. Navegue para a sua conta DB Azure cosmos. Abra a lâmina **CORS.**

2. Especifique uma lista de origens separadas por vírgula que pode fazer chamadas de origem cruzada para a sua conta DB Azure Cosmos. Por `https://www.mydomain.com` exemplo, `https://mydomain.com` . . . `https://api.mydomain.com` . Também pode usar um wildcard \* " " para permitir todas as origens e selecione Enviar por **isso.** 

   > [!NOTE]
   > Atualmente, não é possível utilizar wildcards como parte do nome de domínio. Por `https://*.mydomain.net` exemplo, o formato ainda não está suportado. 

   :::image type="content" source="./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png" alt-text="Permitir a partilha de recursos de origem cruzada utilizando o portal Azure":::

## <a name="enable-cors-support-from-resource-manager-template"></a>Ativar o suporte do CORS a partir do modelo de Gestor de Recursos

Para ativar o CORS utilizando um modelo de Gestor de Recursos, adicione a secção "cors" com a propriedade "AllowedOrigins" a qualquer modelo existente. O JSON seguinte é um exemplo de um modelo que cria uma nova conta Azure Cosmos com CORS ativada.

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

Hoje em dia, a biblioteca Azure Cosmos DB JavaScript tem apenas a versão CommonJS da biblioteca enviada com o seu pacote. Para utilizar esta biblioteca a partir do navegador, você tem que usar uma ferramenta como Rollup ou Webpack para criar uma biblioteca compatível com navegador. Algumas bibliotecas Node.js devem ter simulações de navegador para elas. Segue-se um exemplo de um ficheiro config de webpack que tem as definições de simulação necessárias.

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
 
Aqui está uma [amostra de código](https://github.com/christopheranderson/cosmos-browser-sample) que utiliza o TypeScript e o Webpack com a biblioteca SDK SDK Azure Cosmos DB Para construir uma aplicação Todo que envia atualizações em tempo real quando novos itens são criados.
Como uma boa prática, não use a chave primária para comunicar com a Azure Cosmos DB do navegador. Em vez disso, use fichas de recursos para comunicar. Para obter mais informações sobre fichas de recursos, consulte Garantir o acesso ao artigo [da Azure Cosmos DB.](secure-access-to-data.md#resource-tokens)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre outras formas de garantir a sua conta Azure Cosmos, consulte os seguintes artigos:

* [Configure uma firewall para o artigo da Azure Cosmos DB.](how-to-configure-firewall.md)

* [Configure a rede virtual e o acesso subterrâneo à sua conta DB Azure Cosmos](how-to-configure-vnet-service-endpoint.md)
