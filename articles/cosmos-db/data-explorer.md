---
title: Utilize o explorador DB da Azure Cosmos para gerir os seus dados
description: O Azure Cosmos DB explorer é uma interface independente baseada na web que permite visualizar e gerir os dados armazenados em Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.author: dech
ms.openlocfilehash: 691a2e56230d312416aed3d68bffd361f1d63558
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637120"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Trabalhar com dados com o explorador do Azure Cosmos 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB explorer é uma interface independente baseada na web que permite visualizar e gerir os dados armazenados em Azure Cosmos DB. O explorador DB Azure Cosmos é equivalente ao separador **Data Explorer** existente que está disponível no portal Azure quando cria uma conta DB Azure Cosmos. As principais vantagens do explorador DB da Azure Cosmos sobre o explorador de dados existente são:

* Você tem um imóvel de ecrã completo para ver os seus dados, executar consultas, procedimentos armazenados, gatilhos e ver os seus resultados.  

* Pode fornecer acesso temporário ou permanente à sua conta de base de dados e às suas coleções a outros utilizadores que não tenham acesso ao portal ou subscrição do Azure.  

* Pode partilhar os resultados da consulta com outros utilizadores que não tenham acesso ao portal Azure ou subscrição.  

## <a name="access-azure-cosmos-db-explorer"></a>Aceda a Azure Cosmos DB explorer

1. Inscreva-se no [portal Azure](https://portal.azure.com/). 

2. A partir de **todos os recursos,** encontre e navegue até à sua conta DB Azure Cosmos, selecione Keys e copie a **Cadeia de Ligação Primária**.  

3. Vá https://cosmos.azure.com/ para, cole a cadeia de ligação e selecione **Connect**. Ao utilizar a cadeia de ligação, pode aceder ao explorador DB Azure Cosmos sem limites de tempo.  

   Se pretender fornecer acesso temporário a outros utilizadores à sua conta DB Azure Cosmos, pode fazê-lo utilizando os URLs de leitura e de leitura. 

4. Abra a lâmina **do Data Explorer,** selecione **Abrir o Ecrã Completo**. A partir do diálogo pop-up, pode ver dois URLs de acesso – **Ler** e **Ler**. Estes URLs permitem-lhe partilhar a sua conta DB Azure Cosmos temporariamente com outros utilizadores. O acesso à conta expira em 24 horas após o qual pode voltar a ligar-se utilizando um novo URL de acesso ou a cadeia de ligação. 

   **Ler -Escrever** – Quando partilha o URL Read-Write com outros utilizadores, eles podem visualizar e modificar as bases de dados, coleções, consultas e outros recursos associados a essa conta específica.

   **Ler** - Quando partilha o URL apenas de leitura com outros utilizadores, eles podem ver as bases de dados, coleções, consultas e outros recursos associados a essa conta específica. Por exemplo, se quiser partilhar resultados de uma consulta com os seus colegas de equipa que não têm acesso ao portal Azure ou à sua conta DB Azure Cosmos, pode fornecer-lhes este URL.

   Escolha o tipo de acesso que pretende abrir a conta e clique em **Abrir**. Depois de abrir o explorador, a experiência é a mesma que tinha com o separador Data Explorer no portal Azure.

   :::image type="content" source="./media/data-explorer/open-data-explorer-with-access-url.png" alt-text="Open Azure Cosmos DB explorador":::

## <a name="known-issues"></a>Problemas conhecidos

Atualmente, a experiência **Open Full Screen** que lhe permite partilhar o acesso temporário de leitura ou leitura ainda não é suportada para as contas Azure Cosmos DB Gremlin e Table API. Você ainda pode ver as suas contas API Gremlin e Table, passando a cadeia de conexão para Azure Cosmos DB Explorer. 

Atualmente, a visualização de documentos que contenham um UUID não é suportada no Data Explorer. Isto não afeta as cobranças de carregamento, apenas a visualização de documentos individuais ou consultas que incluam estes documentos. Para visualizar e gerir estes documentos, os utilizadores devem continuar a utilizar a ferramenta que foi originalmente utilizada para criar estes documentos.

Os clientes que recebem erros HTTP-401 podem dever-se a permissões Azure RBAC insuficientes para a conta Azure do cliente, especialmente se a conta tiver um papel personalizado. Quaisquer funções personalizadas devem ter `Microsoft.DocumentDB/databaseAccounts/listKeys/*` ação para utilizar o Data Explorer se iniciar a sua assinatura utilizando as suas credenciais de Diretório Ativo Azure.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a começar com o explorador DB da Azure Cosmos para gerir os seus dados, a seguir pode:

* Comece a definir [consultas](./sql-query-getting-started.md) utilizando a sintaxe SQL e execute [a programação lateral do servidor](stored-procedures-triggers-udfs.md) utilizando procedimentos armazenados, UDFs, gatilhos.
