---
title: Construir autenticação híbrida mais resiliente no Azure Ative Directory
description: Um guia para arquitetos e administradores de TI na construção de uma infraestrutura híbrida resiliente.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de01788c01d4d6dedd9563faaaac07bff30bbd97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724764"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>Construa resiliência na sua arquitetura híbrida

A autenticação híbrida permite que os utilizadores acedam a recursos baseados na nuvem com as suas identidades dominadas no local. Uma infraestrutura híbrida inclui componentes de nuvem e no local.

* Os componentes da nuvem incluem Azure AD, recursos e serviços Azure, aplicações baseadas na nuvem da sua organização e aplicações SaaS.

* Os componentes no local incluem aplicações no local, recursos como bases de dados SQL e um fornecedor de identidade como o Windows Server Ative Directory. 

> [!IMPORTANT]
> Como planeia resiliência na sua infraestrutura híbrida, é fundamental minimizar as dependências e os pontos únicos de fracasso. 

A Microsoft oferece três mecanismos para a autenticação híbrida. As opções estão listadas por ordem de resiliência. Recomendamos que implemente a sincronização de hash de palavra-passe, se possível.

* [A sincronização de hash de palavra-passe](../hybrid/whatis-phs.md) (PHS) utiliza o Azure AD Connect para sincronizar a identidade e um haxixe do hash da palavra-passe para Azure AD, permitindo aos utilizadores iniciarem a sação em recursos baseados na nuvem com as suas palavras-passe no local. PhS tem dependências no local apenas para sincronização, não para autenticação.

* [A Autenticação Transitada](../hybrid/how-to-connect-pta.md) (PTA) redireciona os utilizadores para Azure AD para iniciar sent-in. Em seguida, o nome de utilizador e palavra-passe são validados contra o Ative Directory nas instalações, através de um agente que é implantado na rede corporativa. A PTA tem uma pegada no local dos seus agentes PTA AZURE AD que residem em servidores no local.

* Os clientes [da federação](../hybrid/whatis-fed.md) implantam um serviço de federação como o AD FS, e depois a Azure AD valida a afirmação SAML produzida pelo serviço da federação. A Federação tem a maior dependência das infraestruturas no local e, portanto, mais pontos de insucesso. 

   
‎You may be using one or more of these methods in your organization. Para mais informações, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida AZure AD.](../hybrid/choose-ad-authn.md) Este artigo contém uma árvore de decisão que pode ajudá-lo a decidir sobre a sua metodologia.

## <a name="password-hash-synchronization"></a>Sincronização de hash de palavra-passe

A opção de autenticação híbrida mais simples e resiliente para Azure AD é a [Sincronização de Hash Password](../hybrid/whatis-phs.md) que não tem qualquer dependência de infraestrutura de identidade no local ao processar pedidos de autenticação. Uma vez que as identidades com hashes de palavra-passe são sincronizadas com Azure AD, os utilizadores podem autenticar para cloud recursos sem dependência dos componentes de identidade no local. 

![Diagrama de arquitetura de PHS](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

Se escolher esta opção de autenticação, não sentirá perturbações quando os componentes de identidade no local ficarem indisponíveis. A interrupção no local pode ocorrer por muitas razões, incluindo falha de hardware, falhas de energia, desastres naturais e ataques de malware. 

### <a name="how-do-i-implement-phs"></a>Como implemento o PHS?

Para implementar phs, consulte os seguintes recursos:

* [Implementar sincronização de hash de palavra-passe com Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md)

* [Ativar a sincronização de palavras-passe de hash](../hybrid/how-to-connect-password-hash-synchronization.md)

Se os seus requisitos forem tais que não possa utilizar PHS, utilize a Autenticação Pass-through.

## <a name="pass-through-authentication"></a>Autenticação pass-through

A Autenticação Pass-through tem uma dependência de agentes de autenticação que residem no local em servidores. Uma ligação persistente, ou autocarro de serviço, está presente entre a Azure AD e os agentes de PTA no local. A firewall, os servidores que hospedam os agentes de autenticação e o Windows Server Ative Directory (ou outro fornecedor de identidade) são todos pontos de falha potenciais. 

![Diagrama de arquitetura de PTA](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>Como posso implementar a PTA?

Para implementar a Autenticação Pass-through, consulte os seguintes recursos.

* [Como funciona a autenticação pass-through](../hybrid/how-to-connect-pta-how-it-works.md)

* [Informações detalhadas sobre a segurança na Autenticação Pass-through](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [Instalar autenticação pass-through Azure AD](../hybrid/how-to-connect-pta-quick-start.md)

* Se estiver a utilizar a PTA, defina uma [topologia altamente disponível.](../hybrid/how-to-connect-pta-quick-start.md)

 ## <a name="federation"></a>de Federação

A Federação envolve a criação de uma relação de confiança entre a Azure AD e o serviço da federação, que inclui a troca de pontos finais, certificados de assinatura de fichas simbólicas e outros metadados. Quando um pedido chega ao Azure AD, lê a configuração e redireciona o utilizador para os pontos finais configurados. Nessa altura, o utilizador interage com o serviço da federação, que emite uma afirmação SAML validada pela Azure AD. 

O diagrama seguinte mostra uma topologia de uma empresa Ative Directory Federation Services (AD FS), implantação que inclui servidores de procuração de federação redundante e aplicações web em vários centros de dados no local. Esta configuração baseia-se em componentes de infraestrutura de rede empresarial como DNS, Network Load Balance com capacidades de geofinidade, firewalls, etc. Todos os componentes e ligações no local são suscetíveis a falhas. Visite a Documentação de [Planeamento da Capacidade da AD FS](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) para obter mais informações.

> [!NOTE]
>  A Federação tem o maior número de dependências no local e, portanto, os pontos mais potenciais de insucesso. Embora este diagrama mostre AD FS, outros fornecedores de identidade no local estão sujeitos a considerações de design semelhantes para alcançar alta disponibilidade, escalabilidade e falha.

![Diagrama de arquitetura da federação](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>Como posso implementar a federação?

Se estiver a implementar uma estratégia de autenticação federada ou quiser torná-la mais resistente, consulte os seguintes recursos.

* [O que é a autenticação federada](../hybrid/whatis-fed.md)

* [Como funciona a federação](../hybrid/how-to-connect-fed-whatis.md)

* [Lista de compatibilidades de federação do Azure AD](../hybrid/how-to-connect-fed-compatibility.md)

* Siga a documentação de [planeamento de capacidade da AD FS](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)

* [Implantação de FS AD em Azure IaaS](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* [Ativar o PHS](../hybrid/tutorial-phs-backup.md) juntamente com a sua federação

## <a name="next-steps"></a>Passos seguintes
Recursos de resiliência para administradores e arquitetos
 
* [Construir resiliência com gestão credencial](resilience-in-credentials.md)

* [Construir resiliência com estados de dispositivo](resilience-with-device-states.md)

* [Construa resiliência utilizando a Avaliação contínua de Acesso (CAE)](resilience-with-continuous-access-evaluation.md)

* [Construir resiliência na autenticação externa do utilizador](resilience-b2b-authentication.md)

* [Criar resiliência no acesso à aplicação com Aplicação Proxy](resilience-on-premises-access.md)

Recursos de resiliência para desenvolvedores

* [Construa resiliência do IAM nas suas aplicações](resilience-app-development-overview.md)

* [Construa resiliência nos seus sistemas CIAM](resilience-b2c.md)