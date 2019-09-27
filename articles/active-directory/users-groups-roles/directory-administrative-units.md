---
title: Gerenciamento de unidades administrativas (versão prévia)-Azure Active Directory | Microsoft Docs
description: Usando unidades administrativas para delegação mais granular de permissões no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b61186a876af90c812ec7faf41fa9f5b14bf4e
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336928"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gerenciamento de unidades administrativas no Azure Active Directory (versão prévia)

Este artigo descreve as unidades administrativas no Azure Active Directory (AD do Azure). Uma unidade administrativa é um recurso do Azure AD que pode ser um contêiner para outros recursos do Azure AD. Nesta versão de visualização, esses recursos podem ser apenas usuários. Por exemplo, um administrador de conta de usuário com escopo de unidade administrativa pode atualizar informações de perfil, redefinir senhas e atribuir licenças para usuários somente em sua unidade administrativa.

Você pode usar unidades administrativas para delegar permissões administrativas sobre subconjuntos de usuários e aplicar políticas a um subconjunto de usuários. Você pode usar unidades administrativas para delegar permissões para administradores regionais ou para definir a política em um nível granular.

## <a name="deployment-scenario"></a>Cenário de implantação

As unidades administrativas podem ser úteis em organizações com divisões independentes. Considere o exemplo de uma Universidade grande que é composta por muitas escolas autônomas (escola de negócios, escola de engenharia e assim por diante) que cada uma tem seus próprios administradores de ti que controlam o acesso, gerenciam usuários e definem políticas para sua escola. Um administrador central poderia criar uma unidade administrativa para a escola de negócios e preenchê-la apenas com os alunos e a equipe da escola de negócios. Em seguida, o administrador central pode adicionar a equipe de ti da escola de negócios a uma função com escopo que concede permissões administrativas somente a usuários do Azure AD na unidade administrativa da escola de negócios.

## <a name="license-requirements"></a>Requisitos de licença

O uso de unidades administrativas requer uma licença de Azure Active Directory Premium para cada administrador de unidade administrativa. Para obter mais informações, consulte [introdução ao Azure ad Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="managing-administrative-units"></a>Gerenciando unidades administrativas

Nesta versão de visualização, a única maneira de criar e gerenciar unidades administrativas é usar o módulo Azure Active Directory para cmdlets do Windows PowerShell, conforme descrito em [trabalhando com unidades administrativas](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Para obter mais informações sobre requisitos de software e como instalar o módulo do Azure AD, e para obter informações de referência sobre os cmdlets de módulo do Azure AD para gerenciar unidades administrativas, incluindo sintaxe, descrições de parâmetros e exemplos, consulte [Azure active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Passos seguintes

[Azure Active Directory edições](../fundamentals/active-directory-whatis.md)
