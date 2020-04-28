---
title: Reset de senha de autosserviço de licença - Diretório Ativo Azure
description: Conheça a diferença De autosserviço do Azure Ative Diretório redefinir requisitos de licenciamento
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca11af061e37cf4f804ce2d7ceed72a9448294
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393071"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Requisitos de licenciamento para redefinição da palavra-passe de autosserviço do Diretório Ativo Azure

Para reduzir as chamadas de secretária de ajuda e perda de produtividade quando um utilizador não pode iniciar sessão no seu dispositivo ou numa aplicação, as contas de utilizador no Azure Ative Directory (Azure AD) podem ser ativadas para redefinir a palavra-passe de self-service (SSPR). As funcionalidades que compõem o SSPR incluem a alteração de palavra-passe, reset, desbloqueio e reescrita para um diretório no local. As funcionalidades Básicas de SSPR estão disponíveis para o Office 365 e para todos os utilizadores de AD Azure sem custos.

Este artigo detalha as diferentes formas de redefinir a palavra-passe de autosserviço pode ser licenciada e usada. Para obter detalhes específicos sobre preços e faturação, consulte a página de preços da [AD Azure](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Compare edições e funcionalidades

A SSPR é licenciada por utilizador. Para manter o cumprimento, as organizações são obrigadas a atribuir a licença adequada aos seus utilizadores.

A tabela que se segue descreve os diferentes cenários de SSPR para alteração de passwords, reset ou reescrita no local, e quais as SKUs fornecem a funcionalidade.

| Funcionalidade | Azure AD Gratuito | Office 365 Empresas – Versão Premium | Microsoft 365 Empresas | Azure AD Premium P1 ou P2 |
| --- |:---:|:---:|:---:|:---:|
| **Alteração da palavra-passe do utilizador apenas na nuvem**<br />Quando um utilizador em Azure AD conhece a sua palavra-passe e quer mudá-la para algo novo. | ● | ● | ● | ● |
| **Reset de palavra-passe de utilizador apenas em nuvem**<br />Quando um utilizador em Azure AD esqueceu a sua palavra-passe e precisa de a redefinir. | | ● | ● | ● |
| **Alteração ou reset de palavra-passe do utilizador híbrido com reescrita on-prem**<br />Quando um utilizador em AD Azure que é sincronizado a partir de um diretório no local usando o Azure AD Connect quer alterar ou redefinir a sua palavra-passe e também escrever a nova palavra-passe de volta para on-prem. | | | ● | ● |

> [!WARNING]
> Os planos de licenciamento autónomos do Office 365 não suportam a SSPR com reeminagem no local. Esses planos de licenciamento do Office 365 requerem o Azure AD Premium P1, Premium P2 ou microsoft 365 Business para que esta funcionalidade funcione.

Para obter informações adicionais sobre o licenciamento, incluindo os custos, consulte as seguintes páginas:

* [Preços do Diretório Ativo Azure](https://azure.microsoft.com/pricing/details/active-directory/)
* [Funcionalidades e capacidades do Diretório Ativo Azure](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Empresas](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Ativar licenciamento baseado em grupo ou ao utilizador

A Azure AD apoia o licenciamento baseado em grupo. Os administradores podem atribuir licenças a granel a um grupo de utilizadores, em vez de as atribuir uma de cada vez. Para mais informações, consulte [Atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Alguns serviços da Microsoft não estão disponíveis em todos os locais. Antes de uma licença poder ser atribuída a um utilizador, o administrador deve especificar a propriedade de **localização de utilização** no utilizador. A atribuição de licenças pode ser feita no âmbito da secção**Definições** de**Perfil** > do **Utilizador** > no portal Azure. *Quando utiliza a atribuição de licença de grupo, quaisquer utilizadores sem localização de utilização especificada herdam a localização do diretório.*

## <a name="next-steps"></a>Passos seguintes

Para começar com a SSPR, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Ativar o reset da palavra-passe self-service (SSPR)](tutorial-enable-sspr.md)
