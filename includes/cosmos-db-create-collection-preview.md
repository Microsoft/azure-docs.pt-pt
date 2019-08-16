---
title: incluir ficheiro
description: incluir ficheiro
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 331886f01345aba576cd8f96f95077f9bbdae704
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "68002678"
---
Agora você pode usar a ferramenta Data Explorer no portal do Azure para criar um banco de dados e um contêiner. 

1. Clique em **Data Explorer** > **novo contêiner**. 
    
    A área **Adicionar contêiner** é exibida na extrema direita, talvez seja necessário rolar para a direita para vê-la.

    ![A folha Data Explorer portal do Azure, adicionar contêiner](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. Na página **Adicionar contêiner** , insira as configurações para o novo contêiner.

    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|Tarefas|Designe a nova base de dados como *Tarefas*. Os nomes das bases de dados devem conter de 1 a 255 carateres e não podem conter /, \\, #, ?, ou um espaço à direita. Marque a opção provisionar **taxa de transferência do banco de dados** , que permite que você compartilhe a taxa de transferência provisionada para o banco de dados em todos os contêineres no banco de dados. Essa opção também ajuda na economia de custos. |
    |**Débito**|400|Deixe a taxa de transferência em 400 unidades de solicitação por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.| 
    |**ID do contêiner**|Itens|Insira os *itens* como o nome do novo contêiner. As IDs de contêiner têm os mesmos requisitos de caractere que os nomes de banco de dados.|
    |**Chave de partição**| /categoria| O exemplo descrito neste artigo usa */Category* como a chave de partição. Definir uma chave de partição permite que Azure Cosmos DB dimensione sua coleção para atender às necessidades de armazenamento e taxa de transferência do seu aplicativo. Em geral, uma boa opção de chave de partição é aquela que tem uma ampla variedade de valores distintos e resulta em uma distribuição uniforme de armazenamento e solicitação de volume em sua carga de trabalho. [Saiba mais sobre particionamento.](../articles/cosmos-db/partitioning-overview.md)|
    
    Além das configurações anteriores, você pode opcionalmente adicionar **chaves exclusivas** para o contêiner. Vamos deixar o campo vazio neste exemplo. As chaves exclusivas oferecem aos programadores a capacidade de adicionar uma camada de integridade dos dados na base de dados. Ao criar uma política de chave exclusiva ao criar um contêiner, você garante a exclusividade de um ou mais valores por chave de partição. Para saber mais, consulte o artigo [Chaves exclusivas no Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Selecione **OK**. Data Explorer exibe o novo banco de dados e o contêiner.

