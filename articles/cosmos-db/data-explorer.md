---
title: Use o explorador DB Azure Cosmos para gerir os seus dados
description: O explorador de DB Azure Cosmos é uma interface autónoma baseada na web que permite visualizar e gerir os dados armazenados no Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096811"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Trabalhar com dados com o explorador do Azure Cosmos 

O explorador de DB Azure Cosmos é uma interface autónoma baseada na web que permite visualizar e gerir os dados armazenados no Azure Cosmos DB. O explorador de DB Azure Cosmos é equivalente ao separador **Data Explorer** existente que está disponível no portal Azure quando cria uma conta Azure Cosmos DB. As principais vantagens do explorador de DB Azure Cosmos sobre o explorador de dados existente são:

* Você tem um imobiliário de tela completa para ver os seus dados, executar consultas, procedimentos armazenados, gatilhos e ver os seus resultados.  

* Pode fornecer acesso temporário ou permanente à leitura ou leitura da sua conta de base de dados e às suas coleções a outros utilizadores que não tenham acesso ao portal ou subscrição do Azure.  

* Pode partilhar os resultados da consulta com outros utilizadores que não tenham acesso ao portal Ou subscrição do Azure.  

## <a name="access-azure-cosmos-db-explorer"></a>Access Azure Cosmos DB explorador

1. Inscreva-se no [Portal Azure.](https://portal.azure.com/) 

2. De **todos os recursos,** encontre e navegue até à sua conta Azure Cosmos DB, selecione Keys e copie a Cadeia de **Ligação Primária**.  

3. Vá https://cosmos.azure.com/colar a corda de ligação e selecione **Ligar**. Ao utilizar a cadeia de ligação, pode aceder ao explorador de DB Azure Cosmos sem limites de tempo.  

   Se pretender fornecer a outros utilizadores acesso temporário à sua conta Azure Cosmos DB, pode fazê-lo utilizando os URLs de leitura e leitura de acesso. 

4. Abra a lâmina do Explorador de **Dados,** selecione **Open Full Screen**. A partir do diálogo pop-up, pode ver dois URLs de acesso – **Read-Write** e **Read**. Estes URLs permitem-lhe partilhar temporariamente a sua conta Azure Cosmos DB com outros utilizadores. O acesso à conta expira em 24 horas após o qual pode voltar a ligar utilizando um novo URL de acesso ou a cadeia de ligação. 

   **Read-Write** – Quando partilha o URL de Leitura-Escrita com outros utilizadores, eles podem visualizar e modificar as bases de dados, coleções, consultas e outros recursos associados a essa conta específica.

   **Leia** - Quando partilha o URL apenas para leitura com outros utilizadores, eles podem ver as bases de dados, coleções, consultas e outros recursos associados a essa conta específica. Por exemplo, se quiser partilhar os resultados de uma consulta com os seus colegas de equipa que não têm acesso ao portal Azure ou à sua conta Azure Cosmos DB, pode fornecer-lhes este URL.

   Escolha o tipo de acesso com que gostaria de abrir a conta e clique em **Abrir**. Depois de abrir o explorador, a experiência é a mesma que teve com o separador Data Explorer no portal Azure.   

   ![Explorador DB Open Azure Cosmos](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Problemas conhecidos

Atualmente, a experiência **Open Full Screen** que permite partilhar o acesso temporário de leitura ou leitura ainda não é suportada para as contas Azure Cosmos DB Gremlin e Table API. Ainda pode ver as suas contas Gremlin e Table API, passando a cadeia de ligação ao Azure Cosmos DB Explorer. 

Atualmente, a visualização de documentos que contenham um UUID não é suportado no Data Explorer. Isto não afeta as coleções de carregamento, apenas visualizando documentos ou consultas individuais que incluam estes documentos. Para visualizar e gerir estes documentos, os utilizadores devem continuar a utilizar a ferramenta que foi originalmente utilizada para criar estes documentos.

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a começar com o explorador de DB da Azure Cosmos para gerir os seus dados, a seguir pode:

* Comece a definir [consultas](sql-api-query-reference.md) utilizando sintaxe SQL e execute [a programação lateral](stored-procedures-triggers-udfs.md) do servidor utilizando procedimentos armazenados, UDFs, gatilhos. 
