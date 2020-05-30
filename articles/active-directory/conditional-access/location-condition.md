---
title: Condição de localização no Azure Ative Directy Acesso Condicional
description: Saiba como usar a condição de localização para controlar o acesso às suas aplicações na nuvem com base na localização da rede de um utilizador.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 05/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 781d8b89dd1b7fa6b2ed9707f6d4c485b4abdf20
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220636"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Usando a condição de localização numa política de acesso condicional 

Como explicado no [artigo geral](overview.md) as políticas de acesso condicionado são, no seu estado mais básico, uma declaração se-então combinando sinais, para tomar decisões e impor políticas de organização. Um desses sinais que podem ser incorporados no processo de tomada de decisão é a localização da rede.

![Sinal condicional conceptual mais decisão de obter a aplicação](./media/location-condition/conditional-access-signal-decision-enforcement.png)

As organizações podem usar esta localização da rede para tarefas comuns como: 

- Requerendo autenticação multi-factor para os utilizadores que acedam a um serviço quando estão fora da rede corporativa.
- Bloquear o acesso dos utilizadores que acedam a um serviço de países ou regiões específicos.

A localização da rede é determinada pelo endereço IP público que um cliente fornece ao Azure Ative Directory. As políticas de acesso condicional por padrão aplicam-se a todos os endereços IPv4 e IPv6. 

