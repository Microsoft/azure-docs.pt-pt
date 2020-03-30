---
title: Inscrição de self-service para utilizadores verificados por e-mail - Azure AD / Microsoft Docs
description: Utilize inscrição de self-service num inquilino azure Ative Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 953837e22cdd3ba8a54d702eac61461739db82d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74027639"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>O que é a inscrição de self-service para o Azure Ative Directory?

Este artigo explica como usar a inscrição de self-service para povoar uma organização em Azure Ative Directory (Azure AD). Se quiser assumir um nome de domínio de uma organização não gerida da AD Azure, consulte [assumir um diretório não gerido como administrador](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Por que usar a inscrição de self-service?
* Levar os clientes aos serviços que querem mais rápido
* Criar ofertas baseadas em e-mail para um serviço
* Crie fluxos de inscrição baseados em e-mail que rapidamente permitem que os utilizadores criem identidades usando os seus pseudónimos de e-mail de trabalho de fácil recordação
* Um diretório Azure AD auto-criado pode ser transformado num diretório gerido que pode ser usado para outros serviços

## <a name="terms-and-definitions"></a>Termos e definições
* **Inscrição de self-service**: Este é o método pelo qual um utilizador se inscreve num serviço na nuvem e tem uma identidade criada automaticamente para eles em Azure AD com base no seu domínio de e-mail.
* **Diretório AD Azure não gerido**: Este é o diretório onde essa identidade é criada. Um diretório não gerido é um diretório que não tem administrador global.
* **Utilizador verificado por e-mail**: Este é um tipo de conta de utilizador em Azure AD. Um utilizador que tenha uma identidade criada automaticamente após se inscrever para uma oferta de self-service é conhecido como um utilizador verificado por e-mail. Um utilizador verificado por e-mail é um membro regular de um diretório marcado com método de criação=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Como controlo as definições de self-service?
Os administradores têm dois controlos de autosserviço hoje. Podem controlar se:

* Os utilizadores podem aderir ao diretório por e-mail
* Os utilizadores podem licenciar-se para aplicações e serviços

### <a name="how-can-i-control-these-capabilities"></a>Como posso controlar estas capacidades?
Um administrador pode configurar estas capacidades utilizando os seguintes parâmetros De Set-MsolCompanySettings do Azure AD:

* **AllowEmailVerifiedUsers** controla se um utilizador pode criar ou aderir a um diretório. Se definir esse parâmetro para $false, nenhum utilizador verificado por e-mail pode aderir ao diretório.
* **As Subscrições AllowAdHoc** controlam a capacidade de os utilizadores realizarem o autosserviço. Se configurar esse parâmetro para $false, nenhum utilizador pode realizar o autosserviço.
  
AllowEmailVerifiedUsers e AllowAdHocSubscriptions são configurações de diretório que podem ser aplicadas a um diretório gerido ou não gerido. Aqui está um exemplo onde:

* Administra um diretório com um domínio verificado, como contoso.com
* Você usa a colaboração B2B de um diretório diferenteuserdoesnotexist@contoso.compara convidar um utilizador que já não existe ( ) no diretório doméstico de contoso.com
* O diretório inicial tem os AllowEmailVerifiedUsers ligados

Se as condições anteriores forem verdadeiras, então um utilizador membro é criado no diretório inicial, e um utilizador convidado B2B é criado no diretório convidativo.

As inscrições de teste flow e PowerApps não são controladas pela definição **de Assinaturas AllowAdHoc.** Para obter mais informações, veja os artigos seguintes:

* [Como posso impedir que os utilizadores existentes comecem a utilizar o Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [O Flow na sua organização - Perguntas e Respostas](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Como funcionam os controlos em conjunto?
Estes dois parâmetros podem ser utilizados em conjunto para definir um controlo mais preciso sobre a inscrição de autosserviço. Por exemplo, o seguinte comando permitirá que os utilizadores realizem o autosserviço de inscrição, mas apenas se esses utilizadores já tiverem uma conta em Azure AD (ou seja, os utilizadores que precisariam de uma conta verificada por e-mail para serem criadas primeiro não podem realizar o autosserviço de inscrição):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

O fluxograma seguinte explica as diferentes combinações para estes parâmetros e as condições resultantes para o diretório e inscrição de self-service.

![flowchart de controlos de inscrição de autosserviço](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Para obter mais informações e exemplos de como utilizar estes parâmetros, consulte [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Passos seguintes

* [Adicione um nome de domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md)
* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referência de Cmdlet do Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Feche o seu trabalho ou a sua conta escolar num diretório não gerido](users-close-account.md)
