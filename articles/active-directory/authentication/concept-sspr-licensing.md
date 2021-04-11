---
title: Redefinição da palavra-passe de autosserviço da licença - Azure Ative Directory
description: Saiba mais sobre a diferença Azure Ative Directory autosserviço autosserviço reconfigurar os requisitos de licenciamento
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d332c831cc764c61a4672ea5ad1db231b68e106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952376"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Requisitos de licenciamento para reposição da palavra-passe de autosserviço do Azure Ative Directory

Para reduzir as chamadas de secretária de ajuda e a perda de produtividade quando um utilizador não pode iniciar seditação no seu dispositivo ou numa aplicação, as contas de utilizador no Azure Ative Directory (Azure AD) podem ser ativadas para o reset da palavra-passe de autosserviço (SSPR). As funcionalidades que compõem a SSPR incluem a alteração da palavra-passe, o reset, o desbloqueio e a gravação para um diretório no local. As funcionalidades Basic SSPR estão disponíveis no Microsoft 365 Business Standard ou superior e todos os SKUs AD Premium Azure sem custos.

Este artigo detalha as diferentes formas de reposição de senha de autosserviço pode ser licenciada e usada. Para obter detalhes específicos sobre preços e faturação, consulte a página de preços da [AD Azure](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Compare edições e funcionalidades

A SSPR requer uma licença apenas para o inquilino. 

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
* [Microsoft 365 Empresarial](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Empresas](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="next-steps"></a>Passos seguintes

Para começar com a SSPR, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Permitir o reset da palavra-passe de autosserviço (SSPR)](tutorial-enable-sspr.md)