---
title: Exibir uma loja de conhecimento (versão prévia) com Gerenciador de Armazenamento
titleSuffix: Azure Cognitive Search
description: Exiba e analise uma loja de conhecimento do Azure Pesquisa Cognitiva com o Gerenciador de Armazenamento do portal do Azure. a loja de conhecimento está atualmente em visualização pública.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: 176f0f197db3393c4ef8c37d05234fce41a494e1
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563212"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Exibir uma loja de conhecimento com Gerenciador de Armazenamento

> [!IMPORTANT] 
> A loja de conhecimento está atualmente em visualização pública. A funcionalidade de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de visualização. Atualmente, há suporte ao portal limitado e não há suporte para o SDK do .NET.

Neste artigo, você aprenderá, por exemplo, como se conectar e explorar uma loja de conhecimento usando Gerenciador de Armazenamento no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

+ Siga as etapas em [criar uma loja de conhecimento em portal do Azure](knowledge-store-create-portal.md) para criar o exemplo de armazenamento de conhecimento usado neste passo a passos.

+ Você também precisará do nome da conta de armazenamento do Azure que usou para criar a loja de conhecimento, junto com sua chave de acesso do portal do Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Exibir, editar e consultar uma loja de conhecimento em Gerenciador de Armazenamento

1. Na portal do Azure, [abra a conta de armazenamento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) que você usou para criar a loja de conhecimento.

1. No painel de navegação esquerdo da conta de armazenamento, clique em **Gerenciador de armazenamento**.

1. Expanda a lista **tabelas** para mostrar uma lista de projeções de tabela do Azure que foram criadas quando você executou o assistente de **importação de dados** em seus dados de exemplo de revisões do hotel.

Selecione qualquer tabela para exibir os dados aprimorados, incluindo frases-chave e pontuações de opiniões.

   ![Exibir tabelas no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Exibir tabelas no Gerenciador de Armazenamento")

Para alterar o tipo de dados de qualquer valor de tabela ou alterar valores individuais em sua tabela, clique em **Editar**. Quando você altera o tipo de dados para qualquer coluna em uma linha de tabela, ela será aplicada a todas as linhas.

   ![Editar tabela no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Editar tabela no Gerenciador de Armazenamento")

Para executar consultas, clique em **consulta** na barra de comandos e insira suas condições.  

   ![Tabela de consulta no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Tabela de consulta no Gerenciador de Armazenamento")

## <a name="clean-up"></a>Limpeza

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link **todos os recursos** ou **grupos de recursos** no painel de navegação esquerdo.

Se você estiver usando um serviço gratuito, lembre-se de que você está limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite.

## <a name="next-steps"></a>Passos seguintes

Conecte essa loja de conhecimento para Power BI para análise mais profunda ou avance com código, usando a API REST e o postmaster para criar um repositório de conhecimento diferente.

> [!div class="nextstepaction"]
> [Conectar-se com Power BI](knowledge-store-connect-power-bi.md)
> [criar uma loja de conhecimento em repouso](knowledge-store-howto.md)
