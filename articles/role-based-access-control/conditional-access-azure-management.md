---
title: Gerir o acesso a gestão do Azure com o acesso condicional no Azure Active Directory
description: Saiba como utilizar o acesso condicional no Azure AD para gerir o acesso a gestão do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 4ee58835a5055f5382cefa3a049424ffe97a01c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122950"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gerir o acesso a gestão do Azure com o acesso condicional

Acesso condicional no Azure Active Directory (Azure AD) controla o acesso a aplicações com base em condições específicas que especificou na cloud. Para permitir o acesso, criar políticas de acesso condicional que permitem ou bloquear o acesso com base em se é ou não são cumpridos os requisitos na política. 

Normalmente, utilizar o acesso condicional para controlar o acesso às suas aplicações na cloud. Também pode configurar políticas para controlar o acesso a gestão do Azure com base em determinadas condições (por exemplo, o risco de início de sessão, localização ou dispositivo) e impor requisitos, como a autenticação multifator.

Para criar uma política de gestão do Azure, selecione **Microsoft Azure Management** sob **aplicações na Cloud** ao escolher a aplicação na qual pretende aplicar a política.

![Acesso Condicional para a gestão do Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

A política que criar se aplica a todos os pontos finais de gestão do Azure, incluindo o portal do Azure, o fornecedor do Azure Resource Manager, o clássico portal de administrador de subscrições de APIs de gestão de serviço, o Azure PowerShell e o Visual Studio. Tenha em atenção que a política se aplica ao Azure PowerShell, que chama a API do Azure Resource Manager. Não é aplicável a [do Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), que chama o Microsoft Graph.

> [!CAUTION]
> Certifique-se de que compreende como funciona o acesso condicional antes de configurar uma política para gerir o acesso a gestão do Azure. Certifique-se de que não criar condições que podem bloquear o seu próprio acesso ao portal.

Para obter mais informações sobre como configurar e utilizar o acesso condicional, consulte [acesso condicional no Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).