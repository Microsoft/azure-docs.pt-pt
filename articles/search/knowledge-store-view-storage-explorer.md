---
title: Exibir uma loja de conhecimento com Gerenciador de Armazenamento Azure Search
description: Exiba e analise uma loja de conhecimento Azure Search com o Gerenciador de Armazenamento do portal do Azure.
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 6c90cec91e85f64397f70b015ffde15a2ea6deaf
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962974"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Exibir uma loja de conhecimento com Gerenciador de Armazenamento

> [!Note]
> A loja de conhecimento está em versão prévia e não deve ser usada na produção. A [API REST do Azure Search versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para o SDK do .NET no momento.
>
Neste artigo, você aprenderá a se conectar e explorar uma loja de conhecimento usando Gerenciador de Armazenamento no portal do Azure. Para criar a amostra da loja de conhecimento usada neste passo a passos, consulte [criar uma loja de conhecimento em portal do Azure](knowledge-store-create-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Siga as etapas em [criar uma loja de conhecimento em portal do Azure](knowledge-store-create-portal.md) para criar o exemplo de armazenamento de conhecimento usado neste passo a passos.

+ Você também precisará do nome da conta de armazenamento do Azure que usou para criar a loja de conhecimento, junto com sua chave de acesso do portal do Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Exibir, editar e consultar uma loja de conhecimento em Gerenciador de Armazenamento

1. Na portal do Azure, abra a conta de armazenamento que você usou para criar a loja de conhecimento.

1. No painel de navegação esquerdo da conta de armazenamento, clique em **Gerenciador de armazenamento**.

1. Expanda a lista **tabelas** para mostrar uma lista de projeções de tabela do Azure que foram criadas quando você executou o assistente de **importação de dados** em seus dados de exemplo de revisões do hotel.

Selecione qualquer tabela para exibir os dados aprimorados, incluindo frases-chave opiniões, dados de localização de latitude e longitude e muito mais.

   ![Exibir tabelas no Gerenciador de armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Exibir tabelas no Gerenciador de armazenamento")

Para alterar o tipo de dados de qualquer valor de tabela ou alterar valores individuais em sua tabela, clique em **Editar**. Quando você altera o tipo de dados para qualquer coluna em uma linha de tabela, ela será aplicada a todas as linhas.

   ![Editar tabela no Gerenciador de armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Editar tabela no Gerenciador de armazenamento")

Para executar consultas, clique em **consulta** na barra de comandos e insira suas condições.  

   ![Tabela de consulta no Gerenciador de armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Tabela de consulta no Gerenciador de armazenamento")

## <a name="clean-up"></a>Limpeza

Quando você está trabalhando em sua própria assinatura, é uma boa ideia no final de um projeto identificar se você ainda precisa dos recursos que criou. Os recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir o conjunto inteiro de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link **todos os recursos** ou **grupos de recursos** no painel de navegação esquerdo.

Se você estiver usando um serviço gratuito, lembre-se de que você está limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite.

## <a name="next-steps"></a>Passos Seguintes

Para saber como conectar essa loja de conhecimento a Power BI, consulte o artigo a seguir.

> [!div class="nextstepaction"]
> [Ligar com o Power BI](knowledge-store-connect-power-bi.md)

Para saber como criar uma loja de conhecimento usando as APIs REST e o postmaster, consulte o artigo a seguir.  

> [!div class="nextstepaction"]
> [Criar um repositório de conhecimento em repouso](knowledge-store-howto.md)
