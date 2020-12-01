---
title: Inscrição de self-service para utilizadores verificados por e-mail - Azure AD / Microsoft Docs
description: Utilize inscrição de self-service numa organização Azure Ative Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: overview
ms.workload: identity
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01de8ab86ff5f511fd5e45c4a6e88febdb4fbb15
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340871"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>O que é inscrição de self-service para o Azure Ative Directory?

Este artigo explica como usar a inscrição de self-service para preencher uma organização no Azure Ative Directory (Azure AD). Se quiser assumir um nome de domínio de uma organização não gerida da AD Azure, consulte [assumir um diretório não gerido como administrador](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Por que usar inscrição de autosserviço?

* Faça com que os clientes recebam serviços que querem mais rápido
* Crie ofertas baseadas em e-mail para um serviço
* Crie fluxos de inscrição baseados em e-mail que permitam rapidamente aos utilizadores criar identidades usando os seus pseudónimos de e-mail de trabalho fáceis de lembrar
* Um diretório AD Azure criado por autosserviço pode ser transformado num diretório gerido que pode ser usado para outros serviços

## <a name="terms-and-definitions"></a>Termos e definições

* **Inscrição de self-service**: Este é o método pelo qual um utilizador se inscreve para um serviço na nuvem e tem uma identidade criada automaticamente para eles em Azure AD com base no seu domínio de e-mail.
* **Diretório Ad Ad** não gerido : Este é o diretório onde essa identidade é criada. Um diretório não gerido é um diretório que não tem administrador global.
* **Utilizador verificado por e-mail:** Este é um tipo de conta de utilizador em Azure AD. Um utilizador que tenha uma identidade criada automaticamente depois de se inscrever para uma oferta de self-service é conhecido como um utilizador verificado por e-mail. Um utilizador verificado por e-mail é um membro regular de um diretório marcado com criaçãomethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Como controlo as definições de autosserviço?

Os administradores têm dois controlos de autosserviço hoje. Podem controlar se:

* Os utilizadores podem aderir ao diretório por e-mail
* Os utilizadores podem licenciar-se para aplicações e serviços

### <a name="how-can-i-control-these-capabilities"></a>Como posso controlar estas capacidades?

Um administrador pode configurar estas capacidades utilizando os seguintes parâmetros de cmdlet AD AD adré Set-MsolCompanySettings:

* **AllowEmailVerifiedUsers** controla se um utilizador pode criar ou aderir a um diretório. Se definir esse parâmetro para $false, nenhum utilizador verificado por e-mail pode aderir ao diretório.
* **O AllowAdHocSubscriptions** controla a capacidade de os utilizadores realizarem a inscrição de autosserviço. Se definir esse parâmetro para $false, nenhum utilizador pode efetuar a inscrição de autosserviço.
  
AllowEmailVerifiedUsers e AllowAdHocSubscriptions são configurações de todo o diretório que podem ser aplicadas a um diretório gerido ou não gerido. Aqui está um exemplo onde:

* Administra um diretório com um domínio verificado, como contoso.com
* Você usa a colaboração B2B de um diretório diferente para convidar um utilizador que já não existe ( userdoesnotexist@contoso.com ) no diretório doméstico de contoso.com
* O diretório doméstico tem os AllowEmailVerifiedUsers ligados

Se as condições anteriores forem verdadeiras, então um utilizador membro é criado no diretório doméstico, e um utilizador convidado B2B é criado no diretório convidativo.

Para obter mais informações sobre inscrições de teste flow e PowerApps, consulte os seguintes artigos:

* [Como posso impedir que os utilizadores existentes comecem a utilizar o Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [O Flow na sua organização - Perguntas e Respostas](/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Como funcionam os controlos em conjunto?
Estes dois parâmetros podem ser usados em conjunto para definir um controlo mais preciso sobre a inscrição de autosserviço. Por exemplo, o seguinte comando permitirá aos utilizadores realizar a inscrição de autosserviço, mas apenas se esses utilizadores já tiverem uma conta em Azure AD (ou seja, os utilizadores que precisariam de uma conta verificada por e-mail para serem criados primeiro não podem realizar a inscrição de autosserviço):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

O fluxograma que se segue explica as diferentes combinações para estes parâmetros e as condições resultantes para a inscrição do diretório e do autosserviço.

![fluxograma de controlos de inscrição de autosserviço](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Os detalhes desta definição podem ser recuperados pela seguinte cmdlet Get-MsolCompanyInformation. Para obter mais informações sobre este facto, consulte [Get-MsolCompanyInformation](/powershell/module/msonline/get-msolcompanyinformation?view=azureadps-1.0).

```powershell
    Get-MsolCompanyInformation | Select AllowEmailVerifiedUsers, AllowAdHocSubscriptions
```

Para obter mais informações e exemplos de como utilizar estes parâmetros, consulte [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Passos seguintes

* [Adicionar um nome de domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md)
* [Como instalar e configurar o Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Referência de Cmdlet do Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Feche o seu trabalho ou conta escolar num diretório não gerido](users-close-account.md)
