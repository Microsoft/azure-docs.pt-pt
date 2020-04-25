---
title: Pré-requisitos do módulo Azure IoT Edge Mercado Azure
description: Pré-requisitos para a publicação de um módulo IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d1bfc29a0cb031421b4017454bc8a2cdd8be09f9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142377"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Módulo IoT Edge a publicar pré-requisitos

>[!Important]
>A partir de 13 de abril de 2020, começaremos a movimentar a gestão do seu módulo IoT Edge oferece ao Partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Criar uma oferta de módulo IoT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) para gerir as suas ofertas migratórias.

Este artigo descreve os pré-requisitos para a publicação de uma oferta de móduloS IoT Edge.  Se ainda não o fez, reveja o guia de publicação dos [módulos IoT Edge](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Pré-requisitos de publicação

Para publicar um módulo IoT Edge no Mercado Azure, tem de cumprir os seguintes pré-requisitos:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Acesso ao Portal do [Parceiro cloud.](https://cloudpartner.azure.com/) Para mais informações, consulte o guia de [publicação Azure Marketplace e AppSource.](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)
- Acordo com os Termos do [Mercado Azure](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hospedar o seu ativo técnico do módulo IoT Edge num Registo de Contentores Azure.  Para mais informações, consulte como preparar o seu ativo técnico do [módulo IoT Edge](./cpp-create-technical-assets.md)
- Tenha os metadados do módulo IoT Edge prontos a utilizar. Por exemplo, preparar os seguintes ativos:
    - Um título
    - Uma descrição (em formato HTML)
    - Uma imagem de logotipo (formato PNG e tamanhos de imagem fixos, incluindo 40x40px, 90x90px, 115x115px, 255x115px)
    - Um termo de política de uso e privacidade
    - Uma configuração de módulo padrão que inclui: rotas, propriedades duplas desejadas, criações de opções e variáveis ambientais.
    - Documentação do módulo
    - Contactos de suporte


## <a name="next-steps"></a>Passos seguintes

Assim que tiver preparado o seu ativo técnico de [móduloIO T Edge,](./cpp-create-technical-assets.md)estará pronto para criar a sua oferta de [móduloS IoT Edge](./cpp-create-offer.md). 