> [!TIP]
> As gamas IPV6 só são suportadas na interface **[de localização nomeada (pré-visualização).](#preview-features)** 

## <a name="named-locations"></a>Localizações com nome

As localizações são designadas no **Azure Active Directory**portal Azure sob os  >  **Security**  >  **Conditional Access**  >  **locais nomeados para**acesso condicional de segurança do Diretório Ativo Azure. Estas localizações de rede nomeadas podem incluir localizações como as gamas de rede de sede de uma organização, gamas de rede VPN ou intervalos que deseja bloquear. 

![Localizações nomeadas no portal Azure](./media/location-condition/new-named-location.png)

Para configurar uma localização, terá de fornecer pelo menos um **Nome** e o intervalo IP. 

O número de locais nomeados que pode configurar está limitado pelo tamanho do objeto relacionado em Azure AD. Pode configurar localizações com base nas seguintes limitações:

- Um local com até 1200 gamas IPv4.
- Um máximo de 90 locais nomeados com uma gama de IP atribuída a cada um deles.

> [!TIP]
> As gamas IPV6 só são suportadas na interface **[de localização nomeada (pré-visualização).](#preview-features)** 

### <a name="trusted-locations"></a>Locais fidedignos

Ao criar uma localização de rede, um administrador tem a opção de marcar uma localização como uma localização fidedigna. 

![Localizações confiáveis no portal Azure](./media/location-condition/new-trusted-location.png)

Esta opção pode ter em conta as políticas de Acesso Condicional onde pode, por exemplo, exigir o registo para autenticação de vários fatores a partir de uma localização de rede fidedigna. Também se inseja no cálculo de risco da Azure AD Identity Protection, reduzindo o risco de entrada de um utilizadores quando vem de um local marcado como confiável.

### <a name="countries-and-regions"></a>Países e regiões

Algumas organizações podem optar por definir limites IP de países inteiros ou regiões como locais nomeados para políticas de acesso condicional. Podem utilizar estes locais quando bloqueiam o tráfego desnecessário quando sabem que utilizadores válidos nunca virão de um local como a Coreia do Norte. Estes mapeamentos do endereço IP para o país são atualizados periodicamente. 

> [!NOTE]
> Os países não incluem intervalos de endereços IPv6, apenas gamas de endereços IPv4 conhecidas.

![Criar um novo país ou localização baseada na região no portal Azure](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Incluir áreas desconhecidas

Alguns endereços IP não são mapeados para um país ou região específico. Para capturar estas localizações IP, verifique a caixa **Inclua áreas desconhecidas** ao definir uma localização. Esta opção permite-lhe escolher se estes endereços IP devem ser incluídos no local nomeado. Utilize esta definição quando a política que utiliza a localização nomeada se aplicar a locais desconhecidos.

### <a name="configure-mfa-trusted-ips"></a>Configure IPs fidedignos da MFA

Também pode configurar intervalos de endereços IP que representam a intranet local da sua organização nas [definições de serviço de autenticação multi-factor](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Esta funcionalidade permite-lhe configurar até 50 intervalos de endereços IP. Os intervalos de endereços IP estão no formato CIDR. Para obter mais informações, consulte [IPs fidedignos.](../authentication/howto-mfa-mfasettings.md#trusted-ips)  

Se tiver IPs fidedignos configurados, eles aparecem como **IPS Fidedignos MFA** na lista de locais para a condição de localização.

### <a name="skipping-multi-factor-authentication"></a>Saltar a autenticação de vários fatores

Na página de definições de serviço de autenticação multi-factor, pode identificar utilizadores de intranet corporativa selecionando **a autenticação de vários fatores Skip para pedidos de utilizadores federados na minha intranet**. Esta definição indica que a alegação da rede corporativa interna, emitida pela AD FS, deve ser confiável e usada para identificar o utilizador como estando na rede corporativa. Para obter mais informações, consulte [ativar a funcionalidade IPs fidedigna utilizando o Acesso Condicional](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Após a verificação desta opção, incluindo a localização nomeada **MFA Trusted IPS** aplicar-se-á a quaisquer políticas com esta opção selecionada.

Para aplicações móveis e desktop, que têm longas vidas de sessão, o Acesso Condicional é periodicamente reavaliado. O padrão é uma vez por hora. Quando a reclamação da rede corporativa interna só é emitida no momento da autenticação inicial, a Azure AD pode não ter uma lista de gamas IP fidedignas. Neste caso, é mais difícil determinar se o utilizador ainda está na rede corporativa:

1. Verifique se o endereço IP do utilizador está numa das gamas IP fidedignas.
1. Verifique se os três primeiros octetos do endereço IP do utilizador correspondem aos três primeiros octetos do endereço IP da autenticação inicial. O endereço IP é comparado com a autenticação inicial quando a reclamação da rede corporativa interna foi originalmente emitida e a localização do utilizador foi validada.

Se ambos os passos falharem, considera-se que um utilizador já não se encontra num IP de confiança.

## <a name="preview-features"></a>Funcionalidades de pré-visualização

Além da funcionalidade de localização geralmente disponível, existe também uma localização nomeada (pré-visualização). Pode aceder à pré-visualização da localização com o nome, utilizando o banner no topo da lâmina de localização atual.

![Experimente a pré-visualização dos locais nomeados](./media/location-condition/preview-features.png)

Com a pré-visualização de localização nomeada, você é capaz de

- Configurar até 195 locais nomeados
- Configurar até 2000 gamas IP por localização nomeada
- Configurar até endereços IPv6

Também adicionámos alguns controlos adicionais para ajudar a reduzir a mudança de configuração errada.

- As gamas IP privadas já não podem ser configuradas
- O número de endereços IP que podem ser incluídos numa gama é limitado. Apenas máscaras CIDR superiores a /8 serão permitidas ao configurar um intervalo de IP.

Com a pré-visualização, existem agora duas opções de criação: 

- **Localização dos países**
- **Localização das gamas IP**

> [!NOTE]
> Os países não incluem intervalos de endereços IPv6, apenas gamas de endereços IPv4 conhecidas.

![Interface de pré-visualização de localizações nomeadas](./media/location-condition/named-location-preview.png)

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

Quando um proxy de nuvem está no lugar, uma política que é usada para exigir um dispositivo de união de domínio pode ser usada, ou a reivindicação de corpnet interior de AD FS.

### <a name="api-support-and-powershell"></a>Suporte API e PowerShell

A API e a PowerShell ainda não estão suportadas para locais nomeados.

## <a name="next-steps"></a>Passos seguintes

- Se quiser saber como configurar uma política de acesso condicional, consulte o artigo [Construir uma política de acesso condicional](concept-conditional-access-policies.md).
- Procura uma política de exemplo usando a condição de localização? Ver o artigo, [Acesso Condicional: Bloquear acesso por local](howto-conditional-access-policy-location.md)
