---
title: Pré-visualização de gestão de unidades administrativas no Azure Active Directory
description: A utilização de unidades administrativas para delegação mais granular de permissões no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.openlocfilehash: fe3f41e95cc3aa4f22ff2cdea24388f7e9974755
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181202"
---
# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Gestão de unidades administrativas no Azure AD - pré-visualização pública
Este artigo descreve as unidades administrativas – um novo contentor do Azure Active Directory de recursos que pode ser utilizado para delegar permissões administrativas ao longo de subconjuntos de utilizadores e aplicar políticas para um subconjunto de utilizadores. No Azure Active Directory, unidades administrativas permitem que os administradores de centrais para delegar permissões a administradores regionais ou definir a política a um nível granular.

Isto é útil em organizações com divisões independentes, por exemplo, uma universidade grande que é constituído por muitos autónomas instituições de ensino (Business school, escola de engenharia e assim por diante) que são independentes entre si. Essas divisões tem seus próprios administradores de TI que controlam o acesso, gerir utilizadores e definir políticas especificamente para a respetiva divisão. Os administradores central quer ser capaz de conceder estes divisão permissões dos administradores sobre os utilizadores no respetivos divisões específicos. Mais especificamente, com este exemplo, um administrador central pode, por exemplo, criar uma unidade administrativa para uma escola específica (Business school) e preenchê-lo com apenas os utilizadores da instituição de ensino de negócios. Em seguida, um administrador central pode adicionar a escola de negócios a equipe de TI a uma função de âmbito, em outras palavras, conceda a equipe de TI de permissões administrativas de instituição de ensino comercial apenas sobre a unidade administrativa de instituição de ensino de negócios.

> [!IMPORTANT]
> Para utilizar unidades administrativas requer que o administrador no âmbito do unidade administrativa para ter uma licença do Azure Active Directory Premium e as licenças do Azure Active Directory básico para todos os utilizadores na unidade administrativa. Para obter mais informações, consulte [introdução ao Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).
>


Do ponto de vista do administrador central, uma unidade administrativa é um objeto de diretório que pode ser criado e preenchido com os recursos. **Nesta versão de pré-visualização, esses recursos podem ser apenas os utilizadores.** Depois de criado e preenchido, a unidade administrativa pode servir como um âmbito para restringir as permissões concedidas apenas sobre os recursos contidos na unidade administrativa.

## <a name="managing-administrative-units"></a>Gerir unidades administrativas
Nesta versão de pré-visualização, pode criar e gerir unidades administrativas utilizando os cmdlets do módulo Azure Active Directory para Windows PowerShell. Para saber mais sobre como fazê-lo, consulte [trabalhar com unidades administrativas](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Para obter mais informações sobre os requisitos de software e instalar o módulo do Azure AD e para obter informações sobre os cmdlets do módulo do Azure AD para o gerenciamento de unidades administrativas, incluindo sintaxe, descrições de parâmetros e exemplos, veja [Active Directory do Azure PowerShell do](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Passos Seguintes
[Edições do Azure Active Directory](../fundamentals/active-directory-whatis.md)
