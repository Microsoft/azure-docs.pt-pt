---
title: SKUs para uma imagem de contentores azure / Mercado Azure
description: Configure as SKUs para um recipiente Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: adbe3d4c498c8f4e4968ca903f78c34aedca9a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280002"
---
# <a name="container-skus-tab"></a>Separador SKUs contentor

O separador **SKUs** da página **New Offer** permite-lhe criar uma ou mais SKUs e associá-las à sua nova oferta.  Pode utilizar diferentes SKUs para diferenciar uma solução por conjuntos de funcionalidades, modelos de faturação ou outra característica.

## <a name="sku-settings"></a>Definições SKU

Quando começa a criar uma nova oferta, não há SKUs associados à oferta. Para criar um novo SKU, siga estes passos:

1. No separador SKUs, selecione **New SKU**

   ![Novo pedido sku](./media/containers-sku-settings.png)

2. Forneça as informações necessárias sobre o SKU e o contentor. Cada SKU corresponde a uma imagem de recipiente. Há duas partes para um SKU:

    -   Metadados SKU
    -   Metadados do contentor


### <a name="sku-metadata"></a>Metadados SKU

Os metadados SKU contêm informações de exibição frontal para a listagem do recipiente.

![Metadados SKU](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metadados do contentor

Os metadados do recipiente têm informações de referência dos seus dados de repositório de imagem dentro do Registo de Contentores Azure (ACR). O Azure Marketplace copia esta imagem num registo público específico do Marketplace e, em seguida, disponibiliza a imagem para os clientes após a certificação. Todos os pedidos do utilizador azure para consumir uma imagem de contentor Azure Marketplace são servidos a partir do registo público do Marketplace, e não da ACR.

![Metadados do contentor](./media/containers-image-repository.png)
    
Os detalhes do **repositório** de imagem na captura do ecrã anterior contêm os seguintes campos.  Os campos necessários são indiciados por um asterisco (*).

-   **ID\* de subscrição** - O ID de subscrição azure onde o ACR está presente.
-   **Nome\* ** do grupo de recursos - O nome do grupo de recursos do ACR.
-   **Nome do\* registo** - O nome ACR.
-   **Nome\* repositório** - O nome do repositório. Depois deste nome ser definido, este valor não pode ser alterado. Use um nome único para evitar um conflito com outras ofertas na sua conta.
-   **Nome\* de utilizador** - O nome de utilizador (nome de utilizador administrativo) associado à imagem ACR.
-   **Palavra-passe\* ** - A palavra-passe associada à imagem ACR.

    >[!NOTE]
    >O nome de utilizador e a palavra-passe são necessários para garantir que os parceiros têm acesso ao ACR mencionado no processo de publicação.


### <a name="image-version"></a>Versão da Imagem

Ao publicar uma imagem de recipiente, pode fornecer uma ou mais etiquetas de imagem e a SHA digere.

**Etiqueta\* de imagem ou digestão**
 
- Esta etiqueta ou digestão deve incluir uma `latest` etiqueta e `xx.xx.xx-` uma etiqueta de versão (por exemplo, a começar pelo local onde xx é um número). Devem ser [marcas de manifesto](https://github.com/estesp/manifest-tool) para atingir várias plataformas. Todas as etiquetas referenciadas por uma etiqueta de manifesto também devem ser adicionadas para que possamos carregá-las. 
- Pode adicionar várias versões de recipiente utilizando etiquetas. Todas as etiquetas `latest`de manifesto `X.Y-` (exceto) devem começar por ambos ou `X.Y.Z-` onde X, Y, Z são inteiros. <br/> Por exemplo, `latest` se uma `1.0.1-linux-x64` `1.0.1-linux-arm32`etiqueta `1.0.1-windows-arm32`aponta para , e , estas etiquetas precisam de ser adicionadas aqui.

>[!NOTE]
>Lembre-se de adicionar uma etiqueta de **teste** à sua imagem para que possa identificar a imagem durante os testes.


## <a name="next-steps"></a>Passos seguintes

Utilize o [separador Marketplace](./cpp-marketplace-tab.md) para criar uma descrição do mercado para a sua oferta. 
