---
title: Provisionar uma porta de entrada auto-hospedada na Azure API Management | Microsoft Docs
description: Saiba como providenciar uma porta de entrada auto-hospedada na Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: e79248e16ca21ae84022f8ac7f280d93f489c6a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87050348"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Provisionar uma porta de entrada auto-hospedada na Azure API Management

O fornecimento de um recurso de gateway na sua instância de Gestão API Azure é um pré-requisito para a implementação de um gateway auto-hospedado. Este artigo percorre os passos para a disponibilização de um recurso de gateway na Gestão da API.

## <a name="prerequisites"></a>Pré-requisitos

Complete o seguinte quickstart: [Criar uma instância de gestão API Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Aprovisionar um gateway autoalojado

1. Selecione os **Gateways** a partir de **implementação e infraestrutura**.
2. Clique em **+ Adicionar**.
3. Insira o **Nome** e **Região** da porta de entrada.
> [!TIP]
> **A região** especifica a localização pretendida dos nós de gateway que serão associados a este recurso de gateway. É semântica equivalente a uma propriedade semelhante associada a qualquer recurso Azure, mas pode ser atribuído um valor de corda arbitrário.

4. Opcionalmente, introduza uma **Descrição** do recurso gateway.
5. Opcionalmente, selecione **+** em **APIs** para associar uma ou mais APIs com este recurso gateway.
> [!IMPORTANT]
> Por padrão, nenhuma das APIs existentes será associada ao novo recurso gateway. Portanto, as tentativas de os invocar através do novo portal resultarão em `404 Resource Not Found` respostas.

6. Clique em **Adicionar**.

Agora, o recurso gateway foi aprovisionado na sua instância de Gestão da API. Pode proceder para implantar o portal.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a porta de entrada auto-hospedada, consulte [a Azure API Management auto-hospedada gateway overview](self-hosted-gateway-overview.md)
* Saiba mais sobre como [implementar uma porta de entrada auto-hospedada para Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
* Saiba mais sobre como [implementar uma porta de entrada auto-hospedada para Docker](how-to-deploy-self-hosted-gateway-docker.md)
