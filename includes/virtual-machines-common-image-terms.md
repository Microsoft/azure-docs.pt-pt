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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71174983"
---
## <a name="terminology"></a>Terminologia

Uma imagem do Marketplace em Azure tem os seguintes atributos:

* **Editor**: A organização que criou a imagem. Exemplos: Canonical, MicrosoftWindowsServer
* **Oferta**: O nome de um grupo de imagens relacionadas criadapor uma editora. Exemplos: UbuntuServer, WindowsServer
* **SKU**: Um exemplo de uma oferta, como uma grande divulgação de uma distribuição. Exemplos: 18.04-LTS, 2019-Datacenter
* **Versão**: O número da versão de uma imagem SKU. 

Para identificar uma imagem do Marketplace quando implementar um VM programáticamente, forneça estes valores individualmente como parâmetros. Algumas ferramentas aceitam uma *URN*de imagem, que combina estes valores, separados pelo cólon (:) personagem: *Editor*:*Oferta*:*Sku*:*Versão*. Numa URN, pode substituir o número da versão por "mais recente", que seleciona a versão mais recente da imagem. 

Se o editor de imagem fornecer termos adicionais de licença e compra, então deve aceitar esses termos e ativar a implementação programática. Também terá de fornecer parâmetros de plano de *compra* ao implementar um VM programáticamente. Ver [Implementar uma imagem com termos](#deploy-an-image-with-marketplace-terms)de Marketplace .
