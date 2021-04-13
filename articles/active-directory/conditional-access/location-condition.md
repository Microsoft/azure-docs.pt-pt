---
title: Condição de localização no Azure Ative Directy Acesso Condicional
description: Saiba como usar a condição de localização para controlar o acesso às suas aplicações na nuvem com base na localização da rede de um utilizador.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 07af586bac71ee9b33ef314756454cb3c52ec912
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305927"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Usando a condição de localização numa política de acesso condicional 

Como explicado no [artigo geral](overview.md) as políticas de acesso condicionado são, no seu estado mais básico, uma declaração se-então combinando sinais, para tomar decisões e impor políticas de organização. Um desses sinais que podem ser incorporados no processo de tomada de decisão é a localização da rede.

![Sinal condicional conceptual mais decisão de obter a aplicação](./media/location-condition/conditional-access-signal-decision-enforcement.png)

As organizações podem usar esta localização da rede para tarefas comuns como: 

- Requerendo autenticação multi-factor para os utilizadores que acedam a um serviço quando estão fora da rede corporativa.
- Bloquear o acesso dos utilizadores que acedam a um serviço de países ou regiões específicos.

A localização da rede é determinada pelo endereço IP público que um cliente fornece ao Azure Ative Directory. As políticas de acesso condicional por padrão aplicam-se a todos os endereços IPv4 e IPv6. 

## <a name="named-locations"></a>Localizações com nome

As localizações são designadas no portal Azure sob os  >    >    >  **locais nomeados para** acesso condicional de segurança do Diretório Ativo Azure. Estas localizações de rede nomeadas podem incluir localizações como as gamas de rede de sede de uma organização, gamas de rede VPN ou intervalos que deseja bloquear. As localizações nomeadas podem ser definidas por intervalos de endereços IPv4/IPv6 ou por países/regiões. 

