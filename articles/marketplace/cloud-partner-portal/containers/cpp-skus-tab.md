---
title: SKUs para uma imagem de contêineres do Azure | Azure Marketplace
description: Configure SKUs para um contêiner do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 5b69eea8ad7fd4c62925b50434b653118890e280
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823153"
---
# <a name="container-skus-tab"></a>Guia SKUs de contêiner

A guia **SKUs** da página **nova oferta** permite que você crie uma ou mais SKUs e associe-as à sua nova oferta.  Você pode usar SKUs diferentes para diferenciar uma solução por conjuntos de recursos, modelos de cobrança ou outras características.

## <a name="sku-settings"></a>Configurações de SKU

Quando você começa a criar uma nova oferta, não há SKUs associadas à oferta. Para criar uma nova SKU, siga estas etapas:

1. Na guia SKUs, selecione **novo SKU**

   ![Novo prompt do SKU](./media/containers-sku-settings.png)

2. Forneça as informações necessárias de SKU e contêiner. Cada SKU corresponde a uma imagem de contêiner. Há duas partes em um SKU:

    -   Metadados de SKU
    -   Metadados do contêiner


### <a name="sku-metadata"></a>Metadados de SKU

Os metadados de SKU contêm informações de exibição de vitrine para a listagem de contêiner.

![Metadados de SKU](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metadados do contêiner

Os metadados do contêiner têm informações de referência sobre os detalhes do repositório de imagens dentro do ACR (registro de contêiner do Azure). O Azure Marketplace copia essa imagem para um registro público específico do Marketplace e disponibiliza a imagem para os clientes após a certificação. Todas as solicitações do usuário do Azure para consumir uma imagem de contêiner do Azure Marketplace são servidas pelo registro público do Marketplace, não pelo ACR.

![Metadados do contêiner](./media/containers-image-repository.png)
    
Os **detalhes do repositório de imagens** na captura de tela anterior contêm os campos a seguir.  Os campos obrigatórios são indicted por um asterisco (*).

-   **ID da assinatura\*** -a ID da assinatura do Azure na qual o ACR está presente.
-   **Nome do grupo de recursos\*** -o nome do grupo de recursos do ACR.
-   **Nome do registro\*** -o nome do ACR.
-   **Nome do repositório\*** -o nome do repositório. Depois que esse nome é definido, esse valor não pode ser alterado. Use um nome exclusivo para evitar um conflito com outras ofertas em sua conta.
-   **Username\*** -o nome de usuário (admin username) associado à imagem ACR.
-   **Senha\*** -a senha associada à imagem do ACR.

    >[!NOTE]
    >O nome de usuário e a senha são necessários para garantir que os parceiros tenham acesso ao ACR mencionado no processo de publicação.


### <a name="image-version"></a>Versão da Imagem

Ao publicar uma imagem de contêiner, você pode fornecer uma ou mais marcas de imagem e resumos de SHA.

**\* de marca de imagem ou Resumo**
 
- Essa marcação ou resumo deve incluir uma marca de `latest` e uma marca de versão (por exemplo, começando com `xx.xx.xx-` em que XX é um número). Eles devem ser [marcas de manifesto](https://github.com/estesp/manifest-tool) para direcionar várias plataformas. Todas as marcas referenciadas por uma marca de manifesto também devem ser adicionadas para que possamos carregá-las. 
- Você pode adicionar várias versões do contêiner usando marcas. Todas as marcas de manifesto (exceto `latest`) devem começar com `X.Y-` ou `X.Y.Z-` em que X, Y, Z são inteiros. <br/> Por exemplo, se uma marca de `latest` apontar para `1.0.1-linux-x64`, `1.0.1-linux-arm32`e `1.0.1-windows-arm32`, essas marcas precisarão ser adicionadas aqui.

>[!NOTE]
>Lembre-se de adicionar uma **marca de teste** à sua imagem para que você possa identificar a imagem durante o teste.


## <a name="next-steps"></a>Passos seguintes

Use a [guia Marketplace](./cpp-marketplace-tab.md) para criar uma descrição do Marketplace para sua oferta. 
