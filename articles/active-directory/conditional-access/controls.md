---
title: Quais são os controles de acesso no acesso condicional do Azure Active Directory? | Microsoft Docs
description: Saiba como controla o acesso no trabalho de acesso condicional do Azure Active Directory.
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d08046505f26e8b133b104e7984c8defe5163c7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58163624"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Quais são os controles de acesso no acesso condicional do Azure Active Directory?

Com o [acesso condicional do Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), pode controlar o acesso de utilizadores autorizados como suas aplicações na cloud. Uma política de acesso condicional, vai definir a resposta ("fazê-lo") para o motivo para acionar a política ("quando isso acontece").

![Controlo](./media/controls/10.png)

No contexto do acesso condicional,

- "**Quando isso acontece**" é chamado **condições**

- "**, Em seguida, fazer isso**" é chamado **controlos de acesso**

A combinação de uma instrução de condição com os controles com representa uma política de acesso condicional.

![Controlo](./media/controls/61.png)

Cada controle é um requisito que tem de ser cumprido pela pessoa ou sistema a iniciar sessão ou uma restrição sobre o que o utilizador pode fazer depois de iniciar sessão.

Existem dois tipos de controles:

- **Controlos de concessão** - para acesso de porta

- **Os controlos de sessão** - para restringir o acesso dentro de uma sessão

Este tópico explica os vários controles que estão disponíveis no acesso condicional do Azure AD. 

## <a name="grant-controls"></a>Controlos de concessão

Com controlos de concessão, pode bloquear totalmente o acesso ou permitir o acesso com requisitos adicionais ao selecionar os controles pretendidos. Para vários controlos, pode exigir:

- Todos selecionados controlos para ser concluído (*AND*)
- Um controle para ser concluído de selecionado (*ou*)

![Controlo](./media/controls/17.png)

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Pode utilizar este controlo para exigir autenticação multifator para aceder à aplicação em nuvem especificado. Esse controle suporta os seguintes fornecedores de multi-factor:

- Multi-Factor Authentication do Azure

- Um fornecedor de autenticação multifator no local, combinados com serviços de Federação do Active Directory (AD FS).

Utilizar multi-factor authentication ajuda a proteger os recursos de que está sendo acessado por um utilizador não autorizado que poderá ter obtido acesso às credenciais de principal de um utilizador válido.

### <a name="compliant-device"></a>Dispositivo conforme

Pode configurar políticas de acesso condicional com base no dispositivo. O objetivo de uma política de acesso condicional com base no dispositivo é só conceder acesso às aplicações em nuvem selecionado da [dispositivos geridos](require-managed-devices.md). Exigir um dispositivo seja marcado como conforme é uma opção que tem para limitar o acesso a dispositivos geridos. Um dispositivo pode ser marcado como conforme pelo Intune (para qualquer SO do dispositivo) ou ao seu sistema MDM de terceiros para dispositivos Windows 10. Sistemas MDM de terceiros para tipos de SO do dispositivo que não seja o Windows 10 não são suportados. 

O dispositivo tem de ser registado para o Azure AD antes de poder ser marcado como compatível. Para registar um dispositivo, tem três opções: 

