---
title: Resolução de problemas Azure CDN pontos finais - 404 código de estado
description: Resolução de problemas 404 códigos de resposta com pontos finais Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c332c6712cdf057491e3039854aa1a29bd54196f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083122"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Resolução de problemas Azure CDN pontos finais que devolvem um código de estado 404
Este artigo permite-lhe resolver problemas com pontos finais da Rede de Entrega de Conteúdos Azure (CDN) que devolvem 404 códigos de estado de resposta HTTP.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode arquivar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Suporte**.

## <a name="symptom"></a>Sintoma
Criou um perfil de CDN e um ponto final, mas o seu conteúdo não parece estar disponível no CDN. Os utilizadores que tentem aceder ao seu conteúdo através do URL CDN recebem um código de estado HTTP 404. 

## <a name="cause"></a>Causa
Existem várias possíveis causas, incluindo:

* A origem do ficheiro não é visível para o CDN.
* O ponto final está mal configurado, fazendo com que o CDN procure no lugar errado.
* O anfitrião está rejeitando o cabeçalho do anfitrião da CDN.
* O ponto final não teve tempo de se propagar por toda a CDN.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
> [!IMPORTANT]
> Após a criação de um ponto final da CDN, não estará imediatamente disponível para utilização, uma vez que leva tempo para que o registo se propague através do CDN:
> - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em dez minutos. 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída no prazo de 90 minutos. 
> 
> Se completar os passos deste documento e ainda receber 404 respostas, considere esperar algumas horas para verificar novamente antes de abrir um bilhete de apoio.
> 
> 

### <a name="check-the-origin-file"></a>Verifique o ficheiro de origem
Primeiro, verifique se o ficheiro para cache está disponível no servidor de origem e está acessível ao público na internet. A forma mais rápida de o fazer é abrir um navegador numa sessão privada ou incógnita e navegar diretamente para o ficheiro. Digite ou colhe o URL na caixa de endereços e verifique se resulta no ficheiro que espera. Por exemplo, suponha que tenha um ficheiro numa\/conta de Armazenamento Azure, acessível em https: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Se conseguir carregar com sucesso o conteúdo deste ficheiro, passa no teste.

