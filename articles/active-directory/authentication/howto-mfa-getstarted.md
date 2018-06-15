---
title: Introdução ao MFA do Azure na nuvem | Microsoft Docs
description: Esta é a página do Multi-Factor Authentication do Microsoft Azure que descreve como iniciar o MFA do Azure na nuvem.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 0a822d55e8d7bd0d503eb7d77f96dc9e60e1a4ba
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33882873"
---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Introdução ao Multi-Factor Authentication do Azure na nuvem
Este artigo descreve como começar a utilizar o Multi-Factor Authentication do Azure na nuvem.

> [!NOTE]
> A seguinte documentação fornece informações sobre como ativar utilizadores através do **Portal do Azure**. Se estiver à procura de informações sobre como configurar o Multi-Factor Authentication do Azure para utilizadores do O365, veja [Configurar a autenticação multifator para o Office 365](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US).

![MFA na Nuvem](./media/howto-mfa-getstarted/mfa_in_cloud.png)

## <a name="prerequisite"></a>Pré-requisito
[Inscreva-se numa subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/) - Se ainda não possui uma subscrição do Azure, tem de se inscrever numa. Se estiver apenas a começar e a utilizar o MFA do Azure, pode utilizar uma subscrição de avaliação

## <a name="enable-azure-multi-factor-authentication"></a>Ativar a Multi-Factor Authentication do Azure
Desde que os utilizadores tenham licenças que incluam a Multi-Factor Authentication do Azure, não é necessário fazer nada para ativar a MFA do Azure. Pode requerer a verificação de dois passos para cada utilizador. As licenças que permitem a MFA do Azure são:
- Multi-Factor Authentication do Azure
- Azure Active Directory Premium
- Enterprise Mobility + Security

Se não tiver uma destas três licenças ou não tiver suficiente licenças para abranger todos os utilizadores, não há problema. Apenas tem de efetuar um passo extra e [Criar um fornecedor de Multi-Factor Auth](concept-mfa-authprovider.md) no diretório.

## <a name="turn-on-two-step-verification-for-users"></a>Ativar a verificação de dois passos para os utilizadores

Utilize um dos procedimentos listados em [Como requerer a verificação de dois passos para um utilizador ou grupo](howto-mfa-userstates.md) para começar a utilizar o MFA do Azure. Pode optar por impor a verificação de dois passos para todos os inícios de sessão ou pode criar políticas de acesso condicional para exigir a verificação apenas quando lhe interessa.

## <a name="next-steps"></a>Passos Seguintes
Agora que configurou o Multi-Factor Authentication do Azure na nuvem, pode configurar e definir a sua implementação. Veja [Configuring Azure Multi-Factor Authentication (Configurar o Multi-Factor Authentication do Azure)](howto-mfa-mfasettings.md) para obter mais detalhes.

