---
title: Unidades administrativas em Diretório Ativo Azure Microsoft Docs
description: Utilize unidades administrativas para uma delegação mais granular de permissões no Diretório Ativo Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fb24d0cd2714969b5a888b1036f524c4c062d76
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027623"
---
# <a name="administrative-units-in-azure-active-directory"></a>Unidades administrativas em Diretório Ativo Azure

Este artigo descreve unidades administrativas no Azure Ative Directory (Azure AD). Uma unidade administrativa é um recurso AD Azure que pode ser um recipiente para outros recursos AZure AD. Uma unidade administrativa pode conter apenas utilizadores e grupos.

Ao utilizar unidades administrativas, pode conceder permissões de administração restritas a um departamento, região ou outro segmento da sua organização que define. Pode utilizar unidades administrativas para delegar permissões a administradores regionais ou para definir a política a nível granular. Por exemplo, um administrador de conta de utilizador poderia atualizar informações de perfil, redefinir palavras-passe e atribuir licenças para utilizadores apenas na sua unidade administrativa.

Por exemplo, pode delegar em especialistas de apoio regional a função [de Administrador de Helpdesk,](permissions-reference.md#helpdesk-administrator) que se limita a gerir utilizadores apenas na região que apoiam.

## <a name="deployment-scenario"></a>Cenário de implementação

Pode ser útil restringir o âmbito administrativo utilizando unidades administrativas em organizações que são constituídas por divisões independentes de qualquer tipo. Considere o exemplo de uma grande universidade que é composta por muitas escolas autónomas (School of Business, School of Engineering, e assim por diante). Cada escola tem uma equipa de administradores de TI que controlam o acesso, gerem os utilizadores e definem políticas para a sua escola. 

Um administrador central poderia:

- Crie um papel com permissões administrativas sobre apenas utilizadores da Azure AD na unidade administrativa da Business School.
- Criar uma unidade administrativa para a Escola de Negócios.
- Povoar a unidade administrativa apenas com os alunos e funcionários da escola de negócios.
- Adicione a equipa de TI da business school ao papel, juntamente com o seu âmbito.

## <a name="license-requirements"></a>Requisitos de licença

Para utilizar unidades administrativas, precisa de uma licença Azure Ative Directory Premium para cada administração da unidade administrativa e licenças Azure Ative Directory Free para membros da unidade administrativa. Para mais informações, consulte [Começar com a Azure AD Premium.](../fundamentals/active-directory-get-started-premium.md)

## <a name="manage-administrative-units"></a>Gerir unidades administrativas

Pode gerir unidades administrativas utilizando o portal Azure, cmdlets e scripts PowerShell ou Microsoft Graph. Para obter mais informações, consulte:

- [Criar, remover, preencher e adicionar funções às unidades administrativas:](admin-units-manage.md)Inclui procedimentos completos de como fazer.
- [Trabalhar com unidades administrativas](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true): Cobre como trabalhar com unidades administrativas utilizando o PowerShell.
- [Suporte de unidade administrativa Graph](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true): Fornece documentação detalhada no Microsoft Graph para unidades administrativas.

### <a name="plan-your-administrative-units"></a>Planeie as suas unidades administrativas

Você pode usar unidades administrativas para agrupar logicamente os recursos Azure AD. Por exemplo, para uma organização cujo departamento de TI está disperso globalmente, pode fazer sentido criar unidades administrativas que definam essas fronteiras geográficas. Noutro cenário, em que uma organização multinacional tem *várias sub-organizações* semi-autónomas nas suas operações, uma unidade administrativa pode representar cada sub-organização.

Os critérios em que as unidades administrativas são criadas são guiados pelos requisitos únicos de uma organização. As unidades administrativas são uma forma comum de definir a estrutura em todos os serviços da Microsoft 365. Recomendamos que prepare as suas unidades administrativas com a sua utilização em todos os serviços da Microsoft 365 em mente. Pode obter o valor máximo das unidades administrativas quando pode associar recursos comuns através da Microsoft 365 sob uma unidade administrativa.

Pode esperar que a criação de unidades administrativas na organização passe pelas seguintes fases:

1. **Adoção inicial** : A sua organização começará a criar unidades administrativas com base em critérios iniciais, e o número de unidades administrativas aumentará à medida que os critérios forem refinados.
1. **Poda** : Após a definição dos critérios, serão suprimidas as unidades administrativas que já não são necessárias.
1. **Estabilização** : A sua estrutura organizacional está bem definida, e o número de unidades administrativas não vai mudar significativamente a curto prazo.

## <a name="currently-supported-scenarios"></a>Cenários atualmente apoiados

Como Administrador Global ou Administrador de Função Privilegiado, pode utilizar o portal AD AZure para criar unidades administrativas, adicionar utilizadores como membros de unidades administrativas e, em seguida, atribuir pessoal de TI a funções de administrador administrativo. Os administradores administrativos com âmbito unitário podem então utilizar o centro de administração Microsoft 365 para uma gestão básica dos utilizadores nas suas unidades administrativas.

Além disso, pode adicionar grupos como membros de uma unidade administrativa. Um administrador de grupo administrativo com âmbito de unidade pode geri-los utilizando o PowerShell, o Microsoft Graph e o portal AD AZure.

As tabelas que se seguem descrevem o apoio atual aos cenários da unidade administrativa:

### <a name="administrative-unit-management"></a>Gestão de unidades administrativas

| Permissões |   Gráfico/PowerShell   | Portal Azure AD | Microsoft 365 admin center (Centro de administração do Microsoft 365) | 
| -- | -- | -- | -- |
| Criação e eliminação de unidades administrativas   |    Suportado    |   Suportado   |    Não suportado | 
| Adicionar e remover individualmente os membros da unidade administrativa    |   Suportado    |   Suportado   |    Não suportado | 
| Adicionar e remover membros da unidade administrativa a granel utilizando ficheiros CSV   |    Não suportado     |  Suportado   |    Nenhum plano para apoiar | 
| Atribuição de administradores administrativos com âmbito de unidade  |     Suportado    |   Suportado    |   Não suportado | 
| Adicionar e remover membros da unidade administrativa dinamicamente com base em atributos | Não suportado | Não suportado | Não suportado 

### <a name="user-management"></a>Gestão de utilizadores

| Permissões |   Gráfico/PowerShell   | Portal Azure AD | Microsoft 365 admin center (Centro de administração do Microsoft 365) |
| -- | -- | -- | -- |
| Gestão administrativa de propriedades, senhas e licenças de utilização   |    Suportado     |  Suportado   |   Suportado |
| Bloqueio e desbloqueio de inscrições administrativas por unidades    |   Suportado   |    Suportado   |    Suportado |
| Gestão administrativa unitária das credenciais de autenticação multifactor do utilizador   |    Suportado   |   Suportado   |   Não suportado |

### <a name="group-management"></a>Gestão de grupos

| Permissões |   Gráfico/PowerShell   | Portal Azure AD | Microsoft 365 admin center (Centro de administração do Microsoft 365) |
| -- | -- | -- | -- |
| Gestão administrativa de propriedades e membros do grupo     |  Suportado   |    Suportado    |  Não suportado |
| Gestão administrativa de âmbito unitário de licenciamento de grupo   |    Suportado  |    Suportado   |   Não suportado |


As unidades administrativas aplicam âmbito apenas às permissões de gestão. Não impedem que membros ou administradores utilizem as suas [permissões de utilizador predefinidas](../fundamentals/users-default-permissions.md) para navegar em outros utilizadores, grupos ou recursos fora da unidade administrativa. No centro de administração Microsoft 365, os utilizadores fora das unidades administrativas de um administrador de âmbito são filtrados. Mas pode navegar em outros utilizadores no portal AD AZure, PowerShell e outros serviços da Microsoft.

## <a name="next-steps"></a>Passos seguintes

- [Gerir unidades administrativas](admin-units-manage.md)
- [Gerir utilizadores em unidades administrativas](admin-units-add-manage-users.md)
- [Gerir grupos em unidades administrativas](admin-units-add-manage-groups.md)
- [Atribuir funções âmbito a uma unidade administrativa](admin-units-assign-roles.md)
