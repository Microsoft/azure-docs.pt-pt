---
title: Suporte do Gerenciador de renderização-lote do Azure
description: Usando a integração do Gerenciador de renderização do lote do Azure. Saiba mais sobre o suporte interno ou Complementos para gerentes de processamento populares.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449689"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Usando o lote do Azure com gerenciadores de farm de renderização

Se você estiver usando um farm de renderização local existente, é altamente provável que um Gerenciador de renderização controle a capacidade do farm de renderização e os trabalhos de renderização.

O Azure fornece suporte interno ou Complementos para gerentes de processamento populares. Você pode adicionar e remover VMs do Azure, incluindo VMs com o licenciamento de aplicativos de pagamento por uso e VMs de baixa prioridade.

Os seguintes gerenciadores de renderização têm suporte:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal renderizar](https://www.royalrender.de/)
* [Prazo Thinkbox](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Render Hub

O Hub de renderização do Azure simplifica a criação e o gerenciamento de farms de renderização do Azure.  O Hub de renderização tem suporte nativo para PipelineFx Qube e prazo 10.  Para obter mais informações e instruções detalhadas, consulte [o repositório GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Usando o Azure com PipelineFX Qube

O Hub de renderização do Azure dá suporte a gerentes de renderização populares, incluindo prazo.  Para obter instruções sobre como implantar e usar o Hub de renderização, consulte [o repositório GitHub](https://github.com/Azure/azure-render-hub).

Scripts e instruções para habilitar VMs do pool do lote do Azure a serem usadas como Qube Workers também estão disponíveis no [repositório GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Usando o Azure com o Royal render

A renderização Royal tem integração interna do Azure e do Azure batch, permitindo que você estenda um farm de renderização com VMs baseadas no Azure. Para obter um resumo, consulte [os arquivos de ajuda](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Para obter um exemplo de um cliente do Royal render usando a integração do Azure, consulte a [história do cliente do Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Usando o Azure com o prazo Thinkbox

O Hub de renderização do Azure dá suporte a gerentes de renderização populares, incluindo prazo.  Para obter instruções sobre como implantar e usar o Hub de renderização, consulte [o repositório GitHub](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Passos seguintes

Experimente a integração do lote do Azure para seu Gerenciador de renderização, usando o plug-in apropriado e as instruções no GitHub, quando aplicável.
