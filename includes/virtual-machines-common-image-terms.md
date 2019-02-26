---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 50d71a3967e61e5d531f4bfeae3582b56230a0d2
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56825595"
---
## <a name="terminology"></a>Terminologia

Uma imagem do Marketplace no Azure tem os seguintes atributos:

* **Publisher**: A organização que criou a imagem. Exemplos: Canonical, MicrosoftWindowsServer
* **Oferecer**: O nome de um grupo de imagens relacionadas criadas por um fabricante. Exemplos: UbuntuServer, WindowsServer
* **SKU**: Uma instância de uma oferta, por exemplo, uma versão principal de uma distribuição. Exemplos: 18.04-LTS, 2019-Datacenter
* **Versão**: O número de versão de um SKU de imagem. 

Para identificar uma imagem do Marketplace, ao implementar uma VM através de programação, fornece estes valores individualmente como parâmetros. Algumas ferramentas aceitam uma imagem *URN*, que combina estes valores, separados pelo caractere dois-pontos (:): *Publicador*:*oferecem*:*Sku*:*versão*. Num URN, pode substituir o número de versão com "mais recente", que seleciona a versão mais recente da imagem. 

Se o publicador de imagens fornece adicionais de licença e os termos de compra, tem de aceitar esses termos e ativar a implementação programática. Também precisará fornecer *compra do plano* parâmetros ao implementar uma VM através de programação. Ver [implementar uma imagem com os termos do Marketplace](#deploy-an-image-with-marketplace-terms).
