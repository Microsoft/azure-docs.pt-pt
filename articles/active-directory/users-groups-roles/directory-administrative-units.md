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
ms.openlocfilehash: 7b4bdced50f806367a53881d5ef0abd0a3710496
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736788"
---
# <a name="administrative-units-management-in-azure-active-directory-public-preview"></a>Gerenciamento de unidades administrativas no Azure Active Directory (visualização pública)

Este artigo descreve as unidades administrativas no Azure Active Directory (AD do Azure), que são um contêiner de recursos que podem ser usados para delegar permissões administrativas em subconjuntos de usuários e aplicar políticas a um subconjunto de usuários. No Azure AD, as unidades administrativas permitem que os administradores centrais deleguem permissões a administradores regionais ou definam a política em um nível granular.

Isso é útil em organizações com divisões independentes, por exemplo, uma grande universidade composta por muitas escolas autônomas (escola comercial, escola de engenharia e assim por diante) que são independentes umas das outras. Essas divisões têm seus próprios administradores de ti que controlam o acesso, gerenciam usuários e definem políticas especificamente para sua divisão. Os administradores centrais desejam poder conceder essas permissões de administradores divisões aos usuários em suas divisões específicas. Mais especificamente, usando este exemplo, um administrador central pode, por exemplo, criar uma unidade administrativa para uma escola específica (escola comercial) e preenchê-la apenas com os usuários da escola de negócios. Em seguida, um administrador central pode adicionar a equipe de ti da escola de negócios a uma função com escopo definido, em outras palavras, conceder à equipe de ti as permissões administrativas do estudante de negócios somente na unidade administrativa da escola de negócios.

> [!IMPORTANT]
> Para usar as unidades administrativas, é necessário que o administrador da unidade administrativa tenha uma licença Azure Active Directory Premium. Para obter mais informações, consulte [introdução ao Azure ad Premium](../fundamentals/active-directory-get-started-premium.md).
>

Do ponto de vista do administrador central, uma unidade administrativa é um objeto de diretório que pode ser criado e populado com recursos. **Nesta versão de visualização, esses recursos podem ser apenas usuários.** Depois de criado e populado, a unidade administrativa pode ser usada como um escopo para restringir a permissão concedida somente sobre os recursos contidos na unidade administrativa.

## <a name="managing-administrative-units"></a>Gerenciando unidades administrativas

Nesta versão de visualização, você pode criar e gerenciar unidades administrativas usando o módulo Azure Active Directory para cmdlets do Windows PowerShell. Para saber mais sobre como fazer isso, consulte [trabalhando com unidades administrativas](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Para obter mais informações sobre os requisitos de software e instalar o módulo do Azure AD, e para obter informações sobre os cmdlets do módulo do Azure AD para gerenciar unidades administrativas, incluindo sintaxe, descrições de parâmetros e exemplos, consulte [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Passos Seguintes

[Azure Active Directory edições](../fundamentals/active-directory-whatis.md)
