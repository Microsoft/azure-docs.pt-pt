---
title: Atualize a versão Mongo da sua API da Azure Cosmos para a conta MongoDB
description: Como atualizar a versão do protocolo de arame MongoDB para as contas API da Azure Cosmos DB existentes para as contas mongoDB
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.openlocfilehash: 1818838a68c2712336a3515b2a82b5fdd518d237
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661176"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Atualize a versão API da sua Azure Cosmos DB API para a conta MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este artigo descreve como atualizar a versão API da sua conta Azure Cosmos DB para a conta MongoDB. Depois de fazer o upgrade, pode utilizar a mais recente funcionalidade na API da Azure Cosmos DB para o MongoDB. O processo de atualização não interrompe a disponibilidade da sua conta e não consome RU/s nem diminui a capacidade da base de dados em qualquer momento. Nenhum dado ou índice existente será afetado por este processo. 

Ao atualizar para uma nova versão API, comece com cargas de trabalho de desenvolvimento/teste antes de atualizar as cargas de trabalho de produção. É importante atualizar os seus clientes para uma versão compatível com a versão API a que está a atualizar antes de atualizar o seu API API AZure Cosmos para a conta MongoDB.

>[!Note]
> Neste momento, apenas as contas elegíveis utilizando a versão 3.2 do servidor podem ser atualizadas para as versões 3.6 ou 4.0. Se a sua conta não mostrar a opção de upgrade, por favor [preencha um bilhete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Upgrade para 4.0 ou 3.6

### <a name="benefits-of-upgrading-to-version-40"></a>Benefícios da atualização para a versão 4.0

Seguem-se as novas funcionalidades incluídas na versão 4.0:
- Suporte a transações multi-documentos dentro de coleções nãohardificadas.
- Novos operadores de agregação
- Desempenho da varredura melhorada
- Armazenamento mais rápido e eficiente

### <a name="benefits-of-upgrading-to-version-36"></a>Benefícios da atualização para a versão 3.6

Seguem-se as novas funcionalidades incluídas na versão 3.6:
- Desempenho e estabilidade melhorados
- Suporte para novos comandos da base de dados
- Suporte para o pipeline de agregação por fases de agregação novas e predefinidas
- Suporte para Fluxos de Alterações
- Suporte para Índices compostos
- Suporte entre partições para as seguintes operações: atualização, eliminação, contagem e ordenação
- Desempenho melhorado para as seguintes operações de agregação: $count, $skip, $limit e $group
- A indexação de carateres universais é agora suportada

### <a name="changes-from-version-32"></a>Alterações da versão 3.2

- Por predefinição, a função [De Retry (SSR) do Lado do Servidor (SSR)](prevent-rate-limiting-errors.md) está ativada, de modo que os pedidos da aplicação do cliente não irão devolver 16500 erros. Em vez disso, os pedidos serão retomados até completarem ou atingirem o intervalo de 60 segundos.
- O tempo limite por pedido está definido como 60 segundos.
- As coleções do MongoDB criadas na nova versão do protocolo de invocação terão apenas a propriedade `_id` indexada por predefinição.

### <a name="action-required-when-upgrading-from-32"></a>Ações necessárias para a atualização a partir do 3.2

Ao atualizar a partir de 3.2, o sufixo de ponto final da conta de base de dados será atualizado para o seguinte formato:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Se estiver a atualizar a partir da versão 3.2, terá de substituir o ponto final existente nas suas aplicações e controladores que se ligam a esta conta de base de dados. **Apenas as ligações que estiverem a utilizar o novo ponto final terão acesso às funcionalidades da nova versão API**. O anterior ponto final 3.2 deve ter o sufixo `.documents.azure.com` .

>[!Note]
> Este ponto final pode ter ligeiras diferenças se a sua conta foi criada numa Nuvem Soberana, Governamental ou Restrita.

## <a name="how-to-upgrade"></a>Como atualizar

1. Vá ao portal Azure e navegue para o seu Azure Cosmos DB API para a lâmina geral da conta MongoDB. Verifique se a versão atual do servidor é o que espera.

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Portal Azure com visão geral da conta do MongoDB" border="false":::

2. A partir das opções à esquerda, selecione a `Features` lâmina. Isto irá revelar as funcionalidades de nível de Conta que estão disponíveis para a sua conta de base de dados.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Portal Azure com visão geral da conta do MongoDB com lâmina de funcionalidades em destaque" border="false":::

3. Clique na `Upgrade Mongo server version` linha. Se não vir esta opção, a sua conta poderá não ser elegível para esta atualização. Por favor, preencha [um bilhete de apoio,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se for esse o caso.

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Possui lâmina com opções." border="false":::

4. Reveja as informações apresentadas sobre a atualização. Clique em `Enable` assim que estiver pronto para iniciar o processo.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Orientação de upgrade expandida." border="false":::

5. Após o início do processo, o `Features` menu mostrará o estado da atualização. O estatuto passará `Pending` de, para `In Progress` , para `Upgraded` . Este processo não afetará a funcionalidade ou operações existentes da conta de base de dados.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Estado de atualização após o início." border="false":::

6. Uma vez concluída a atualização, o estado mostrará como `Upgraded` . Clique nele para saber mais sobre os próximos passos e ações que você precisa tomar para finalizar o processo. Por favor contacte [o suporte](https://azure.microsoft.com/en-us/support/create-ticket/) se houver algum problema a processar o seu pedido.

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Estado da conta atualizada." border="false":::

7. 
    1. Se tiver atualizado a partir do 3.2, volte para a `Overview` lâmina e copie a nova cadeia de ligação para usar na sua aplicação. A antiga corda de ligação que funciona 3.2 não será interrompida. Para garantir uma experiência consistente, todas as suas aplicações devem utilizar o novo ponto final.
    2. Se for atualizado a partir de 3.6, a sua cadeia de ligação existente será atualizada para a versão especificada e deverá continuar a ser utilizada.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Nova lâmina de visão geral." border="false":::


## <a name="how-to-downgrade"></a>Como desvalorizar
Também pode reduzir a sua conta de 4.0 para 3.6 através dos mesmos passos na secção 'Como Actualizar'. 

Se tiver atualizado de 3.2 para (4.0 ou 3.6) e desejar reduzir para 3.2, pode simplesmente voltar a utilizar a sua ligação anterior (3.2) com o `accountname.documents.azure.com` anfitrião, que permanece ativa após a atualização da versão 3.2.


## <a name="next-steps"></a>Passos seguintes

- Conheça as funcionalidades suportadas e não suportadas [da versão 4.0 do MongoDB](mongodb-feature-support-40.md).
- Conheça as funcionalidades suportadas e não suportadas [da versão 3.6 do MongoDB](mongodb-feature-support-36.md).
- Para mais informações consulte as [funcionalidades da versão Mongo 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
