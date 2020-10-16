---
title: Gerir o acesso à gestão da Azure com Acesso Condicional em Azure AD
description: Saiba como utilizar o Acesso Condicional em Azure AD para gerir o acesso à gestão do Azure.
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
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 2a52635dbaa7a76034f3a535b099320a901e8c07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83758780"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gerir o acesso à gestão do Azure com Acesso Condicional

> [!CAUTION]
> Certifique-se de que compreende como funciona o Acesso Condicional antes de criar uma política para gerir o acesso à gestão do Azure. Certifique-se de que não cria condições que possam bloquear o seu próprio acesso ao portal.

O Acesso Condicional no Diretório Ativo Azure (Azure AD) controla o acesso a aplicações na nuvem com base em condições específicas que especifique. Para permitir o acesso, cria políticas de Acesso Condicional que permitem ou bloqueiam o acesso com base no facto de os requisitos da apólice serem ou não cumpridos. 

Normalmente, utiliza acesso condicional para controlar o acesso às suas aplicações na nuvem. Também pode configurar políticas para controlar o acesso à gestão do Azure com base em determinadas condições (como risco de inscrição, localização ou dispositivo) e para impor requisitos como a autenticação de vários fatores.

Para criar uma política para a gestão do Azure, seleciona **o Microsoft Azure Management** em **aplicações Cloud** ao escolher a app para aplicar a política.

![Acesso Condicional para a gestão do Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

A política que cria aplica-se a todos os pontos finais de gestão da Azure, incluindo os seguintes:

- Portal do Azure
- Fornecedor de Gestor de Recursos Azure
- APIs de Gestão de Serviços Clássicos
- Azure PowerShell
- Portal de administrador de subscrições de Estúdio Visual
- DevOps do Azure
- Portal Azure Data Factory

Note que a política se aplica ao Azure PowerShell, que chama a API do Gestor de Recursos Azure. Não se aplica ao [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), que chama Microsoft Graph.

Para obter mais informações sobre como configurar uma política de amostragem para permitir o acesso condicional à gestão do Microsoft Azure, consulte o artigo [Acesso Condicional: Exija MFA para gestão do Azure](../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).
