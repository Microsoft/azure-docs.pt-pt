---
title: Utilize o Explorador do Azure Cosmos DB para gerir os seus dados
description: O Explorador do Azure Cosmos DB é uma interface de baseada na web autónoma que permite-lhe ver e gerir os dados armazenados no Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 04cfdd1f96f83898710b6f292116f0afddc8df96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237240"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Trabalhar com dados com o explorador do Azure Cosmos 

O Explorador do Azure Cosmos DB é uma interface de baseada na web autónoma que permite-lhe ver e gerir os dados armazenados no Azure Cosmos DB. O Explorador do Azure Cosmos DB é equivalente a existente **Data Explorer** separador que está disponível no portal do Azure ao criar uma conta do Azure Cosmos DB. As principais vantagens do Explorador do Azure Cosmos DB sobre o Data explorer existente são:

* Tem um total imóveis do ecrã para ver os seus dados, executar consultas, procedimentos armazenados, disparadores e exiba os resultados.  

* Pode fornecer leitura permanente ou temporária ou de acesso de leitura / escrita para a sua conta de base de dados e respetivas coleções a outros utilizadores que não têm acesso ao portal do Azure ou de subscrição.  

* Pode compartilhar os resultados da consulta com outros utilizadores que não têm acesso ao portal do Azure ou de subscrição.  

## <a name="access-azure-cosmos-db-explorer"></a>Explorador do acesso do Azure Cosmos DB

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/). 

2. Partir **todos os recursos**, localize e navegue para a sua conta do Azure Cosmos DB, selecione as chaves e copiar o **cadeia de ligação primária**.  

3. Aceda a https://cosmos.azure.com/, cole a cadeia de ligação e selecione **Connect**. Ao utilizar a cadeia de ligação, pode acessar o Explorador do Azure Cosmos DB sem limites de tempo.  

   Se quiser fornecer outros utilizadores acesso temporário à sua conta do Azure Cosmos DB, pode fazê-lo ao utilizar os URLs de acesso de leitura e de leitura / escrita. 

4. Abra o **Data Explorer** painel, selecione **abrir ecrã inteiro**. Na caixa de diálogo pop-up, pode ver dois aceder aos URLs – **leitura / escrita** e **leitura**. Estes URLs permitem-lhe partilhar a sua conta do Azure Cosmos DB temporariamente com outros utilizadores. Acesso à conta de expira dentro de 24 horas após o qual poderá restabelecer a ligação com um novo URL de acesso ou a cadeia de ligação. 

   **Leitura e escrita** -se de que partilhe o URL de leitura / escrita com outros utilizadores podem ver e modificar as bases de dados, coleções, consultas e outros recursos associados essa conta específica.

   **Leitura** - quando partilha o URL de só de leitura com outros utilizadores podem visualizar as bases de dados, coleções, consultas e outros recursos associados essa conta específica. Por exemplo, se pretender partilhar resultados de uma consulta com seus colegas de equipe que não tem acesso ao portal do Azure ou a sua conta do Azure Cosmos DB, pode fornecê-los com este URL.

   Escolha o tipo de acesso que pretende abrir a conta com e clique em **abra**. Depois de abrir o Explorador, a experiência é mesmo, pois era necessário com o separador de Data Explorer no portal do Azure.   

   ![Abra o Explorador do Azure Cosmos DB](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Problemas conhecidos

Atualmente o **aberto de ecrã inteiro** experiência que permite-lhe partilhar leitura-escrita temporário ou de acesso de leitura ainda não é suportada para contas do Azure Cosmos DB Gremlin e API de tabela. Pode continuar a ver as contas do Gremlin e API de tabela, passando a cadeia de ligação para o Explorador do Azure Cosmos DB. 

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu como começar com o Explorador do Azure Cosmos DB para gerir os seus dados, em seguida, pode:

* Começar a definir [consultas](sql-api-query-reference.md) usando a sintaxe SQL e realizar [programação do lado de servidor](stored-procedures-triggers-udfs.md) usando procedimentos armazenados, UDFs, acionadores. 
