---
title: Resolução de problemas de compressão de ficheiros na CDN do Azure | Documentos da Microsoft
description: Resolva problemas com a compressão de ficheiros da CDN do Azure.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 2a41316eadb43145628d6c625935c751bfbc6ad6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531532"
---
# <a name="troubleshooting-cdn-file-compression"></a>Resolver problemas de compressão de ficheiros CDN
Este artigo ajuda-o a resolver problemas com o [compressão de ficheiros CDN](cdn-improve-performance.md).

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure no [do Azure do MSDN e os fóruns de Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **obter suporte**.

## <a name="symptom"></a>Sintoma
A compressão para o ponto final está ativada, mas os ficheiros estão a ser devolvidos não comprimidos.

> [!TIP]
> Para verificar se os ficheiros estão a ser devolvidos comprimidos, tem de utilizar uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) ou do seu browser [ferramentas de programação](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Cabeçalhos de resposta da verificação do HTTP devolveu o CDN em cache conteúdos.  Se existe um cabeçalho denominado `Content-Encoding` com um valor de **gzip**, **bzip2**, ou **deflate**, seu conteúdo é compactado.
> 
> ![Cabeçalho de codificação de conteúdo](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Causa
Existem várias causas possíveis, incluindo:

* O conteúdo solicitado não é elegível para compressão.
* Compressão não está ativada para o tipo de ficheiros pedida.
* O pedido HTTP não inclui um cabeçalho de solicitar um tipo de compressão válido.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
> [!TIP]
> Tal como acontece com a implementar novos pontos de extremidade, alterações de configuração de CDN demoram algum tempo para propagar através da rede.  Normalmente, as alterações são aplicadas dentro de 90 minutos.  Se esta for a primeira vez que tiver configurado a compressão para o ponto final da CDN, deve considerar a aguardar de 1 a 2 horas para garantir que a compactação definições terem sido propagados para os POPs. 
> 
> 

### <a name="verify-the-request"></a>Verifique se o pedido
Em primeiro lugar, devemos fazer uma verificação da funcionalidade de rápida no pedido.  Pode utilizar o browser [ferramentas de programação](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) para ver os pedidos que estão sendo feitos.

* Certifique-se de que o pedido é enviado para o URL de ponto de extremidade, `<endpointname>.azureedge.net`e não a sua origem.
* Certifique-se de que o pedido contém um **Accept-Encoding** cabeçalho e o valor para esse cabeçalho contém **gzip**, **deflate**, ou **bzip2**.

> [!NOTE]
> **A CDN do Azure da Akamai** perfis só suportam **gzip** codificação.
> 
> 

![Cabeçalhos de pedido CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Verifique se as definições de compressão (perfis CDN standard)
> [!NOTE]
> Este passo aplica-se apenas se o seu perfil da CDN é uma **CDN Standard do Microsoft Azure**, **CDN do Azure Standard da Verizon**, ou **CDN do Azure Standard da Akamai** perfil. 
> 
> 

Navegue para o ponto final no [portal do Azure](https://portal.azure.com) e clique nas **configurar** botão.

* Verifique se a compressão está ativada.
* Certifique-se de que o tipo de MIME para o conteúdo para comprimir está incluído na lista de formatos de compressão.

![Definições de compressão de CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Verifique se as definições de compressão (perfis de Premium CDN)
> [!NOTE]
> Este passo aplica-se apenas se o seu perfil da CDN é uma **CDN do Azure Premium da Verizon** perfil.
> 
> 

Navegue para o ponto final no [portal do Azure](https://portal.azure.com) e clique nas **gerir** botão.  É aberto o portal suplementar.  Paire o rato sobre o **HTTP grandes** separador, em seguida, coloque o cursor sobre o **definições de Cache** submenu.  Clique em **compressão**. 

* Verifique se a compressão está ativada.
* Verifique se o **tipos de ficheiro** lista contém uma lista separada por vírgulas (sem espaços) de tipos de MIME.
* Certifique-se de que o tipo de MIME para o conteúdo para comprimir está incluído na lista de formatos de compressão.

![Definições de compressão do CDN premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Certifique-se de que o conteúdo está em cache (perfis de CDN da Verizon)
> [!NOTE]
> Este passo aplica-se apenas se o seu perfil da CDN é uma **CDN do Azure Standard da Verizon** ou **CDN do Azure Premium da Verizon** perfil.
> 
> 

Utilizar ferramentas de programador do seu browser, verifique os cabeçalhos de resposta para garantir que o arquivo é armazenado em cache na região onde está a ser requerido.

* Verifique os **servidor** cabeçalho de resposta.  O cabeçalho deve ter o formato **plataforma (ID de servidor/POP)**, como mostrado no exemplo a seguir.
* Verifique os **X-Cache** cabeçalho de resposta.  O cabeçalho deve ler **ATINGIR**.  

![Cabeçalhos de resposta CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Certifique-se de que o ficheiro cumpre os requisitos de tamanho (perfis de CDN da Verizon)
> [!NOTE]
> Este passo aplica-se apenas se o seu perfil da CDN é uma **CDN do Azure Standard da Verizon** ou **CDN do Azure Premium da Verizon** perfil.
> 
> 

Para ser elegível para compressão, um ficheiro tem de cumprir os seguintes requisitos de tamanho:

* Maior do que 128 bytes.
* Menor que 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Verifique o pedido para o servidor de origem para um **Via** cabeçalho
O **Via** cabeçalho HTTP indica ao servidor web que a solicitação está sendo passada por um servidor proxy.  Servidores de web do Microsoft IIS por predefinição não comprimem respostas quando o pedido contém um **Via** cabeçalho.  Para substituir esse comportamento, execute o seguinte:

* **IIS 6**: [Definir HcNoCompressionForProxies = "FALSE" nas propriedades da linha de Metabase do IIS](https://msdn.microsoft.com/library/ms525390.aspx)
* **IIS 7 e até**: [Definir ambos **noCompressionForHttp10** e **noCompressionForProxies** como False na configuração do servidor](http://www.iis.net/configreference/system.webserver/httpcompression)

