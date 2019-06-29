---
title: Receita de instância de contentor do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como implementar contentores de serviços cognitivos na instância de contentor do Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 45a03a0912681b4fc33ef8df88fa00fd5458f720
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445824"
---
# <a name="deploy-and-run-container-on-azure-container-instance-aci"></a>Implementar e executar o contentor na instância de contentor do Azure (ACI)

Com os seguintes passos, dimensione as aplicações de serviços cognitivos do Azure na cloud facilmente com o Azure [instância de contentor](https://docs.microsoft.com/azure/container-instances/) (ACI). Esta ajuda que se concentrar na criação de seus aplicativos em vez de gerir a infraestrutura.

## <a name="prerequisites"></a>Pré-requisitos

Esta solução funciona com qualquer contentor de serviços cognitivos. O recurso de serviço cognitivo tem de ser criado no portal do Azure antes de utilizar essa receita. Cada serviço cognitivos que suporte contentores tem um documento de "Como instalar" especificamente para instalar e configurar o serviço para um contentor. Uma vez que alguns serviços requerem um ficheiro ou conjunto de arquivos como entrada para o contentor, é importante que entenda e ter usado o contentor com êxito antes de utilizar esta solução.

* Um recurso de serviço cognitivo, criado no portal do Azure.
* Serviço cognitivo **URL de ponto final** -rever seu serviço específico "como instalar o" para o contentor, para localizar onde o URL de ponto final é a partir do portal do Azure e o que um exemplo correto do URL é semelhante. O formato exato pode alterar a partir de serviços.
* Serviço cognitivo **chave** -as chaves são sobre o **chaves** página para o recurso do Azure. Só precisa de uma das duas chaves. A chave é uma cadeia de caracteres de 32 carateres de alfanuméricos.
* Um único cognitivos serviços de contentor no seu anfitrião local (o seu computador). Certifique-se de que pode:
  * Obter a imagem com um `docker pull` comando.
  * Executar o contentor local com êxito com todas as definições de configuração necessárias com um `docker run` comando.
  * Chame o ponto de extremidade do contentor, voltando uma resposta de 2xx e uma resposta JSON.

Todas as variáveis em colchetes angulares, `<>`, precisam ser substituídos pelos seus próprios valores. Essa substituição inclui dos parênteses.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](./includes/create-aci-resource.md)]

## <a name="use-the-container-instance"></a>Utilizar a instância de contentor

1. Selecione o **descrição geral** e copie o endereço IP. É um endereço IP numérico como `55.55.55.55`.
1. Abra um novo separador do browser e utilizar o endereço IP, por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000`). Verá a homepage do contentor, permitindo-lhe saber que o contentor está em execução.

1. Selecione **descrição do serviço de API** para ver a página de swagger para o contentor.

1. Selecione qualquer uma da **POST** APIs e selecione **experimentá-lo**.  Os parâmetros são exibidos, incluindo a entrada. Preencha os parâmetros.

1. Selecione **Execute** para enviar o pedido para a instância do contentor.

    Criou com êxito e utilizado contentores de serviços cognitivos na instância de contentor do Azure.
