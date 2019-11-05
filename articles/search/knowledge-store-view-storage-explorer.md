---
title: Exibir uma loja de conhecimento com Gerenciador de Armazenamento
titleSuffix: Azure Cognitive Search
description: Exiba e analise uma loja de conhecimento do Azure Pesquisa Cognitiva com o Gerenciador de Armazenamento do portal do Azure.
manager: nitinme
author: lisaleib
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9ba11b69a51a5bc563764a7e75189bed67cb2ac6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485002"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Exibir uma loja de conhecimento com Gerenciador de Armazenamento

> [!Note]
> A loja de conhecimento está em versão prévia e não deve ser usada na produção. O portal e a [pesquisa REST API versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para o SDK do .NET no momento.
>
Neste artigo, você aprenderá, por exemplo, como se conectar e explorar uma loja de conhecimento usando Gerenciador de Armazenamento no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

+ Siga as etapas em [criar uma loja de conhecimento em portal do Azure](knowledge-store-create-portal.md) para criar o exemplo de armazenamento de conhecimento usado neste passo a passos.

+ Você também precisará do nome da conta de armazenamento do Azure que usou para criar a loja de conhecimento, junto com sua chave de acesso do portal do Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Exibir, editar e consultar uma loja de conhecimento em Gerenciador de Armazenamento

1. Na portal do Azure, [abra a conta de armazenamento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) que você usou para criar a loja de conhecimento.

1. No painel de navegação esquerdo da conta de armazenamento, clique em **Gerenciador de armazenamento**.

1. Expanda a lista **tabelas** para mostrar uma lista de projeções de tabela do Azure que foram criadas quando você executou o assistente de **importação de dados** em seus dados de exemplo de revisões do hotel.

Selecione qualquer tabela para exibir os dados aprimorados, incluindo frases-chave opiniões, dados de localização de latitude e longitude e muito mais.

   ![Exibir tabelas no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Exibir tabelas no Gerenciador de Armazenamento")

Para alterar o tipo de dados de qualquer valor de tabela ou alterar valores individuais em sua tabela, clique em **Editar**. Quando você altera o tipo de dados para qualquer coluna em uma linha de tabela, ela será aplicada a todas as linhas.

   ![Editar tabela no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Editar tabela no Gerenciador de Armazenamento")

Para executar consultas, clique em **consulta** na barra de comandos e insira suas condições.  

   ![Tabela de consulta no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Tabela de consulta no Gerenciador de Armazenamento")

## <a name="clean-up"></a>Limpeza

Quando você está trabalhando em sua própria assinatura, é uma boa ideia no final de um projeto identificar se você ainda precisa dos recursos que criou. Os recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir o conjunto inteiro de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link **todos os recursos** ou **grupos de recursos** no painel de navegação esquerdo.

Se você estiver usando um serviço gratuito, lembre-se de que você está limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite.

## <a name="next-steps"></a>Passos seguintes

Conecte essa loja de conhecimento para Power BI para análise mais profunda ou avance com código, usando a API REST e o postmaster para criar um repositório de conhecimento diferente.

> [!div class="nextstepaction"]
> [Conectar-se com Power BI](knowledge-store-connect-power-bi.md)
> [criar uma loja de conhecimento em repouso](knowledge-store-howto.md)
