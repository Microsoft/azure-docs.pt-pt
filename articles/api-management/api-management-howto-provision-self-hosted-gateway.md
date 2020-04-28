---
title: Providenciar uma porta de entrada auto-hospedada na Azure API Management [ Microsoft Docs
description: Saiba como fornecer uma porta de entrada auto-hospedada na Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74075291"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Providenciar uma porta de entrada auto-hospedada na Azure API Management

Fornecer um recurso de gateway na sua instância de Gestão API Azure é um pré-requisito para a implementação de um gateway auto-hospedado. Este artigo percorre as etapas para fornecer um recurso de gateway na Gestão API.

> [!NOTE]
> A funcionalidade de gateway auto-hospedada está em pré-visualização. Durante a pré-visualização, o gateway auto-hospedado só está disponível nos níveis Developer e Premium sem custos adicionais. O nível de desenvolvimento está limitado a uma única implementação de gateway auto-hospedada.

## <a name="prerequisites"></a>Pré-requisitos

Complete o seguinte quickstart: Criar uma instância de [Gestão API Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Aprovisionar um gateway autoalojado

1. Selecione os **Gateways** a partir de **definições**.
2. Clique em **+ Adicionar**.
3. Insira o **Nome** e **Região** do portal.
> [!TIP]
> **A região** especifica a localização prevista dos nós de gateway que estarão associados a este recurso gateway. É semânticamente equivalente a uma propriedade semelhante associada a qualquer recurso Azure, mas pode ser atribuído um valor de cadeia arbitrário.

4. Opcionalmente, introduza uma **Descrição** do recurso gateway.
5. Opcionalmente, **+** selecione em **APIs** para associar uma ou mais APIs com este recurso gateway.
> [!TIP]
> Pode associar e remover uma API de uma porta de entrada no separador **Definições** da API.

> [!IMPORTANT]
> Por padrão, nenhuma das APIs existentes estará associada ao novo recurso gateway. Por conseguinte, as tentativas de os invocar `404 Resource Not Found` através da nova porta de entrada resultarão em respostas.

6. Clique em **Adicionar**.

Agora, o recurso gateway foi aprovisionado na sua instância de Gestão API. Pode proceder à implantação da porta de entrada.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o gateway auto-hospedado, consulte a [visão geral da Azure API Management](self-hosted-gateway-overview.md)
* Saiba mais sobre como [implementar uma porta de entrada auto-hospedada para Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Saiba mais sobre como [implementar uma porta de entrada auto-hospedada para Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
