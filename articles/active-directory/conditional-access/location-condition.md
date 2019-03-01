---
title: O que inclui as condições de localização acesso condicional do Azure Active Directory? | Microsoft Docs
description: Saiba como utilizar a condição de localização para controlar o acesso às suas aplicações na cloud com base na localização de rede de um utilizador.
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/28/2019
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14b277e505e2a4975945f4b2f8f8410e25e0f8b3
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193590"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>O que é a condição de localização no acesso condicional do Azure Active Directory? 

Com o [acesso condicional do Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), pode controlar os utilizadores autorizados como pode aceder às suas aplicações na cloud. A condição de localização de uma política de acesso condicional permite-lhe associar as definições de controlos de acesso para as localizações de rede dos seus utilizadores.

Este artigo fornece as informações necessárias configurar a condição de localização. 

## <a name="locations"></a>Localizações

Azure AD permite início de sessão único em dispositivos e aplicações e serviços a partir de qualquer lugar na internet pública. Com a condição de localização, pode controlar o acesso às suas aplicações na cloud com base na localização de rede de um utilizador. Casos de utilização comuns para a condição de localização são:

- Exigir autenticação multifator para os utilizadores que acedem um serviço, quando estiverem desconectados da rede corporativa.

- Bloquear o acesso de utilizadores que acedem um serviço de países ou regiões específicos.

Uma localização é uma etiqueta, para uma localização de rede que qualquer um representa uma localização com o nome ou a autenticação multifator de IPs fidedignos.


## <a name="named-locations"></a>Localizações com nome 

Com localizações com nome, pode criar agrupamentos lógicos de intervalos de endereços IP, países e regiões. 

Pode aceder as localizações com nome no **gerir** secção da página de acesso condicional.

![Localizações](./media/location-condition/02.png)

 


Um local nomeado tem os seguintes componentes:

![Localizações](./media/location-condition/42.png)

- **Nome** -o nome a apresentar de um local nomeado.

- **Intervalos de IP** -um ou mais intervalos de endereços de IPv4 no formato CIDR. Especificar um intervalo de endereços IPv6 não é suportado.

- **Marcar como localização fidedigna** -um sinalizador pode ser definido para um local nomeado indicar a localização fidedigna. Normalmente, os locais confiáveis são áreas de rede que são controladas pelo seu departamento de TI. Além do acesso condicional, localizações com nome confiáveis também são utilizadas pelos relatórios de segurança do Azure Identity Protection e o Azure AD para reduzir [falsos positivos](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).

- **Países/regiões** -esta opção permite-lhe selecionar um ou mais país ou região para definir um local nomeado. 

- **Incluir áreas desconhecidas** -endereços IP alguns não são mapeados para um país específico. Esta opção permite-lhe escolher se estes endereços IP devem ser incluídos na localização com nome. Utilize esta definição se a política a utilizar a localização com o nome deve aplicar a localizações desconhecidas.

O número de localizações com nome, que pode configurar é restrito pelo tamanho do objeto relacionado no Azure AD. Pode configurar:

- Um com o nome local com até 1200 intervalos IP.

- Um máximo de 90 locais nomeados com um intervalo IP atribuído a cada um deles.




## <a name="trusted-ips"></a>IPs Fidedignos

