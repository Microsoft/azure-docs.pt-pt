---
title: Atualize a versão Mongo da sua API da Azure Cosmos para a conta MongoDB
description: Como atualizar a versão do protocolo de arame MongoDB para as contas API da Azure Cosmos DB existentes para as contas mongoDB
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: jasonh
ms.openlocfilehash: c6369be39d0a964f07c64083e3269bb1c0c49c7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91409668"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>Atualize a versão do protocolo de arame MongoDB da sua API da Azure Cosmos para a conta MongoDB

Este artigo descreve como atualizar a versão do protocolo de fio da sua API da Azure Cosmos para a conta MongoDB. Depois de atualizar a versão do protocolo de fio, pode utilizar a mais recente funcionalidade na API da Azure Cosmos DB para o MongoDB. O processo de atualização não interrompe a disponibilidade da sua conta e não consome RU/s nem diminui a capacidade da base de dados em qualquer momento. Nenhum dado ou índice existente será afetado por este processo.

>[!Note]
> Neste momento, apenas as contas elegíveis utilizando a versão 3.2 do servidor podem ser atualizadas para a versão 3.6. Se a sua conta não mostrar a opção de upgrade, por favor [preencha um bilhete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-from-version-32-to-36"></a>Upgrade da versão 3.2 para 3.6

### <a name="benefits-of-upgrading-to-version-36"></a>Benefícios da atualização para a versão 3.6

Seguem-se as novas funcionalidades incluídas na versão 3.6:
- Desempenho e estabilidade melhorados
- Suporte para novos comandos da base de dados
- Suporte para o pipeline de agregação por fases de agregação novas e predefinidas
- Suporte para Fluxos de Mudança
- Suporte para Índices Compostos
- Suporte de partição cruzada para as seguintes operações: atualização, exclusão, contagem e classificação
- Melhor desempenho das seguintes operações agregadas: $count, $skip, $limit e $group
- A indexação wildcard está agora suportada

### <a name="changes-from-version-32"></a>Alterações da versão 3.2

- **Pedido Os erros do RequestRateIsLarge foram removidos**. Os pedidos da aplicação do cliente não devolverão mais 16500 erros. Em vez disso, os pedidos serão retomados até completarem ou cumprirem o tempo limite.
- Por pedido, o tempo limite está definido para 60 segundos.
- As coleções MongoDB criadas na nova versão do protocolo de fio só terão a `_id` propriedade indexada por padrão.

### <a name="action-required"></a>Ação necessária

Para a atualização para a versão 3.6, o sufixo de ponto final da conta de base de dados será atualizado para o seguinte formato:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Tem de substituir o ponto final existente nas suas aplicações e controladores que se conectam com esta conta de base de dados. **Apenas as ligações que estiverem a utilizar o novo ponto final terão acesso às funcionalidades da versão 3.6 do MongoDB**. O ponto final anterior deve ter o sufixo `.documents.azure.com` .

>[!Note]
> Este ponto final pode ter ligeiras diferenças se a sua conta foi criada numa Nuvem Soberana, Governamental ou Restrita.

### <a name="how-to-upgrade"></a>Como atualizar

1. Primeiro, vá ao portal Azure e navegue para o seu Azure Cosmos DB API para a lâmina geral da conta MongoDB. Verifique se a versão do servidor é `3.2` . 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Portal Azure com visão geral da conta do MongoDB" border="false":::

2. A partir das opções à esquerda, selecione a `Features` lâmina. Isto irá revelar as funcionalidades de nível de Conta que estão disponíveis para a sua conta de base de dados.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Portal Azure com visão geral da conta do MongoDB" border="false":::

3. Clique na `Upgrade to Mongo server version 3.6` linha. Se não vir esta opção, a sua conta poderá não ser elegível para esta atualização. Por favor, preencha [um bilhete de apoio,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se for esse o caso.

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Portal Azure com visão geral da conta do MongoDB" border="false":::

4. Reveja as informações apresentadas sobre esta atualização específica. Note que a atualização só será concluída até que as suas aplicações utilizem o ponto final atualizado, conforme realçado nesta secção. Clique em `Enable` assim que estiver pronto para iniciar o processo.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Portal Azure com visão geral da conta do MongoDB" border="false":::

5. Após o início do processo, o `Features` menu mostrará o estado da atualização. O estatuto passará `Pending` de, para `In Progress` , para `Upgraded` . Este processo não afetará a funcionalidade ou operações existentes da conta de base de dados.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Portal Azure com visão geral da conta do MongoDB" border="false":::

6. Uma vez concluída a atualização, o estado mostrará como `Upgraded` . Clique nele para saber mais sobre os próximos passos e ações que você precisa tomar para finalizar o processo. Por favor contacte [o suporte](https://azure.microsoft.com/en-us/support/create-ticket/) se houver algum problema a processar o seu pedido.

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Portal Azure com visão geral da conta do MongoDB" border="false":::

7. **Para começar a utilizar a versão atualizada da sua conta de base de dados,** volte para a `Overview` lâmina e copie o novo fio de ligação para utilizar na sua aplicação. As aplicações vão começar a utilizar a versão atualizada assim que se ligarem ao novo ponto final. As ligações existentes não serão interrompidas e podem ser atualizadas à sua conveniência. Para garantir uma experiência consistente, todas as suas aplicações devem utilizar o novo ponto final.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Portal Azure com visão geral da conta do MongoDB" border="false":::

## <a name="next-steps"></a>Passos seguintes

- Conheça as funcionalidades suportadas e não suportadas [da versão 3.6 do MongoDB](mongodb-feature-support-36.md).
- Para mais informações consulte as [funcionalidades da versão Mongo 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
