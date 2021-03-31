---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 2b28cc742bb981e0db535d39cb0c7fbf027470ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83673769"
---
### <a name="identity-tier"></a>Nível de identidade 

A parceria Microsoft-Oracle permite-lhe criar uma identidade unificada em toda a Azure, OCI e a sua aplicação Oracle. Para a suíte de aplicação JD Edwards EnterpriseOne ou PeopleSoft, é necessário um exemplo do Servidor HTTP (OHS) do Oracle HTTP para configurar um único registo entre Azure AD e Oracle IDCS.

O OHS funciona como um representante inverso ao nível de aplicação, o que significa que todos os pedidos para os pedidos finais passam por ele. Oracle Access Manager WebGate é um plugin de servidor web OHS que interceta todos os pedidos que vão para a aplicação final. Se um recurso que está a ser acedido for protegido (requer uma sessão autenticada), o WebGate inicia o fluxo de autenticação OIDC com o Serviço de Nuvem de Identidade através do navegador do utilizador. Para obter mais informações sobre os fluxos suportados pelo WebGate OpenID Connect, consulte a [documentação](https://docs.oracle.com/cd/E52734_01/oam/AIAAG/GUID-1E927D1B-FB83-425B-8768-85DB441821A4.htm#AIAAG7327)do Oracle Access Manager .

Com esta configuração, um utilizador já iniciado no Azure AD pode navegar para a aplicação JD Edwards EnterpriseOne ou PeopleSoft sem iniciar sessão novamente, através do Oracle Identity Cloud Service. Os clientes que implementam esta solução ganham os benefícios de um único sign-on, incluindo um único conjunto de credenciais, uma experiência de inscrição melhorada, uma segurança melhorada e um custo de ajuda reduzido.

Para saber mais sobre a configuração de um único sind on para JD Edwards EnterpriseOne ou PeopleSoft com Azure AD, consulte o [papel branco](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)da Oracle associado.