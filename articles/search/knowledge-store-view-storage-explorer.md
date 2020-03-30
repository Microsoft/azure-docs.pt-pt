---
title: Ver uma loja de conhecimentos (pré-visualização) com o Storage Explorer
titleSuffix: Azure Cognitive Search
description: Ver e analisar uma loja de conhecimentos de Pesquisa Cognitiva Azure com o Explorador de Armazenamento do portal Azure. a loja de conhecimento está atualmente em pré-visualização pública.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: 167316eca1f85530a040d4543f98ae34a9fb93c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754062"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Ver uma loja de conhecimentos com o Storage Explorer

> [!IMPORTANT] 
> A loja de conhecimento está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . A [versão REST API 2019-05-06-Preview](search-api-preview.md) fornece funcionalidades de pré-visualização. Existe atualmente um suporte de portal limitado e nenhum suporte sdk .NET.

Neste artigo, você aprenderá por exemplo como conectar e explorar uma loja de conhecimento usando o Storage Explorer no portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

+ Siga os passos na [Create a knowledge store no portal Azure](knowledge-store-create-portal.md) para criar a loja de conhecimento de amostras utilizada nesta passagem.

+ Também necessitará do nome da conta de armazenamento Azure que usou para criar a loja de conhecimentos, juntamente com a sua chave de acesso a partir do portal Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Ver, editar e consultar uma loja de conhecimentos no Storage Explorer

1. No portal Azure, [abra a conta de Armazenamento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) que usou para criar a loja de conhecimento.

1. No painel de navegação esquerdo da conta de armazenamento, clique no **Storage Explorer**.

1. Expanda a lista **TABLE** para mostrar uma lista de projeções de tabelas Azure que foram criadas quando executou o assistente de **dados de importação** nos dados da amostra do seu hotel.

Selecione qualquer tabela para visualizar os dados enriquecidos, incluindo frases-chave e pontuações de sentimento.

   ![Ver tabelas no Explorador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Ver tabelas no Explorador de Armazenamento")

Para alterar o tipo de dados para qualquer valor de tabela ou para alterar valores individuais na sua tabela, clique em **Editar**. Quando alterar o tipo de dados para qualquer coluna numa linha de tabela, será aplicado a todas as linhas.

   ![Editar tabela no Explorador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Editar tabela no Explorador de Armazenamento")

Para fazer consultas, clique em **Consultar** na barra de comando e insira as suas condições.  

   ![Mesa de consulta no Explorador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Mesa de consulta no Explorador de Armazenamento")

## <a name="clean-up"></a>Limpeza

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite.

## <a name="next-steps"></a>Passos seguintes

Ligue esta loja de conhecimentos ao Power BI para uma análise mais profunda, ou avance com código, utilizando a Rest API e o Carteiro para criar uma loja de conhecimento diferente.

> [!div class="nextstepaction"]
> [Conecte-se com power bi](knowledge-store-connect-power-bi.md)
> Criar uma loja de[conhecimentos em REST](knowledge-store-create-rest.md)
