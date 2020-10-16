---
title: Criar um ponto de final personalizado Microsoft Docs
description: Neste artigo, aprenda a configurar um ponto final personalizado para medir com o seu recurso Internet Analyzer.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6ca71bce726f16eeacc96e10eb654bb3e21c5924
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744090"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Meça pontos finais personalizados para avaliar nos seus testes de Analisador de Internet 

Este artigo demonstra como configurar um ponto final personalizado para medir como parte dos seus testes de Analisador de Internet. Os pontos finais personalizados ajudam a avaliar as cargas de trabalho no local, as cargas de trabalho em execução em outros fornecedores de nuvem e as configurações azure personalizadas.  Comparar dois pontos finais personalizados num teste é possível se um ponto final for um recurso Azure. Para obter mais informações sobre o Analisador de Internet, consulte a [visão geral.](internet-analyzer-overview.md) 

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que configura um recurso analisador de Internet e selecione a opção "Ponto final personalizado". O Internet Analyzer assume que o seu ponto final personalizado é acessível à Internet. Para obter mais informações, consulte [Criar um Recurso de Analisador de Internet.](internet-analyzer-create-test-portal.md)


## <a name="create-custom-endpoint"></a>Criar ponto de final personalizado

1. Faça o download de uma imagem transparente de um pixel [aqui.](https://fpc.msedge.net/apc/trans.gif) Esta imagem de um pixel é o ativo que o cliente JavaScript irá obter para medir o desempenho.
2. Na sua aplicação web personalizada, desloque a imagem de teste num caminho acessível ao público. O caminho deve funcionar sobre HTTPS. 
3. Copie o URL de ponto final personalizado completo (por `https://contoso.com/test/trans.gif` exemplo) no campo de ponto final personalizado durante a sua criação de teste.

## <a name="next-steps"></a>Passos seguintes

Leia o [Analisador de Internet FAQ](internet-analyzer-faq.md)

