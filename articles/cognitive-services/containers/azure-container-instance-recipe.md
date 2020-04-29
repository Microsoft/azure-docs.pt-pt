---
title: Receita de instância de recipiente azure
titleSuffix: Azure Cognitive Services
description: Saiba como implantar recipientes de serviços cognitivos em instância de contentores azure
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876662"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Implementar e executar um contentor na Instância de Contentor do Azure

Com os seguintes passos, escamas as aplicações dos Serviços Cognitivos Azure na nuvem facilmente com instâncias de [contentores](https://docs.microsoft.com/azure/container-instances/)Azure . A containerização ajuda-o a concentrar-se na construção das suas aplicações em vez de gerir a infraestrutura. Para obter mais informações sobre a utilização de recipientes, consulte [funcionalidades e benefícios.](../cognitive-services-container-support.md#features-and-benefits)

## <a name="prerequisites"></a>Pré-requisitos

A receita funciona com qualquer recipiente de Serviços Cognitivos. O recurso do Serviço Cognitivo deve ser criado no portal Azure antes de usar a receita. Cada Serviço Cognitivo que suporta contentores tem um documento "Como instalar" especificamente para instalar e configurar o serviço para um recipiente. Alguns serviços requerem um ficheiro ou conjunto de ficheiros como entrada para o recipiente, é importante que compreenda e tenha usado o recipiente com sucesso antes de utilizar esta solução.

* Um recurso do Serviço Cognitivo, criado no portal Azure.
* URL **de ponto final** do Serviço Cognitivo - reveja o "Como instalar" do seu serviço específico para o recipiente, para descobrir de onde vem o URL do ponto final dentro do portal Azure, e como é um exemplo correto do URL. O formato exato pode mudar de serviço para serviço.
* Chave **do** Serviço Cognitivo - as teclas estão na página **Keys** para o recurso Azure. Só precisas de uma das duas chaves. A chave é uma cadeia de 32 caracteres alfa-numéricos.
* Um único recipiente de serviços cognitivos no seu hospedeiro local (o seu computador). Certifique-se de que pode:
  * Puxe a imagem `docker pull` com um comando.
  * Executar o recipiente local com sucesso com `docker run` todas as configurações de configuração necessárias com um comando.
  * Ligue para o ponto final do contentor, recebendo uma resposta de HTTP 2xx e uma resposta JSON de volta.

Todas as variáveis em `<>`suportes angulares, precisam de ser substituídas por valores próprios. Esta substituição inclui os suportes angulares.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Utilize a instância do contentor

1. Selecione a **visão geral** e copie o endereço IP. Será um endereço IP numérico, como `55.55.55.55`.
1. Abra um novo separador de navegador e `http://<IP-address>:5000 (http://55.55.55.55:5000`use o endereço IP, por exemplo, ). Verá a página inicial do contentor, informando-o de que o contentor está a funcionar.

1. Selecione **Descrição da API** de serviço para visualizar a página swagger para o recipiente.

1. Selecione qualquer uma das APIs **postais** e selecione **Experimente-**  Os parâmetros são apresentados incluindo a entrada. Preencha os parâmetros.

1. Selecione **Executar** para enviar o pedido para a sua Instância de Contentores.

    Criou e utilizou com sucesso recipientes de Serviços Cognitivos em Caso de Contentores Azure.
