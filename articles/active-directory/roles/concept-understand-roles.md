---
title: Compreender conceitos de papel do Azure Ative Directory
description: Saiba como entender as funções integradas e personalizadas do Azure Ative Directory com âmbito de recursos no Azure Ative Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71291458eec0aec13542d3e0dfaf04a96f391a23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466121"
---
# <a name="understand-roles-in-azure-active-directory"></a>Compreender papéis no Azure Ative Directory

Existem cerca de 60 funções incorporadas no Azure Ative Directory (Azure AD), que são funções com um conjunto fixo de permissões de funções. Para complementar os papéis incorporados, a Azure AD também suporta papéis personalizados. Utilize funções personalizadas para selecionar as permissões de função que deseja. Por exemplo, pode criar um para gerir recursos Azure AD específicos, como aplicações ou diretores de serviço.

Este artigo explica quais são as funções de AD Azure e como podem ser usadas.

## <a name="how-azure-ad-roles-are-different-from-other-microsoft-365-roles"></a>Como os papéis de AD do Azure são diferentes dos outros papéis da Microsoft 365

Existem muitos serviços diferentes na Microsoft 365, como Azure AD e Intune. Alguns destes serviços têm os seus próprios sistemas de controlo de acesso baseados em funções; especificamente:

- Azure AD
- Troca
- Intune
- Centro de Segurança
- Centro de Conformidade
- Microsoft Cloud App Security
- Comércio

Outros serviços como Teams, SharePoint e Managed Desktop não têm sistemas de controlo de acesso separados baseados em funções. Usam papéis AZURE AD para o seu acesso administrativo. O Azure tem o seu próprio sistema de controlo de acesso baseado em funções para recursos Azure, como máquinas virtuais, e este sistema não é o mesmo que as funções de Azure AD.

