---
title: Criar um ponto de extremidade personalizado | Microsoft Docs
description: Neste artigo, saiba como configurar um ponto de extremidade personalizado para medir com o recurso do Internet Analyzer.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 44d570746a54154a2f8d3c7a200c761a59b7422b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501926"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Meça pontos de extremidade personalizados para avaliar nos testes do Internet Analyzer 

Este artigo demonstra como configurar um ponto de extremidade personalizado para medir como parte dos testes do Internet Analyzer. Os pontos de extremidade personalizados ajudam a avaliar cargas de trabalho locais, cargas de trabalho em execução em outros provedores de nuvem e configurações personalizadas do Azure.  A comparação de dois pontos de extremidade personalizados em um único teste é possível se um deles for um recurso do Azure. Para obter mais informações sobre o Internet Analyzer, consulte a [visão geral](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de configurar um recurso do Internet Analyzer e selecione a opção "ponto de extremidade personalizado". O Internet Analyzer pressupõe que seu ponto de extremidade personalizado seja acessível à Internet. Para obter mais informações, consulte [criar um recurso do Internet Analyzer](internet-analyzer-create-test-portal.md).


## <a name="create-custom-endpoint"></a>Criar ponto de extremidade personalizado

1. Baixe uma imagem transparente de teste de um pixel [aqui](https://fpc.msedge.net/apc/trans.gif). Essa imagem de um pixel é o ativo que o JavaScript do cliente buscará para medir o desempenho.
2. Em seu aplicativo Web personalizado, implante a imagem de teste em um caminho publicamente acessível. O caminho deve funcionar em HTTPS. 
3. Copie a URL do ponto de extremidade personalizado completo (por exemplo, https://contoso.com/test/trans.gif) no campo de ponto de extremidade personalizado durante a criação do teste.

## <a name="next-steps"></a>Passos seguintes

Leia as [perguntas frequentes do Internet Analyzer](internet-analyzer-faq.md)

