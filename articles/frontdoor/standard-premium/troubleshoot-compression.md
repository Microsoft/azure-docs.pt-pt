---
title: Compressão de ficheiros de resolução de problemas em Azure Front Door Standard/Premium
description: Aprenda a resolver problemas com compressão de ficheiros na Porta frontal Azure. Este artigo abrange várias causas possíveis.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100231"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Resolução de problemas Azure Front Door Standard/Premium compressão de ficheiros

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Este artigo ajuda-o a resolver problemas com problemas de compressão de ficheiros Azure Front Door Standard/Premium.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="symptom"></a>Sintoma

A compressão para a sua rota está ativada, mas os ficheiros estão a ser devolvidos sem comprimir.

> [!TIP]
> Para verificar se os seus ficheiros estão a ser devolvidos [comprimidos,](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)tem de utilizar uma ferramenta como [o Fiddler](https://www.telerik.com/fiddler) ou as ferramentas de desenvolvimento do seu navegador .  Verifique os cabeçalhos de resposta HTTP devolvidos com o conteúdo em cache CDN.  Se houver um cabeçalho com `Content-Encoding` o valor do **gzip**, **bzip2** ou **esvaziar**, o seu conteúdo é comprimido.
> 
> ![Cabeçalho de codificação de conteúdos](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>Causa

Existem várias causas possíveis, incluindo:

* O conteúdo solicitado não é elegível para compressão.
* A compressão não está ativada para o tipo de ficheiro solicitado.
* O pedido HTTP não incluía um cabeçalho solicitando um tipo de compressão válido.
* A origem está a enviar conteúdo em pedaços.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

> [!TIP]
> Tal como com a implementação de novos pontos finais, as alterações de configuração da AFD demoram algum tempo a propagar-se através da rede.  Normalmente, as alterações são aplicadas dentro de 90 minutos.  Se esta for a primeira vez que configura a compressão para o seu ponto final CDN, deve considerar esperar 1-2 horas para se certificar de que as definições de compressão se propagaram para os POPs. 
> 

### <a name="verify-the-request"></a>Verifique o pedido

Primeiro, devemos verificar o pedido. Pode utilizar as **[ferramentas](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** de desenvolvimento do seu navegador para visualizar os pedidos que estão a ser feitos.

* Verifique se o pedido está a ser enviado para o seu URL de ponto `<endpointname>.z01.azurefd.net` final, e não para a sua origem.
* Verifique se o pedido contém um cabeçalho **de aceitação** e o valor desse cabeçalho contém **gzip**, **deflate**, ou **bzip2**.

![Cabeçalhos de pedido cdn](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>Verificar definições de compressão

Navegue até ao seu ponto final no [portal Azure](https://portal.azure.com) e selecione o botão **Configure** no painel Rotas. Verifique se a compressão está **ativada**.

![Definições de compressão CDN](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Verifique o pedido no servidor de origem para obter um cabeçalho **Via**

O cabeçalho **Via** HTTP indica ao servidor web que o pedido está a ser transmitido por um servidor proxy.  Os servidores web do Microsoft IIS por padrão não comprimem respostas quando o pedido contém um cabeçalho **Via.**  Para anular este comportamento, faça o seguinte:

* **IIS 6**: Definir HcNoCompressionForProxies="FALSE" nas propriedades do Metabase IIS. Para obter informações, consulte [iIS 6 Compressão](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90)).
* **IIS 7 e up**: **Configurar sem compressãoForHttp10** e **noCompressionForProxies** a *Falsos* na configuração do servidor. Para mais informações, consulte, [HTTP Compressão](https://www.iis.net/configreference/system.webserver/httpcompression).
