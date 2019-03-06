---
title: Utilizar o Azure Media Clipper no Portal do | Documentos da Microsoft
description: Crie clips a utilizar o Azure Media Clipper no Portal do Azure
services: media-services
keywords: Clip; subclip; codificação; suporte de dados
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 02/08/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 253b4fa511ec3efed2fb94c02ed4c4686d7a4549
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438453"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Crie clips com o Azure Media Clipper no portal  

Pode utilizar o Azure Media Clipper no portal para crie clips a partir de recursos nas suas contas de serviços de multimédia. Para começar a utilizar, navegue para a conta de serviços de multimédia no portal. Em seguida, selecione o **Subclip** separador.

Sobre o **Subclip** guia, pode começar a compor clips. No portal, o Clipper carrega MP4s de velocidade de transmissão única, MP4s de velocidade de transmissão e arquivos em direto, que são publicados com um localizador de transmissão em fluxo válido. Não são carregados ativos não publicados.

O Clipper está atualmente em pré-visualização pública. Para acessar o Clipper no portal do Azure, aceda à [página pré-visualização pública](https://portal.azure.com/?feature.subclipper=true).

A imagem seguinte ilustra a página de destino Clipper na sua conta dos media services: ![Azure Media Clipper no portal do Azure](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Produzir clips
Para criar um clip, arrastar e soltar um recurso para a interface de clip. Se os tempos de mark são conhecidos, possível inseri-los manualmente para a interface. Caso contrário, o recurso de reprodução ou arrastar a cabeça de leitura para localizar o mark-in desejado e fora de marca de tempo. Se não for fornecido um tempo de mark ou mark horizontalmente, o clip inicia no começo ou continua o fim do elemento de entrada, respectivamente.

Para navegar com quadro-precisão/GOP-precisão, utilize os botões de quadro-forward/GOP-forward ou quadro-com versões anteriores/GOP-com versões anteriores. Para recorte em relação a vários recursos, arraste e largue vários recursos na interface do clip a partir do painel de seleção de elemento. Pode selecionar e reordenar ativos na interface para a ordem pretendida. O painel de seleção de elemento fornece a duração do recurso, tipo e metadados de resolução para cada recurso. Quando a concatenação de vários recursos em conjunto, considere a resolução de origem de cada ficheiro de entrada. Se as resoluções de origem forem diferentes, mais baixa de resolução de entrada é upscaled para satisfazer a resolução de ativo de resolução mais alta. Para pré-visualizar a saída da tarefa de recorte, selecione o botão de pré-visualização e o clip reproduz a partir dos tempos de mark selecionado.

## <a name="producing-dynamic-manifest-filters"></a>Produção de filtros do manifesto dinâmico
[Filtros do manifesto dinâmico](https://azure.microsoft.com/blog/dynamic-manifest/) descrevem um conjunto de regras com base nos atributos de manifestos e a linha cronológica de recurso. Estas regras determinam como o ponto final de transmissão em fluxo manipula a lista de reprodução de saída (manifesto). O filtro pode ser utilizado para alterar que segmentos são transmitidas em fluxo para a reprodução. Os filtros produzidos pelo Clipper são filtros locais e são específicos para o elemento de origem. Ao contrário de clips renderizados, os filtros não são novos recursos e não necessitam de uma tarefa de codificação para produzir. Podem ser criadas rapidamente por meio dos [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) ou [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), no entanto, estão apenas GOP corretas. Normalmente, os ativos codificados para transmissão em fluxo têm um tamanho de GOP de dois segundos.

Para criar um filtro do manifesto dinâmico, navegue para o **ativos** separador e selecione o elemento desejado. Selecione o **Subclip** botão de menu superior. Selecione o filtro do manifesto dinâmico como o modo de recorte no menu de definições avançadas. Em seguida, pode seguir o mesmo processo para produzir um clip renderizado para criar o filtro. Filtros só podem ser produzidos de um único recurso.

A imagem seguinte ilustra o Clipper no modo de filtro de manifesto dinâmico no portal do Azure: ![Azure Media Clipper no modo de filtro de manifesto dinâmico no portal do Azure](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Submeter tarefas de recorte
Quando tiver terminado de compor o clip, selecione o botão de tarefa de envio para iniciar a tarefa de recorte correspondente ou a chamada de manifesto dinâmica.

## <a name="next-steps"></a>Passos Seguintes
Para começar a utilizar o Azure Media Clipper, leia os [introdução ao](media-services-azure-media-clipper-getting-started.md) artigo para obter detalhes sobre como implementar o widget.
