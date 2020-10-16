---
title: Hospedar vários sites em Azure Application Gateway
description: Este artigo fornece uma visão geral do suporte multi-site do Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: surmb
ms.topic: conceptual
ms.openlocfilehash: 53f6f37454de886934a483b40daad24204958baf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87474330"
---
# <a name="application-gateway-multiple-site-hosting"></a>Alojamento de vários sites do Gateway de Aplicação

O alojamento de vários sites permite-lhe configurar mais do que uma aplicação web na mesma porta de entrada de uma aplicação. Permite-lhe configurar uma topologia mais eficiente para as implementações ao adicionar até 100 sites a um gateway de aplicação. Cada site pode ser direcionado para o seu próprio agrupamento de back-end. Por exemplo, três domínios, contoso.com, fabrikam.com e adatum.com, apontam para o endereço IP do gateway de aplicação. Teria de criar três serviços de escuta com vários sites e configurar cada serviço de escuta para a respetiva definição de porta e protocolo. 

Além disso, pode definir nomes de anfitrião de caráter universal num serviço de escuta com vários sites e até cinco nomes de anfitrião por serviço de escuta. Para saber mais, consulte [os nomes dos anfitriões wildcard no ouvinte.](#wildcard-host-names-in-listener-preview)

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="Gateway de aplicação multi-site":::

> [!IMPORTANT]
> As regras são processadas na ordem em que estão listadas no portal para o V1 SKU. Para o V2 SKU, os jogos exatos têm maior precedência. Antes de configurar um serviço de escuta básico, recomenda-se vivamente que configure serviços de escuta de múltiplos sites.  Desta forma, assegura-se que o tráfego é encaminhado para o back-end certo. Se for apresentado primeiro um serviço de escuta básico e este corresponde a um pedido de entrada, o pedido é processado por esse serviço de escuta.

Os pedidos de `http://contoso.com` são encaminhados para ContosoServerPool e os pedidos de `http://fabrikam.com` são encaminhados para FabrikamServerPool.

Da mesma forma, pode hospedar vários subdomínios do mesmo domínio principal na mesma implementação do gateway de aplicação. Por exemplo, pode `http://blog.contoso.com` hospedar-se e `http://app.contoso.com` numa única implementação de gateway de aplicação.

## <a name="wildcard-host-names-in-listener-preview"></a>Wildcard nomes de anfitriões no ouvinte (Pré-visualização)

O Application Gateway permite o encaminhamento baseado no anfitrião utilizando o ouvinte HTTP(S) de vários locais. Agora, você tem a capacidade de usar caracteres wildcard como asterisco (*) e ponto de interrogação (?) no nome de anfitrião, e até 5 nomes de anfitrião por ouvinte HTTP(S) multi-site. Por exemplo, `*.contoso.com`.

Utilizando um personagem wildcard no nome do anfitrião, pode combinar vários nomes de anfitriões num único ouvinte. Por exemplo, `*.contoso.com` pode combinar `ecom.contoso.com` com, assim como por `b2b.contoso.com` `customer1.b2b.contoso.com` diante. Usando uma variedade de nomes de anfitrião, você pode configurar mais do que um nome de anfitrião para um ouvinte, para encaminhar pedidos para uma piscina de backend. Por exemplo, um ouvinte pode conter `contoso.com, fabrikam.com` que aceitará pedidos para ambos os nomes dos anfitriões.

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="Gateway de aplicação multi-site":::

>[!NOTE]
> Esta funcionalidade está em pré-visualização e está disponível apenas para Standard_v2 e WAF_v2 SKU de Application Gateway. Para saber mais sobre pré-visualizações, consulte [os termos de utilização aqui.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

>[!NOTE]
>Esta funcionalidade está atualmente disponível apenas através do [Azure PowerShell](tutorial-multiple-sites-powershell.md) e [do Azure CLI.](tutorial-multiple-sites-cli.md) O apoio ao portal está para breve.
> Por favor, note que, uma vez que o suporte ao portal não está totalmente disponível, se estiver a utilizar apenas o parâmetro HostNames, o ouvinte aparecerá como um ouvinte básico no portal e a coluna de nome anfitrião da visualização da lista de ouvintes não mostrará os nomes dos anfitriões que estão configurados. Para quaisquer alterações a um ouvinte wildcard, certifique-se de que utiliza o Azure PowerShell ou o CLI até que seja suportado no portal.

Em [Azure PowerShell,](tutorial-multiple-sites-powershell.md)deve utilizar `-HostNames` em vez de `-HostName` . Com hostNames, pode mencionar até 5 nomes de anfitriões como valores separados por vírgula e usar caracteres wildcard. Por exemplo, `-HostNames "*.contoso.com,*.fabrikam.com"`

Em [Azure CLI,](tutorial-multiple-sites-cli.md)deve utilizar `--host-names` em vez de `--host-name` . Com os nomes dos anfitriões, pode mencionar até 5 nomes de anfitriões como valores separados por vírgula e usar caracteres wildcard. Por exemplo, `--host-names "*.contoso.com,*.fabrikam.com"`

### <a name="allowed-characters-in-the-host-names-field"></a>Personagens permitidos no campo dos nomes dos anfitriões:

* `(A-Z,a-z,0-9)` - caracteres alfanuméricos
* `-` - hífen ou menos
* `.` - período como delimiter
*   `*` - pode combinar com vários caracteres na gama permitida
*   `?` - pode combinar com um único personagem na gama permitida

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>Condições para a utilização de caracteres wildcard e vários nomes de anfitriões num ouvinte:

*   Só pode mencionar até 5 nomes de anfitriões num único ouvinte
*   Asterisco `*` só pode ser mencionado uma vez num componente de um nome de estilo de domínio ou nome de anfitrião. Por exemplo, componente1 *.component2.component3.* `(*.contoso-*.com)` é válido.
*   Só pode haver até dois asteriscos `*` num nome de hospedeiro. Por exemplo, `*.contoso.*` é válido e é `*.contoso.*.*.com` inválido.
*   Só pode haver um máximo de 4 caracteres wildcard num nome de anfitrião. Por `????.contoso.com` exemplo, `w??.contoso*.edu.*` são válidos, mas `????.contoso.*` inválidos.
*   A utilização de asterisco `*` e ponto de interrogação `?` juntos num componente de um nome de hospedeiro `*?` `?*` (ou ) é `**` inválida. Por exemplo, `*?.contoso.com` e `**.contoso.com` são inválidos.

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>Considerações e limitações da utilização de nomes de anfitriões ou wildcard ou vários num ouvinte:

*   [A rescisão SSL e a SSL de ponta a ponta](ssl-overview.md) requer que configurá o protocolo como HTTPS e faça o upload de um certificado a utilizar na configuração do ouvinte. Se for um ouvinte multi-site, também pode inserir o nome de anfitrião, normalmente este é o CN do certificado SSL. Quando estiver a especificar vários nomes de anfitriões no ouvinte ou utilizar caracteres wildcard, deve considerar o seguinte:
    *   Se for um nome de anfitrião wildcard como *.contoso.com, você deve carregar um certificado wildcard com CN como *.contoso.com
    *   Se vários nomes de anfitriões forem mencionados no mesmo ouvinte, deve carregar um certificado SAN (Nomes Alternativos Sujeitos) com os CNs correspondentes aos nomes dos anfitriões mencionados.
*   Não pode usar uma expressão regular para mencionar o nome do anfitrião. Só pode usar caracteres wildcard como asterisco (*) e ponto de interrogação (?) para formar o padrão de nome do anfitrião.
*   Para uma verificação de saúde de backend, não é possível associar [várias sondas personalizadas](application-gateway-probe-overview.md) por definições HTTP. Em vez disso, pode sondar um dos websites no backend ou usar "127.0.0.1" para sondar a 2ª localidade do servidor de backend. No entanto, quando estiver a utilizar um wildcard ou vários nomes de anfitriões num ouvinte, os pedidos de todos os padrões de domínio especificados serão encaminhados para o pool de backend, dependendo do tipo de regra (básico ou baseado em trajetória).
*   As propriedades "hostname" têm uma corda como entrada, onde pode mencionar apenas um nome de domínio não wildcard e "hostnames" leva uma variedade de cordas como entrada, onde você pode mencionar até 5 nomes de domínio wildcard. Mas ambas as propriedades não podem ser usadas ao mesmo tempo.
*   Não é possível criar uma regra [de redirecionamento](redirect-overview.md) com um ouvinte-alvo que usa nomes de wildcard ou vários anfitriões.

Consulte [criar vários sites utilizando o Azure PowerShell](tutorial-multiple-sites-powershell.md) ou utilizando o [Azure CLI](tutorial-multiple-sites-cli.md) para o guia passo a passo sobre como configurar os nomes dos anfitriões wildcard num ouvinte multi-site.

## <a name="host-headers-and-server-name-indication-sni"></a>Cabeçalhos de anfitrião e Indicação do Nome de Servidor (SNI)

Existem três mecanismos comuns que permitem alojar vários sites na mesma infraestrutura.

1. Aloje várias aplicações Web, cada uma num endereço IP exclusivo.
2. Utilize o nome do anfitrião para alojar várias aplicações Web no mesmo endereço IP.
3. Utilize portas diferentes para alojar várias aplicações Web no mesmo endereço IP.

Atualmente, o Application Gateway suporta um único endereço IP público onde ouve o tráfego. Assim, várias aplicações, cada uma com o seu próprio endereço IP não é atualmente suportada. 

O Application Gateway suporta múltiplas aplicações cada uma a ouvir em diferentes portas, mas este cenário requer que as aplicações aceitem o tráfego em portas não standard. Esta não é frequentemente uma configuração que você deseja.

O Gateway de Aplicação conta com os cabeçalhos de anfitrião HTTP 1.1 para alojar mais do que um site no mesmo endereço IP público e porta. Os sites alojados no gateway de aplicações também podem suportar a descarga de TLS com a extensão TLS de Indicação de Nome do Servidor (SNI). Neste cenário, o browser cliente e o web farm de back-end têm de suportar HTTP/1.1 e a extensão TLS conforme definido em RFC 6066.

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar vários locais hospedados em Application Gateway
* [Com o Portal do Azure](create-multiple-sites-portal.md)
* [Utilizar o Azure PowerShell](tutorial-multiple-sites-powershell.md) 
* [Utilizar a CLI do Azure](tutorial-multiple-sites-cli.md)

Pode visitar o [modelo do Resource Manager através do alojamento de vários sites](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) para obter uma implementação baseada num modelo ponto a ponto.
