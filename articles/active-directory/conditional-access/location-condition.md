---
title: Condição de localização no Acesso Condicional do Diretório Ativo Azure
description: Saiba como usar a condição de localização para controlar o acesso às suas aplicações na nuvem com base na localização da rede de um utilizador.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263234"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Qual é a condição de localização no Acesso Condicional do Diretório Ativo Azure? 

Com o Acesso Condicional do [Diretório Ativo Azure (Azure AD),](../active-directory-conditional-access-azure-portal.md)pode controlar como os utilizadores autorizados podem aceder às suas aplicações na nuvem. O estado de localização de uma política de Acesso Condicional permite-lhe ligar as definições de controlos de acesso às localizações da rede dos seus utilizadores.

Este artigo fornece-lhe a informação de que precisa para configurar a condição de localização.

## <a name="locations"></a>Localizações

A Azure AD permite um único acesso a dispositivos, aplicações e serviços de qualquer parte da internet pública. Com a condição de localização, pode controlar o acesso às suas aplicações na nuvem com base na localização da rede de um utilizador. Os casos de utilização comuns para a condição de localização são:

- Exigindo a autenticação de vários fatores para os utilizadores que acedam a um serviço quando estão fora da rede corporativa.
- Bloquear o acesso aos utilizadores que acedam a um serviço de países ou regiões específicos.

Uma localização é uma etiqueta para uma localização de rede que representa uma localização nomeada ou iPs fidedignos de autenticação multifactor.

## <a name="named-locations"></a>Locais nomeados

Com localizações nomeadas, pode criar agrupamentos lógicos de gamas de endereços IP ou países e regiões.

Pode aceder aos seus locais nomeados na secção **Gerir** a página de Acesso Condicional.

![Locais nomeados em Acesso Condicional](./media/location-condition/02.png)

Um local nomeado tem os seguintes componentes:

![Criar uma nova localização nomeada](./media/location-condition/42.png)

- **Nome** - O nome de exibição de um local nomeado.
- **Intervalos IP** - Uma ou mais gamas de endereços IPv4 em formato CIDR. A especificação de uma gama de endereços IPv6 não é suportada.

   > [!NOTE]
   > As gamas de endereços IPv6 não podem ser incluídas num local nomeado. Isto significa que as gamas IPv6 não podem ser excluídas de uma política de acesso condicional.

- **Marque como local de confiança** - Uma bandeira que pode definir para um local nomeado para indicar uma localização confiável. Normalmente, as localizações fidedignas são áreas de rede que são controladas pelo seu departamento de TI. Além do Acesso Condicional, as localizações com nomes confiáveis também são usadas pela Azure Identity Protection e pelos relatórios de segurança da Azure AD para reduzir [falsos positivos.](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1)
- **Países/Regiões** - Esta opção permite-lhe selecionar um ou mais países ou regiões para definir um local nomeado.
- **Incluir áreas desconhecidas** - Alguns endereços IP não estão mapeados para um país ou região específicos. Esta opção permite-lhe escolher se estes endereços IP devem ser incluídos no local nomeado. Utilize esta definição quando a política que utiliza a localização nomeada deve aplicar-se a locais desconhecidos.

O número de locais nomeados que pode configurar está limitado pelo tamanho do objeto relacionado em Azure AD. Pode configurar localizações com base nas seguintes limitações:

- Um local nomeado com até 1200 gamas IP.
- Um máximo de 90 locais nomeados com uma gama IP atribuída a cada um deles.

A política de acesso condicional aplica-se ao tráfego IPv4 e IPv6. Atualmente, as localizações nomeadas não permitem configurar as gamas IPv6. Esta limitação provoca as seguintes situações:

- A política de acesso condicional não pode ser direcionada a gamas específicas de IPv6
- A política de acesso condicional não pode excluir gamas específicas do IPV6

Se uma política estiver configurada para se aplicar a "Qualquer localização", aplicar-se-á ao tráfego IPv4 e IPv6. Locais nomeados configurados para países e regiões especificados apenas suportam endereços IPv4. O tráfego iPv6 só está incluído se a opção de "incluir áreas desconhecidas" selecionada.

## <a name="trusted-ips"></a>IPs Fidedignos

