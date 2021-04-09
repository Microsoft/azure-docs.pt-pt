---
title: Compressão de ficheiros de resolução de problemas em Azure CDN | Microsoft Docs
description: Saiba como resolver problemas com a compressão de ficheiros na Rede de Entrega de Conteúdos Azure. Este artigo abrange várias causas possíveis.
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
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f49af1488a0c044639a72fc2ea52ba0a47727a24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996156"
---
# <a name="troubleshooting-cdn-file-compression"></a>Resolver problemas de compressão de ficheiros CDN
Este artigo ajuda-o a resolver problemas com [a compressão de ficheiros CDN](cdn-improve-performance.md).

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn azure e nos fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em Obter **Apoio.**

## <a name="symptom"></a>Sintoma
A compressão para o seu ponto final está ativada, mas os ficheiros estão a ser devolvidos sem comprimir.

> [!TIP]
> Para verificar se os seus ficheiros estão a ser devolvidos [comprimidos,](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)tem de utilizar uma ferramenta como [o Fiddler](https://www.telerik.com/fiddler) ou as ferramentas de desenvolvimento do seu navegador .  Verifique os cabeçalhos de resposta HTTP devolvidos com o conteúdo em cache CDN.  Se houver um cabeçalho com `Content-Encoding` o valor do **gzip**, **bzip2** ou **esvaziar**, o seu conteúdo é comprimido.
> 
> ![Cabeçalho de codificação de conteúdos](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Causa
Existem várias causas possíveis, incluindo:

* O conteúdo solicitado não é elegível para compressão.
* A compressão não está ativada para o tipo de ficheiro solicitado.
* O pedido HTTP não incluía um cabeçalho solicitando um tipo de compressão válido.
* A origem está a enviar conteúdo em pedaços.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
> [!TIP]
> Tal como com a implementação de novos pontos finais, as alterações de configuração do CDN demoram algum tempo a propagar-se através da rede.  Normalmente, as alterações são aplicadas dentro de 90 minutos.  Se esta for a primeira vez que configura a compressão para o seu ponto final CDN, deve considerar esperar 1-2 horas para se certificar de que as definições de compressão se propagaram para os POPs. 
> 
> 

### <a name="verify-the-request"></a>Verifique o pedido
Primeiro, devemos fazer uma verificação rápida do pedido.  Pode utilizar as [ferramentas](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) de desenvolvimento do seu navegador para visualizar os pedidos que estão a ser feitos.

* Verifique se o pedido está a ser enviado para o seu URL de ponto `<endpointname>.azureedge.net` final, e não para a sua origem.
* Verifique se o pedido contém um cabeçalho **de aceitação** e o valor desse cabeçalho contém **gzip**, **deflate**, ou **bzip2**.

> [!NOTE]
> **Azure CDN dos** perfis da Akamai suporta apenas a codificação **gzip.**
> 
> 

![Cabeçalhos de pedido cdn](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Verificar definições de compressão (perfis padrão de CDN)
> [!NOTE]
> Este passo só se aplica se o seu perfil CDN for um **Azure CDN Standard da Microsoft**, **Azure CDN Standard da Verizon**, ou **Azure CDN Standard do perfil DaCosa.** 
> 
> 

Navegue até ao seu ponto final no [portal Azure](https://portal.azure.com) e clique no botão **Configure.**

* Verifique se a compressão está ativada.
* Verifique se o tipo MIME para o conteúdo a ser comprimido está incluído na lista de formatos comprimidos.

![Definições de compressão CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Verifique as definições de compressão (perfis premium cdn)
> [!NOTE]
> Este passo só se aplica se o seu perfil CDN for um **Azure CDN Premium do** perfil Verizon.
> 
> 

Navegue até ao seu ponto final no [portal Azure](https://portal.azure.com) e clique no botão **Gerir.**  O portal suplementar abrirá.  Passe por cima do separador **HTTP Large** e, em seguida, paire sobre o voo **das Definições cache.**  Clique **em Compressão**. 

* Verifique se a compressão está ativada.
* Verifique se a lista **de tipos de ficheiros** contém uma lista separada por vírgula (sem espaços) dos tipos MIME.
* Verifique se o tipo MIME para o conteúdo a ser comprimido está incluído na lista de formatos comprimidos.

![Definições de compressão premium CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Verifique se o conteúdo está em cache (perfis Verizon CDN)
> [!NOTE]
> Este passo só se aplica se o seu perfil CDN for um **Azure CDN Standard da Verizon** ou **Azure CDN Premium do** perfil Verizon.
> 
> 

Utilizando as ferramentas de desenvolvimento do seu navegador, verifique os cabeçalhos de resposta para garantir que o ficheiro está em cache na região onde está a ser solicitado.

* Verifique o cabeçalho de resposta do **Servidor.**  O cabeçalho deve ter o formato **Plataforma (POP/Server ID)**, como se pode ver no exemplo seguinte.
* Verifique o cabeçalho de resposta **X-Cache.**  O cabeçalho deve ler **HIT**.  

![Cabeçalhos de resposta CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Verifique se o ficheiro cumpre os requisitos de tamanho (perfis Verizon CDN)
> [!NOTE]
> Este passo só se aplica se o seu perfil CDN for um **Azure CDN Standard da Verizon** ou **Azure CDN Premium do** perfil Verizon.
> 
> 

Para ser elegível para compressão, um ficheiro deve satisfazer os seguintes requisitos de tamanho:

* Maior que 128 bytes (Comprimento de Conteúdo: 128)
* Menor que 3 MB

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Verifique o pedido no servidor de origem para obter um cabeçalho **Via**
O cabeçalho **Via** HTTP indica ao servidor web que o pedido está a ser transmitido por um servidor proxy.  Os servidores web do Microsoft IIS por padrão não comprimem respostas quando o pedido contém um cabeçalho **Via.**  Para anular este comportamento, execute o seguinte:

* **IIS 6**: [Definir HcNoCompressionForProxies="FALSE" nas propriedades do Metabase IIS](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 e up**: [Configurar sem **compressãoForHttp10** e **noCompressionForProxies** a Falsos na configuração do servidor](https://www.iis.net/configreference/system.webserver/httpcompression)

