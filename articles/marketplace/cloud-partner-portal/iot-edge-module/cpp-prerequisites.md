---
title: Pré-requisitos do módulo de Azure IoT Edge | Azure Marketplace
description: Pré-requisitos para publicar um módulo de IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 610f9bc8d87ac75e2868e247fe1332574480b498
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813867"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Pré-requisitos de publicação de módulo IoT Edge

Este artigo descreve os pré-requisitos para a publicação de uma oferta de módulo IoT Edge.  Se você ainda não tiver feito isso, examine o [Guia de publicação de módulos de IOT Edge](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Pré-requisitos de publicação

Para publicar um módulo IoT Edge no Azure Marketplace, você precisa atender aos seguintes pré-requisitos:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Acesso ao [portal do Cloud Partner](https://cloudpartner.azure.com/). Para obter mais informações, consulte o [Guia de publicação do Azure Marketplace e do AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Contrato com os [termos do Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hospede seu ativo técnico do módulo IoT Edge em um registro de contêiner do Azure.  Para obter mais informações, consulte [como preparar seu ativo técnico do módulo de IOT Edge](./cpp-create-technical-assets.md)
- Os metadados do módulo IoT Edge estão prontos para uso. Por exemplo, prepare os seguintes ativos:
    - Um título
    - Uma descrição (no formato HTML)
    - Uma imagem de logotipo (formato PNG e tamanhos de imagem fixa, incluindo 40x40px, 90x90px, 115x115px, 255x115px)
    - Um termo de uso e política de privacidade
    - Uma configuração de módulo padrão que inclui: rotas, propriedades desejadas de entrelaçamento, criaroptions e variáveis de ambiente.
    - Documentação do módulo
    - Contatos de suporte


## <a name="next-steps"></a>Passos seguintes

Depois de [preparar seu ativo técnico de IOT Edge módulo](./cpp-create-technical-assets.md), você estará pronto para [criar sua oferta de módulo IOT Edge](./cpp-create-offer.md). 
