---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 98d765e2f6909f00f8dfe76d06aef017aad67adf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "71174983"
---
## <a name="terminology"></a>Terminologia

Uma imagem de Marketplace em Azure tem os seguintes atributos:

* **Editora**: A organização que criou a imagem. Exemplos: Canonical, MicrosoftWindowsServer
* **Oferta**: O nome de um grupo de imagens relacionadas criadas por uma editora. Exemplos: UbuntuServer, WindowsServer
* **SKU**: Um caso de oferta, como uma grande libertação de uma distribuição. Exemplos: 18.04-LTS, 2019-Datacenter
* **Versão**: O número de versão de uma imagem SKU. 

Para identificar uma imagem do Marketplace quando implementar um VM programáticamente, forneça estes valores individualmente como parâmetros. Algumas ferramentas aceitam uma *URN de* imagem, que combina estes valores, separada pelo cólon (:) personagem: *Editor*:*Oferta*:*Sku*:*Versão*. Numa URN, pode substituir o número da versão por "mais recente", que seleciona a versão mais recente da imagem. 

Se o editor de imagem fornecer termos de licença e de compra adicionais, então deve aceitar esses termos e permitir a implementação programática. Também terá de fornecer parâmetros do plano de *compra* ao implementar um VM programáticamente. Ver [Implementar uma imagem com os termos do Mercado](#deploy-an-image-with-marketplace-terms).
