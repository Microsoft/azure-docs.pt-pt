---
title: Controlos de acesso no Acesso Condicional do Diretório Ativo azure
description: Saiba como funcionam os controlos de acesso no Acesso Condicional do Diretório Ativo Azure.
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
ms.openlocfilehash: 2ad8894078a15bf37a5383cdff3721f4bf7be910
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186219"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>O que são os controlos de acesso no Acesso Condicional do Diretório Ativo Azure?

Com o Acesso Condicional do [Diretório Ativo Azure (Azure AD),](../active-directory-conditional-access-azure-portal.md)pode controlar a forma como os utilizadores autorizados acedem às suas aplicações na nuvem. Numa política de Acesso Condicional, define-se a resposta ("faça isto") à razão para desencadear a sua política ("quando isso acontece").

![Controlo](./media/controls/10.png)

No contexto do Acesso Condicional,

- "Quando**isto acontece**" é chamado **de condições**
- **"Então faça isto**" é chamado de **controlos** de acesso

A combinação de uma declaração de condição com os seus controlos representa uma política de Acesso Condicional.

![Controlo](./media/controls/61.png)

Cada controlo é um requisito que deve ser cumprido pela pessoa ou pelo sistema de sessão, ou uma restrição ao que o utilizador pode fazer após a sua inscrição.

Existem dois tipos de controlos:

- **Controlos de subvenções** - Para o acesso ao portão
- **Controlos de sessão** - Restringir o acesso dentro de uma sessão

Este tópico explica os vários controlos que estão disponíveis no Acesso Condicional da AD Azure. 

## <a name="grant-controls"></a>Controlos de subvenções

Com os controlos de subvenção, pode bloquear completamente o acesso ou permitir o acesso com requisitos adicionais selecionando os controlos pretendidos. Para vários controlos, pode exigir:

- Todos os controlos selecionados a cumprir *(E)*
- Um controlo selecionado a ser cumprido *(OR)*

