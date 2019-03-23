---
title: Guia de implementação de reposição personalizada de palavras-passe - Azure Active Directory
description: Sugestões para a implementação bem-sucedida da reposição de palavras-passe self-service do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb9333845a78204ea1c6e56e17b3b6f9e5513c9c
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370691"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Como implementar com êxito a reposição personalizada de palavras-passe

Para garantir uma suave implementação da funcionalidade de reposição personalizada de palavra-passe (SSPR) do Azure Active Directory (Azure AD), a maioria dos clientes realizam os passos seguintes:

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. Conclua uma implementação piloto com um pequeno subconjunto de sua organização.
   * Informações sobre como conduzir o piloto podem ser encontradas no [Tutorial: Concluído um piloto de reposição de palavra-passe self-service do Azure AD distribuam](tutorial-sspr-pilot.md).
1. Informe o suporte técnico.
   * Como é que eles ajudará os usuários?
   * Irá forçar os utilizadores para utilizar o SSPR e não permitir que o suporte técnico para ajudar os utilizadores?
   * Fornecido-las os URLs para o registo e reposição?
      * Registo:  https://aka.ms/ssprsetup
      * Repor: https://aka.ms/sspr
1. Formação aos seus utilizadores.
   * Este secções seguintes deste documento aceda através de comunicação de exemplo, portais de palavra-passe, a imposição de registo e preencher dados de autenticação.
   * O grupo de produtos do Azure Active Directory criou um [plano de implementação passo a passo](https://aka.ms/SSPRDeploymentPlan) que as organizações podem utilizar em paralelo com a documentação neste site para criar um caso comercial e um plano de implementação de reposição personalizada de palavra-passe.
1. Ative senhas de auto-atendimento repor para toda a organização.
   * Quando estiver pronto, ative a reposição de palavras-passe para todos os utilizadores mediante a definição do comutador **Reposição Personalizada de Palavras-Passe Ativada** como **Todos**.

## <a name="sample-communication"></a>Comunicação de exemplo

Muitos clientes consideram que a forma mais fácil de pôr os utilizadores a utilizar a SSPR é criar uma campanha de e-mail que inclua instruções de utilização simples. [Criámos e-mails simples e outros materiais que pode utilizar como modelos para ajudar na sua implementação](https://www.microsoft.com/download/details.aspx?id=56768):

* **Em breve**: Um modelo de e-mail que utilizar nas semanas ou dias anteriores à implementação para permitir que os utilizadores que precisam de fazer algo.
* **Já estão disponíveis**: Inicie um modelo de e-mail que utiliza o dia do programa para levar os utilizadores para se registarem e confirmarem os respetivos dados de autenticação. Se os utilizadores se registarem nesse momento, a SSPR estará à disposição deles quando precisarem.
* **Lembrete de inscrição**: Um modelo de e-mail por alguns dias a algumas semanas após a implementação, para relembrar aos utilizadores registarem e confirmarem os respetivos dados de autenticação.
* **Cartazes SSPR**: Cartazes pode personalizar e apresentar em torno de sua organização nos dias e semanas que leva até e depois da sua implementação.
* **Tents de tabela de SSPR**: Cartões de tabela é possível colocar na sala do almoço, salas de conferência, ou em escrivaninhas encorajar os utilizadores para concluir o registo.
* **Autocolantes SSPR**: Modelos de autocolante pode personalizar e imprimir para colocar os computadores portáteis, monitores, teclados ou celulares de lembrar como aceder à SSPR.

![Exemplos de e-mail SSPR para a implementação aos utilizadores][Email]

## <a name="create-your-own-password-portal"></a>Criar o seu próprio portal de palavras-passe

Muitos clientes optam por alojar uma página Web e criar uma entrada DNS de raiz, como https://passwords.contoso.com. Preenchem esta página com ligações para as informações seguintes:

* [Portal da reposição de palavras-passe do Azure AD - https://aka.ms/sspr](https://aka.ms/sspr)
* [Portal de registo da reposição de palavras-passe do Azure AD - https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Portal de alteração de palavras-passe do Azure AD - https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* Outras informações específicas da organização

Pode incluir, em qualquer comunicação ou panfleto por e-mail que distribuir, um URL corporativo e memorável, ao qual os utilizadores podem aceder quando precisam de utilizar os serviços. Para seu benefício, criámos uma [página de reposição de palavras-passe de exemplo](https://github.com/ajamess/password-reset-page) que pode utilizar e personalizar consoante as necessidades da sua organização.

## <a name="use-enforced-registration"></a>Utilizar o registo forçado

Se quiser que os utilizadores se registem na reposição de palavras-passe, pode obrigá-los a registarem-se quando iniciam sessão através do Azure AD. Pode ativar esta opção no painel **Reposição de palavras-passe** do seu diretório ao ativar a opção **Exigir que os Utilizadores se Registem Quando Iniciam Sessão**, no separador **Registo**.

Os administradores podem exigir que os utilizadores se voltem a registar após um determinado período de tempo. Podem definir a opção **Número de dias antes de ser pedido aos utilizadores que voltem a confirmar as informações de autenticação** entre 0 e 730 dias.

Depois de ativar esta opção, quando iniciam sessão, os utilizadores verão uma mensagem que os informa de que o administrador lhes exigiu que verifiquem as informações de autenticação.

## <a name="populate-authentication-data"></a>Preencher os dados de autenticação

Deve considerar [pré-preenchimento alguns dados de autenticação para os seus utilizadores](howto-sspr-authenticationdata.md). Desta forma, não precisam de se registar na reposição de palavras-passe antes de poderem utilizar a SSPR. Desde que os utilizadores tenham fornecido os dados de autenticação que cumpram a política de reposição de palavras-passe, podem repor as palavras-passe deles.

## <a name="disable-self-service-password-reset"></a>Desativar a reposição de palavras-passe self-service

Se sua organização decidir desativar a reposição de palavra-passe self-service é um processo simples. Abra o seu inquilino do Azure AD e aceda a **Reposição de Palavras-passe** > **Propriedades** e selecione **Ninguém**, em **Reposição de Palavras-passe Self-Service Ativada**. Os utilizadores manterá os métodos de autenticação registado para utilização futura.

## <a name="next-steps"></a>Passos Seguintes

* [Repor ou alterar a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Ativar o registo convergido para a reposição de palavra-passe self-service do Azure multi-factor Authentication e o Azure AD](concept-registration-mfa-sspr-converged.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "Personalizar estes modelos de e-mail para satisfazer as suas necessidades organizacionais"
