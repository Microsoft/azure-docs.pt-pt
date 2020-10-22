---
title: Unidades administrativas em Diretório Ativo Azure Microsoft Docs
description: Utilização de unidades administrativas para uma delegação mais granular de permissões no Diretório Ativo do Azure
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
ms.openlocfilehash: bc7f28f9369d54c65f633bd24da5443415a80625
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379301"
---
# <a name="administrative-units-in-azure-active-directory"></a>Unidades administrativas em Diretório Ativo Azure

Este artigo descreve unidades administrativas no Azure Ative Directory (Azure AD). Uma unidade administrativa é um recurso AD Azure que pode ser um recipiente para outros recursos AZure AD. Uma unidade administrativa pode conter apenas utilizadores e grupos.

As unidades administrativas permitem-lhe conceder permissões administrativas restritas a um departamento, região ou outro segmento da sua organização que define. Pode utilizar unidades administrativas para delegar permissões a administradores regionais ou para definir a política a nível granular. Por exemplo, um administrador de conta de utilizador poderia atualizar informações de perfil, redefinir palavras-passe e atribuir licenças para utilizadores apenas na sua unidade administrativa.

Por exemplo, delegando a especialistas regionais de apoio, o papel [de Administrador de Helpdesk](permissions-reference.md#helpdesk-administrator) restringido à gestão apenas dos utilizadores da região que apoiam.

## <a name="deployment-scenario"></a>Cenário de implementação

Restringir o âmbito administrativo utilizando unidades administrativas pode ser útil em organizações que são constituídas por divisões independentes de qualquer tipo. Considere o exemplo de uma grande universidade que é composta por muitas escolas autónomas (School of Business, School of Engineering, e assim por diante) que cada um tem uma equipa de administradores de TI que controlam o acesso, gerem os utilizadores e definem políticas para a sua escola. Um administrador central poderia:

- Criar uma função com permissões administrativas sobre apenas utilizadores da AZure AD na unidade administrativa da Business School
- Criar uma unidade administrativa para a Escola de Negócios
- Povoar a unidade de administração com apenas os alunos e funcionários da escola de negócios
- Adicione a equipa de TI da Business school ao papel com o seu âmbito

## <a name="license-requirements"></a>Requisitos de licença

A utilização de unidades administrativas requer uma licença Azure Ative Directory Premium para cada administração administrativa e licenças Azure Ative Directory Free para membros da unidade administrativa. Para mais informações, consulte [Começar com a Azure AD Premium.](../fundamentals/active-directory-get-started-premium.md)

## <a name="manage-administrative-units"></a>Gerir unidades administrativas

Pode gerir unidades administrativas utilizando o portal Azure, cmdlets e scripts PowerShell ou o Microsoft Graph. Pode consultar a nossa documentação para mais detalhes:

- [Criar, remover, preencher e adicionar funções às unidades administrativas](admin-units-manage.md): Complete os procedimentos de como fazer
- [Trabalhar com unidades de administração](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true): Como trabalhar com unidades administrativas utilizando o PowerShell
- [Suporte de gráfico de unidade administrativa](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true): Documentação detalhada no Microsoft Graph para unidades administrativas.

### <a name="planning-your-administrative-units"></a>Planejando as suas unidades administrativas

As unidades administrativas podem ser usadas para agrupar logicamente os recursos Azure AD. Por exemplo, para uma organização cujo departamento de TI está disperso globalmente, pode fazer sentido criar unidades administrativas que definam essas fronteiras geográficas. Num outro cenário em que uma organização multinacional tem diferentes "sub-organizações", semi-autónomas em operações, cada sub-organização pode ser representada por uma unidade administrativa.

Os critérios sobre os quais são criadas unidades administrativas serão guiados pelos requisitos únicos de uma organização. As Unidades Administrativas são uma forma comum de definir a estrutura em todos os serviços da Microsoft 365. Recomendamos que prepare as suas unidades administrativas com a sua utilização em todos os serviços da Microsoft 365 em mente. Pode obter o valor máximo das unidades administrativas quando pode associar recursos comuns através da Microsoft 365 sob uma unidade administrativa.

Pode esperar que a criação de unidades administrativas na organização passe pelas seguintes fases:

1. Adoção Inicial: A sua organização começará a criar unidades administrativas com base em critérios iniciais e o número de unidades administrativas aumentará à medida que os critérios são refinados.
1. Poda: Uma vez definidos os critérios, serão eliminadas as unidades administrativas que já não são necessárias.
1. Estabilização: A sua estrutura organizacional está bem definida e o número de unidades administrativas não vai mudar significativamente em curtas durações.

## <a name="currently-supported-scenarios"></a>Cenários atualmente apoiados

Os administradores globais ou os administradores de funções privilegiados podem usar o portal AD AZure para criar unidades administrativas, adicionar utilizadores como membros de unidades administrativas e, em seguida, atribuir pessoal de TI a funções de administrador administrativo. Os administradores administrativos com âmbito de unidade podem então utilizar o centro de administração Microsoft 365 para uma gestão básica dos utilizadores nas suas unidades administrativas.

Além disso, os grupos podem ser adicionados como membros da unidade administrativa, e um administrador de grupo de administração pode geri-los usando o PowerShell, o Microsoft Graph e o portal AD AD.

O quadro abaixo descreve o apoio atual aos cenários da unidade administrativa.

### <a name="administrative-unit-management"></a>Gestão de unidades administrativas

Permissões |   MS Graph/PowerShell   | Portal Azure AD | Microsoft 365 admin center (Centro de administração do Microsoft 365)
----------- | ----------------------- | --------------- | -----------------
Criação e eliminação de unidades administrativas   |    Suportado    |   Suportado   |    Não suportado
Adicionar e remover individualmente os membros da unidade administrativa    |   Suportado    |   Suportado   |    Não suportado
Adição e remoção a granel de membros da unidade administrativa utilizando o ficheiro .csv   |    Não suportado     |  Suportado   |    Nenhum plano para apoiar
Atribuição de administradores administrativos com âmbito de unidade  |     Suportado    |   Suportado    |   Não suportado
Adicionar e remover membros da AU dinamicamente com base em atributos | Não suportado | Não suportado | Não suportado

### <a name="user-management"></a>Gestão de utilizadores

Permissões |   MS Graph/PowerShell   | Portal Azure AD | Microsoft 365 admin center (Centro de administração do Microsoft 365)
----------- | ----------------------- | --------------- | -----------------
gestão administrativa de propriedades de utilizadores, senhas, licenças   |    Suportado     |  Suportado   |   Suportado
bloqueio e desbloqueio das inscrições dos utilizadores    |   Suportado   |    Suportado   |    Suportado
gestão administrativa das credenciais de MFA dos utilizadores   |    Suportado   |   Suportado   |   Não suportado

### <a name="group-management"></a>Gestão de grupos

Permissões |   MS Graph/PowerShell   | Portal Azure AD | Microsoft 365 admin center (Centro de administração do Microsoft 365)
----------- | ----------------------- | --------------- | -----------------
gestão administrativa de propriedades e membros do grupo     |  Suportado   |    Suportado    |  Não suportado
gestão administrativa do licenciamento do grupo   |    Suportado  |    Suportado   |   Não suportado


As unidades administrativas aplicam âmbito apenas às permissões de gestão. Não impedem que membros ou administradores utilizem as suas [permissões de utilizador predefinidas](../fundamentals/users-default-permissions.md) para navegar em outros utilizadores, grupos ou recursos fora da unidade administrativa. No centro de administração microsoft 365, os utilizadores fora das unidades administrativas de um administrador de âmbito são filtrados, mas pode navegar em outros utilizadores no portal AD AD, PowerShell e outros serviços da Microsoft.

## <a name="next-steps"></a>Passos seguintes

- [Gestão de AUs](admin-units-manage.md)
- [Gerir utilizadores em AUs](admin-units-add-manage-users.md)
- [Gerir grupos em AUs](admin-units-add-manage-groups.md)
- [Atribuir funções âmbito a uma AU](admin-units-assign-roles.md)
