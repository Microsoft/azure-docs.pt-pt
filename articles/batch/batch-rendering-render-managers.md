---
title: Apoio ao gestor de renderização
description: Utilizando a integração do gestor de renderização do Azure Batch. Saiba mais sobre suporte incorporado ou add-ons para gestores de renderização populares.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: c44cbf86d8bf2fe83a6dc91dee1c4f58eec156c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83726456"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Usando O Lote Azure com gestores de fazenda renderizador

Se você está usando uma fazenda existente no local, então é altamente provável que um gestor de renderização controle a capacidade da fazenda e faça empregos.

O Azure fornece suporte incorporado ou complementos para gestores de renderização populares. Em seguida, pode adicionar e remover VMs Azure, incluindo VMs com o licenciamento de aplicação pay-for-use e VMs de baixa prioridade.

São apoiados os seguintes gestores de renderização:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Render real](https://www.royalrender.de/)
* [Prazo da caixa de pensamento](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Render Hub

O Azure Render Hub simplifica a criação e gestão das explorações de renderização da Azure.  Render Hub tem apoio nativo para PipelineFx Qube e Deadline 10.  Para obter mais informações e instruções detalhadas consulte [o repositório GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Usando Azure com PipelineFX Qube

O Azure Render Hub suporta gestores de renderização populares, incluindo o Deadline.  Para obter instruções sobre a implantação e utilização do Render Hub consulte [o repositório GitHub](https://github.com/Azure/azure-render-hub).

Scripts e instruções para permitir que os VMs da piscina de Azure Batch sejam usados como trabalhadores qube também estão disponíveis [no repositório GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Usando Azure com Render Real

A Royal Render tem integração Azure e Azure Batch incorporada, permitindo-lhe estender uma quinta de renderização com VMs baseados em Azure. Para um resumo, consulte [os ficheiros de ajuda](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Para um exemplo de um cliente royal render usando a integração Azure, consulte a história do [cliente da Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Utilização do Azure com Prazo de Caixa de Pensamento

O Azure Render Hub suporta gestores de renderização populares, incluindo o Deadline.  Para obter instruções sobre a implantação e utilização do Render Hub consulte [o repositório GitHub](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Passos seguintes

Experimente a integração do Azure Batch para o seu gestor de renderização, utilizando o plug-in apropriado e as instruções no GitHub, sempre que aplicável.
