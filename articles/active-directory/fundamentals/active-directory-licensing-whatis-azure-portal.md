---
title: O que é baseado no grupo de licenciamento - Azure Active Directory | Documentos da Microsoft
description: Saiba mais sobre o Azure Active Directory com base em grupo licenciamento, incluindo como funciona e melhores práticas.
services: active-directory
keywords: Licenciamento do Azure AD
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eec896e6cccaf58c83820161c54c4f10cfadadd
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561566"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>O que é o licenciamento no Azure Active Directory com base em grupo?

Os serviços cloud pagos da Microsoft, como o Office 365, Enterprise Mobility + Security, Dynamics 365 e outros produtos semelhantes, requerem licenças. Estas licenças são atribuídas a cada utilizador que tenha de aceder a estes serviços. Para gerir licenças, os administradores utilizam um dos portais de gestão (Office ou Azure) e cmdlets do PowerShell. O Azure Active Directory (Azure AD) é a infraestrutura subjacente que suporta a gestão de identidades para todos os serviços cloud da Microsoft. O Azure AD armazena informações sobre os estados de atribuição de licenças para os utilizadores.

Até agora, só foi possível atribuir licenças ao nível do utilizador individual, o que pode dificultar a gestão em grande escala. Por exemplo, para adicionar ou remover licenças de utilizador baseadas em alterações organizacionais, como utilizadores a aderir ou a sair da organização ou um departamento, um administrador tem de escrever muitas vezes um script complexo do PowerShell. Este script faz chamadas individuais para o serviço cloud.

Para resolver esses desafios, o Azure AD inclui agora licenciamento baseado no grupo. Pode atribuir uma ou mais licenças de produto a um grupo. O Azure AD garante que as licenças são atribuídas a todos os membros do grupo. São atribuídas as licenças adequadas a quaisquer novos membros que adiram ao grupo. Ao saírem do grupo, essas licenças são removidas. Esta gestão de licenciamento elimina a necessidade de automatizar a gestão de licenças através da PowerShell para refletir as mudanças na organização e estrutura departamental por utilizador.

## <a name="licensing-requirements"></a>Requisitos de licenciamento
Deve ter uma das seguintes licenças para utilizar o licenciamento baseado em grupo:

- Subscrição paga ou experimental para Azure AD Premium P1 ou superior

- Edição paga ou experimental do Office 365 Enterprise E3 ou Office 365 A3 ou Office 365 GCC G3 ou Office 365 E3 para GCCH ou Office 365 E3 para DOD e superior

### <a name="required-number-of-licenses"></a>Número obrigatório de licenças
Para qualquer grupo que tenha uma licença atribuída, você também deve ter uma licença para cada membro único. Embora não tenha de atribuir uma licença a cada membro do grupo, deve ter pelo menos licenças suficientes para incluir todos os membros. Por exemplo, se você tem 1.000 membros únicos que fazem parte de grupos licenciados no seu inquilino, você deve ter pelo menos 1.000 licenças para cumprir o contrato de licenciamento.

## <a name="features"></a>Funcionalidades

Seguem-se as principais funcionalidades do licenciamento baseado no grupo:

- As licenças podem ser atribuídas a qualquer grupo de segurança no Azure AD. Grupos de segurança podem ser sincronizados no local, utilizando o Azure AD Connect. Também pode criar grupos de segurança diretamente no Azure AD (também denominados grupos apenas na cloud) ou automaticamente através da funcionalidade de grupos dinâmicos do Azure AD.

- Quando uma licença de produto é atribuída a um grupo, o administrador pode desativar um ou mais planos de serviço no produto. Normalmente, esta atribuição é feita quando a organização ainda não está pronta para começar a usar um serviço incluído num produto. Por exemplo, o administrador pode atribuir o Office 365 a um departamento, mas desativar temporariamente o serviço Yammer.

- São suportados todos os serviços cloud da Microsoft que exigem licenciamento ao nível do utilizador. Este suporte inclui todos os produtos do Office 365, Enterprise Mobility + Security e Dynamics 365.

- O licenciamento baseado em grupo está atualmente disponível apenas através do [portal Azure.](https://portal.azure.com) Se utilizar principalmente outros portais de gestão para gestão de utilizadores e grupos, como o centro de administração microsoft [365,](https://admin.microsoft.com)pode continuar a fazê-lo. No entanto, deve utilizar o portal do Azure para gerir as licenças ao nível do grupo.

- O Azure AD gere automaticamente as modificações de licença que resultam de alterações à associação ao grupo. Normalmente, as modificações de licença entram em vigor alguns minutos após uma alteração de associação.

- Um utilizador pode ser membro de vários grupos com políticas de licença especificadas. Um utilizador também pode ter algumas licenças atribuídas diretamente, fora de quaisquer grupos. O estado de utilizador resultante é uma combinação de todos os produtos e licenças de serviço atribuídos. Se a mesma licença é atribuída a um utilizador de várias origens, a licença será consumida apenas uma vez.

- Em alguns casos, não é possível atribuir licenças a um utilizador. Por exemplo, podem não existir licenças disponíveis suficientes no inquilino ou podem ter sido atribuídos serviços em conflito ao mesmo tempo. Os administradores têm acesso a informações sobre os utilizadores para os quais o Azure AD não conseguiu processar totalmente licenças de grupo. Em seguida, podem tomar medidas corretivas com base nessas informações.

## <a name="your-feedback-is-welcome"></a>Os seus comentários são bem-vindos!

Se tiver feedback ou pedidos de funcionalidades, partilhe-os connosco através [do fórum de administração da AD Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre outros cenários para gestão de licenças através do licenciamento baseado no grupo, veja:

* [Atribuir licenças a um grupo no Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Como migrar os utilizadores licenciados individuais para o licenciamento baseado no grupo no Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Como migrar os utilizadores entre licenças de produtos utilizando licenciamento baseado em grupo no Diretório Ativo azure](../users-groups-roles/licensing-groups-change-licenses.md)
* [Cenários adicionais de licenciamento baseado no grupo do Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
* [Exemplos da PowerShell para licenciamento baseado em grupo no Diretório Ativo Azure](../users-groups-roles/licensing-ps-examples.md)