Quando dizemos um sistema de controlo de acesso baseado em funções. significa que há uma loja de dados diferente onde definições de funções e atribuições de funções são armazenadas. Do mesmo modo, existe um ponto de decisão político diferente em que os controlos de acesso acontecem. Para obter mais informações , consulte [Funções para os serviços microsoft 365 em](m365-workload-docs.md) funções de administrador de subscrição AZure AD e [Classic, funções Azure e AD AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="why-some-azure-ad-roles-are-for-other-services"></a>Por que alguns papéis de AD Azure são para outros serviços

O Microsoft 365 possui uma série de sistemas de controlo de acesso baseados em funções que se desenvolveram de forma independente ao longo do tempo, cada um com o seu próprio portal de serviços. Para que seja conveniente gerir a identidade através da Microsoft 365 a partir do portal AD AZure, adicionámos algumas funções integradas específicas ao serviço, cada uma das quais concede acesso administrativo a um serviço Microsoft 365. Um exemplo desta adição é a função de Administrador de Câmbio no Azure AD. Esta função é equivalente ao grupo de [funções de Gestão](/exchange/organization-management-exchange-2013-help) da Organização no sistema de controlo de acesso baseado em papéis exchange, e pode gerir todos os aspetos do Exchange. Da mesma forma, adicionámos o papel de Administrador Intune, Administrador de Equipas, Administrador SharePoint, e assim por diante. Funções específicas do serviço é uma categoria de funções incorporadas Azure na secção seguinte.

## <a name="categories-of-azure-ad-roles"></a>Categorias de funções AD Azure

Os papéis integrados da AD AD diferem nos lugares onde podem ser utilizados, que se enquadram nas três categorias gerais seguintes.

- **Funções específicas da AD Azure**: Estas funções concedem permissões para gerir recursos apenas dentro da Azure AD. Por exemplo, Administrador de Utilizador, Administrador de Aplicações, Administrador de Grupos todas as permissões de concessão para gerir recursos que vivem em Azure AD.
- **Funções específicas do serviço**: Para os principais serviços microsoft 365 (não-Azure AD), construímos funções específicas de serviço que concedem permissões para gerir todas as funcionalidades dentro do serviço.  Por exemplo, as funções de Exchange Admin, Intune Admin, SharePoint Admin e Teams Admin podem gerir funcionalidades com os respetivos serviços. O Exchange Admin pode gerir caixas de correio, o Intune Admin pode gerir as políticas do dispositivo, o SharePoint Admin pode gerir as coleções de sites, o Teams Admin pode gerir as qualidades de chamada e assim por diante.
- **Funções de cross-service**: Existem algumas funções que abrangem os serviços. Temos dois papéis globais - Administrador Global e Global Reader. Todos os serviços da Microsoft 365 honram estes dois papéis. Além disso, existem algumas funções relacionadas com a segurança, como o Security Admin e o Security Reader, que concedem acesso a vários serviços de segurança dentro da Microsoft 365. Por exemplo, utilizando funções de Administrador de Segurança em Azure AD, pode gerir o Microsoft 365 Security Center, o Microsoft Defender Advanced Threat Protection e o Microsoft Cloud App Security. Da mesma forma, na função de Administrador de Conformidade pode gerir as definições relacionadas com a Conformidade no Microsoft 365 Compliance Center, Exchange, e assim por diante.

![As três categorias de funções integradas AZure AD](./media/concept-understand-roles/role-overlap-diagram.png)

O quadro seguinte é oferecido como um auxílio à compreensão destas categorias de funções. As categorias são nomeadas arbitrariamente, e não se destinam a insinuar quaisquer outras capacidades para além das [permissões de funções documentadas.](permissions-reference.md)

Categoria | Função
---- | ----
Funções específicas da AD Azure | Administrador da Aplicação<br>Programador de Aplicações<br>Administrador de Autenticação<br>Administrador do keyset B2C IEF<br>Administrador de Política B2C IEF<br>Administrador de Aplicações na Cloud<br>Administrador de dispositivos de nuvem<br>Administrador de acesso condicional<br>Administradores de Dispositivos<br>Leitores de Diretório<br>Contas de Sincronização do Diretório<br>Escritores do Diretório<br>Administrador de fluxo de id externo<br>Administrador de atributo de fluxo de id do utilizador externo<br>Administrador de fornecedor de identidade externa<br>Administrador de Grupos<br>Convidado Convidado<br>Administrador helpdesk<br>Administrador de Identidade Híbrida<br>Administrador de Licença<br>Suporte parceiro Tier1<br>Suporte parceiro Tier2<br>Administrador de password<br>Administrador de Autenticação Privilegiada<br>Administrador privilegiado<br>Leitor de Relatórios<br>Administrador de Conta de Utilizador
Funções de cross-service | Administrador Global<br>Administrador de Conformidade<br>Administrador de Dados de Conformidade<br>Leitor Global<br>Administrador de Segurança<br>Operador de Segurança<br>Leitor de Segurança<br>Administrador de Suporte de Serviço
Funções específicas do serviço | Administrador da Azure DevOps<br>Administrador de Proteção de Informação da Azure<br>Administrador de Faturação<br>Administrador de serviço crm<br>Aprovação de acesso ao cliente LockBox<br>Administrador de Análise de Desktop<br>Administrador de Serviço de Intercâmbio<br>Administrador de Insights<br>Insights Business Leader<br>Administrador de Serviços do Intune<br>Administrador kaizala<br>Administrador de Serviço Lync<br>Leitor de Privacidade do Centro de Mensagens<br>Leitor do Centro de Mensagens<br>Utilizador de Comércio Moderno<br>Administrador de rede<br>Administrador de Aplicações de Escritório<br>Administrador do Serviço Power BI<br>Administrador da Plataforma de Energia<br>Administrador de impressora<br>Técnico de Impressora<br>Administrador de Pesquisa<br>Editor de Pesquisa<br>Administrador de serviço do SharePoint<br>Administrador de Comunicações de Equipas<br>Engenheiro de Suporte de Comunicações de Equipas<br>Especialista em Apoio às Comunicações das Equipas<br>Administrador de dispositivos de equipas<br>Administrador de Serviço de Equipas

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do controlo de acesso baseado em funções da Azure AD](custom-overview.md)
- Criar atribuições de funções utilizando [o portal Azure, Azure AD PowerShell e API de gráficos](custom-create.md)
- [Listar atribuições de função](view-assignments.md)
