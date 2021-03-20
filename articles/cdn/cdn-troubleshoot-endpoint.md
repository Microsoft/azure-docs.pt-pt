---
title: Pontos finais do Azure CDN - código de estado 404
description: Saiba como resolver problemas com os pontos finais da Rede de Entrega de Conteúdos Azure que devolvem 404 códigos de estado de resposta HTTP.
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
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d6ad0b8b37bd4f04c22ed52d4ac6717202f22889
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192492"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Resolução de problemas Azure CDN pontos finais que devolvem um código de estado 404
Este artigo permite-lhe resolver problemas com pontos finais da Rede de Entrega de Conteúdos Azure (CDN) que devolvem 404 códigos de estado de resposta HTTP.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn azure e nos fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.

## <a name="symptom"></a>Sintoma
Criaste um perfil CDN e um ponto final, mas o teu conteúdo não parece estar disponível no CDN. Os utilizadores que tentem aceder ao seu conteúdo através do URL CDN recebem um código de estado HTTP 404. 

## <a name="cause"></a>Causa
Existem várias causas possíveis, incluindo:

* A origem do ficheiro não é visível para o CDN.
* O ponto final está mal configurado, fazendo com que o CDN procure no lugar errado.
* O anfitrião está rejeitando o cabeçalho do anfitrião do CDN.
* O ponto final não teve tempo de se propagar por toda a CDN.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
> [!IMPORTANT]
> Após a criação de um ponto final cdn, não estará imediatamente disponível para utilização, uma vez que leva tempo para que o registo se propague através do CDN:
> - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em dez minutos. 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída no prazo de 90 minutos. 
> 
> Se completar os passos deste documento e ainda estiver a receber 404 respostas, considere esperar algumas horas para verificar novamente antes de abrir um bilhete de apoio.
> 
> 

### <a name="check-the-origin-file"></a>Verifique o ficheiro de origem
Em primeiro lugar, verifique se o ficheiro para cache está disponível no servidor de origem e está acessível ao público na internet. A maneira mais rápida de o fazer é abrir um navegador numa sessão privada ou incógnita e navegar diretamente para o ficheiro. Digite ou cole o URL na caixa de endereços e verifique se resulta no ficheiro que espera. Por exemplo, suponha que tem um ficheiro numa conta de Armazenamento Azure, acessível em https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Se conseguir carregar com sucesso o conteúdo deste ficheiro, passa no teste.

