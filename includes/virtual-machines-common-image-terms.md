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
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174983"
---
## <a name="terminology"></a>Terminologia

Uma imagem do Marketplace no Azure tem os seguintes atributos:

* **Editor**: A organização que criou a imagem. Exemplos: Canonical, MicrosoftWindowsServer
* **Oferta**: O nome de um grupo de imagens relacionadas criadas por um Publicador. Exemplos: UbuntuServer, WindowsServer
* **SKU**: Uma instância de uma oferta, por exemplo, uma versão principal de uma distribuição. Exemplos: 18.04-LTS, 2019-Datacenter
* **Versão**: O número de versão de uma SKU de imagem. 

Para identificar uma imagem do Marketplace ao implantar uma VM programaticamente, forneça esses valores individualmente como parâmetros. Algumas ferramentas aceitam uma imagem *urn*, que combina esses valores, separados por dois-pontos (:) espaço *Publicador*:*oferta*:*SKU*:*versão*. Em um URN, você pode substituir o número de versão por "mais recente", que seleciona a versão mais recente da imagem. 

Se o Publicador de imagem fornecer licença adicional e termos de compra, você deverá aceitar esses termos e habilitar a implantação programática. Você também precisará fornecer parâmetros de *plano de compra* ao implantar uma VM programaticamente. Consulte [implantar uma imagem com os termos do Marketplace](#deploy-an-image-with-marketplace-terms).