![Êxito!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Embora esta seja a forma mais rápida e fácil de verificar se o seu ficheiro está disponível ao público, algumas configurações de rede na sua organização podem fazer parecer que um ficheiro está disponível publicamente quando é, na verdade, apenas visível para os utilizadores da sua rede (mesmo que esteja hospedado em Azure). Para garantir que não é esse o caso, teste o ficheiro com um navegador externo, como um dispositivo móvel que não esteja ligado à rede da sua organização, ou uma máquina virtual no Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Verifique as definições de origem
Depois de verificar que o ficheiro está disponível publicamente na internet, verifique as definições de origem. No [Portal Azure,](https://portal.azure.com)navegue para o seu perfil CDN e selecione o ponto final que está a resolver. A partir da página de **Endpoint** resultante, selecione a origem.  

![Página de endpoint com origem em destaque](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

A página **Origin** aparece. 

![Página de origem](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Tipo de origem e nome de anfitrião
Verifique se os valores do **tipo Origem** e nome de **anfitrião origem** estão corretos. Neste exemplo, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, a parte do nome de anfitrião do URL é *cdndocdemo.blob.core.windows.net,* o que é correto. Uma vez que as origens do Azure Storage, Web App e Cloud Service usam um valor de lista de abandono para o campo de nome de **origem,** as ortografias incorretas não são um problema. No entanto, se utilizar uma origem personalizada, certifique-se de que o seu nome de anfitrião está corretamente escrito.

#### <a name="http-and-https-ports"></a>Portas HTTP e HTTPS
Verifique as **portas** **HTTP** e HTTPS . Na maioria dos casos, 80 e 443 estão corretos, e não vai precisar de alterações.  No entanto, se o servidor de origem estiver a ouvir uma porta diferente, isso terá de ser representado aqui. Se não tiver a certeza, veja o URL para o seu ficheiro de origem. As especificações HTTP e HTTPS utilizam as portas 80 e 443 como incumprimento. No url de exemplo, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, uma porta não é especificada, por isso o padrão de 443 é assumido e as definições estão corretas.  

No entanto, suponha que o URL para\/o ficheiro de origem que testou anteriormente é http: /www.contoso.com:8080/file.txt. Note a porção *:8080* no final do segmento de nome de anfitrião. Este número instrui o navegador a utilizar a porta 8080 para se ligar ao servidor web em www\.contoso.com, pelo que terá de introduzir o *8080* no campo da porta **HTTP.** É importante notar que estas definições de porta afetam apenas o que o ponto final usa para recolher informações da origem.

> [!NOTE]
> **A Norma Azure CDN dos** pontos finais da Akamai não permite a gama completa de portas TCP para origens.  Para obter uma lista das portas de origem que não são permitidas, consulte [Portas de Origem Permitidas do Azure CDN da Akamai](/previous-versions/azure/mt757337(v=azure.100)).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Verifique as definições do ponto final
Na página **Endpoint,** selecione o botão **Configurar.**

![Página de ponto final com botão de configuração realçado](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Aparece a página de **configuração** do ponto final do CDN.

![Página de configuração](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolos
Para **Protocolos,** verifique se o protocolo utilizado pelos clientes é selecionado. Como o mesmo protocolo utilizado pelo cliente é o utilizado para aceder à origem, é importante ter as portas de origem configuradas corretamente na secção anterior. O ponto final do CDN ouve apenas as portas HTTP e HTTPS padrão (80 e 443), independentemente das portas de origem.

Voltemos ao nosso hipotético\/exemplo com http: /www.contoso.com:8080/file.txt.  Como se lembrará, Contoso especificou *8080* como a sua porta HTTP, mas vamos também assumir que especificaram *44300* como a sua porta HTTPS.  Se criassem um ponto final chamado *contoso,* o seu nome de anfitrião de ponto final da CDN seria *contoso.azureedge.net*.  Um pedido de\/http: /contoso.azureedge.net/file.txt é um pedido HTTP, por isso o ponto final usaria http na porta 8080 para recuperá-lo da origem.  Um pedido seguro sobre\/HTTPS, https: /contoso.azureedge.net/file.txt, faria com que o ponto final usasse HTTPS na porta 44300 ao recuperar o ficheiro da origem.

#### <a name="origin-host-header"></a>Cabeçalho de anfitrião de origem
O **cabeçalho** do hospedeiro Origin é o valor do cabeçalho do hospedeiro enviado para a origem com cada pedido.  Na maioria dos casos, este deve ser o mesmo que o nome de **anfitrião origin** que verificado anteriormente.  Um valor incorreto neste campo geralmente não causa 404 estatutos, mas é provável que cause outros estatutos 4xx, dependendo do que a origem espera.

#### <a name="origin-path"></a>Caminho de origem
Por último, devemos verificar o nosso **caminho de Origem.**  Por defeito, isto é em branco.  Só deve utilizar este campo se quiser reduzir o âmbito dos recursos de origem que pretende disponibilizar no CDN.  

No ponto final, queríamos que todos os recursos da conta de armazenamento estivessem disponíveis, por isso o **caminho da Origem** ficou em branco.  Isto significa que um\/pedido de https: /cdndocdemo.azureedge.net/publicblob/lorem.txt resulta numa ligação do ponto final à cdndocdemo.core.windows.net que solicita */publicblob/lorem.txt*.  Da mesma forma, um\/pedido de https: /cdndocdemo.azureedge.net/donotcache/status.png resulta no ponto final solicitando */donotcache/status.png* a partir da origem.

Mas e se não quiser usar o CDN para todos os caminhos da sua origem?  Diga que só queria expor o caminho *do publicblob.*  Se entrarmos */publicblob* no campo **de caminho** origem, isso fará com que o ponto final insira/publicblob antes de cada pedido ser feito para a origem. */publicblob*  Isto significa que o\/pedido de https: /cdndocdemo.azureedge.net/publicblob/lorem.txt irá agora levar a parte de pedido do URL, */publicblob/lorem.txt,* e *apêndice/publicblob* para o início. Isto resulta num pedido de */publicblob/publicblob/lorem.txt* de origem.  Se esse caminho não for resolvido a um ficheiro real, a origem devolverá um estatuto de 404.  O URL correto para recuperar lorem.txt neste\/exemplo seria realmente https: /cdndocdemo.azureedge.net/lorem.txt.  Note que não incluímos o caminho */publicblob* em tudo, porque a parte de pedido do URL é */lorem.txt* e o ponto final adiciona */publicblob,* resultando em */publicblob/lorem.txt* sendo o pedido passado para a origem.

