---
title: Ver uma loja de conhecimento com o Storage Explorer
titleSuffix: Azure Cognitive Search
description: Ver e analisar uma loja de conhecimentos de Pesquisa Cognitiva Azure com o Explorador de Armazenamento do portal Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: ba0b02067c032f9038051c169866588ded44af73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85566015"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Ver uma loja de conhecimento com o Storage Explorer

Neste artigo, você aprenderá por exemplo como conectar e explorar uma loja de conhecimento usando Storage Explorer no portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

+ Siga os passos na [Criar uma loja de conhecimento no portal Azure](knowledge-store-create-portal.md) para criar a loja de conhecimentos de amostras utilizada nesta passagem.

+ Também vai precisar do nome da conta de armazenamento Azure que usou para criar a loja de conhecimento, juntamente com a sua chave de acesso a partir do portal Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Ver, editar e consultar uma loja de conhecimentos no Storage Explorer

1. No portal Azure, [abra a conta de Armazenamento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) que usou para criar a loja de conhecimento.

1. No painel de navegação à esquerda da conta de armazenamento, clique no **Storage Explorer**.

1. Expanda a lista **de TABELAS** para mostrar uma lista de projeções da tabela Azure que foram criadas quando executou o assistente **de Dados de Importação** nos dados da amostra de avaliação do seu hotel.

Selecione qualquer tabela para ver os dados enriquecidos, incluindo frases-chave e pontuações de sentimento.

   ![Ver tabelas no Explorador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Ver tabelas no Explorador de Armazenamento")

Para alterar o tipo de dados para qualquer valor de tabela ou para alterar valores individuais na sua tabela, clique em **Editar**. Quando alterar o tipo de dados para qualquer coluna numa linha de tabela, será aplicado a todas as linhas.

   ![Editar tabela no Explorador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Editar tabela no Explorador de Armazenamento")

Para executar consultas, clique em **Consultar** a barra de comando e insira as suas condições.  

   ![Tabela de consultas no Explorador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Tabela de consultas no Explorador de Armazenamento")

## <a name="clean-up"></a>Limpeza

Ao trabalhar na sua própria subscrição, depois de concluir um projeto, recomendamos que verifique se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite.

## <a name="next-steps"></a>Passos seguintes

Ligue esta loja de conhecimentos ao Power BI para uma análise mais profunda, ou avance com o código, utilizando a API REST e o Carteiro para criar uma loja de conhecimento diferente.

> [!div class="nextstepaction"]
> [Conecte-se com Power BI](knowledge-store-connect-power-bi.md) 
>  [Criar uma loja de conhecimento em REST](knowledge-store-create-rest.md)
