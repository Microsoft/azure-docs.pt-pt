---
title: Controles de acesso no Azure Active Directory acesso condicional
description: Saiba como os controles de acesso no Azure Active Directory acesso condicional funcionam.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 12/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 342ec46aabafec975d780aa03fe75d7e3cf50497
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424975"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>O que são controles de acesso no Azure Active Directory acesso condicional?

Com o [acesso condicional do Azure Active Directory (AD do Azure)](../active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários autorizados acessam seus aplicativos de nuvem. Em uma política de acesso condicional, você define a resposta ("fazer isso") para o motivo do acionamento de sua política ("quando isso acontece").

![Controlo](./media/controls/10.png)

No contexto de acesso condicional,

- "**Quando isso acontece**" é chamado de **condições**
- "**Então faça isso**" é chamado de **controles de acesso**

A combinação de uma instrução Condition com seus controles representa uma política de acesso condicional.

![Controlo](./media/controls/61.png)

Cada controle é um requisito que deve ser atendido pela pessoa ou pelo sistema que está entrando ou uma restrição sobre o que o usuário pode fazer depois de entrar.

Há dois tipos de controles:

- **Controles de concessão** -para acesso à porta
- **Controles de sessão** – para restringir o acesso em uma sessão

Este tópico explica os vários controles que estão disponíveis no acesso condicional do Azure AD. 

## <a name="grant-controls"></a>Controles de concessão

Com os controles de concessão, você pode bloquear o acesso completamente ou permitir o acesso com requisitos adicionais selecionando os controles desejados. Para vários controles, você pode exigir:

- Todos os controles selecionados a serem atendidos (*e*)
- Um controle selecionado a ser atendido (*ou*)

![Controlo](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Autenticação multifator

Você pode usar esse controle para exigir a autenticação multifator para acessar o aplicativo de nuvem especificado. Esse controle dá suporte aos seguintes provedores multifator:

- Multi-Factor Authentication do Azure
- Um provedor de autenticação multifator local, combinado com Serviços de Federação do Active Directory (AD FS) (AD FS).

O uso da autenticação multifator ajuda a proteger os recursos de serem acessados por um usuário não autorizado que pode ter obtido acesso às credenciais primárias de um usuário válido.

### <a name="compliant-device"></a>Dispositivo conforme

Você pode configurar políticas de acesso condicional que são baseadas em dispositivo. O objetivo de uma política de acesso condicional com base em dispositivo é conceder acesso apenas aos aplicativos de nuvem selecionados de [dispositivos gerenciados](require-managed-devices.md). Exigir que um dispositivo seja marcado como compatível é uma opção que você precisa limitar o acesso a dispositivos gerenciados. Um dispositivo pode ser marcado como compatível pelo Intune (para qualquer sistema operacional do dispositivo) ou por seu sistema MDM de terceiros para dispositivos Windows 10. Não há suporte para sistemas MDM de terceiros para tipos de so do dispositivo que não sejam o Windows 10. 

Seu dispositivo precisa ser registrado no Azure AD antes que ele possa ser marcado como em conformidade. Para registrar um dispositivo, você tem três opções: 

- Dispositivos registados no Azure AD
- Dispositivos associados ao Azure AD  
- Dispositivos híbridos associados ao Azure AD

Essas três opções são discutidas no artigo [o que é uma identidade de dispositivo?](../devices/overview.md)

Para obter mais informações, consulte [como exigir dispositivos gerenciados para acesso ao aplicativo de nuvem com acesso condicional](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Dispositivo ingressado no Azure AD híbrido

Exigir um dispositivo ingressado no Azure AD híbrido é outra opção que você precisa configurar políticas de acesso condicional com base no dispositivo. Esse requisito se refere a áreas de trabalho do Windows, laptops e tablets corporativos que ingressaram em um Active Directory local. Se essa opção for selecionada, sua política de acesso condicional concederá acesso a tentativas de acesso feitas com dispositivos que ingressaram em seu Active Directory local e seu Azure Active Directory. Os dispositivos Mac não dão suporte à junção híbrida do Azure AD.

Para obter mais informações, consulte [configurar Azure Active Directory políticas de acesso condicional com base no dispositivo](require-managed-devices.md).

### <a name="approved-client-app"></a>Aplicativo cliente aprovado

Como seus funcionários usam dispositivos móveis para tarefas pessoais e de trabalho, talvez você queira ter a capacidade de proteger os dados da empresa acessados usando dispositivos mesmo no caso em que eles não são gerenciados por você.
Você pode usar [as políticas de proteção de aplicativo do Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da sua empresa independentemente de qualquer solução de MDM (gerenciamento de dispositivo móvel).

Com os aplicativos cliente aprovados, você pode exigir um aplicativo cliente que tenta acessar seus aplicativos de nuvem para dar suporte a [políticas de proteção de aplicativo do Intune](https://docs.microsoft.com/intune/app-protection-policy). Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo Outlook. Uma política de acesso condicional que requer aplicativos cliente aprovados também é conhecida como [política de acesso condicional baseada em aplicativo](app-based-conditional-access.md). Para obter uma lista de aplicativos cliente aprovados com suporte, consulte [requisito de aplicativo cliente aprovado](technical-reference.md#approved-client-app-requirement).

### <a name="app-protection-policy-preview"></a>Política de proteção de aplicativo (visualização)

Como seus funcionários usam dispositivos móveis para tarefas pessoais e de trabalho, talvez você queira ter a capacidade de proteger os dados da empresa acessados usando dispositivos mesmo no caso em que eles não são gerenciados por você.
Você pode usar [as políticas de proteção de aplicativo do Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da sua empresa independentemente de qualquer solução de MDM (gerenciamento de dispositivo móvel).

Com a política de proteção de aplicativo, você pode limitar o acesso a aplicativos cliente que relataram ao Azure AD que receberam [políticas de proteção de aplicativo do Intune](https://docs.microsoft.com/intune/app-protection-policy). Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo do Outlook que tem uma política de proteção de aplicativo do Intune. Uma política de acesso condicional que requer a política de proteção de aplicativo também é conhecida como [política de acesso condicional com base na proteção do aplicativo](app-protection-based-conditional-access.md). 

Seu dispositivo deve ser registrado no Azure AD antes que um aplicativo possa ser marcado como protegido pela política.

Para obter uma lista de aplicativos cliente protegidos por política com suporte, consulte [requisito de política de proteção de aplicativo](technical-reference.md#app-protection-policy-requirement).

### <a name="terms-of-use"></a>Termos de utilização

Você pode exigir que um usuário em seu locatário concorde com os termos de uso antes de receber acesso a um recurso. Como administrador, você pode configurar e personalizar os termos de uso carregando um documento PDF. Se um usuário estiver no escopo desse controle, o acesso a um aplicativo só será concedido se os termos de uso tiverem sido acordados.

## <a name="custom-controls-preview"></a>Controles personalizados (versão prévia)

Os controles personalizados são uma funcionalidade da edição P1 Azure Active Directory Premium. Ao usar controles personalizados, os usuários são redirecionados para um serviço compatível para atender a requisitos adicionais fora do Azure Active Directory. Para atender a esse controle, o navegador de um usuário é redirecionado para o serviço externo, executa todas as atividades necessárias de autenticação ou validação e, em seguida, é Redirecionado de volta para o Azure Active Directory. Azure Active Directory verifica a resposta e, se o usuário foi autenticado ou validado com êxito, o usuário continua no fluxo de acesso condicional.

Esses controles permitem o uso de determinados serviços externos ou personalizados como controles de acesso condicional e geralmente estendem os recursos de acesso condicional.

Os provedores que atualmente oferecem um serviço compatível incluem:

- [Segurança Duo](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Identidade de ping](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [VIP da Symantec](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Para obter mais informações sobre esses serviços, entre em contato diretamente com os provedores.

### <a name="creating-custom-controls"></a>Criando controles personalizados

Para criar um controle personalizado, primeiro você deve entrar em contato com o provedor que deseja utilizar. Cada provedor que não é da Microsoft tem seu próprio processo e requisitos para inscrever-se, assinar ou se tornar uma parte do serviço e indicar que você deseja integrar com o acesso condicional. Nesse ponto, o provedor fornecerá um bloco de dados no formato JSON. Esses dados permitem que o provedor e o acesso condicional funcionem juntos para seu locatário, cria o novo controle e define como o acesso condicional pode informar se os usuários executaram com êxito a verificação com o provedor.

Os controles personalizados não podem ser usados com a automação da proteção de identidade que requer autenticação multifator ou para elevar funções no PIM (Privileged Identity Manager).

Copie os dados JSON e cole-os na caixa de texto relacionada. Não faça nenhuma alteração no JSON, a menos que você entenda explicitamente a alteração que está fazendo. Fazer qualquer alteração pode interromper a conexão entre o provedor e a Microsoft e, potencialmente, bloquear você e seus usuários de suas contas.

A opção para criar um controle personalizado está na seção **gerenciar** da página de **acesso condicional** .

![Controlo](./media/controls/82.png)

Clicar em **novo controle personalizado**abre uma folha com uma caixa de texto para os dados JSON do seu controle.  

![Controlo](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Excluindo controles personalizados

Para excluir um controle personalizado, primeiro você deve verificar se ele não está sendo usado em nenhuma política de acesso condicional. Após a conclusão:

1. Ir para a lista de controles personalizados
1. Clique em...  
1. Selecione **Eliminar**.

### <a name="editing-custom-controls"></a>Editando controles personalizados

Para editar um controle personalizado, você deve excluir o controle atual e criar um novo controle com as informações atualizadas.

## <a name="session-controls"></a>Controles de sessão

Controles de sessão permitem uma experiência limitada em um aplicativo de nuvem. Os controles de sessão são impostos por aplicativos de nuvem e dependem de informações adicionais fornecidas pelo Azure AD para o aplicativo sobre a sessão.

![Controlo](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Usar restrições impostas do aplicativo

Você pode usar esse controle para exigir que o Azure AD passe informações do dispositivo para os aplicativos de nuvem selecionados. As informações do dispositivo permitem que os aplicativos de nuvem saibam se uma conexão é iniciada de um dispositivo em conformidade ou ingressado no domínio. Esse controle só dá suporte ao SharePoint Online e ao Exchange Online como aplicativos de nuvem selecionados. Quando selecionado, o aplicativo de nuvem usa as informações do dispositivo para fornecer aos usuários, dependendo do estado do dispositivo, com uma experiência limitada ou completa.

Para saber mais, consulte:

- [Habilitando o acesso limitado com o SharePoint Online](https://aka.ms/spolimitedaccessdocs)
- [Habilitando o acesso limitado com o Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Passos seguintes

- Se você quiser saber como configurar uma política de acesso condicional, consulte [exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, consulte as [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md).
