---
title: Suporte de gestor render
description: Utilizando a integração do gestor de renderização do Azure Batch. Saiba mais sobre suporte incorporado ou complementos para gestores de renderização populares.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 9921c68bf6ebe44d6d2a6b7b74afbe3fa98e0803
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115742"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Utilização do Lote Azure com gestores de quintas renderizados

Se você está usando uma fazenda de renderização existente no local, então é altamente provável que um gestor de renderização controle a capacidade de renderização da fazenda e torne empregos.

O Azure fornece suporte incorporado ou complementos para gestores de renderização populares. Em seguida, pode adicionar e remover VMs Azure, incluindo VMs com o licenciamento de aplicação pay-for-use e VMs de baixa prioridade.

Os seguintes gestores de renderização são suportados:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Render real](https://www.royalrender.de/)
* [Prazo da caixa de reflexão](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Render Hub

O Azure Render Hub simplifica a criação e gestão das quintas de renderização Azure.  Render Hub tem suporte nativo para PipelineFx Qube e Deadline 10.  Para obter mais informações e instruções detalhadas consulte [o repositório GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Utilização de Azure com Qube PipelineFX

O Azure Render Hub apoia gestores de renderização populares, incluindo o Deadline.  Para obter instruções sobre a implantação e utilização do Render Hub, consulte [o repositório GitHub](https://github.com/Azure/azure-render-hub).

Scripts e instruções para permitir a utilização de VMs de piscina de lote de Azure, uma vez que os trabalhadores do Qube também estão disponíveis no [repositório GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Usando Azure com Render Real

A Royal Render tem integração azure e azure batch incorporada, permitindo-lhe estender uma quinta rendercom VMs baseadoem em Azure. Para um resumo, consulte [os ficheiros de ajuda.](https://www.royalrender.de/help8/index.html?Cloudrendering.html)

Para um exemplo de um cliente da Royal Render usando a integração Azure, consulte a história do [cliente da Jellyfish Pictures.](https://customers.microsoft.com/story/jellyfishpictures)

## <a name="using-azure-with-thinkbox-deadline"></a>Utilização de Azure com prazo de thinkbox

O Azure Render Hub apoia gestores de renderização populares, incluindo o Deadline.  Para obter instruções sobre a implantação e utilização do Render Hub, consulte [o repositório GitHub](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Passos seguintes

Experimente a integração do Lote Azure para o seu gestor de renderização, utilizando o plug-in apropriado e instruções no GitHub, quando aplicável.
