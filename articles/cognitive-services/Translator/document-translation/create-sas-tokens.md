---
title: Criar ficha de acesso partilhado (SAS) para recipientes e bolhas com o Microsoft Storage Explorer
description: Como criar um Token de Acesso Partilhado (SAS) para contentores e bolhas com o Microsoft Storage Explorer e o portal Azure
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: e40fc569ad1c8ec5894f06915422bea37cfc40ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102489638"
---
# <a name="create-sas-tokens-for-document-translation-processing"></a>Criar fichas SAS para processamento de tradução de documentos

Neste artigo, você vai aprender como criar fichas de acesso compartilhado (SAS) usando o Azure Storage Explorer ou o portal Azure. Um token SAS fornece acesso seguro e delegado aos recursos na sua conta de armazenamento Azure.

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>Crie os seus tokens SAS com O Explorador de Armazenamento Azure

### <a name="prerequisites"></a>Pré-requisitos

* Você precisará de uma aplicação [**Azure Storage Explorer**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) instalada no seu ambiente de desenvolvimento Windows, macOS ou Linux. O Azure Storage Explorer é uma ferramenta gratuita que lhe permite gerir facilmente os seus recursos de armazenamento em nuvem Azure.
* Depois da aplicação Azure Storage Explorer ser instalada, [conecte-a à conta de armazenamento](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) que está a utilizar para tradução de documentos.

### <a name="create-your-tokens"></a>Crie os seus tokens

### <a name="sas-tokens-for-containers"></a>[Fichas SAS para contentores](#tab/Containers)

1. Abra a aplicação Azure Storage Explorer na sua máquina local e navegue nas suas **Contas de Armazenamento** conectadas.
1. Expandir o nó de contas de armazenamento e selecionar **recipientes Blob**.
1. Expanda o nó de recipientes Blob e clique com o botão direito num nó de **recipiente** de armazenamento ou para visualizar o menu de opções.
1. Selecione **Obter Assinatura de Acesso Partilhado...** do menu de opções.
1. Na janela **Assinatura de Acesso Partilhado,** faça as seguintes seleções:
    * Selecione a **sua política de Acesso** (o padrão não é nenhum).
    * Especifique a chave assinada **Data** e **validade.** Recomenda-se uma vida útil curta porque, uma vez gerado, um SAS não pode ser revogado.
    * Selecione o **fuso horário** para a data e hora de início e validade (o padrão é Local).
    * Defina as **permissões** do seu contentor verificando e/ou limpando a caixa de verificação apropriada.
    * Reveja e **selecione Criar**.

1. Aparecerá uma nova janela com o nome **do Recipiente,** **URI,** e **a corda de consulta** para o seu recipiente.  
1. **Copie e cole o recipiente, URI e os valores das cordas de consulta num local seguro. Só serão exibidos uma vez e não podem ser recuperados uma vez que a janela esteja fechada.**
1. Para construir um URL SAS, adiciem o token SAS (URI) ao URL para um serviço de armazenamento.

### <a name="sas-tokens-for-blobs"></a>[Fichas SAS para bolhas](#tab/blobs)

1. Abra a aplicação Azure Storage Explorer na sua máquina local e navegue nas suas **Contas de Armazenamento** conectadas.
1. Expanda o seu nó de armazenamento e selecione **Blob Containers**.
1. Expanda o nó de recipientes Blob e selecione um nó **de recipiente** para visualizar o conteúdo na janela principal.
1. Selecione a bolha onde deseja delegar o acesso SAS e clique à direita para exibir o menu de opções.
1. Selecione **Obter Assinatura de Acesso Partilhado...** do menu de opções.
1. Na janela **Assinatura de Acesso Partilhado,** faça as seguintes seleções:
    * Selecione a **sua política de Acesso** (o padrão não é nenhum).
    * Especifique a chave assinada **Data** e **validade.** Recomenda-se uma vida útil curta porque, uma vez gerado, um SAS não pode ser revogado.
    * Selecione o **fuso horário** para a data e hora de início e validade (o padrão é Local).
    * Defina as **permissões** do seu contentor verificando e/ou limpando a caixa de verificação apropriada.
    * Reveja e **selecione Criar**.
1. Aparecerá uma nova janela com o nome **Blob,** **URI,** e **a corda de consulta** para a sua bolha.  
1. **Copie e cole os valores de cadeia de bolhas, URI e de consulta num local seguro. Só serão exibidos uma vez e não podem ser recuperados uma vez que a janela esteja fechada.**
1. Para construir um URL SAS, adiciem o token SAS (URI) ao URL para um serviço de armazenamento.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Criar fichas SAS para bolhas no portal Azure

> [!NOTE]
> A criação de fichas SAS para contentores diretamente no portal Azure não é suportada. No entanto, pode criar um token SAS com [**o Azure Storage Explorer**](#create-your-sas-tokens-with-azure-storage-explorer) ou completar a tarefa [programáticamente](../../../storage/blobs/sas-service-create.md).

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

* Uma [**conta Azure**](https://azure.microsoft.com/free/cognitive-services/)ativa.  Se não tiver uma, pode [**criar uma conta gratuita.**](https://azure.microsoft.com/free/)
* Um recurso de serviço [**tradutor**](https://ms.portal.azure.com/#create/Microsoft) **(não** um recurso multi-serviço dos Serviços Cognitivos.  *Ver* [Criar um novo recurso Azure](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* Uma [**conta de armazenamento de bolhas Azure.**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) Irá criar recipientes para armazenar e organizar os seus dados blob dentro da sua conta de armazenamento.

### <a name="create-your-tokens"></a>Crie os seus tokens

Vá ao [portal Azure](https://ms.portal.azure.com/#home) e navegue da seguinte forma:  

 **A sua conta de armazenamento** → **contentores** → **o seu recipiente** → a sua **bolha**

1. **Selecione Gerar SAS** a partir do menu perto do topo da página.

1. Selecione **o método de assinatura** → chave da **delegação do utilizador**.

1. Defina **permissões** verificando e/ou limpando a caixa de verificação apropriada.

1. Especifique os tempos de **início** e **expiração assinados.**

1. O campo **de endereços IP permitidos** é opcional e especifica um endereço IP ou um conjunto de endereços IP a partir dos quais aceitar pedidos. Se o endereço IP pedido não corresponder ao endereço IP ou intervalo de endereços especificado no token SAS, não será autorizado.

1. O campo **de protocolos permitidos** é opcional e especifica o protocolo permitido para um pedido feito com o SAS. O valor predefinido é HTTPS.

1. Em seguida, **selecione Gerar ficha SAS e URL**.

1. A cadeia de consulta **de token Blob SAS** e **o URL Blob SAS** serão apresentados na área inferior da janela.  

1. **Copie e cole os valores de símbolo e URL blob SAS num local seguro. Só serão exibidos uma vez e não podem ser recuperados uma vez que a janela esteja fechada.**

1. Para construir um URL SAS, adiciem o token SAS (URI) ao URL para um serviço de armazenamento.

## <a name="learn-more"></a>Saber mais

* [Criar fichas SAS para bolhas ou contentores programáticamente](../../../storage/blobs/sas-service-create.md)
* [Permissões para um diretório, contentor ou bolha](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com tradução de documentos](get-started-with-document-translation.md)
>
>