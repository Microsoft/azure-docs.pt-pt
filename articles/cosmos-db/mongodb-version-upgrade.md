---
title: Atualize a versão Mongo da sua API da Azure Cosmos para a conta MongoDB
description: Como atualizar a versão do protocolo de arame MongoDB para as contas API da Azure Cosmos DB existentes para as contas mongoDB
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: chrande
ms.openlocfilehash: 8865a16c2840b65f432de679c6dd63b285b1f760
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771829"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Atualize a versão API da sua Azure Cosmos DB API para a conta MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este artigo descreve como atualizar a versão API da sua conta Azure Cosmos DB para a conta MongoDB. Depois de fazer o upgrade, pode utilizar a mais recente funcionalidade na API da Azure Cosmos DB para o MongoDB. O processo de atualização não interrompe a disponibilidade da sua conta e não consome RU/s nem diminui a capacidade da base de dados em qualquer momento. Nenhum dado ou índice existente será afetado por este processo. 

Ao atualizar para uma nova versão API, comece com cargas de trabalho de desenvolvimento/teste antes de atualizar as cargas de trabalho de produção. É importante atualizar os seus clientes para uma versão compatível com a versão API a que está a atualizar antes de atualizar o seu API API AZure Cosmos para a conta MongoDB.

>[!Note]
> Neste momento, apenas as contas elegíveis utilizando a versão 3.2 do servidor podem ser atualizadas para as versões 3.6 ou 4.0. Se a sua conta não mostrar a opção de upgrade, por favor [preencha um bilhete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Atualizar para a versão 4.0 ou 3.6

### <a name="benefits-of-upgrading-to-version-40"></a>Benefícios da atualização para a versão 4.0

Seguem-se as novas funcionalidades incluídas na versão 4.0:
- Suporte a transações multi-documentos dentro de coleções nãohardificadas.
- Novos operadores de agregação
- Desempenho de análise melhorado
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

### <a name="changes-from-version-32"></a>Alterações da versão 3.2

- Por predefinição, a funcionalidade [Repetição do Lado do Servidor (SSR)](prevent-rate-limiting-errors.md) está ativada, pelo que os pedidos da aplicação cliente não irão devolver erros 16500. Em vez disso, os pedidos serão retomados até serem concluídos ou atingirem o tempo limite de 60 segundos.
- O tempo limite por pedido está definido como 60 segundos.
- As coleções do MongoDB criadas na nova versão do protocolo de invocação terão apenas a propriedade `_id` indexada por predefinição.

### <a name="action-required-when-upgrading-from-32"></a>Ação necessária ao atualizar a partir da versão 3.2

Ao atualizar a partir da versão 3.2, o sufixo do ponto final da conta de base de dados será atualizado para o seguinte formato:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Se estiver a atualizar a partir da versão 3.2, terá de substituir o ponto final existente nas suas aplicações e controladores que se ligam a esta conta de base de dados. **Apenas as ligações que utilizarem o novo ponto final terão acesso às funcionalidades da nova versão da API**. O ponto final anterior da versão 3.2 deve ter o sufixo `.documents.azure.com`.

>[!Note]
> Este ponto final pode ter ligeiras diferenças se a sua conta foi criada numa Nuvem Soberana, Governamental ou Restrita.

## <a name="how-to-upgrade"></a>Como atualizar

1. Inscreva-se no [portal Azure.](https://portal.azure.com/)

1. Navegue para a sua Azure Cosmos DB API para a conta MongoDB. Abra o **painel de visão geral** e verifique se a versão atual do **Servidor** é 3.2 ou 3.6.

    :::image type="content" source="./media/mongodb-version-upgrade/check-current-version.png" alt-text="Consulte a versão atual da sua conta MongoDB a partir do portal Azure." border="true":::

1. A partir do menu esquerdo, abra o `Features` painel. Este painel mostra as funcionalidades de nível de conta que estão disponíveis para a sua conta de base de dados.

1. Selecione a linha `Upgrade MongoDB server version`. Se não vir esta opção, a sua conta poderá não ser elegível para esta atualização. Por favor, preencha [um bilhete de apoio,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se for esse o caso.

    :::image type="content" source="./media/mongodb-version-upgrade/upgrade-server-version.png" alt-text="Abra a lâmina 'Características' e atualize a sua conta." border="true":::

1. Veja as informações apresentadas sobre a atualização. Selecione `Set server version to 4.0` (ou 3.6 dependendo da versão atual).

    :::image type="content" source="./media/mongodb-version-upgrade/select-upgrade.png" alt-text="Reveja a orientação de upgrade e selecione upgrade." border="true":::

1. Depois de iniciar a atualização, o menu **'Característica'** está acinzentado e o estado está definido para *Pendente*. A atualização leva cerca de 15 minutos para ser concluída. Este processo não irá afetar a funcionalidade ou operações existentes na sua conta de base de dados. Depois de concluído, o estado da **versão do servidor Update MongoDB** mostrará a versão atualizada. Por favor contacte [o suporte](https://azure.microsoft.com/en-us/support/create-ticket/) se houver algum problema a processar o seu pedido.

1. Seguem-se algumas considerações após a atualização da sua conta:

    1. Se tiver atualizado a partir do 3.2, volte ao painel **de visão geral** e copie o novo fio de ligação para utilizar na sua aplicação. A cadeia de ligação antiga que executa a versão 3.2 não será interrompida. Para garantir uma experiência consistente, todas as suas aplicações devem utilizar o novo ponto final.

    1. Se tiver atualizado a partir da versão 3.6, a sua cadeia de ligação existente será atualizada para a versão especificada e deverá continuar a ser utilizada.

## <a name="how-to-downgrade"></a>Como mudar para uma versão anterior

Também pode reduzir a sua conta de 4.0 para 3.6 através dos mesmos passos na secção 'Como Actualizar'.

Se tiver atualizado de 3.2 para (4.0 ou 3.6) e desejar reduzir para 3.2, pode simplesmente voltar a utilizar a sua ligação anterior (3.2) com o `accountname.documents.azure.com` anfitrião, que permanece ativa após a atualização da versão 3.2.

## <a name="next-steps"></a>Passos seguintes

- Conheça as funcionalidades suportadas e não suportadas da [versão 4.0 do MongoDB](mongodb-feature-support-40.md).
- Conheça as funcionalidades suportadas e não suportadas da [versão 3.6 do MongoDB](mongodb-feature-support-36.md).
- Para obter mais informações, veja as [funcionalidades da versão 3.6 do Mongo](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
