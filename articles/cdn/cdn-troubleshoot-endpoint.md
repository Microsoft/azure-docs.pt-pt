---
title: Solucionando problemas de pontos de extremidade da CDN do Azure-código de status 404
description: Solucione problemas 404 de códigos de resposta com pontos de extremidade da CDN do Azure.
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
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083122"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Solucionando problemas de pontos de extremidade da CDN do Azure que retornam um código de status 404
Este artigo permite solucionar problemas com pontos de extremidade da CDN (rede de distribuição de conteúdo) do Azure que retornam códigos de status de resposta HTTP 404.

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure no [msdn do Azure e nos fóruns de Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

## <a name="symptom"></a>Sintoma
Você criou um perfil CDN e um ponto de extremidade, mas seu conteúdo parece não estar disponível na CDN. Os usuários que tentarem acessar seu conteúdo por meio da URL da CDN receberão um código de status HTTP 404. 

## <a name="cause"></a>Causa
Há várias causas possíveis, incluindo:

* A origem do arquivo não é visível para a CDN.
* O ponto de extremidade está configurado incorretamente, fazendo com que a CDN examine o local errado.
* O host está rejeitando o cabeçalho de host da CDN.
* O ponto de extremidade não tinha tempo para se propagar em toda a CDN.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
> [!IMPORTANT]
> Depois de criar um ponto de extremidade CDN, ele não estará imediatamente disponível para uso, pois leva tempo para que o registro se propague através da CDN:
> - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em dez minutos. 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída no prazo de 90 minutos. 
> 
> Se você concluir as etapas neste documento e ainda estiver recebendo respostas de 404, considere aguardar algumas horas para verificar novamente antes de abrir um tíquete de suporte.
> 
> 

### <a name="check-the-origin-file"></a>Verificar o arquivo de origem
Primeiro, verifique se o arquivo para o cache está disponível no servidor de origem e se está publicamente acessível na Internet. A maneira mais rápida de fazer isso é abrir um navegador em uma sessão particular ou Incognito e navegar diretamente até o arquivo. Digite ou cole a URL na caixa de endereço e verifique se ela resulta no arquivo esperado. Por exemplo, suponha que você tenha um arquivo em uma conta de armazenamento do Azure, acessível em https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Se você puder carregar o conteúdo desse arquivo com êxito, ele passará o teste.

![Êxito!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Embora essa seja a maneira mais rápida e fácil de verificar se o arquivo está disponível publicamente, algumas configurações de rede em sua organização podem fazer parecer que um arquivo está publicamente disponível quando é, de fato, visível apenas para os usuários da sua rede (mesmo se ele estiver hospedado em Azure). Para garantir que esse não seja o caso, teste o arquivo com um navegador externo, como um dispositivo móvel que não esteja conectado à rede da sua organização ou uma máquina virtual no Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Verificar as configurações de origem
Depois de verificar se o arquivo está publicamente disponível na Internet, verifique suas configurações de origem. No [portal do Azure](https://portal.azure.com), navegue até o seu perfil CDN e selecione o ponto de extremidade que você está solucionando. Na página **ponto de extremidade** resultante, selecione a origem.  

![Página de ponto de extremidade com origem realçada](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

A página **origem** é exibida. 

![Página de origem](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Tipo de origem e nome do host
Verifique se os valores do **tipo de origem** e o **nome do host de origem** estão corretos. Neste exemplo, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, a parte hostname da URL é *cdndocdemo.blob.Core.Windows.net*, que está correta. Como as origens do armazenamento do Azure, do aplicativo Web e do serviço de nuvem usam um valor de lista suspensa para o campo **nome do host de origem** , as grafias incorretas não são um problema. No entanto, se você usar uma origem personalizada, verifique se o nome do host está escrito corretamente.

#### <a name="http-and-https-ports"></a>Portas HTTP e HTTPS
Verifique as portas **http** e **https**. Na maioria dos casos, 80 e 443 estão corretos e você não precisará de nenhuma alteração.  No entanto, se o servidor de origem estiver escutando em uma porta diferente, isso precisará ser representado aqui. Se você não tiver certeza, exiba a URL do arquivo de origem. As especificações de HTTP e HTTPS usam as portas 80 e 443 como padrões. Na URL de exemplo, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, uma porta não é especificada, portanto, o padrão de 443 é assumido e as configurações estão corretas.  

No entanto, suponha que a URL do arquivo de origem testado anteriormente seja http:\//www.contoso.com:8080/file.txt. Observe a parte *: 8080* no final do segmento do nome do host. Esse número instrui o navegador a usar a porta 8080 para se conectar ao servidor Web em www\.contoso.com, portanto, você precisará inserir *8080* no campo **porta http** . É importante observar que essas configurações de porta afetam apenas a porta que o ponto de extremidade usa para recuperar informações da origem.

> [!NOTE]
> Os pontos **de extremidade da CDN standard do Azure da Akamai** não permitem o intervalo de portas TCP completo para origens.  Para obter uma lista das portas de origem que não são permitidas, consulte [Portas de Origem Permitidas do Azure CDN da Akamai](/previous-versions/azure/mt757337(v=azure.100)).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Verificar as configurações do ponto de extremidade
Na página **ponto de extremidade** , selecione o botão **Configurar** .

![Página de ponto de extremidade com o botão configurar realçado](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

A página **Configurar** ponto de extremidade CDN é exibida.

![Configurar página](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolos
Para **protocolos**, verifique se o protocolo que está sendo usado pelos clientes está selecionado. Como o mesmo protocolo usado pelo cliente é aquele usado para acessar a origem, é importante ter as portas de origem configuradas corretamente na seção anterior. O ponto de extremidade da CDN Escuta apenas nas portas HTTP e HTTPS padrão (80 e 443), independentemente das portas de origem.

Vamos retornar ao nosso exemplo hipotético com http:\//www.contoso.com:8080/file.txt.  Como você se lembrará, a contoso especificou *8080* como sua porta http, mas vamos supor também que especificou *44300* como sua porta HTTPS.  Se eles criaram um ponto de extremidade chamado *contoso*, o nome de host do ponto de extremidade da CDN seria *contoso.azureedge.net*.  Uma solicitação de http:\//contoso.azureedge.net/file.txt é uma solicitação HTTP, portanto, o ponto de extremidade usaria HTTP na porta 8080 para recuperá-la da origem.  Uma solicitação segura por HTTPS, https:\//contoso.azureedge.net/file.txt, fará com que o ponto de extremidade use HTTPS na porta 44300 ao recuperar o arquivo da origem.

#### <a name="origin-host-header"></a>Cabeçalho de host de origem
O **cabeçalho de host de origem** é o valor de cabeçalho de host enviado para a origem com cada solicitação.  Na maioria dos casos, isso deve ser o mesmo que o **nome de host de origem** que verificamos anteriormente.  Um valor incorreto nesse campo geralmente não causará status de 404, mas provavelmente causará outros status de 4xx, dependendo do que a origem espera.

#### <a name="origin-path"></a>Caminho de origem
Por fim, devemos verificar nosso **caminho de origem**.  Por padrão, está em branco.  Você só deve usar esse campo se quiser restringir o escopo dos recursos hospedados na origem que deseja disponibilizar na CDN.  

No ponto de extremidade de exemplo, queremos que todos os recursos na conta de armazenamento estejam disponíveis, portanto, o **caminho de origem** foi deixado em branco.  Isso significa que uma solicitação para https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt resulta em uma conexão do ponto de extremidade para cdndocdemo.core.windows.net que solicita */publicblob/Lorem.txt*.  Da mesma forma, uma solicitação para https:\//cdndocdemo.azureedge.net/donotcache/status.png resulta no ponto de extremidade que solicita */donotcache/status.png* da origem.

Mas e se você não quiser usar a CDN para cada caminho em sua origem?  Digamos que você queria apenas expor o caminho *publicblob* .  Se inserirmos */publicblob* no campo **caminho de origem** , isso fará com que o ponto de extremidade insira */publicblob* antes de cada solicitação ser feita à origem.  Isso significa que a solicitação de https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt agora pegará a parte de solicitação da URL, */publicblob/Lorem.txt*e acrescentará */publicblob* ao início. Isso resulta em uma solicitação de */publicblob/publicblob/Lorem.txt* da origem.  Se esse caminho não for resolvido para um arquivo real, a origem retornará um status 404.  A URL correta para recuperar o Lorem. txt neste exemplo seria, na verdade, https:\//cdndocdemo.azureedge.net/lorem.txt.  Observe que não incluímos o caminho */publicblob* , porque a parte de solicitação da URL é */Lorem.txt* e o ponto de extremidade adiciona */publicblob*, resultando em */publicblob/Lorem.txt* sendo a solicitação passada para a origem.

