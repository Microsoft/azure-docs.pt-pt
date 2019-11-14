---
title: Provisionar um gateway auto-hospedado no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como provisionar um gateway auto-hospedado no gerenciamento de API do Azure.
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
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075291"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Provisionar um gateway auto-hospedado no gerenciamento de API do Azure

O provisionamento de um recurso de gateway em sua instância de gerenciamento de API do Azure é um pré-requisito para implantar um gateway de hospedagem interna. Este artigo percorre as etapas para provisionar um recurso de gateway no gerenciamento de API.

> [!NOTE]
> O recurso de gateway auto-hospedado está em versão prévia. Durante a visualização, o gateway auto-hospedado está disponível apenas nas camadas desenvolvedor e Premium sem custo adicional. A camada de desenvolvedor é limitada a uma única implantação de gateway de hospedagem interna.

## <a name="prerequisites"></a>Pré-requisitos

Concluir o início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Aprovisionar um gateway autoalojado

1. Selecione os **gateways** em **configurações**.
2. Clique em **+ Adicionar**.
3. Insira o **nome** e a **região** do gateway.
> [!TIP]
> **Region** especifica o local pretendido dos nós de gateway que serão associados a esse recurso de gateway. É semanticamente equivalente a uma propriedade semelhante associada a qualquer recurso do Azure, mas pode ser atribuído um valor de cadeia de caracteres arbitrário.

4. Opcionalmente, insira uma **Descrição** do recurso de gateway.
5. Opcionalmente, selecione **+** em **APIs** para associar uma ou mais APIs a esse recurso de gateway.
> [!TIP]
> Você pode associar e remover uma API de um gateway na guia **configurações** da API.

> [!IMPORTANT]
> Por padrão, nenhuma das APIs existentes será associada ao novo recurso de gateway. Portanto, as tentativas de chamá-las por meio do novo gateway resultarão em `404 Resource Not Found` respostas.

6. Clique em **Adicionar**.

Agora o recurso de gateway foi provisionado na sua instância de gerenciamento de API. Você pode continuar a implantar o gateway.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o gateway auto-hospedado, confira [visão geral do gateway de gerenciamento de API do Azure-hospedado](self-hosted-gateway-overview.md)
* Saiba mais sobre como [implantar um gateway auto-hospedado no kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Saiba mais sobre como [implantar um gateway auto-hospedado no Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