- [Dispositivos do Azure AD registado](../devices/overview.md#azure-ad-registered-devices)
- [Dispositivos associados ao Azure AD](../devices/overview.md#azure-ad-joined-devices)  
- [Dispositivos associados ao Azure AD híbrido](../devices/overview.md#hybrid-azure-ad-joined-devices)

Para obter mais informações, consulte [como exigir que os dispositivos geridos para aceder à aplicação de cloud com o acesso condicional](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Dispositivo de associado ao Azure AD híbrido

Exigir uma versão híbrida do Azure AD de dispositivo associado ao é outra opção, que tem de configurar políticas de acesso condicional com base no dispositivo. Este requisito refere-se para desktops Windows, computadores portáteis e tablets de enterprise que estão associados a um Active Directory no local. Se esta opção estiver selecionada, a sua política de acesso condicional concede tentativas de acesso para acesso com dispositivos que estão associados ao Active Directory no local e o Azure Active Directory.  

Para obter mais informações, consulte [definir políticas de acesso condicional com base no dispositivo do Azure Active Directory](require-managed-devices.md).

### <a name="approved-client-app"></a>Aplicação aprovada do cliente

Uma vez que os funcionários utilizam dispositivos móveis para pessoais e tarefas de trabalho, pode querer ter a capacidade de proteger os dados da empresa acedidos através de dispositivos, mesmo no caso em que não são geridos por si.
Pode usar [políticas de proteção de aplicações do Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da sua empresa independentes de qualquer solução de gestão de dispositivos móveis (MDM).

Com as aplicações aprovadas do cliente, pode exigir uma aplicação de cliente tenta aceder às suas aplicações na cloud para suportar [políticas de proteção de aplicações do Intune](https://docs.microsoft.com/intune/app-protection-policy). Por exemplo, pode restringir o acesso ao Exchange Online para a aplicação Outlook. Uma política de acesso condicional que requer que as aplicações aprovadas do cliente também é conhecido como [política de acesso condicional com base na aplicação](app-based-conditional-access.md). Para obter uma lista de aplicações aprovadas do cliente suportados, consulte [aprovado requisito de aplicação de cliente](technical-reference.md#approved-client-app-requirement).

### <a name="terms-of-use"></a>Termos de Utilização

Pode exigir que um utilizador no seu inquilino para aceitar os termos de utilização antes de lhes ser concedido acesso a um recurso. Como administrador, pode configurar e personalizar os termos de utilização através do carregamento de um documento PDF. Se um utilizador for abrangido pelo âmbito deste controlar o acesso a uma aplicação é concedido apenas se os termos de utilização tem sido combinado entre.

### <a name="custom-controls-preview"></a>Controlos personalizados (pré-visualização)

Pode criar controles personalizados no acesso condicional que redirecionar os utilizadores a um serviço compatível para satisfazer mais requisitos fora do Azure Active Directory. Isso permite que use determinados externo multi-factor authentication e fornecedores de verificação para impor regras de acesso condicional, ou para criar seu próprio serviço personalizado. Para atender a esse controle, browser de um utilizador é redirecionado para o serviço externo, realiza qualquer autenticação necessária ou atividades de validação e, em seguida, é redirecionado para o Azure Active Directory. Se o utilizador com êxito foi autenticado ou validado, o usuário continua no fluxo de acesso condicional. 

## <a name="custom-controls"></a>Controles personalizados

Controles personalizados são uma funcionalidade da edição Azure Active Directory Premium P1. Ao usar controles personalizados, os utilizadores são redirecionados para um serviço compatível para satisfazer mais requisitos fora do Azure Active Directory. Para atender a esse controle, browser de um utilizador é redirecionado para o serviço externo, realiza qualquer autenticação necessária ou atividades de validação e, em seguida, é redirecionado para o Azure Active Directory. O Azure Active Directory verifica a resposta e, se o utilizador com êxito foi autenticado ou validado, o usuário continua no fluxo de acesso condicional.

Esses controles permitem a utilização de determinados serviços externos ou personalizadas como controles de acesso condicional e geralmente estendem os recursos de acesso condicional.

Fornecedores atualmente fornecem um serviço de compatível incluem:

- [Segurança de Duo](https://duo.com/docs/azure-ca)
- [Datacard da Entrust](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [A ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Para obter mais informações sobre esses serviços, contacte diretamente os fornecedores.

### <a name="creating-custom-controls"></a>Criação de controles personalizados

Para criar um controle personalizado, primeiro deve contactar o fornecedor de que pretende utilizar. Cada fornecedor de terceiros tem seu próprio processo e os requisitos para inscrever-se, subscrever ou, caso contrário, se tornam uma parte do serviço e para indicar que pretende integrar com o acesso condicional. Nesse ponto, o fornecedor fornecerá um bloco de dados no formato JSON. Estes dados permite que o fornecedor e o acesso condicional para funcionarem em conjunto para o seu inquilino, cria o novo controle e define como o acesso condicional pode dizer se os seus utilizadores executou com êxito a verificação com o fornecedor.

Copiar os dados JSON e, em seguida, cole-o na caixa de texto relacionada. Não faça quaisquer alterações para o JSON, a menos que explicitamente compreende a alteração que faz. Faça nenhuma alteração foi a conexão entre o fornecedor e a Microsoft e potencialmente bloquear e seus usuários fora de suas contas.

A opção para criar um controle personalizado está no **Manage** secção a **acesso condicional** página.

![Controlo](./media/controls/82.png)

Clicar **novo controle personalizado**, é aberto um painel com uma caixa de texto para os dados JSON de seu controle.  

![Controlo](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>A eliminar a controles personalizados

Para eliminar um controle personalizado, primeiro tem de se certificar de que não está a ser utilizada qualquer política de acesso condicional. Depois de concluído:

1. Aceda à lista de controles personalizados

2. Clique em...  

3. Selecione **Eliminar**.

### <a name="editing-custom-controls"></a>Edição de controles personalizados

Para editar um controle personalizado, tem de eliminar o controlo atual e criar um novo controle com as informações atualizadas.

## <a name="session-controls"></a>Controlos de sessão

Os controlos de sessão proporcionam uma experiência limitada dentro de uma aplicação na cloud. Os controlos de sessão são impostos por aplicações na cloud e dependem de informações adicionais fornecidas pelo Azure AD para a aplicação sobre a sessão.

![Controlo](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Utilizar as restrições da aplicação imposta

Pode utilizar este controlo para exigir o Azure AD para passar as informações do dispositivo para as aplicações na cloud selecionada. As informações de dispositivo permite que as aplicações na cloud saber se uma conexão é iniciada a partir de um dispositivo associado a um domínio ou em conformidade. Este controlo só suporta a aplicações na cloud selecionada de Exchange Online e SharePoint Online. Quando selecionada, a aplicação de cloud utiliza as informações do dispositivo para fornecer aos usuários, dependendo do Estado do dispositivo, com uma experiência completa ou limitada.

Para saber mais, consulte:

- [Ativar o acesso limitado com o SharePoint Online](https://aka.ms/spolimitedaccessdocs)

- [Ativar o acesso limitado com o Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Passos Seguintes

- Se quiser saber como configurar uma política de acesso condicional, consulte [exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory](app-based-mfa.md).

- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, veja [Best practices for conditional access in Azure Active Directory](best-practices.md) (Melhores práticas do acesso condicional no Azure Active Directory).
