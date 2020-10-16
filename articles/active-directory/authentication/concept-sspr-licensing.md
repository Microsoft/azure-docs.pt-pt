---
title: Redefinição da palavra-passe de autosserviço da licença - Azure Ative Directory
description: Saiba mais sobre a diferença Azure Ative Directory autosserviço autosserviço reconfigurar os requisitos de licenciamento
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dd17e446766514fa90f8beaaab104bbbd2f389f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965034"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Requisitos de licenciamento para reposição da palavra-passe de autosserviço do Azure Ative Directory

Para reduzir as chamadas de secretária de ajuda e a perda de produtividade quando um utilizador não pode iniciar seditação no seu dispositivo ou numa aplicação, as contas de utilizador no Azure Ative Directory (Azure AD) podem ser ativadas para o reset da palavra-passe de autosserviço (SSPR). As funcionalidades que compõem a SSPR incluem a alteração da palavra-passe, o reset, o desbloqueio e a gravação para um diretório no local. As funcionalidades Basic SSPR estão disponíveis no Microsoft 365 Business Standard ou superior e todos os SKUs AD Premium Azure sem custos.

Este artigo detalha as diferentes formas de reposição de senha de autosserviço pode ser licenciada e usada. Para obter detalhes específicos sobre preços e faturação, consulte a página de preços da [AD Azure](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Compare edições e funcionalidades

A SSPR é licenciada por utilizador. Para manter a conformidade, as organizações são obrigadas a atribuir a licença adequada aos seus utilizadores.

O quadro que se segue descreve os diferentes cenários SSPR para a alteração de passwords, reset ou writeback no local, e que os SKUs fornecem a funcionalidade.

| Funcionalidade | Azure AD Gratuito | Microsoft 365 Business Standard | Microsoft 365 Business Premium | Azure AD Premium P1 ou P2 |
| --- |:---:|:---:|:---:|:---:|
| **Alteração da palavra-passe do utilizador apenas na nuvem**<br />Quando um utilizador em Azure AD conhece a sua palavra-passe e quer mudá-la para algo novo. | ● | ● | ● | ● |
| **Reset da palavra-passe do utilizador apenas na nuvem**<br />Quando um utilizador em Azure AD esqueceu a sua palavra-passe e precisa de a redefinir. | | ● | ● | ● |
| **Alteração de senha de utilizador híbrida ou reset com writeback on-prem**<br />Quando um utilizador em Azure AD que é sincronizado a partir de um diretório no local usando o Azure AD Connect quer alterar ou redefinir a sua palavra-passe e também escrever a nova palavra-passe de volta para o pré-pré-m. | | | ● | ● |

> [!WARNING]
> Os planos de licenciamento autónomos do Microsoft 365 Basic e Standard não suportam sSPR com writeback no local. A funcionalidade de writeback no local requer Azure AD Premium P1, Premium P2 ou Microsoft 365 Business Premium.

Para obter informações adicionais sobre o licenciamento, incluindo custos, consulte as seguintes páginas:

* [Preços do Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Funcionalidades e capacidades do Azure Ative Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Permitir licenciamentos em grupo ou no utilizador

A Azure AD apoia o licenciamento baseado em grupo. Os administradores podem atribuir licenças a granel a um grupo de utilizadores, em vez de as atribuir uma de cada vez. Para mais informações, consulte [Atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Alguns serviços da Microsoft não estão disponíveis em todos os locais. Antes de uma licença poder ser atribuída a um utilizador, o administrador deve especificar a propriedade **de localização de utilização** no utilizador. A atribuição de licenças pode ser feita na secção Definições de Perfil do **Utilizador**  >  **Profile**  >  **Settings** no portal Azure. *Quando utiliza a atribuição de licença de grupo, qualquer utilizadores sem localização de utilização especificada herda a localização do diretório.*

## <a name="next-steps"></a>Passos seguintes

Para começar com a SSPR, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Permitir o reset da palavra-passe de autosserviço (SSPR)](tutorial-enable-sspr.md)