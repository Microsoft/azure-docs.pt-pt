---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 433e909563602a2ef32b7986959b428c9afaf9f4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016003"
---
| Recurso | Descrição|
|----------|------------|
| **Fonte de imagem** | Este é um recurso que pode ser usado para criar uma **versão de imagem** numa galeria de imagens. Uma fonte de imagem pode ser um Azure VM existente que é [generalizado ou especializado](../articles/virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images), uma imagem gerida, um instantâneo ou uma versão de imagem em outra galeria de imagens. |
| **Galeria de imagens** | Tal como o Azure Marketplace, uma **galeria de imagens** é um repositório para gerir e partilhar imagens, mas você controla quem tem acesso. |
| **Definição de imagem** | As definições de imagem são criadas dentro de uma galeria e transportam informações sobre a imagem e requisitos para a sua utilização interna. Isto inclui se a imagem é Windows ou Linux, notas de lançamento e requisitos mínimos e máximo de memória. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão de imagem** é o que se usa para criar um VM quando se utiliza uma galeria. Pode ter várias versões de uma imagem necessária para o seu ambiente. Como uma imagem gerida, quando se usa uma **versão de imagem** para criar um VM, a versão de imagem é usada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes. |