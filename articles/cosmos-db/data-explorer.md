---
title: Utilize o Explorador do Azure Cosmos DB para gerir os seus dados
description: O Explorador do Azure Cosmos DB é uma interface de baseada na web autónoma que permite-lhe ver e gerir os dados armazenados no Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096811"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Trabalhar com dados com o explorador do Azure Cosmos 

O Explorador do Azure Cosmos DB é uma interface de baseada na web autónoma que permite-lhe ver e gerir os dados armazenados no Azure Cosmos DB. O explorador de DB Azure Cosmos é equivalente ao separador **Data Explorer** existente que está disponível no portal Azure quando cria uma conta Azure Cosmos DB. As principais vantagens do Explorador do Azure Cosmos DB sobre o Data explorer existente são:

* Tem um total imóveis do ecrã para ver os seus dados, executar consultas, procedimentos armazenados, disparadores e exiba os resultados.  

* Pode fornecer leitura permanente ou temporária ou de acesso de leitura / escrita para a sua conta de base de dados e respetivas coleções a outros utilizadores que não têm acesso ao portal do Azure ou de subscrição.  

* Pode compartilhar os resultados da consulta com outros utilizadores que não têm acesso ao portal do Azure ou de subscrição.  

## <a name="access-azure-cosmos-db-explorer"></a>Explorador do acesso do Azure Cosmos DB

1. Inscreva-se no [Portal Azure.](https://portal.azure.com/) 

2. De **todos os recursos,** encontre e navegue até à sua conta Azure Cosmos DB, selecione Keys e copie a Cadeia de **Ligação Primária**.  

3. Vá para https://cosmos.azure.com/, cola a corda de ligação e selecione **Ligar**. Ao utilizar a cadeia de ligação, pode acessar o Explorador do Azure Cosmos DB sem limites de tempo.  

   Se quiser fornecer outros utilizadores acesso temporário à sua conta do Azure Cosmos DB, pode fazê-lo ao utilizar os URLs de acesso de leitura e de leitura / escrita. 

4. Abra a lâmina do Explorador de **Dados,** selecione **Open Full Screen**. A partir do diálogo pop-up, pode ver dois URLs de acesso – **Read-Write** e **Read**. Estes URLs permitem-lhe partilhar a sua conta do Azure Cosmos DB temporariamente com outros utilizadores. Acesso à conta de expira dentro de 24 horas após o qual poderá restabelecer a ligação com um novo URL de acesso ou a cadeia de ligação. 

   **Read-Write** – Quando partilha o URL de Leitura-Escrita com outros utilizadores, eles podem visualizar e modificar as bases de dados, coleções, consultas e outros recursos associados a essa conta específica.

   **Leia** - Quando partilha o URL apenas para leitura com outros utilizadores, eles podem ver as bases de dados, coleções, consultas e outros recursos associados a essa conta específica. Por exemplo, se pretender partilhar resultados de uma consulta com seus colegas de equipe que não tem acesso ao portal do Azure ou a sua conta do Azure Cosmos DB, pode fornecê-los com este URL.

   Escolha o tipo de acesso com que gostaria de abrir a conta e clique em **Abrir**. Depois de abrir o Explorador, a experiência é mesmo, pois era necessário com o separador de Data Explorer no portal do Azure.   

   ![Abra o Explorador do Azure Cosmos DB](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Problemas conhecidos

Atualmente, a experiência **Open Full Screen** que permite partilhar o acesso temporário de leitura ou leitura ainda não é suportada para as contas Azure Cosmos DB Gremlin e Table API. Pode continuar a ver as contas do Gremlin e API de tabela, passando a cadeia de ligação para o Explorador do Azure Cosmos DB. 

Atualmente, a visualização de documentos que contenham um UUID não é suportado no Data Explorer. Isto não afeta as coleções de carregamento, apenas visualizando documentos ou consultas individuais que incluam estes documentos. Para visualizar e gerir estes documentos, os utilizadores devem continuar a utilizar a ferramenta que foi originalmente utilizada para criar estes documentos.

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu como começar com o Explorador do Azure Cosmos DB para gerir os seus dados, em seguida, pode:

* Comece a definir [consultas](sql-api-query-reference.md) utilizando sintaxe SQL e execute [a programação lateral](stored-procedures-triggers-udfs.md) do servidor utilizando procedimentos armazenados, UDFs, gatilhos. 
