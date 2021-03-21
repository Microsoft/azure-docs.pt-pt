---
title: Unidades administrativas em Azure Ative Directory | Microsoft Docs
description: Utilize unidades administrativas para uma delegação mais granular de permissões no Diretório Ativo Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49f2c290c69fcadd594d6cbd5879e7d9f5304a42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558020"
---
# <a name="administrative-units-in-azure-active-directory"></a>Unidades administrativas em Diretório Ativo Azure

Este artigo descreve unidades administrativas no Azure Ative Directory (Azure AD). Uma unidade administrativa é um recurso AD Azure que pode ser um recipiente para outros recursos AZure AD. Uma unidade administrativa pode conter apenas utilizadores e grupos.

As unidades administrativas restringem as permissões numa função a qualquer parte da sua organização que defina. Pode, por exemplo, utilizar unidades administrativas para delegar o papel [de Administrador de Ajuda](permissions-reference.md#helpdesk-administrator) a especialistas de apoio regional, para que possam gerir os utilizadores apenas na região que apoiam.

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
- [Trabalhar com unidades administrativas](/powershell/azure/active-directory/working-with-administrative-units): Cobre como trabalhar com unidades administrativas utilizando o PowerShell.
- [Suporte de unidade administrativa Graph](/graph/api/resources/administrativeunit): Fornece documentação detalhada no Microsoft Graph para unidades administrativas.

### <a name="plan-your-administrative-units"></a>Planeie as suas unidades administrativas

Você pode usar unidades administrativas para agrupar logicamente os recursos Azure AD. Uma organização cujo departamento de TI está disperso globalmente pode criar unidades administrativas que definem fronteiras geográficas relevantes. Noutro cenário, em que uma organização global tem suborganizações semi-autónomas nas suas operações, as unidades administrativas podem representar as suborganizações.

Os critérios em que as unidades administrativas são criadas são guiados pelos requisitos únicos de uma organização. As unidades administrativas são uma forma comum de definir a estrutura em todos os serviços da Microsoft 365. Recomendamos que prepare as suas unidades administrativas com a sua utilização em todos os serviços da Microsoft 365 em mente. Pode obter o valor máximo das unidades administrativas quando pode associar recursos comuns através da Microsoft 365 sob uma unidade administrativa.

Pode esperar que a criação de unidades administrativas na organização passe pelas seguintes fases:

1. **Adoção inicial**: A sua organização começará a criar unidades administrativas com base em critérios iniciais, e o número de unidades administrativas aumentará à medida que os critérios forem refinados.
1. **Poda**: Após a definição dos critérios, serão suprimidas as unidades administrativas que já não são necessárias.
1. **Estabilização**: A sua estrutura organizacional está definida e o número de unidades administrativas não vai mudar significativamente a curto prazo.

## <a name="currently-supported-scenarios"></a>Cenários atualmente apoiados

Como Administrador Global ou Administrador privilegiado, pode utilizar o portal AD Azure para:

- Criar unidades administrativas
- Adicionar utilizadores e grupos membros de unidades administrativas
- Atribua pessoal de TI às funções de administrador administrativo.

Os administradores administrativos com âmbito unitário podem utilizar o centro de administração Microsoft 365 para a gestão básica dos utilizadores nas suas unidades administrativas. Um administrador de grupo com âmbito de unidade administrativa pode gerir grupos utilizando o PowerShell, o Microsoft Graph e os centros de administração microsoft 365.

>[!Note]
>Apenas as funcionalidades descritas nesta secção estão disponíveis no centro de administração microsoft 365. Não existem funcionalidades de nível de organização para um papel AD Azure com âmbito de unidade administrativa.

As secções seguintes descrevem o atual apoio aos cenários da unidade administrativa.

### <a name="administrative-unit-management"></a>Gestão de unidades administrativas

| Permissões |   Gráfico/PowerShell   | Portal Azure AD | Microsoft 365 admin center (Centro de administração do Microsoft 365) |
| --- | --- | --- | --- |
| Criação e eliminação de unidades administrativas   |    Suportado    |   Suportado   |    Não suportado |
| Adicionar e remover individualmente os membros da unidade administrativa    |   Suportado    |   Suportado   |    Não suportado |
| Adicionar e remover membros da unidade administrativa a granel utilizando ficheiros CSV   |    Não suportado     |  Suportado   |    Nenhum plano para apoiar |
| Atribuição de administradores administrativos com âmbito de unidade  |     Suportado    |   Suportado    |   Não suportado |
| Adicionar e remover membros da unidade administrativa dinamicamente com base em atributos | Não suportado | Não suportado | Não suportado

### <a name="user-management"></a>Gestão de utilizadores

| Permissões |   Gráfico/PowerShell   | Portal Azure AD | Microsoft 365 admin center (Centro de administração do Microsoft 365) |
| --- | --- | --- | --- |
| Gestão administrativa de propriedades, senhas e licenças de utilização   |    Suportado     |  Suportado   |   Suportado |
| Bloqueio e desbloqueio de inscrições administrativas por unidades    |   Suportado   |    Suportado   |    Suportado |
| Gestão administrativa unitária das credenciais de autenticação multifactor do utilizador   |    Suportado   |   Suportado   |   Não suportado |

### <a name="group-management"></a>Gestão de grupos

| Permissões |   Gráfico/PowerShell   | Portal Azure AD | Microsoft 365 admin center (Centro de administração do Microsoft 365) |
| --- | --- | --- | --- |
| Gestão administrativa de propriedades e membros do grupo     |  Suportado   |    Suportado    |  Não suportado |
| Gestão administrativa de âmbito unitário de licenciamento de grupo   |    Suportado  |    Suportado   |   Não suportado |

As unidades administrativas aplicam âmbito apenas às permissões de gestão. Não impedem que membros ou administradores utilizem as suas [permissões de utilizador predefinidas](../fundamentals/users-default-permissions.md) para navegar em outros utilizadores, grupos ou recursos fora da unidade administrativa. No centro de administração Microsoft 365, os utilizadores fora das unidades administrativas de um administrador de âmbito são filtrados. Mas pode navegar em outros utilizadores no portal AD AZure, PowerShell e outros serviços da Microsoft.

## <a name="next-steps"></a>Passos seguintes

- [Gerir unidades administrativas](admin-units-manage.md)
- [Gerir utilizadores em unidades administrativas](admin-units-add-manage-users.md)
- [Gerir grupos em unidades administrativas](admin-units-add-manage-groups.md)
- [Atribuir funções âmbito a uma unidade administrativa](admin-units-assign-roles.md)