Também pode configurar os intervalos de endereços IP que representa a intranet local de sua organização no [definições do serviço de autenticação multifator](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Esta funcionalidade permite-lhe configurar até 50 intervalos de endereços IP. Os intervalos de endereços IP estão no formato CIDR. Para obter mais informações, consulte [IPs fidedignos](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Ter fidedignas IPs configurada, eles aparecem como **IPS fidedignos de MFA** na lista de localizações para a condição de localização.   

### <a name="skipping-multi-factor-authentication"></a>A ignorar a autenticação multifator

Na página de definições do serviço de autenticação multifator, pode identificar os utilizadores de intranet corporativa, selecionando **ignorar multi-factor authentication para pedidos de utilizadores federados na minha intranet**. Esta definição indica que o interior empresarial rede afirmação, o que é emitida pelo AD FS, deve ser considerado fidedigno e utilizado para identificar o utilizador como sendo na rede empresarial. Para obter mais informações, consulte [ativar a funcionalidade de IPs fidedignos com o acesso condicional](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Depois de verificar esta opção, incluindo o local nomeado **IPS fidedignos de MFA** será aplicada a todas as políticas com esta opção selecionada.

Para aplicativos móveis e de Desktops, o que há muito tempo tem vivido durações de sessão, acesso condicional é periodicamente reavaliado. A predefinição é, uma vez, uma hora. Quando o interior afirmação da rede empresarial apenas é emitida no momento da autenticação inicial, do Azure AD pode não ter uma lista de intervalos de IP fidedignos. Neste caso, é mais difícil determinar se o utilizador é ainda na rede empresarial:

1. Verifique se o endereço IP do usuário está em um dos intervalos de IP fidedignos.

2. Verifique se os três primeiros octetos do endereço IP do utilizador correspondem os primeiros 3 octetos do endereço IP da autenticação inicial. O endereço IP é comparado com a autenticação inicial porque se trata de quando o interior afirmação da rede empresarial originalmente foi emitida e a localização do utilizador foi validada.

Se os dois passos falharem, é considerado um utilizador já não estar num IP fidedigno.



## <a name="location-condition-configuration"></a>Configuração da condição de localização

Ao configurar a condição de localização, tem a opção de distinguir entre:

- Qualquer localização 
- Todas as localizações fidedignas
- Localizações selecionadas

![Localizações](./media/location-condition/01.png)

### <a name="any-location"></a>Qualquer localização

Por padrão, selecionando **qualquer localização** faz com que uma política para ser aplicada a todos os endereços IP, o que significa que qualquer endereço na Internet. Esta definição não está limitada a endereços IP que configurou como localização com nome. Quando seleciona **qualquer localização**, ainda pode excluir localizações específicas de uma política. Por exemplo, pode aplicar uma política para todas as localizações, exceto de locais confiáveis para definir o âmbito para todas as localizações, exceto à rede empresarial.

### <a name="all-trusted-locations"></a>Todas as localizações fidedignas

Esta opção aplica-se a:

- Todas as localizações que foram marcadas como localização fidedigna
- **IPS fidedignos de MFA** (se configurada)


### <a name="selected-locations"></a>Localizações selecionadas

Com esta opção, pode selecionar um ou mais localizações com nome. Para uma política com esta definição para aplicar, o utilizador precisa de ligar a partir de qualquer uma das localizações selecionadas. Quando clica em **selecione** abre o controlo de seleção de rede com o nome que mostra a lista de redes com nome. A lista mostra também se a localização de rede foi marcada como fidedigna. A localização nomeada chamado **IPs fidedignos de MFA** é utilizado para incluir as definições de IP que podem ser configuradas na página de definições do serviço de autenticação multifator.

## <a name="what-you-should-know"></a>O que deve saber

### <a name="when-is-a-location-evaluated"></a>Quando é avaliada uma localização?

Políticas de acesso condicional são avaliadas quando: 

- Inicialmente um utilizador inicia sessão a uma aplicação web de aplicação, móveis ou ambiente de trabalho. 

- Uma aplicação móvel ou ambiente de trabalho que utiliza autenticação moderna, utiliza um token de atualização para adquirir um novo token de acesso. Por predefinição, esta é uma vez uma hora. 

Isso significa que para dispositivos móveis e aplicativos de área de trabalho que utilizam autenticação moderna, seria possível detetar uma alteração na localização dentro de uma hora de alterar a localização de rede. Para aplicações de ambiente de trabalho e móveis que não utilizam autenticação moderna, a política é aplicada em cada pedido de token. A frequência do pedido pode variar consoante a aplicação. Da mesma forma, para aplicações web, a política é aplicada no início de sessão inicial e é bom para a duração da sessão no aplicativo web. Devido às diferenças de durações de sessão em todas as aplicações, o tempo entre a avaliação de políticas também podem variar. Sempre que a aplicação solicite um novo início de sessão no token, a política é aplicada.


Por predefinição, o Azure AD emite um token numa base horária. Depois de mover fora da rede corporativa, dentro de uma hora a política é imposta para aplicativos que utilizam autenticação moderna.


### <a name="user-ip-address"></a>Endereço IP do utilizador

O endereço IP que é utilizado na avaliação da política é o endereço IP público do utilizador. Para dispositivos numa rede privada, isso não é o IP do cliente de dispositivo do utilizador na intranet, é o endereço utilizado pela rede para ligar à Internet pública. Se o seu dispositivo tem apenas um endereço IPv6, a configurar a condição de localização não é suportada.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Carregamento em massa e o download de localizações com nome

Quando criar ou atualizar com o nome localizações, para atualizações em massa, pode carregar ou transferir um ficheiro CSV com os intervalos IP. Um carregamento substitui os intervalos IP na lista com os do ficheiro. Cada linha do ficheiro contém um intervalo de endereços IP no formato CIDR. 


### <a name="cloud-proxies-and-vpns"></a>Proxies de nuvem e de VPNs 

Quando utiliza uma solução VPN ou um proxy de nuvem alojada, o endereço IP do Azure AD utiliza enquanto avaliar uma política é o endereço IP do proxy. O cabeçalho de X-Forwarded-For (XFF) que contém o endereço IP público do utilizador não é utilizado porque não existe nenhuma validação que se trata de uma origem fidedigna, para que fornece um método para simular um endereço IP. 

Quando um proxy de nuvem está no local, uma política que é utilizada para exigir um dispositivo associado ao domínio pode ser utilizado ou o interior corpnet afirmações do AD FS.



### <a name="api-support-and-powershell"></a>Suporte de API e PowerShell 

API e PowerShell ainda não é suportado para localizações com nome, ou para políticas de acesso condicional.





## <a name="next-steps"></a>Passos Seguintes

- Se quiser saber como configurar uma política de acesso condicional, consulte [exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory](app-based-mfa.md).

- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, veja [Best practices for conditional access in Azure Active Directory](best-practices.md) (Melhores práticas do acesso condicional no Azure Active Directory). 
