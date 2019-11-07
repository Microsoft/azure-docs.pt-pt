---
title: Usar o Azure Media Clipper no portal | Microsoft Docs
description: Criar clipes usando o Azure Media Clipper no portal do Azure
services: media-services
keywords: clip; subclip; codificação; mídia
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ec43fa469547dcd6481c0c6781c438f42ab4e2bd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685001"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Criar clipes com o Azure Media Clipper no portal  

Você pode usar o Azure Media Clipper no portal para criar clipes de ativos em suas contas de serviços de mídia. Para começar, navegue até sua conta de serviços de mídia no Portal. Em seguida, selecione a guia **subclipe** .

Na guia **subclipe** , você pode começar a compor clipes. No portal, o Clipper carrega MP4s de taxa de bits única, MP4s de múltiplas taxas de bits e arquivos dinâmicos que são publicados com um localizador de streaming válido. Os ativos não publicados não são carregados.

O Clipper está atualmente em visualização pública. Para acessar o Clipper no portal do Azure, vá para esta [página de visualização pública](https://portal.azure.com/?feature.subclipper=true).

A imagem a seguir ilustra a página de aterrissagem do Clipper em sua conta dos serviços de mídia: ![Azure Media Clipper no portal do Azure](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Produzindo clipes
Para criar um clipe, arraste e solte um ativo na interface do clipe. Se os tempos de marca forem conhecidos, você poderá inseri-los manualmente na interface. Caso contrário, reproduza o ativo ou arraste o indicador de reprodução para localizar o horário desejado de marca de entrada e de saída. Se não for fornecida uma hora de marcação ou de saída, o clipe começará do início ou continuará até o final do ativo de entrada, respectivamente.

Para navegar com precisão de quadro/GOP de precisão, use os botões quadro para frente/GOP-encaminhar ou quadro para trás/GOP. Para recorte em vários ativos, arraste e solte vários ativos na interface do clipe do painel de seleção de ativos. Você pode selecionar e reordenar os ativos na interface para a ordem desejada. O painel seleção de ativos fornece a duração do ativo, o tipo e os metadados de resolução para cada ativo. Ao concatenar vários ativos, considere a resolução de origem de cada arquivo de entrada. Se as resoluções de origem forem diferentes, a entrada de resolução mais baixa será ajustada para atender à resolução do ativo de resolução mais alta. Para visualizar a saída do trabalho de recorte, selecione o botão Visualizar e o clipe será reproduzido a partir das horas de marca selecionadas.

## <a name="producing-dynamic-manifest-filters"></a>Produzindo filtros de manifesto dinâmico
Os [filtros de manifesto dinâmico](https://azure.microsoft.com/blog/dynamic-manifest/) descrevem um conjunto de regras com base nos atributos do manifesto e na linha do tempo do ativo. Essas regras determinam como o ponto de extremidade de streaming manipula a playlist de saída (manifesto). O filtro pode ser usado para alterar quais segmentos são transmitidos para reprodução. Os filtros produzidos pelo Clipper são filtros locais e são específicos para o ativo de origem. Ao contrário dos clipes renderizados, os filtros não são novos ativos e não exigem um trabalho de codificação para produzir. Eles podem ser criados rapidamente por meio do [SDK do .net](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) ou da [API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), no entanto, eles são apenas GOP. Normalmente, os ativos codificados para streaming têm um tamanho de GOP de dois segundos.

Para criar um filtro de manifesto dinâmico, navegue até a guia **ativos** e selecione o ativo desejado. Selecione o botão **subclipe** para o menu superior. Selecione filtro de manifesto dinâmico como o modo de recorte no menu de configurações avançadas. Em seguida, você pode seguir o mesmo processo para produzir um clipe renderizado para criar o filtro. Os filtros só podem ser produzidos de um único ativo.

A imagem a seguir ilustra o Clipper no modo de filtro de manifesto dinâmico no portal do Azure: ![Azure Media Clipper no modo de filtro de manifesto dinâmico no portal do Azure](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Enviando trabalhos de recorte
Quando terminar de compor o clipe, selecione o botão enviar trabalho para iniciar o trabalho de recorte correspondente ou a chamada de manifesto dinâmico.

## <a name="next-steps"></a>Passos seguintes
Para começar a usar o Azure Media Clipper, leia o artigo [introdução](media-services-azure-media-clipper-getting-started.md) para obter detalhes sobre como implantar o widget.