![Êxito!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Embora esta seja a forma mais rápida e fácil de verificar se o seu ficheiro está disponível publicamente, algumas configurações de rede na sua organização podem fazer parecer que um ficheiro está disponível ao público quando, na verdade, só é visível para os utilizadores da sua rede (mesmo que esteja hospedado no Azure). Para garantir que este não é o caso, teste o ficheiro com um navegador externo, como um dispositivo móvel que não esteja ligado à rede da sua organização, ou uma máquina virtual em Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Verifique as definições de origem
Depois de verificar se o ficheiro está disponível publicamente na internet, verifique as definições de origem. No [Portal Azure,](https://portal.azure.com)navegue no seu perfil CDN e selecione o ponto final que está a resolver problemas. A partir da página **endpoint** resultante, selecione a origem.  

![Página de ponto final com origem realçada](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

A página **Origin** aparece. 

![Página de origem](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Tipo de origem e nome de hospedeiro
Verifique se os valores do **tipo Origem** e do nome de hospedeiro Origin estão **corretos.** Neste exemplo, https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, a parte do nome de anfitrião do URL é *cdndocdemo.blob.core.windows.net*, o que é correto. Como as origens do Azure Storage, Web App e Cloud Service utilizam um valor de lista para o campo **de nome de anfitrião Origin,** as ortografias incorretas não são um problema. No entanto, se utilizar uma origem personalizada, certifique-se de que o seu nome de anfitrião está corretamente escrito.

#### <a name="http-and-https-ports"></a>Portas HTTP e HTTPS
Consulte as portas **HTTP** e **HTTPS.** Na maioria dos casos, 80 e 443 estão corretos, e não vai precisar de alterações.  No entanto, se o servidor de origem estiver a ouvir numa porta diferente, isso terá de ser representado aqui. Se não tiver a certeza, consulte o URL para o seu ficheiro de origem. As especificações HTTP e HTTPS utilizam as portas 80 e 443 como padrão. No exemplo URL, https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, uma porta não é especificada, pelo que o padrão de 443 é assumido e as definições estão corretas.  

No entanto, suponha que o URL do ficheiro de origem que testou anteriormente é http: \/ /www.contoso.com:8080/file.txt. Note a porção *:8080* no final do segmento de nome hospedeiro. Este número instrui o navegador a utilizar a porta 8080 para ligar ao servidor web em www \. contoso.com, pelo que terá de introduzir *8080* no campo **de portas HTTP.** É importante notar que estas definições de porta afetam apenas o que o ponto final utiliza para obter informações da origem.

> [!NOTE]
> **Azure CDN Standard dos** pontos finais da Akamai não permite a gama completa de portas TCP para as origens.  Para obter uma lista das portas de origem que não são permitidas, consulte [Portas de Origem Permitidas do Azure CDN da Akamai](/previous-versions/azure/mt757337(v=azure.100)).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Verifique as definições do ponto final
Na página **'Ponto final',** selecione o **botão Configurar.**

![Página de ponto final com botão de configuração realçado](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Aparece a página cdn endpoint **Configure.**

![Página de configuração](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolos
Para **Protocolos,** verifique se o protocolo utilizado pelos clientes está selecionado. Como o mesmo protocolo usado pelo cliente é o usado para aceder à origem, é importante ter as portas de origem configuradas corretamente na secção anterior. O ponto final do CDN ouve apenas nas portas HTTP e HTTPS predefinidos (80 e 443), independentemente das portas de origem.

Voltemos ao nosso hipotético exemplo com http: \/ /www.contoso.com:8080/file.txt.  Como se lembrará, Contoso especificou *8080* como porta HTTP, mas vamos também assumir que especificaram *44300* como porta HTTPS.  Se criassem um ponto final chamado *contoso,* o seu nome de anfitrião cdn seria *contoso.azureedge.net*.  Um pedido de http: \/ /contoso.azureedge.net/file.txt é um pedido HTTP, pelo que o ponto final utilizaria HTTP na porta 8080 para o recuperar da origem.  Um pedido seguro sobre HTTPS, https: \/ /contoso.azureedge.net/file.txt, faria com que o ponto final usasse HTTPS na porta 44300 ao recuperar o ficheiro da origem.

#### <a name="origin-host-header"></a>Cabeçalho de anfitrião de origem
O **cabeçalho do anfitrião Origin** é o valor do cabeçalho do anfitrião enviado para a origem a cada pedido.  Na maioria dos casos, este deve ser o mesmo que o **nome de hospedeiro Origin** que verificámos anteriormente.  Um valor incorreto neste campo geralmente não causará 404 estados, mas é provável que cause outros estados 4xx, dependendo do que a origem espera.

#### <a name="origin-path"></a>Caminho de origem
Por último, devemos verificar o nosso **caminho de Origem.**  Por defeito, isto está em branco.  Só deve utilizar este campo se quiser reduzir o âmbito dos recursos alojados na origem que pretende disponibilizar no CDN.  

No ponto final, queríamos que todos os recursos da conta de armazenamento ficassem disponíveis, pelo que o **caminho da Origem** ficou em branco.  Isto significa que um pedido de https: \/ /cdndocdemo.azureedge.net/publicblob/lorem.txt resulta numa ligação entre o ponto final e o cdndocdemo.core.windows.net que solicita */publicblob/lorem.txt*.  Da mesma forma, um pedido de https: \/ /cdndocdemo.azureedge.net/donotcache/status.png resulta no ponto final solicitando */donotcache/status.png* da origem.

Mas e se não quiser usar o CDN para todos os caminhos da sua origem?  Diga que só queria expor o caminho *do público.*  Se entrarmos */publicblob* no campo **caminho Origem,** isso fará com que o ponto final insira */publicblob* antes de cada pedido ser feito à origem.  Isto significa que o pedido de https: \/ /cdndocdemo.azureedge.net/publicblob/lorem.txt irá agora efetivamente aceitar a parte de pedido do URL, */publicblob/lorem.txt*, e o apêndice */publicblob* para o início. Isto resulta num pedido de */publicblob/publicblob/lorem.txt* da origem.  Se esse caminho não se resolver a um ficheiro real, a origem devolverá um estado 404.  O URL correto para recuperar lorem.txt neste exemplo seria, na verdade, https: \/ /cdndocdemo.azureedge.net/lorem.txt.  Note que não incluímos o caminho */publicblob* de todo, porque a parte de pedido do URL é */lorem.txt* e o ponto final adiciona */publicblob,* resultando em */publicblob/lorem.txt* sendo o pedido passado para a origem.

