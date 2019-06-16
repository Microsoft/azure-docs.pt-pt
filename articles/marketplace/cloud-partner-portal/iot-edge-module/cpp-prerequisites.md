---
title: Os pré-requisitos do módulo do IoT Edge do Azure | O Azure Marketplace
description: Pré-requisitos para a publicação de um módulo do IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: a5d1d6fdaf07f8b27820021d4d2ac45ec67c9915
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942117"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Pré-requisitos de publicação de módulo do IoT Edge

Este artigo descreve os pré-requisitos para a publicação de uma oferta de módulo do IoT Edge.  Se ainda não o fez, reveja os [módulos do IoT Edge, guia de publicação](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Publicação de pré-requisitos

Para publicar o módulo do IoT Edge no Azure Marketplace, tem de cumprir os seguintes pré-requisitos:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Acesso para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/). Para obter mais informações, consulte [guia de publicação do Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Contrato para o [termos do Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Aloje o seu elemento de técnico de módulo do IoT Edge no Azure Container Registry.  Para obter mais informações, consulte [como preparar o IoT Edge recursos técnicos do módulo](./cpp-create-technical-assets.md)
- Ter seus metadados do módulo do IoT Edge pronto a utilizar. Por exemplo, prepare os recursos seguintes:
    - Um título
    - Uma descrição (no formato HTML)
    - Uma imagem de logótipo (formato PNG e tamanhos de imagem fixa incluindo 40x40px, 90x90px, 115x115px, 255x115px)
    - Uma condição de política de privacidade e de utilização
    - Uma configuração de módulo predefinida que inclui: duplo de rotas, as propriedades pretendidas, createOptions e variáveis de ambiente.
    - Documentação do módulo
    - Contactos de suporte


## <a name="next-steps"></a>Passos Seguintes

Assim que tiver [preparado o elemento de técnico de módulo do IoT Edge](./cpp-create-technical-assets.md), estará pronto para [criar a sua oferta de módulo do IoT Edge](./cpp-create-offer.md). 