Também pode configurar gamas de endereços IP que representam a intranet local da sua organização nas [definições do serviço de autenticação de vários fatores.](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx) Esta funcionalidade permite configurar até 50 intervalos de endereços IP. As gamas de endereços IP estão no formato CIDR. Para mais informações, consulte [IPs fidedignos](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Se tiver IPs fidedignos configurados, eles aparecem como **IPS fidedignos** do MFA na lista de locais para a condição de localização.

### <a name="skipping-multi-factor-authentication"></a>Saltar a autenticação de vários fatores

Na página de definições de serviço de autenticação de vários fatores, pode identificar utilizadores de intranet corporativos selecionando a **autenticação de vários fatores Skip para pedidos de utilizadores federados na minha intranet**. Esta definição indica que a alegação de rede corporativa interna, emitida pela AD FS, deve ser confiável e usada para identificar o utilizador como estando na rede corporativa. Para mais informações, consulte [a funcionalidade IPs fidedigno utilizando acesso condicional](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Depois de verificar esta opção, incluindo a localização nomeada, o **MFA Trusted IPS** aplicar-se-á a quaisquer políticas com esta opção selecionadas.

Para aplicações móveis e de desktop, que têm vida longa, o Acesso Condicional é periodicamente reavaliado. O padrão é uma vez por hora. Quando a reclamação de rede corporativa interna só for emitida no momento da autenticação inicial, a Azure AD pode não ter uma lista de gamas IP fidedignas. Neste caso, é mais difícil determinar se o utilizador ainda está na rede corporativa:

1. Verifique se o endereço IP do utilizador está numa das gamas IP confiáveis.
2. Verifique se os três primeiros octetos do endereço IP do utilizador correspondem aos três primeiros octetos do endereço IP da autenticação inicial. O endereço IP é comparado com a autenticação inicial quando a reclamação da rede corporativa interna foi originalmente emitida e a localização do utilizador foi validada.

Se ambos os passos falharem, considera-se que um utilizador já não se encontra num IP fidedigno.

## <a name="location-condition-configuration"></a>Configuração da condição de localização

Quando configurar a condição de localização, tem a opção de distinguir entre:

- Qualquer local
- Todos os locais confiáveis
- Locais selecionados

![Configuração da condição de localização](./media/location-condition/01.png)

### <a name="any-location"></a>Qualquer local

Por predefinição, a seleção **de qualquer local** faz com que uma política seja aplicada a todos os endereços IP, o que significa qualquer endereço na Internet. Esta definição não se limita aos endereços IP que configuracomo localização nomeada. Ao selecionar **Qualquer local,** ainda pode excluir localizações específicas de uma política. Por exemplo, pode aplicar uma política em todos os locais, exceto locais fidedignos para definir o âmbito para todos os locais, exceto a rede corporativa.

### <a name="all-trusted-locations"></a>Todos os locais confiáveis

Esta opção aplica-se a:

- Todos os locais que foram marcados como localização confiável
- **IPS fidedignos** do MFA (se configurado)

### <a name="selected-locations"></a>Locais selecionados

Com esta opção, pode selecionar um ou mais locais nomeados. Para uma política com esta definição a aplicar, o utilizador precisa de se conectar a partir de qualquer um dos locais selecionados. Quando **clicar, selecione** o controlo de seleção de rede nomeado que mostra a lista de redes nomeadas abertas. A lista também mostra se a localização da rede foi marcada como confiável. A localização denominada **MFA Trusted IPs** é usada para incluir as definições IP que podem ser configuradas na página de definição do serviço de autenticação de vários fatores.

## <a name="what-you-should-know"></a>O que deve saber

### <a name="when-is-a-location-evaluated"></a>Quando é avaliado um local?

As políticas de Acesso Condicional são avaliadas quando:

- Um utilizador inicialmente inicia sintetizar uma aplicação web, móvel ou desktop.
- Uma aplicação móvel ou de ambiente de trabalho que utiliza a autenticação moderna, usa um token refrescante para adquirir um novo token de acesso. Por defeito, este cheque é uma vez por hora.

Este controlo significa aplicações móveis e de ambiente de trabalho utilizando a autenticação moderna, uma alteração de localização seria detetada no prazo de uma hora após a alteração da localização da rede. Para aplicações móveis e de desktop que não utilizem a autenticação moderna, a política é aplicada em cada pedido simbólico. A frequência do pedido pode variar em função da aplicação. Da mesma forma, para aplicações web, a política é aplicada no início do início de sessão e é boa para a vida útil da sessão na aplicação web. Devido às diferenças nas sessões de vida através das candidaturas, o tempo entre a avaliação de políticas também variará. Sempre que o pedido solicita um novo sinal de inscrição, a política é aplicada.

Por padrão, a Azure AD emite um símbolo de hora a hora. Depois de sair da rede corporativa, dentro de uma hora a política é aplicada para aplicações usando a autenticação moderna.

### <a name="user-ip-address"></a>Endereço IP do utilizador

O endereço IP que é utilizado na avaliação de políticas é o endereço IP público do utilizador. Para dispositivos numa rede privada, este endereço IP não é o IP cliente do dispositivo do utilizador na intranet, é o endereço utilizado pela rede para ligar à internet pública.

> [!WARNING]
> Se o seu dispositivo tiver apenas um endereço IPv6, configurar a condição de localização não é suportado.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Upload e descarregamento a granel de locais nomeados

Quando criar ou atualizar localizações nomeadas, para atualizações a granel, pode fazer o upload ou descarregar um ficheiro CSV com as gamas IP. Um upload substitui as gamas IP na lista com as do ficheiro. Cada linha do ficheiro contém uma gama de endereços IP no formato CIDR.

### <a name="cloud-proxies-and-vpns"></a>Proxies de nuvem e VPNs

Quando utiliza uma solução de procuração ou VPN hospedada em nuvem, o endereço IP Que o AD do Azure utiliza enquanto avalia uma apólice é o endereço IP do proxy. O cabeçalho X-Forwarded-For (XFF) que contém o endereço IP público do utilizador não é utilizado porque não existe validação de que provém de uma fonte de confiança, pelo que apresentaria um método para fingir um endereço IP.

Quando um proxy de nuvem está em vigor, uma política que é usada para exigir um dispositivo de domínio unificado pode ser usada, ou a reivindicação interna da rede de rede a partir de AD FS.

### <a name="api-support-and-powershell"></a>Suporte aAPI e PowerShell

A API e powerShell ainda não são suportadas para locais nomeados, ou para políticas de Acesso Condicional.

## <a name="next-steps"></a>Passos seguintes

- Se quiser saber configurar uma política de Acesso Condicional, consulte [RequireM para aplicações específicas com Acesso Condicional de Diretório Ativo Azure](app-based-mfa.md).
- Se estiver pronto para configurar as políticas de Acesso Condicional para o seu ambiente, consulte as [melhores práticas de Acesso Condicional no Diretório Ativo Azure](best-practices.md).