![Controlo](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Pode utilizar este controlo para exigir a autenticação de vários fatores para aceder à aplicação em nuvem especificada. Este controlo suporta os seguintes fornecedores de múltiplos fatores:

- Multi-Factor Authentication do Azure
- Um fornecedor de autenticação multifactor no local, combinado com serviços da Federação de Diretórios Ativos (AD FS).

A utilização da autenticação de vários fatores ajuda a proteger os recursos de serem acedidos por um utilizador não autorizado que possa ter tido acesso às credenciais primárias de um utilizador válido.

### <a name="compliant-device"></a>Dispositivo conforme

Pode configurar políticas de Acesso Condicional baseadas em dispositivos. O objetivo de uma política de acesso condicional baseada em dispositivos é apenas dar acesso às aplicações em nuvem selecionadas a partir de [dispositivos geridos](require-managed-devices.md). Exigir que um dispositivo seja marcado como conforme é uma opção que tem de limitar o acesso a dispositivos geridos. Um dispositivo pode ser marcado como conforme pela Intune (para qualquer dispositivo OS) ou pelo seu sistema MDM de terceiros para dispositivos Windows 10. Os sistemas de MDM de terceiros para tipos de SISTEMA de dispositivos que não o Windows 10 não são suportados. 

O seu dispositivo tem de ser registado em Nome do Azure AD antes de ser marcado como conforme. Para registar um dispositivo, tem três opções: 

- Dispositivos registados no Azure AD
- Dispositivos associados ao Azure AD  
- Dispositivos híbridos associados ao Azure AD

Estas três opções são discutidas no artigo O que é uma identidade de [dispositivo?](../devices/overview.md)

Para mais informações, consulte [como exigir dispositivos geridos para acesso](require-managed-devices.md)a aplicações na nuvem com Acesso Condicional .

### <a name="hybrid-azure-ad-joined-device"></a>Dispositivo adestado pela Hybrid Azure

Exigir um dispositivo híbrido Azure AD é outra opção que tem para configurar as políticas de Acesso Condicional baseadas em dispositivos. Este requisito refere-se a desktops do Windows, portáteis e tablets empresariais que se juntam a um Diretório Ativo no local. Se esta opção for selecionada, a sua política de Acesso Condicional concede acesso a tentativas de acesso feitas com dispositivos que se unem ao seu Diretório Ativo no local e ao seu Diretório Ativo Azure. Os dispositivos Mac não suportam a adesão híbrida azure AD.

Para mais informações, consulte a configuração de políticas de acesso condicional baseadas em [dispositivos Azure Ative Diretório.](require-managed-devices.md)

### <a name="approved-client-app"></a>App de cliente aprovada

Uma vez que os seus colaboradores utilizam dispositivos móveis para tarefas pessoais e de trabalho, é possível que pretenda ter a capacidade de proteger os dados da empresa acedidos através de dispositivos, mesmo no caso de estes não serem geridos por si.
Pode utilizar políticas de proteção de [aplicações Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da sua empresa independentemente de qualquer solução de gestão de dispositivos móveis (MDM).

Com aplicações de clientes aprovadas, pode exigir uma aplicação de cliente que tente aceder às suas aplicações na nuvem para suportar as políticas de proteção de [aplicações Intune](https://docs.microsoft.com/intune/app-protection-policy). Por exemplo, pode restringir o acesso ao Exchange Online à aplicação Outlook. Uma política de acesso condicional que requer aplicações de clientes aprovadas também é conhecida como [política de acesso condicional baseada em aplicações.](app-based-conditional-access.md) Para obter uma lista de aplicações de clientes aprovadas suportadas, consulte o [requisito de aplicação de cliente aprovado](concept-conditional-access-grant.md#require-approved-client-app).

### <a name="app-protection-policy-preview"></a>Política de proteção de aplicativos (pré-visualização)

Uma vez que os seus colaboradores utilizam dispositivos móveis para tarefas pessoais e de trabalho, é possível que pretenda ter a capacidade de proteger os dados da empresa acedidos através de dispositivos, mesmo no caso de estes não serem geridos por si.
Pode utilizar políticas de proteção de [aplicações Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da sua empresa independentemente de qualquer solução de gestão de dispositivos móveis (MDM).

Com a política de proteção de aplicações, pode limitar o acesso a aplicações de clientes que reportaram à Azure AD ter recebido políticas de proteção de [aplicações Intune](https://docs.microsoft.com/intune/app-protection-policy). Por exemplo, pode restringir o acesso ao Exchange Online à aplicação Outlook que tem uma política de proteção de aplicações Intune. Uma política de acesso condicional que requer a política de proteção de aplicações também é conhecida como [política de acesso condicional baseada na proteção de aplicações.](concept-conditional-access-session.md#application-enforced-restrictions) 

O seu dispositivo deve ser registado em Azure AD antes que uma aplicação possa ser marcada como protegida pela política.

Para obter uma lista de aplicações de clientes protegidas por políticas suportadas, consulte a exigência da política de proteção de [aplicações](concept-conditional-access-session.md#application-enforced-restrictions).

### <a name="terms-of-use"></a>Termos de utilização

Pode exigir que um utilizador do seu inquilino consinta os termos de utilização antes de ter acesso a um recurso. Como administrador, pode configurar e personalizar termos de utilização através do upload de um documento PDF. Se um utilizador se der ao âmbito deste controlo, o acesso a uma aplicação só é concedido se os termos de utilização tiverem sido acordados.

## <a name="custom-controls-preview"></a>Controlos personalizados (pré-visualização)

Os controlos personalizados são uma capacidade da edição Azure Ative Directory Premium P1. Ao utilizar controlos personalizados, os seus utilizadores são redirecionados para um serviço compatível para satisfazer outros requisitos fora do Diretório Ativo do Azure. Para satisfazer este controlo, o navegador de um utilizador é redirecionado para o serviço externo, realiza quaisquer atividades de autenticação ou validação necessárias, sendo depois redirecionado para o Diretório Ativo azure. O Diretório Ativo azure verifica a resposta e, se o utilizador foi autenticado ou validado com sucesso, o utilizador continua no fluxo de Acesso Condicional.

Estes controlos permitem a utilização de certos serviços externos ou personalizados como controlos de Acesso Condicional e, em geral, alargar as capacidades de Acesso Condicional.

Os fornecedores que atualmente oferecem um serviço compatível incluem:

- [Segurança duo](https://duo.com/docs/azure-ca)
- [Cartão de Dados de Confiar](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Identidade ping](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Forte de Prata](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Rio Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Para obter mais informações sobre esses serviços, contacte diretamente os prestadores.

### <a name="creating-custom-controls"></a>Criação de controlos personalizados

Para criar um controlo personalizado, deve contactar primeiro o fornecedor que pretende utilizar. Cada fornecedor não Microsoft tem o seu próprio processo e requisitos para se inscrever, subscrever ou tornar-se parte do serviço, e indicar que pretende integrar-se com o Acesso Condicional. Nessa altura, o fornecedor fornecer-lhe-á um bloco de dados no formato JSON. Estes dados permitem ao fornecedor e ao Acesso Condicional trabalhar em conjunto para o seu inquilino, cria o novo controlo e define como o Acesso Condicional pode dizer se os seus utilizadores realizaram com sucesso a verificação com o fornecedor.

Os controlos personalizados não podem ser utilizados com a automatização da Proteção de Identidade que exija a autenticação de vários fatores ou para elevar as funções no Gestor de Identidade Privilegiado (PIM).

Copie os dados da JSON e cole-os na caixa de texto relacionada. Não faça alterações na JSON a menos que compreenda explicitamente a mudança que está a fazer. Fazer qualquer alteração pode quebrar a ligação entre o fornecedor e a Microsoft e potencialmente bloqueá-lo e aos seus utilizadores fora das suas contas.

A opção de criar um controlo personalizado está na secção **Gerir** a página **de Acesso Condicional.**

![Controlo](./media/controls/82.png)

Clicando em **Novo controlo personalizado,** abre uma lâmina com uma caixa de texto para os dados JSON do seu controlo.  

![Controlo](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Apagar controlos personalizados

Para eliminar um controlo personalizado, tem primeiro de garantir que não está a ser utilizado em nenhuma política de Acesso Condicional. Uma vez concluído:

1. Ir para a lista de controlos personalizados
1. Clique...  
1. Selecione **Eliminar**.

### <a name="editing-custom-controls"></a>Edição de controlos personalizados

Para editar um controlo personalizado, deve eliminar o controlo atual e criar um novo controlo com as informações atualizadas.

## <a name="session-controls"></a>Controlos de sessão

Os controlos de sessão permitem uma experiência limitada dentro de uma aplicação na nuvem. Os controlos da sessão são aplicados por aplicações na nuvem e contam com informações adicionais fornecidas pela Azure AD à app sobre a sessão.

![Controlo](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Use restrições impostas por aplicações

Pode utilizar este controlo para exigir que o Azure AD passe informações do dispositivo para as aplicações em nuvem selecionadas. A informação do dispositivo permite que as aplicações em nuvem saibam se uma ligação é iniciada a partir de um dispositivo compatível ou unido. Este controlo apenas suporta o SharePoint Online e o Exchange Online como aplicações em nuvem selecionadas. Quando selecionada, a aplicação cloud utiliza as informações do dispositivo para fornecer aos utilizadores, dependendo do estado do dispositivo, com uma experiência limitada ou completa.

Para saber mais, consulte:

- [Permitindo acesso limitado com O SharePoint Online](https://aka.ms/spolimitedaccessdocs)
- [Permitir o acesso limitado com o Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Passos seguintes

- Se quiser saber configurar uma política de Acesso Condicional, consulte [RequireM para aplicações específicas com Acesso Condicional de Diretório Ativo Azure](app-based-mfa.md).
- Se estiver pronto para configurar as políticas de Acesso Condicional para o seu ambiente, consulte as [melhores práticas de Acesso Condicional no Diretório Ativo Azure](best-practices.md).
