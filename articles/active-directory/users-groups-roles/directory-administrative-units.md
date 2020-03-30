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
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028418"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gestão de unidades administrativas em Diretório Ativo Azure (pré-visualização)

Este artigo descreve unidades administrativas em Azure Ative Directory (Azure AD). Uma unidade administrativa é um recurso Azure AD que pode ser um recipiente para outros recursos da AD Azure. Neste lançamento de pré-visualização, estes recursos podem ser apenas utilizadores. Por exemplo, um administrador administrativo de conta de utilizador com âmbito de unidade administrativa pode atualizar informações de perfil, redefinir senhas e atribuir licenças para utilizadores apenas na sua unidade administrativa.

Pode utilizar unidades administrativas para delegar permissões administrativas sobre subconjuntos de utilizadores e aplicar políticas a um subconjunto de utilizadores. Pode utilizar unidades administrativas para delegar permissões a administradores regionais ou definir a política a um nível granular.

## <a name="deployment-scenario"></a>Cenário de implementação

As unidades administrativas podem ser úteis em organizações com divisões independentes. Considere o exemplo de uma grande universidade que é composta por muitas escolas autónomas (School of Business, School of Engineering, e assim por diante) que cada uma tem os seus próprios administradores de TI que controlam o acesso, gerem os utilizadores e estabelecem políticas para a sua escola. Um administrador central poderia criar uma unidade administrativa para a Escola de Negócios e povoá-la apenas com os alunos e funcionários da escola de negócios. Em seguida, o administrador central pode adicionar o pessoal de TI da Business school a um papel de âmbito que concede permissões administrativas sobre apenas utilizadores de AD Azure na unidade administrativa da escola de negócios.

## <a name="license-requirements"></a>Requisitos de licença

Para utilizar unidades administrativas é necessária uma licença Azure Ative Directory Premium para cada administrador de unidade administrativa. Para mais informações, consulte [Começar com o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="managing-administrative-units"></a>Gestão de unidades administrativas

Nesta versão de pré-visualização, a única forma de criar e gerir unidades administrativas é utilizar o Módulo de Diretório Ativo Azure para cmdlets Windows PowerShell, conforme descrito em [Trabalhar com Unidades Administrativas](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Para obter mais informações sobre os requisitos de software e a instalação do módulo Azure AD, e para informações de referência sobre o Módulo AD Azure cmdlets para gestão de unidades administrativas, incluindo sintaxe, descrições de parâmetros e exemplos, consulte [o Azure Ative Directory PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

## <a name="next-steps"></a>Passos seguintes

[Edições azure ative diretório](../fundamentals/active-directory-whatis.md)
