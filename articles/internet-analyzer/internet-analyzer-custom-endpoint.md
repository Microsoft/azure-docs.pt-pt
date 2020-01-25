---
title: Criar um ponto final personalizado  Microsoft Docs
description: Neste artigo, aprenda a configurar um ponto final personalizado para medir com o seu recurso Analisador de Internet.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6a781e767393f35a1ec22f195ba787e3018d8d1e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713091"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Meça pontos finais personalizados para avaliar nos seus testes de Análise de Internet 

Este artigo demonstra como configurar um ponto final personalizado para medir como parte dos seus testes de Analisador de Internet. Os pontos finais personalizados ajudam a avaliar as cargas de trabalho no local, as cargas de trabalho a funcionar noutros fornecedores de nuvem e as configurações personalizadas do Azure.  Comparar dois pontos finais personalizados num teste é possível se um ponto final for um recurso Azure. Para mais informações sobre o Analisador de Internet, consulte a [visão geral](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de configurar um recurso do Analisador de Internet e selecionar a opção "Custom Endpoint". O Internet Analyzer assume que o seu ponto final personalizado é acessível à Internet. Para mais informações, consulte [Criar um Recurso analisador](internet-analyzer-create-test-portal.md)de Internet .


## <a name="create-custom-endpoint"></a>Criar ponto final personalizado

1. Descarregue uma imagem transparente de teste de um pixel [aqui](https://fpc.msedge.net/apc/trans.gif). Esta imagem de um pixel é o ativo que o cliente JavaScript irá buscar para medir o desempenho.
2. Na sua aplicação web personalizada, implemente a imagem de teste num caminho acessível ao público. O caminho deve funcionar sobre HTTPS. 
3. Copie o URL completo do ponto final personalizado (por exemplo, https://contoso.com/test/trans.gif) no campo de ponto final personalizado durante a sua criação de teste.

## <a name="next-steps"></a>Passos seguintes

Leia as [perguntas frequentes do Internet Analyzer](internet-analyzer-faq.md)

