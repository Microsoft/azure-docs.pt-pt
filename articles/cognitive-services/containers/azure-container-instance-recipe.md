---
title: Receita de instância de recipiente azure
titleSuffix: Azure Cognitive Services
description: Saiba como implantar recipientes de serviços cognitivos em instância de contentores azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 78f35042678aa7c30cebf73796df3e5d564b4502
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716995"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Implementar e executar um contentor na Instância de Contentor do Azure

Com os seguintes passos, escamas as aplicações dos Serviços Cognitivos Azure na nuvem facilmente com instâncias de [contentores](https://docs.microsoft.com/azure/container-instances/)Azure . A containerização ajuda-o a concentrar-se na construção das suas aplicações em vez de gerir a infraestrutura. Para obter mais informações sobre a utilização de recipientes, consulte [funcionalidades e benefícios.](../cognitive-services-container-support.md#features-and-benefits)

## <a name="prerequisites"></a>Pré-requisitos

A receita funciona com qualquer recipiente de Serviços Cognitivos. O recurso do Serviço Cognitivo deve ser criado no portal Azure antes de usar a receita. Cada Serviço Cognitivo que suporta contentores tem um documento "Como instalar" especificamente para instalar e configurar o serviço para um recipiente. Alguns serviços requerem um ficheiro ou conjunto de ficheiros como entrada para o recipiente, é importante que compreenda e tenha usado o recipiente com sucesso antes de utilizar esta solução.

* Um recurso do Serviço Cognitivo, criado no portal Azure.
* URL **de ponto final** do Serviço Cognitivo - reveja o "Como instalar" do seu serviço específico para o recipiente, para descobrir de onde vem o URL do ponto final dentro do portal Azure, e como é um exemplo correto do URL. O formato exato pode mudar de serviço para serviço.
* Chave **do** Serviço Cognitivo - as teclas estão na página **Keys** para o recurso Azure. Só precisa de uma das duas chaves. A chave é uma cadeia de 32 caracteres alfa-numéricos.
* Um único recipiente de serviços cognitivos no seu hospedeiro local (o seu computador). Certifique-se de que pode:
  * Puxe a imagem com um comando `docker pull`.
  * Executar o recipiente local com sucesso com todas as configurações de configuração necessárias com um comando de `docker run`.
  * Ligue para o ponto final do contentor, recebendo uma resposta de HTTP 2xx e uma resposta JSON de volta.

Todas as variáveis em suportes angulares, `<>`, precisam de ser substituídas por valores próprios. Esta substituição inclui os suportes angulares.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Utilize a instância do contentor

1. Selecione a **visão geral** e copie o endereço IP. Será um endereço IP numérico, como `55.55.55.55`.
1. Abra um novo separador de navegador e use o endereço IP, por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000`). Verá a página inicial do contentor, informando-o de que o contentor está a funcionar.

1. Selecione **Descrição da API** de serviço para visualizar a página swagger para o recipiente.

1. Selecione qualquer uma das APIs **postais** e selecione **Experimente-**  Os parâmetros são apresentados incluindo a entrada. Preencha os parâmetros.

1. Selecione **Executar** para enviar o pedido para a sua Instância de Contentores.

    Criou e utilizou com sucesso recipientes de Serviços Cognitivos em Caso de Contentores Azure.
