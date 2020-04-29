---
title: Gestão de unidades administrativas (pré-visualização) - Azure AD / Microsoft Docs
description: Utilização de unidades administrativas para uma delegação mais granular de permissões no Diretório Ativo do Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12101b170066bc52e75f4aa281c6f1c21283c004
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406475"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gestão de unidades administrativas em Diretório Ativo Azure (pré-visualização)

Este artigo descreve unidades administrativas em Azure Ative Directory (Azure AD). Uma unidade administrativa é um recurso Azure AD que pode ser um recipiente para outros recursos da AD Azure. Neste comunicado de pré-visualização, uma unidade administrativa só pode conter utilizadores e grupos.

As unidades administrativas permitem-lhe conceder permissões de administração restritas a um departamento, região ou outro segmento da sua organização que define. Pode utilizar unidades administrativas para delegar permissões a administradores regionais ou definir a política a um nível granular. Por exemplo, um administrador de conta utilizador poderia atualizar informações de perfil, redefinir senhas e atribuir licenças para utilizadores apenas na sua unidade administrativa.

 Por exemplo, delegando aos especialistas de apoio regional a função de Administrador de [Helpdesk](directory-assign-admin-roles.md#helpdesk-administrator) restrita à gestão apenas dos utilizadores na região que apoiam.

## <a name="deployment-scenario"></a>Cenário de implementação

Restringir o âmbito administrativo utilizando unidades administrativas pode ser útil em organizações que são constituídas por divisões independentes de qualquer tipo. Considere o exemplo de uma grande universidade que é composta por muitas escolas autónomas (School of Business, School of Engineering, e assim por diante) que cada uma tem uma equipa de administradores de TI que controlam o acesso, gerem os utilizadores e estabelecem políticas para a sua escola. Um administrador central poderia:

- Criar um papel com permissões administrativas sobre apenas utilizadores de AD Azure na unidade administrativa da escola de negócios
- Criar uma unidade administrativa para a Escola de Negócios
- Povoar a unidade de administração apenas com os alunos e funcionários da escola de negócios
- Adicione a equipa de TI da business school ao papel com o seu âmbito

## <a name="license-requirements"></a>Requisitos de licença

A utilização de unidades administrativas requer uma licença Azure Ative Directory Premium para cada administrador de unidade administrativa, e licenças Azure Ative Directory Free para membros da unidade administrativa. Para mais informações, consulte [Começar com o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Gerir unidades administrativas

Nesta versão de pré-visualização, pode gerir unidades administrativas utilizando o portal Azure, powerShell cmdlets e scripts, ou o Microsoft Graph. Pode consultar a nossa documentação para mais detalhes:

- [Criar, remover, povoar e adicionar funções às unidades administrativas](roles-admin-units-manage.md): Concluir procedimentos de como fazer
- [Trabalhar com unidades de administração](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0): Como trabalhar com unidades administrativas usando a PowerShell
- [Suporte do gráfico](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta)da unidade administrativa : Documentação detalhada no Microsoft Graph para unidades administrativas.

### <a name="planning-your-administrative-units"></a>Planejando as suas unidades administrativas

As unidades administrativas podem ser usadas para agrupar logicamente os recursos da AD Azure. Por exemplo, para uma organização cujo departamento de TI está dispersa globalmente, talvez faça sentido criar unidades administrativas que definam essas fronteiras geográficas. Num outro cenário em que uma organização multinacional tem diferentes "suborganizações", semi-autónomas em operações, cada suborganização pode ser representada por uma unidade administrativa.

Os critérios em que as unidades administrativas são criadas serão guiados pelos requisitos únicos de uma organização. As Unidades Administrativas são uma forma comum de definir a estrutura através dos serviços M365. Recomendamos que prepare as suas unidades administrativas com a sua utilização em todos os serviços M365 em mente. Pode obter o valor máximo das unidades administrativas quando pode associar recursos comuns através da M365 sob uma unidade administrativa.

Pode esperar que a criação de unidades administrativas na organização passe pelas seguintes fases:

1. Adoção Inicial: A sua organização começará a criar unidades administrativas com base em critérios iniciais e o número de unidades administrativas aumentará à medida que os critérios forem refinados.
1. Poda: Uma vez definidos os critérios, as unidades administrativas que já não são necessárias serão suprimidas.
1. Estabilização: A sua estrutura organizacional está bem definida e o número de unidades administrativas não vai mudar significativamente ao longo de curtos períodos de tempo.

## <a name="currently-supported-scenarios"></a>Atualmente apoiados cenários

Administradores globais ou administradores de funções privilegiadas podem usar o portal Azure AD para criar unidades administrativas, adicionar utilizadores como membros de unidades administrativas e, em seguida, atribuir pessoal de TI a funções administrativas de administrador de aplicação da unidade. Os administradores de aplicação da unidade administrativa podem então utilizar o portal do Office 365 para a gestão básica dos utilizadores nas suas unidades administrativas.

Além disso, os grupos podem ser adicionados como membros da unidade administrativa, e um administrador de grupo de administração pode geri-los usando PowerShell, o Microsoft Graph e o portal Azure AD.

O quadro abaixo descreve o suporte atual para cenários de unidade administrativa.

### <a name="administrative-unit-management"></a>Gestão de unidades administrativas

Permissões |   Gráfico/PowerShell   | Portal Azure AD | Centro de administração do Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Criação e aleitamento de unidades administrativas   |    Suportado    |   Suportado   |    Não suportado
Adicionar e remover individualmente os membros da unidade administrativa    |   Suportado    |   Suportado   |    Não suportado
A granel adicionando e removendo membros da unidade administrativa utilizando o ficheiro .csv   |    Não suportado     |  Suportado   |    Nenhum plano para apoiar
Atribuição de administradores de unidades administrativas  |     Suportado    |   Suportado    |   Não suportado
Adicionar e remover os membros da UA dinamicamente com base em atributos | Não suportado | Não suportado | Não suportado

### <a name="user-management"></a>Gestão de utilizadores

Permissões |   Gráfico/PowerShell   | Portal Azure AD | Centro de administração do Microsoft 365
----------- | ----------------------- | --------------- | -----------------
gestão unitária administrativa das propriedades dos utilizadores, palavras-passe, licenças   |    Suportado     |  Suportado   |   Suportado
bloqueio e desbloqueio de unidades administrativas    |   Suportado   |    Suportado   |    Suportado
gestão unitária administrativa das credenciais de MFA dos utilizadores   |    Suportado   |   Suportado   |   Não suportado

### <a name="group-management"></a>Gestão de grupos

Permissões |   Gráfico/PowerShell   | Portal Azure AD | Centro de administração do Microsoft 365
----------- | ----------------------- | --------------- | -----------------
gestão unitária administrativa de propriedades de grupo e membros     |  Suportado   |    Suportado    |  Não suportado
gestão unitária administrativa do licenciamento do grupo   |    Suportado  |    Suportado   |   Não suportado

> [!NOTE]
>
> Os administradores com um âmbito de unidade administrativa não conseguem gerir regras dinâmicas de adesão ao grupo.

As unidades administrativas aplicam o seu âmbito apenas às permissões de gestão. Não impedem que membros ou administradores utilizem as suas [permissões de utilizador padrão](../fundamentals/users-default-permissions.md) para navegar em outros utilizadores, grupos ou recursos fora da unidade administrativa. No portal Office 365, os utilizadores fora das unidades administrativas de um administrador são filtrados, mas pode navegar por outros utilizadores no portal Azure AD, PowerShell e outros serviços da Microsoft.

## <a name="next-steps"></a>Passos seguintes

- [Gestão da AUs](roles-admin-units-manage.md)
- [Gerir utilizadores em UA](roles-admin-units-add-manage-users.md)
- [Gerir grupos em UA](roles-admin-units-add-manage-groups.md)
- [Atribuir funções de âmbito a uma UA](roles-admin-units-assign-roles.md)