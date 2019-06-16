---
title: SKUs de uma imagem de contentores do Azure | O Azure Marketplace
description: Configure SKUs para um contentor do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 6953329bfabe99fc4bb28f2494cb412ba9cbbba0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942920"
---
# <a name="container-skus-tab"></a>Separador de SKUs de contentor

O **SKUs** separador da **nova oferta** página permite-lhe criar um ou mais SKUs e associá-las a sua nova oferta.  Pode usar diferentes SKUs para diferenciar uma solução por conjuntos de funcionalidades, modelos de faturação ou outras características.

## <a name="sku-settings"></a>Definições de SKU

Quando começar a criar uma nova oferta, não há qualquer SKUs associadas com a oferta. Para criar um novo SKU, siga estes passos:

1. No separador de SKUs, selecione **novo SKU**

   ![Nova linha de SKU](./media/containers-sku-settings.png)

2. Indique as informações necessárias de SKU e o contentor. Cada SKU corresponde a uma imagem de contentor. Existem duas partes para um SKU:

    -   Metadados SKU
    -   Metadados do contentor


### <a name="sku-metadata"></a>Metadados SKU

Os metadados SKU contém vitrine de apresentar as informações para a listagem de contentor.

![Metadados SKU](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metadados do contentor

Os metadados do contentor tem informações de referência de seus detalhes de repositório de imagem dentro do Azure Container Registry (ACR). O Azure Marketplace copia esta imagem para um registo específico do Marketplace, público e, em seguida, disponibiliza a imagem para os clientes após a certificação. Todos os pedidos do utilizador do Azure para consumir uma imagem de contentor do Azure Marketplace são servidos a partir do registo de público do Marketplace, não ACR.

![Metadados do contentor](./media/containers-image-repository.png)
    
O **detalhes de repositório de imagem** no ecrã anterior captura contém os seguintes campos.  Campos obrigatórios são indicted por um asterisco (*).

-   **ID de subscrição\***  -ID de subscrição do Azure onde o ACR está presente.
-   **Nome do grupo de recursos\***  -o nome do grupo de recursos do ACR.
-   **Nome do registo\***  -nome do ACR.
-   **Nome do repositório\***  -o nome do repositório. Este nome está definido, este valor não pode ser alterado. Utilize um nome exclusivo para evitar um conflito com outras ofertas na sua conta.
-   **Nome de utilizador\***  -o nome de utilizador (nome de utilizador do administrador) associado à imagem ACR.
-   **Palavra-passe\***  -a palavra-passe associada à imagem ACR.

    >[!NOTE]
    >O nome de utilizador e palavra-passe são necessários para assegurar que os parceiros têm acesso para o ACR mencionado no processo de publicação.


### <a name="image-version"></a>Versão da Imagem

Ao publicar uma imagem de contentor, pode fornecer uma ou mais etiquetas de imagem e SHA digests.

**Etiqueta de imagem\* ou Digest**
 
- Este resumo ou etiquetas tem de incluir um `latest` etiqueta e uma etiqueta de versão (por exemplo, começando com `xx.xx.xx-` onde xx é um número). Eles devem ser [etiquetas de manifesto](https://github.com/estesp/manifest-tool) para várias plataformas de destino. Também devem ser adicionadas a todas as etiquetas referenciadas por uma marca de manifesto, de modo que pode carregá-los. 
- Pode adicionar várias versões do contentor utilizando etiquetas. Todas as etiquetas de manifesto (exceto `latest`) tem de começar com um `X.Y-` ou `X.Y.Z-` em que X, Y, Z são números inteiros. <br/> Por exemplo, se um `latest` Etiquetar pontos para `1.0.1-linux-x64`, `1.0.1-linux-arm32`, e `1.0.1-windows-arm32`, estas etiquetas tem de ser adicionada aqui.

>[!NOTE]
>Não se esqueça de adicionar uma **teste de marcas** à sua imagem para que possa identificar a imagem durante o teste.


## <a name="next-steps"></a>Passos Seguintes

Utilize o [separador Marketplace](./cpp-marketplace-tab.md) para criar uma descrição do marketplace para a sua oferta. 
