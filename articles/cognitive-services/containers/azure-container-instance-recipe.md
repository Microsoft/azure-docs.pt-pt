---
title: Receita de caso de recipiente de azure
titleSuffix: Azure Cognitive Services
description: Saiba como implantar recipientes de serviços cognitivos em instância de contentores Azure
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 003b4411ac791898f4a7467b9b03f29aadba2fc7
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704848"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Implementar e executar um contentor na Instância de Contentor do Azure

Com os seguintes passos, escale facilmente as aplicações dos Serviços Cognitivos Azure na nuvem com [instâncias de contentores Azure](../../container-instances/index.yml). A contentorização ajuda-o a concentrar-se na construção das suas aplicações em vez de gerir a infraestrutura. Para obter mais informações sobre a utilização de recipientes, consulte [as funcionalidades e benefícios.](../cognitive-services-container-support.md#features-and-benefits)

## <a name="prerequisites"></a>Pré-requisitos

A receita funciona com qualquer recipiente dos Serviços Cognitivos. O recurso Serviço Cognitivo deve ser criado antes de utilizar a receita. Cada Serviço Cognitivo que suporta contentores tem um artigo "Como instalar" para instalar e configurar o serviço para um recipiente. Alguns serviços requerem um ficheiro ou conjunto de ficheiros como entrada para o recipiente, é importante que compreenda e tenha utilizado o recipiente com sucesso antes de utilizar esta solução.

* Um recurso Azure para o Serviço Cognitivo Azure que está a usar.
* Cognitive Service **endpoint URL** - reveja o "Como instalar" do seu serviço específico para o recipiente, para saber de onde é o URL do ponto final dentro do portal Azure, e como é um exemplo correto do URL. O formato exato pode mudar de serviço para serviço.
* Chave **de** Serviço Cognitivo - as chaves estão na página **Chaves** para o recurso Azure. Só precisa de uma das duas chaves. A chave é uma sequência de 32 caracteres alfanuméricos.

* Um único Recipiente de Serviços Cognitivos no seu anfitrião local (o seu computador). Certifique-se de que pode:
  * Puxe a imagem com um `docker pull` comando.
  * Executar o recipiente local com sucesso com todas as definições de configuração necessárias com um `docker run` comando.
  * Ligue para o ponto final do recipiente, recebendo uma resposta de HTTP 2xx e uma resposta JSON de volta.

Todas as variáveis em suportes angulares, `<>` precisam de ser substituídas por seus próprios valores. Esta substituição inclui os suportes angulares.

> [!IMPORTANT]
> O recipiente LUIS requer um `.gz` ficheiro modelo que é puxado para dentro em tempo de execução. O recipiente deve poder aceder a este ficheiro modelo através de um suporte de volume a partir da instância do recipiente. Para fazer o upload de um ficheiro modelo, siga estes passos:
> 1. [Criar uma partilha de ficheiros Azure](../../storage/files/storage-how-to-create-file-share.md). Tome nota do nome da conta, chave e nome da partilha de ficheiros Azure, pois mais tarde necessitará deles.
> 2. [exportar o seu modelo LUIS (app embalado) a partir do portal LUIS.](../LUIS/luis-container-howto.md#export-packaged-app-from-luis) 
> 3. No portal Azure, navegue para a página **geral** do seu recurso de conta de armazenamento e selecione **ações de ficheiros**. 
> 4. Selecione o nome de partilha de ficheiros que criou recentemente e, em seguida, selecione **Upload**. Em seguida, faça upload da sua aplicação embalada. 

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>Utilize a instância do contentor

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Selecione a **visão geral** e copie o endereço IP. Será um endereço IP numérico, como `55.55.55.55` .
1. Abra um novo separador de navegador e use o endereço IP, por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Verá a página inicial do contentor, informando-o de que o contentor está a funcionar.

    ![Página inicial do contentor](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. Selecione **API Descrição** do serviço para ver a página swagger para o recipiente.

1. Selecione qualquer uma das APIs **post** e selecione **Experimentá-lo**.  Os parâmetros são apresentados, incluindo a entrada. Preencha os parâmetros.

1. **Selecione Executar** para enviar o pedido para a sua Instância de Contentor.

    Criou e utilizou com sucesso recipientes de Serviços Cognitivos em Instância de Contentores Azure.

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> Se estiver a executar o Text Analytics para recipiente de saúde, utilize o seguinte URL para submeter consultas: `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
