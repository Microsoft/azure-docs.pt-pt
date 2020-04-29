---
title: Gerir acesso à gestão azure com acesso condicional em Azure AD
description: Saiba utilizar o Acesso Condicional em Azure AD para gerir o acesso à gestão azure.
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
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77137417"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gerir acesso à gestão azure com acesso condicional

> [!CAUTION]
> Certifique-se de que compreende como funciona o Acesso Condicionado antes de criar uma política de gestão de acesso à gestão do Azure. Certifique-se de que não cria condições que possam bloquear o seu próprio acesso ao portal.

O Acesso Condicional no Diretório Ativo azure (Azure AD) controla o acesso a aplicações em nuvem com base em condições específicas que especifica. Para permitir o acesso, cria políticas de Acesso Condicional que permitem ou bloqueiam o acesso com base no facto de os requisitos da apólice serem ou não cumpridos. 

Normalmente, utiliza o Acesso Condicional para controlar o acesso às suas aplicações em nuvem. Também pode configurar políticas para controlar o acesso à gestão do Azure com base em determinadas condições (tais como risco de entrada, localização ou dispositivo) e para fazer cumprir requisitos como a autenticação de vários fatores.

Para criar uma política para a gestão do Azure, selecione a **Microsoft Azure Management** sob **aplicações Cloud** ao escolher a app para aplicar a política.

![Acesso Condicional para a gestão do Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

A política que cria aplica-se a todos os pontos finais de gestão do Azure, incluindo os seguintes:

- Portal do Azure
- Fornecedor de Gestor de Recursos Azure
- APIs de Gestão de Serviços Clássicos
- Azure PowerShell
- Portal de administrador de subscrições do Estúdio Visual
- DevOps do Azure
- Portal azure data factory

Note que a política se aplica ao Azure PowerShell, que chama a API do Gestor de Recursos Azure. Não se aplica ao [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), que chama microsoft graph.


Para obter mais informações sobre como configurar e utilizar o Acesso Condicional, consulte [acesso condicional no Diretório Ativo Azure](../active-directory/active-directory-conditional-access-azure-portal.md).
