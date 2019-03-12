---
title: Arquitetura de identidade para o Azure Stack | Documentos da Microsoft
description: Saiba mais sobre a arquitetura de identidade que pode utilizar com o Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 9ae8a55d04c34ec65d964927fd25c3bc01adeae4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777877"
---
# <a name="identity-architecture-for-azure-stack"></a>Arquitetura de identidade para o Azure Stack

Ao escolher um fornecedor de identidade para utilizar com o Azure Stack, deve compreender as diferenças importantes entre as opções do Azure Active Directory (Azure AD) e serviços de Federação do Active Directory (AD FS).

## <a name="capabilities-and-limitations"></a>Capacidades e limitações 
O fornecedor de identidade que escolher, pode limitar as opções, incluindo suporte para vários inquilinos. 

  

|Capacidade ou ao seu cenário        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Ligado à internet     |Sim       |Opcional|
|Suporte para vários inquilinos     |Sim       |Não      |
|Itens de oferta no Marketplace |Sim       |Sim. Requer a utilização do [offline Marketplace Syndication](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) ferramenta.|
|Suporte para Active Directory Authentication Library (ADAL) |Sim |Sim|
|Suporte para ferramentas, como a CLI do Azure, o Visual Studio e o PowerShell  |Sim |Sim|
|Criar principais de serviço através do portal do Azure     |Sim |Não|
|Criar principais de serviço com certificados      |Sim |Sim|
|Criar principais de serviço com segredos (chaves)    |Sim |Não|
|As aplicações podem utilizar o serviço de gráficos           |Sim |Não|
|Aplicações podem utilizar o fornecedor de identidade para início de sessão |Sim |Sim. Requer aplicativos para federar com locais instâncias do AD FS. |

## <a name="topologies"></a>Topologias
As secções seguintes abordam as várias topologias de identidade que pode utilizar.

### <a name="azure-ad-single-tenant-topology"></a>Do Azure AD: topologia de inquilino único 
Por predefinição, quando instalar o Azure Stack e utilizar o Azure AD, o Azure Stack utiliza uma topologia de inquilino único. 

Uma topologia de inquilino único é útil quando:
- Todos os utilizadores fazem parte do mesmo inquilino.
- Um fornecedor de serviços aloja uma instância do Azure Stack para uma organização. 

![Topologia de inquilino único de pilha do Azure com o Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Esta topologia apresenta as seguintes características:
- O Azure Stack registra todas as aplicações e serviços para o mesmo Azure AD directory de inquilino. 
- O Azure Stack autentica apenas os utilizadores e aplicações a partir do diretório, incluindo os tokens. 
- Identidades para os administradores (operadores da nuvem) e os utilizadores de inquilino estão no mesmo inquilino do diretório. 
- Para permitir que um utilizador a partir de outro diretório aceder a este ambiente do Azure Stack, terá [convidar o utilizador como convidado](azure-stack-identity-overview.md#guest-users) para o diretório de inquilinos. 

### <a name="azure-ad-multi-tenant-topology"></a>Do Azure AD: topologia de multi-inquilino
Podem configurar o Azure Stack para permitir o acesso às aplicações por inquilinos de organizações de um ou mais operadores da nuvem. Os utilizadores acedem a aplicações através do portal de utilizador. Nesta configuração, o portal de administração (utilizado pela operadora de rede na cloud) está limitado a utilizadores de um único diretório. 

Uma topologia de multi-inquilino é útil quando:
- Um fornecedor de serviços quer permitir que os utilizadores de várias organizações aceder ao Azure Stack.

![Topologia de multi-inquilino de pilha do Azure com o Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Esta topologia apresenta as seguintes características:
- Acesso a recursos deve ser numa base por organização. 
- Os utilizadores de uma organização devem ser não é possível conceder acesso a recursos para os utilizadores que estão fora da sua organização. 
- Identidades para os administradores (operadores da nuvem) podem estar num inquilino do diretório separado de identidades dos utilizadores. Essa separação proporciona isolamento de conta ao nível do fornecedor de identidade. 
 
### <a name="ad-fs"></a>AD FS  
A topologia do AD FS é necessário quando uma das seguintes condições for verdadeira:
- O Azure Stack não liga à internet.
- O Azure Stack pode ligar à internet, mas optar por utilizar o AD FS para o seu fornecedor de identidade.
  
![Topologia de pilha do Azure através do AD FS](media/azure-stack-identity-architecture/adfs.png)

Esta topologia apresenta as seguintes características:
- Para suportar a utilização desta topologia na produção, tem de integrar a instância do Azure Stack AD FS incorporada com uma instância do AD FS existente que é sustentada pelo Active Directory, por meio de uma fidedignidade de Federação. 
- Pode integrar o serviço de gráficos no Azure Stack com a instância existente do Active Directory. Também pode utilizar o serviço Graph API baseada em OData que suporte APIs que são consistentes com o Azure AD Graph API. 

  Para interagir com a instância do Active Directory, a Graph API requer credenciais de utilizador da sua instância do Active Directory que tem permissões só de leitura. 
  - A instância do AD FS interna se baseia no Windows Server 2016. 
  - As instâncias do AD FS e do Active Directory devem ser baseadas no Windows Server 2012 ou posterior. 
  
  Entre a instância do Active Directory e a instância do AD FS incorporada, interações não são restritas a OpenID Connect e eles podem usar qualquer protocolo mutuamente suportado. 
  - Contas de utilizador são criadas e geridas na sua instância do Active Directory no local.
  - Principais de serviço e registos para as aplicações são geridos na instância do Active Directory incorporada.



## <a name="next-steps"></a>Passos Seguintes
- [Descrição geral de identidade](azure-stack-identity-overview.md)   
- [Integração do Centro de dados - identidade](azure-stack-integrate-identity.md)
