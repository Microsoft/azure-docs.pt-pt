---
title: Compressão de ficheiros de resolução de problemas em Azure CDN Microsoft Docs
description: Problemas de resolução de problemas com compressão de ficheiros Azure CDN.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: aff2dadee365fcdc7e14070714aa1d2cbba901ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476428"
---
# <a name="troubleshooting-cdn-file-compression"></a>Resolver problemas de compressão de ficheiros CDN
Este artigo ajuda-o a resolver problemas com a compressão de [ficheiros CDN](cdn-improve-performance.md).

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode arquivar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em Obter **Suporte**.

## <a name="symptom"></a>Sintoma
A compressão para o seu ponto final está ativada, mas os ficheiros estão a ser devolvidos sem comprimidos.

> [!TIP]
> Para verificar se os seus ficheiros estão a ser devolvidos comprimidos, precisa de utilizar uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) ou as [ferramentas](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)de desenvolvimento do seu navegador.  Verifique os cabeçalhos de resposta HTTP devolvidos com o conteúdo do CDN em cache.  Se houver um `Content-Encoding` cabeçalho com o nome de um **gzip2**, **bzip2**, ou **esvaziar,** o seu conteúdo é comprimido.
> 
> ![Cabeçalho de codificação de conteúdo](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Causa
Existem várias possíveis causas, incluindo:

* O conteúdo solicitado não é elegível para compressão.
* A compressão não está ativada para o tipo de ficheiro solicitado.
* O pedido HTTP não incluía um cabeçalho solicitando um tipo de compressão válido.
* A origem está a enviar conteúdo em pedaços.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
> [!TIP]
> Tal como acontece com a implementação de novos pontos finais, as alterações de configuração do CDN demoram algum tempo a propagar-se através da rede.  Normalmente, as alterações são aplicadas dentro de 90 minutos.  Se esta for a primeira vez que configura a compressão para o seu ponto final de CDN, deve considerar esperar 1 a 2 horas para ter a certeza de que as definições de compressão se propagaram aos POPs. 
> 
> 

### <a name="verify-the-request"></a>Verifique o pedido
Primeiro, devemos fazer uma rápida verificação da sanidade do pedido.  Pode utilizar [as ferramentas](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) de desenvolvimento do seu navegador para ver os pedidos que estão a ser feitos.

* Verifique se o pedido está a `<endpointname>.azureedge.net`ser enviado para o seu URL de ponto final, e não para a sua origem.
* Verifique se o pedido contém um cabeçalho **accept-encoding,** e o valor para esse cabeçalho contém **fecho, esvaziamento**ou **bzip2**. **deflate**

> [!NOTE]
> **Azure CDN dos** perfis da Akamai apenas suportam a codificação **de gzip.**
> 
> 

![Cabeçalhos de pedido da CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Verifique as definições de compressão (perfis padrão de CDN)
> [!NOTE]
> Este passo só se aplica se o seu perfil CDN for um **Padrão CDN Azure da Microsoft,** **Azure CDN Standard da Verizon,** ou **Azure CDN Standard do** perfil Akamai. 
> 
> 

Navegue até ao seu ponto final no [portal Azure](https://portal.azure.com) e clique no botão **Configure.**

* Verifique se a compressão está ativada.
* Verifique se o tipo MIME para que o conteúdo seja comprimido está incluído na lista de formatos comprimidos.

![Definições de compressão CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Verifique as definições de compressão (perfis Premium CDN)
> [!NOTE]
> Este passo só se aplica se o seu perfil CDN for um **Azure CDN Premium do** perfil Verizon.
> 
> 

Navegue até ao seu ponto final no [portal Azure](https://portal.azure.com) e clique no botão **Gerir.**  O portal suplementar abrirá.  Paire sobre a aba **HTTP Grande** e, em seguida, paire sobre as **Definições de Cache** flyout.  Clique em **Compressão**. 

* Verifique se a compressão está ativada.
* Verifique se a lista de Tipos de **Ficheiros** contém uma lista separada pela vírina (sem espaços) dos tipos MIME.
* Verifique se o tipo MIME para que o conteúdo seja comprimido está incluído na lista de formatos comprimidos.

![Definições de compressão premium CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Verifique se o conteúdo está em cache (perfis Verizon CDN)
> [!NOTE]
> Este passo só se aplica se o seu perfil CDN for um **Padrão CDN Azure da Verizon** ou **Azure CDN Premium do** perfil verizon.
> 
> 

Utilizando as ferramentas de desenvolvimento do seu navegador, verifique os cabeçalhos de resposta para garantir que o ficheiro está em cache na região onde está a ser solicitado.

* Verifique o cabeçalho de resposta do **Servidor.**  O cabeçalho deve ter a Plataforma de formato **(ID POP/Servidor)**, como se pode ver no exemplo seguinte.
* Verifique o cabeçalho de resposta **X-Cache.**  O cabeçalho deve ler **HIT**.  

![Cabeçalhos de resposta CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Verifique se o ficheiro satisfaz os requisitos de tamanho (perfis Verizon CDN)
> [!NOTE]
> Este passo só se aplica se o seu perfil CDN for um **Padrão CDN Azure da Verizon** ou **Azure CDN Premium do** perfil verizon.
> 
> 

Para ser elegível para compressão, um ficheiro deve satisfazer os seguintes requisitos de tamanho:

* Maiores que 128 bytes.
* Menor que 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Verifique o pedido no servidor de origem para um cabeçalho **Via**
O cabeçalho **Via** HTTP indica ao servidor web que o pedido está a ser passado por um servidor proxy.  Os servidores web do Microsoft IIS por padrão não comprimem respostas quando o pedido contém um cabeçalho **Via.**  Para anular este comportamento, execute o seguinte:

* **IIS 6**: [Conjunto hcNoCompressionForProxies="FALSE" nas propriedades da Metabase IIS](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 e up**: [Definir tanto **noCompressionForHttp10** como **noCompressionForProxies** para Falso na configuração do servidor](https://www.iis.net/configreference/system.webserver/httpcompression)

