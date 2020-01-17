---
title: Escolher o tipo de preço certo | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre os tipos de preço oferecidos pelo Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 8764e9161f952118ca7ae28343dcd16477cf1eee
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155762"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Escolha o tipo de preço certo no Azure Maps

O mapas do Azure oferece dois tipos de preço, S0 e S1. A finalidade deste artigo é ajudá-lo a escolher o tipo de preço certo para suas necessidades. Para ajudar a escolher o tipo de preço certo, faça as duas perguntas a seguir.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quais recursos geoespaciais eu planejo usar?
O tipo de preço S0 é ideal para você se as APIs geoespaciais principais atenderem aos seus requisitos de serviço. Se você quiser recursos mais avançados para seu aplicativo, considere a possibilidade de optar pelo tipo de preço S1. Exemplo de recursos avançados: imagens híbridas, como obter intervalo de rotas e geocodificação de lote. A tabela de **capacidades de tipo de preço** pode ajudá-lo a escolher um tipo de preço mais adequado para seu aplicativo.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>A quantos usuários simultâneos eu planejo oferecer suporte? 
Os tipos de preço S0 e S1 lidam com diferentes quantidades de taxa de transferência de dados. O tipo de preço S0 lida com até **50 consultas por segundo**, enquanto a camada S1 lida com **mais de 50 consultas por segundo**.

### <a name="pricing-tier-capabilities"></a>Recursos de tipo de preço

| Capacidade                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Renderização de mapa                              | ✓                   | ✓       |
| Imagens satélite                       |                     | ✓        |
| Search                                  | ✓                    | ✓        |
| Pesquisa do Batch                            |                     | ✓        |
| Encaminhar                                   | ✓                    |✓        |
| Encaminhamento do Batch                            |                    | ✓        |
| Encaminhamento da Matriz                          |                     | ✓        |
| Intervalo de Rotas (Isócronos)                |                     | ✓        |
| Trânsito                                |✓                    |✓        |
| Fuso Horário                               |✓                    |✓        |
| Localização geográfica (versão prévia)                    |✓                   |✓        |
| Operações Geográficas                        |                    |✓        |
| Perímetro geográfico                                |                    |✓        |
| Dados do Azure Maps (versão prévia)                |                     | ✓        |
| Mobilidade (visualização)                       |                     | ✓        |
| Clima (visualização)                        |✓                    |✓        |

Vale a pena considerar esses pontos de dados adicionais:
* Que tipo de empresa você tem?
* Quão crítico é seu aplicativo?

### <a name="pricing-tier-targeted-customers"></a>Clientes com direcionamento de tipo de preço

Consulte a tabela **clientes do tipo de preço direcionados** para obter uma noção melhor dos tipos de preço S0 e S1. Para obter mais informações, consulte [preços do Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Escalão de preço  |     Clientes de destino                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>O tipo de preço S0 funciona para aplicativos em todos os estágios de produção: desde desenvolvimento de prova de conceito e teste antecipado para produção e implantação de aplicativos. No entanto, essa camada foi projetada para desenvolvimento de pequena escala ou para clientes com poucos usuários simultâneos, ou ambos. <p>|
| S1            |    <p>O tipo de preço S1 é para os clientes que precisam de suporte para empresas de grande escala, aplicativos críticos de missão ou grandes volumes de usuários simultâneos. Ele também é para os clientes que precisam de serviços geoespaciais avançados.</p>|

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como exibir e alterar os tipos de preço:

> [!div class="nextstepaction"] 
> [Gerenciar um tipo de preço](how-to-manage-pricing-tier.md)
