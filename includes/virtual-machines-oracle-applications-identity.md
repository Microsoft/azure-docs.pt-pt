---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361560"
---
### <a name="identity-tier"></a>Nível de identidade 

A parceria Microsoft-Oracle permite-lhe criar uma identidade unificada em toda a Azure, OCI e na sua aplicação Oracle. Para a suíte de aplicação JD Edwards EnterpriseOne ou PeopleSoft, é necessária uma instância do Oracle HTTP Server (OHS) para configurar um único sign-on entre o Azure AD e o Oracle IDCS.

A OHS atua como um representante inverso para o nível de aplicação, o que significa que todos os pedidos para as aplicações finais passam por ele. Oracle Access Manager WebGate é um plugin de servidor web OHS que interceta todos os pedidos que vão para a aplicação final. Se um recurso a ser acedido estiver protegido (requer uma sessão autenticada), o WebGate inicia o fluxo de autenticação OIDC com o Identity Cloud Service através do navegador do utilizador. Para obter mais informações sobre os fluxos suportados pelo OpenID Connect WebGate, consulte a [documentação](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)do Oracle Access Manager .

Com esta configuração, um utilizador já ligado ao Azure AD pode navegar para a aplicação JD Edwards EnterpriseOne ou PeopleSoft sem voltar a fazer login, através do Oracle Identity Cloud Service. Os clientes que implementam esta solução ganham os benefícios de um único sinal, incluindo um único conjunto de credenciais, uma melhor experiência de inscrição, uma segurança melhorada e um custo reduzido de help-desk.

Para saber mais sobre a criação de um único sign-on para jD Edwards EnterpriseOne ou PeopleSoft com Azure AD, consulte o [livro branco oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)associado .