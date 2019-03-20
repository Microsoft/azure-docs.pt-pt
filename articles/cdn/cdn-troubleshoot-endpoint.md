---
title: Resolução de problemas de pontos finais de CDN do Azure que devolvem um código de 404 Estado | Documentos da Microsoft
description: Resolver problemas relacionados com códigos de resposta 404 com pontos finais de CDN do Azure.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 8c1e463378cc2c1ba3fdc0bcf91f800f634cc5f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077127"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Resolução de problemas de pontos finais de CDN do Azure que devolvem um código de 404 Estado
Este artigo permite-lhe resolver problemas com pontos finais de rede de entrega conteúdo (CDN) que retornam códigos de estado de resposta HTTP 404.

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure no [do Azure do MSDN e os fóruns de Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

## <a name="symptom"></a>Sintoma
Criar um perfil CDN e um ponto de extremidade, mas seu conteúdo parece não estar disponível na CDN. Os utilizadores que tentarem aceder ao conteúdo via a URL do CDN recebem um código de estado HTTP 404. 

## <a name="cause"></a>Causa
Existem várias causas possíveis, incluindo:

* Origem do ficheiro não é visível para a CDN.
* O ponto final está configurado incorretamente, fazendo com que a CDN procurar no lugar errado.
* O anfitrião está a rejeitar o cabeçalho de anfitrião da CDN.
* O ponto final ainda não teve tempo para propagar em toda a CDN.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
> [!IMPORTANT]
> Depois de criar um ponto final da CDN, não imediatamente estará disponível para uso, como demora algum tempo a propagar através da CDN do registo:
> - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em dez minutos. 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída no prazo de 90 minutos. 
> 
> Se concluir os passos neste documento e ainda estamos a preparar 404 respostas, considere a aguardar algumas horas para verificar novamente antes de abrir um pedido de suporte.
> 
> 

### <a name="check-the-origin-file"></a>Verifique o ficheiro de origem
Em primeiro lugar, certifique-se de que o ficheiro para cache está disponível no servidor de origem e está acessível ao público na internet. A maneira mais rápida de fazer isso é abrir um browser numa sessão privada ou navegação anónima e procure diretamente o ficheiro. Escreva ou cole o URL na caixa de endereço e certifique-se de que o que resulta no ficheiro esperado. Por exemplo, suponha que possui um arquivo numa conta de armazenamento do Azure, acessível em https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Se com êxito, pode carregar conteúdos neste ficheiro, ele passa o teste.

![Êxito!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Embora essa seja a maneira mais rápida e fácil para verificar o ficheiro está disponível ao público, algumas configurações de rede na sua organização podem fazer com que pareça que um arquivo fica disponível ao público, quando é, na verdade, apenas visível para os utilizadores da sua rede (mesmo que ele está hospedado em Azure). Para garantir que não é o caso, teste o ficheiro com um browser externo, como um dispositivo móvel que não está ligado à rede da sua organização ou uma máquina virtual no Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Verifique as definições de origem
Depois de verificar que o arquivo está publicamente disponível na internet, verifique se as definições de origem. Na [Portal do Azure](https://portal.azure.com), navegue para o perfil de CDN e selecione o ponto final estiver a resolver problemas. De resultante **ponto final** , selecione a origem.  

![Página de ponto final com origem realçada](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

O **origem** é apresentada a página. 

![Página de origem](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Tipo de origem e o nome de anfitrião
Certifique-se de que os valores do **tipo de origem** e **nome do anfitrião** estão corretos. Neste exemplo, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, a parte de nome de anfitrião do URL é *cdndocdemo.blob.core.windows.net*, que está correto. Porque as origens de armazenamento do Azure, a aplicação Web e o serviço em nuvem utilizam um valor na lista pendente para o **nome do anfitrião** ortografias de campos, incorretas não é um problema. No entanto, se utilizar uma origem personalizada, certifique-se de que o nome de anfitrião está escrito corretamente.

#### <a name="http-and-https-ports"></a>Portas HTTP e HTTPS
Verifique sua **HTTP** e **portas HTTPS**. Na maioria dos casos, 80 e 443 estão corretos e será necessário sem alterações.  No entanto, se o servidor de origem estiver a escutar numa porta diferente, que terá de ser representado aqui. Se não tiver a certeza, ver o URL para o ficheiro de origem. As especificações HTTP e HTTPS usam as portas 80 e 443 como as predefinições. O URL de exemplo, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, uma porta não for especificada, para que o padrão de 443 pressupõe-se e as definições estão corretas.  

No entanto, suponha que o URL para o ficheiro de origem que testado anteriormente é http:\//www.contoso.com:8080/file.txt. Tenha em atenção a *: 8080* parte no final do segmento do nome de anfitrião. Que número dá instruções ao browser para utilizar a porta 8080 para ligar ao servidor web em www\.contoso.com, portanto precisará digitar *8080* no **porta HTTP** campo. É importante ter em atenção que estas definições de porta afetam apenas porta em que o ponto final utiliza para obter informações a partir da origem.

> [!NOTE]
> **CDN Standard do Azure da Akamai** os pontos finais não permitem o intervalo de portas TCP completo para origens.  Para obter uma lista das portas de origem que não são permitidas, consulte [Portas de Origem Permitidas do Azure CDN da Akamai](https://msdn.microsoft.com/library/mt757337.aspx).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Verifique as definições de ponto final
Sobre o **ponto final** página, selecione a **configurar** botão.

![Página de ponto final com o botão Configurar realçado](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Ponto final da CDN **configurar** é apresentada a página.

![Configurar página](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolos
Para **protocolos**, certifique-se de que o protocolo a ser utilizado pelos clientes está selecionado. Uma vez que o mesmo protocolo utilizado pelo cliente é utilizado para aceder a origem, é importante ter as portas de origem configuradas corretamente na secção anterior. Ponto final da CDN escuta apenas nas HTTP e HTTPS portas predefinidas (80 e 443), independentemente das portas de origem.

Vamos voltar ao nosso exemplo hipotético com http:\//www.contoso.com:8080/file.txt.  Como deve se lembrar, a Contoso especificado *8080* como o HTTP de porta, mas vamos também supor que especificadas *44300* como sua porta HTTPS.  Se eles criaram um ponto de extremidade com o nome *contoso*, o nome de anfitrião do ponto final CDN seria *contoso.azureedge.net*.  Um pedido de http:\//contoso.azureedge.net/file.txt é uma solicitação HTTP, para que o ponto final seria usar HTTP na porta 8080 para recuperá-lo a partir da origem.  Uma solicitação segura através de HTTPS, https: \/ /contoso.azureedge.net/file.txt, fará com que o ponto final utilizar HTTPS na porta 44300 ao obter o ficheiro a partir da origem.

#### <a name="origin-host-header"></a>Cabeçalho de anfitrião de origem
O **cabeçalho de anfitrião de origem** é o valor de cabeçalho de anfitrião enviado para a origem com cada solicitação.  Na maioria dos casos, isso deve ser igual a **nome do anfitrião** verificamos anteriormente.  Um valor incorreto neste campo, geralmente, não fazer com que devolvem 404 Estados, mas é a probabilidade de causar outros Estados de 4xx, consoante o que espera que a origem.

#### <a name="origin-path"></a>Caminho de origem
Por último, deve verificar nosso **caminho de origem**.  Por predefinição, está em branco.  Só deve utilizar este campo se quiser restringir o âmbito dos recursos alojados de origem que pretende disponibilizar na CDN.  

O ponto final de exemplo, Queríamos que todos os recursos da conta de armazenamento para estar disponível, então **caminho de origem** foi deixado em branco.  Isso significa que um pedido para https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt resulta numa ligação do ponto final para cdndocdemo.core.windows.net que solicite */publicblob/lorem.txt*.  Da mesma forma, um pedido para https:\//cdndocdemo.azureedge.net/donotcache/status.png resulta no pedido de ponto final */donotcache/status.png* da origem.

Mas e se não quiser utilizar a CDN para cada caminho na sua origem?  Digamos que queria apenas para expor o *publicblob* caminho.  Se inserirmos */publicblob* no **caminho de origem** campo, o que fará com que o ponto final inserir */publicblob* antes de cada solicitação a ser feita para a origem.  Isso significa que o pedido para https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt agora irá demorar, na verdade, a parte do pedido de URL, */publicblob/lorem.txt*e de acréscimo */publicblob* para o início. Isso resulta numa solicitação para */publicblob/publicblob/lorem.txt* da origem.  Se esse caminho não resolver para um arquivo real, a origem irá devolver um estado 404.  O URL correto para obter lorem.txt neste exemplo, na verdade, seria https:\//cdndocdemo.azureedge.net/lorem.txt.  Tenha em atenção que não incluímos o */publicblob* caminho em tudo, uma vez que é a parte do pedido do URL */lorem.txt* e adiciona o ponto final */publicblob*, o que resulta no */publicblob/lorem.txt* o pedido transmitido para a origem.

