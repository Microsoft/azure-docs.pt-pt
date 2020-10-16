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
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80876662"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Implementar e executar um contentor na Instância de Contentor do Azure

Com os seguintes passos, escale facilmente as aplicações dos Serviços Cognitivos Azure na nuvem com [instâncias de contentores Azure](https://docs.microsoft.com/azure/container-instances/). A contentorização ajuda-o a concentrar-se na construção das suas aplicações em vez de gerir a infraestrutura. Para obter mais informações sobre a utilização de recipientes, consulte [as funcionalidades e benefícios.](../cognitive-services-container-support.md#features-and-benefits)

## <a name="prerequisites"></a>Pré-requisitos

A receita funciona com qualquer recipiente dos Serviços Cognitivos. O recurso Serviço Cognitivo deve ser criado no portal Azure antes de utilizar a receita. Cada Serviço Cognitivo que suporta contentores tem um documento "Como instalar" especificamente para instalar e configurar o serviço para um recipiente. Alguns serviços requerem um ficheiro ou conjunto de ficheiros como entrada para o recipiente, é importante que compreenda e tenha utilizado o recipiente com sucesso antes de utilizar esta solução.

* Um recurso de Serviço Cognitivo, criado no portal Azure.
* Cognitive Service **endpoint URL** - reveja o "Como instalar" do seu serviço específico para o recipiente, para saber de onde é o URL do ponto final dentro do portal Azure, e como é um exemplo correto do URL. O formato exato pode mudar de serviço para serviço.
* Chave **de** Serviço Cognitivo - as chaves estão na página **Chaves** para o recurso Azure. Só precisa de uma das duas chaves. A chave é uma sequência de 32 caracteres alfanuméricos.
* Um único Recipiente de Serviços Cognitivos no seu anfitrião local (o seu computador). Certifique-se de que pode:
  * Puxe a imagem com um `docker pull` comando.
  * Executar o recipiente local com sucesso com todas as definições de configuração necessárias com um `docker run` comando.
  * Ligue para o ponto final do recipiente, recebendo uma resposta de HTTP 2xx e uma resposta JSON de volta.

Todas as variáveis em suportes angulares, `<>` precisam de ser substituídas por seus próprios valores. Esta substituição inclui os suportes angulares.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Utilize a instância do contentor

1. Selecione a **visão geral** e copie o endereço IP. Será um endereço IP numérico, como `55.55.55.55` .
1. Abra um novo separador de navegador e use o endereço IP, por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Verá a página inicial do contentor, informando-o de que o contentor está a funcionar.

1. Selecione **API Descrição** do serviço para ver a página swagger para o recipiente.

1. Selecione qualquer uma das APIs **post** e selecione **Experimentá-lo**.  Os parâmetros são apresentados, incluindo a entrada. Preencha os parâmetros.

1. **Selecione Executar** para enviar o pedido para a sua Instância de Contentor.

    Criou e utilizou com sucesso recipientes de Serviços Cognitivos em Instância de Contentores Azure.