![Localizações nomeadas no portal Azure](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>Intervalos de endereços IP

Para definir uma localização nomeada pelos intervalos de endereços IPv4/IPv6, terá de fornecer um **Nome** e uma gama DE IP. 

As localizações nomeadas definidas pelas gamas de endereços IPv4/IPv6 estão sujeitas às seguintes limitações: 
- Configurar até 195 locais nomeados
- Configurar até 2000 gamas IP por localização nomeada
- As gamas IPv4 e IPv6 são suportadas
- Gamas ip privadas conotações são configuradas
- O número de endereços IP contidos num intervalo é limitado. Apenas máscaras CIDR superiores a /8 são permitidas ao definir uma gama de IP. 

### <a name="trusted-locations"></a>Locais fidedignos

Os administradores podem designar localizações nomeadas definidas por intervalos de endereços IP para serem confiáveis locais nomeados. 

![Localizações confiáveis no portal Azure](./media/location-condition/new-trusted-location.png)

Os acessos de locais com nome fidedignos melhoram a precisão do cálculo de risco da Azure AD Identity Protection, reduzindo o risco de entrada de um utilizador quando autenticam a partir de um local marcado como confiável. Além disso, locais com nomes fidedignos podem ser direcionados para políticas de Acesso Condicional. Por exemplo, pode exigir restringir o registo de autenticação de vários fatores apenas para locais com nome confiável. 

### <a name="countries-and-regions"></a>Países e regiões

Algumas organizações podem optar por restringir o acesso a determinados países ou regiões que utilizam o Acesso Condicional. Além de definir localizações nomeadas por gamas IP, os administradores podem definir locais nomeados por país ou regiões. Quando um utilizador assina, o Azure AD resolve o endereço IPv4 do utilizador para um país ou região, e o mapeamento é atualizado periodicamente. As organizações podem usar locais nomeados definidos por países para bloquear o tráfego de países onde não fazem negócios, como a Coreia do Norte. 

> [!NOTE]
> As inscrições dos endereços IPv6 não podem ser mapeadas para países ou regiões e são consideradas áreas desconhecidas. Apenas os endereços IPv4 podem ser mapeados para países ou regiões.

![Criar um novo país ou localização baseada na região no portal Azure](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Incluir áreas desconhecidas

Alguns endereços IP não estão mapeados para um país ou região específico, incluindo todos os endereços IPv6. Para capturar estas localizações IP, verifique a caixa **Inclua áreas desconhecidas** ao definir uma localização. Esta opção permite-lhe escolher se estes endereços IP devem ser incluídos no local nomeado. Utilize esta definição quando a política que utiliza a localização nomeada se aplicar a locais desconhecidos.

### <a name="configure-mfa-trusted-ips"></a>Configure IPs fidedignos da MFA

Também pode configurar intervalos de endereços IP que representam a intranet local da sua organização nas [definições de serviço de autenticação multi-factor](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Esta funcionalidade permite-lhe configurar até 50 intervalos de endereços IP. Os intervalos de endereços IP estão no formato CIDR. Para obter mais informações, consulte [IPs fidedignos.](../authentication/howto-mfa-mfasettings.md#trusted-ips)  

Se tiver IPs fidedignos configurados, eles aparecem como **IPS Fidedignos MFA** na lista de locais para a condição de localização.

### <a name="skipping-multi-factor-authentication"></a>Saltar a autenticação de vários fatores

Na página de definições de serviço de autenticação multi-factor, pode identificar utilizadores de intranet corporativa selecionando  **a autenticação de vários fatores Skip para pedidos de utilizadores federados na minha intranet**. Esta definição indica que a alegação da rede corporativa interna, emitida pela AD FS, deve ser confiável e usada para identificar o utilizador como estando na rede corporativa. Para obter mais informações, consulte [ativar a funcionalidade IPs fidedigna utilizando o Acesso Condicional](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Após a verificação desta opção, incluindo a localização nomeada **MFA Trusted IPS** aplicar-se-á a quaisquer políticas com esta opção selecionada.

Para aplicações móveis e desktop, que têm longas vidas de sessão, o Acesso Condicional é periodicamente reavaliado. O padrão é uma vez por hora. Quando a reclamação da rede corporativa interna só é emitida no momento da autenticação inicial, a Azure AD pode não ter uma lista de gamas IP fidedignas. Neste caso, é mais difícil determinar se o utilizador ainda está na rede corporativa:

1. Verifique se o endereço IP do utilizador está numa das gamas IP fidedignas.
1. Verifique se os três primeiros octetos do endereço IP do utilizador correspondem aos três primeiros octetos do endereço IP da autenticação inicial. O endereço IP é comparado com a autenticação inicial quando a reclamação da rede corporativa interna foi originalmente emitida e a localização do utilizador foi validada.

Se ambos os passos falharem, considera-se que um utilizador já não se encontra num IP de confiança.

## <a name="location-condition-in-policy"></a>Condição de localização na política

Ao configurar a condição de localização, tem a opção de distinguir entre:

- Qualquer localização
- Todos os locais de confiança
- Locais selecionados

### <a name="any-location"></a>Qualquer localização

Por predefinição, a seleção de **Qualquer localização** faz com que uma política seja aplicada a todos os endereços IP, o que significa qualquer endereço na Internet. Esta definição não se limita a endereços IP configurados como localização nomeada. Quando seleciona **Qualquer local,** ainda pode excluir localizações específicas de uma apólice. Por exemplo, pode aplicar uma política a todos os locais, exceto locais fidedignos, para definir o âmbito de aplicação em todos os locais, exceto a rede corporativa.

### <a name="all-trusted-locations"></a>Todos os locais de confiança

Esta opção aplica-se a:

- Todos os locais que foram marcados como localização confiável
- **IPS Fidedignos MFA** (se configurado)

### <a name="selected-locations"></a>Locais selecionados

Com esta opção, pode selecionar um ou mais locais nomeados. Para que uma política com esta definição se aplique, um utilizador precisa de se conectar a partir de qualquer um dos locais selecionados. Quando clicar Em **Selecionar** o controlo de seleção de rede nomeado que mostra a lista de redes nomeadas abre.' A lista também mostra se a localização da rede foi marcada como confiável. A localização nomeada chamada **MFA Trusted IPs** é usada para incluir as definições de IP que podem ser configuradas na página de definição de serviço de autenticação multi-factor.

## <a name="ipv6-traffic"></a>Tráfego IPv6

Por predefinição, as políticas de Acesso Condicional aplicar-se-ão a todo o tráfego IPv6. Pode excluir gamas específicas de endereços IPv6 de uma política de Acesso Condicional se não quiser que as políticas sejam aplicadas para gamas específicas de IPv6. Por exemplo, se não pretender impor uma política de utilização na sua rede corporativa, e a sua rede corporativa está hospedada em gamas públicas de IPv6.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>Quando é que o meu inquilino vai ter tráfego IPv6?

O Azure Ative Directory (Azure AD) não suporta atualmente ligações diretas de rede que utilizam o IPv6. No entanto, existem alguns casos em que o tráfego de autenticação é proxiitado através de outro serviço. Nestes casos, o endereço IPv6 será utilizado durante a avaliação política.

A maior parte do tráfego IPv6 que é proxiitado ao Azure AD vem do Microsoft Exchange Online. Quando disponível, o Exchange preferirá as ligações IPv6. **Portanto, se tiver alguma política de Acesso Condicional para Troca, que tenha sido configurada para gamas específicas do IPv4, deverá certificar-se de que também adicionou as gamas IPv6 das suas organizações.** Sem incluir as gamas IPv6 causará um comportamento inesperado para os dois casos seguintes:

- Quando um cliente de correio é utilizado para ligar ao Exchange Online com a autenticação antiga, a Azure AD pode receber um endereço IPv6. O pedido inicial de autenticação vai para a Exchange e é então proxiitado ao Azure AD.
- Quando o Outlook Web Access (OWA) for utilizado no navegador, verificará periodicamente todas as políticas de Acesso Condicional que continuam a ser satisfeitas. Esta verificação é usada para capturar casos em que um utilizador pode ter mudado de um endereço IP permitido para um novo local, como o café ao fundo da rua. Neste caso, se for utilizado um endereço IPv6 e se o endereço IPv6 não estiver num intervalo configurado, o utilizador poderá ter a sua sessão interrompida e ser encaminhado para Azure AD para reauttenticar. 

Estas são as razões mais comuns para configurar as gamas IPv6 nas suas localizações nomeadas. Além disso, se estiver a utilizar VNets Azure, terá tráfego proveniente de um endereço IPv6. Se tiver o tráfego VNet bloqueado por uma política de Acesso Condicional, verifique o seu registo de registo de ad Azure. Uma vez identificado o tráfego, pode obter o endereço IPv6 sendo usado e excluí-lo da sua política. 

> [!NOTE]
> Se pretender especificar um intervalo IP CIDR para um único endereço, aplique a máscara /128 bit. Se você disser o endereço IPv6 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a e queria excluir esse único endereço como um intervalo, você usaria 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a/128.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Identificação do tráfego IPv6 nos relatórios de atividades de inscrição da Azure AD

Você pode descobrir o tráfego IPv6 no seu inquilino, indo os [relatórios de atividades de inscrição da AZure AD](../reports-monitoring/concept-sign-ins.md). Depois de ter o relatório de atividade aberto, adicione a coluna "endereço IP". Esta coluna vai dar-lhe para identificar o tráfego IPv6.

Também pode encontrar o IP do cliente clicando numa linha no relatório e, em seguida, indo para o separador "Localização" nos detalhes da atividade de login. 

## <a name="what-you-should-know"></a>O que deve saber

### <a name="when-is-a-location-evaluated"></a>Quando é avaliado um local?

As políticas de acesso condicional são avaliadas quando:

- Um utilizador inicialmente assina uma aplicação web, aplicação móvel ou desktop.
- Uma aplicação móvel ou desktop que utiliza a autenticação moderna, usa um token de atualização para adquirir um novo token de acesso. Por predefinição, esta verificação é uma vez por hora.

Esta verificação significa que para aplicações móveis e desktop usando a autenticação moderna, uma alteração na localização seria detetada dentro de uma hora após a alteração da localização da rede. Para aplicações móveis e desktop que não utilizem a autenticação moderna, a política é aplicada em cada pedido simbólico. A frequência do pedido pode variar em função da aplicação. Da mesma forma, para aplicações web, a política é aplicada no início da sessão e é boa para o tempo de vida da sessão na aplicação web. Devido às diferenças de vida de sessão entre aplicações, o tempo entre a avaliação das políticas também variará. Cada vez que o pedido solicita um novo sinal de inscrição, a apólice é aplicada.

Por padrão, a Azure AD emite um símbolo de hora a hora. Depois de sair da rede corporativa, dentro de uma hora a política é aplicada para aplicações usando a autenticação moderna.

### <a name="user-ip-address"></a>Endereço IP do utilizador

O endereço IP utilizado na avaliação de políticas é o endereço IP público do utilizador. Para dispositivos numa rede privada, este endereço IP não é o IP do cliente do dispositivo do utilizador na intranet, é o endereço utilizado pela rede para ligar à internet pública.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Carregamento e descarregamento em massa de locais nomeados

Quando criar ou atualizar localizações nomeadas, para atualizações a granel, pode carregar ou descarregar um ficheiro CSV com as gamas IP. Um upload substitui as gamas IP da lista por essas variações a partir do ficheiro. Cada linha do ficheiro contém uma gama de endereços IP no formato CIDR.

### <a name="cloud-proxies-and-vpns"></a>Proxies de nuvem e VPNs

Quando utiliza uma solução de procuração ou VPN hospedada na nuvem, o endereço IP AZure AD utiliza enquanto avalia uma política o endereço IP do representante. O cabeçalho X-Forwarded-For (XFF) que contém o endereço IP público do utilizador não é utilizado porque não existe validação de que provém de uma fonte fidedigna, pelo que apresentaria um método para falsificar um endereço IP.

Quando um proxy de nuvem está no lugar, uma política que é usada para exigir um dispositivo híbrido Azure AD pode ser usado, ou a reivindicação de corpnet interno de AD FS.

### <a name="api-support-and-powershell"></a>Suporte API e PowerShell

Está disponível uma versão de pré-visualização da API do gráfico para localizações nomeadas, para obter mais informações consulte a [API delocalização](/graph/api/resources/namedlocation?view=graph-rest-beta).

> [!NOTE]
> Locais nomeados que cria usando o ecrã PowerShell apenas em locais nomeados (pré-visualização). Não se pode ver locais nomeados na vista antiga.  

## <a name="next-steps"></a>Passos seguintes

- Se quiser saber como configurar uma política de acesso condicional, consulte o artigo [Construir uma política de acesso condicional](concept-conditional-access-policies.md).
- Procura uma política de exemplo usando a condição de localização? Ver o artigo, [Acesso Condicional: Bloquear acesso por local](howto-conditional-access-policy-location.md)
