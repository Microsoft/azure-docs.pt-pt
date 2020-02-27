---
title: Controlos personalizados no Acesso Condicional Azure AD
description: Saiba como funcionam os controlos personalizados no Acesso Condicional do Diretório Ativo Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f1df037b66c72177a96f77231cee70782d04992
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620717"
---
# <a name="custom-controls-preview"></a>Controlos personalizados (pré-visualização)

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

## <a name="creating-custom-controls"></a>Criação de controlos personalizados

Para criar um controlo personalizado, deve contactar primeiro o fornecedor que pretende utilizar. Cada fornecedor não Microsoft tem o seu próprio processo e requisitos para se inscrever, subscrever ou tornar-se parte do serviço, e indicar que pretende integrar-se com o Acesso Condicional. Nessa altura, o fornecedor fornecer-lhe-á um bloco de dados no formato JSON. Estes dados permitem ao fornecedor e ao Acesso Condicional trabalhar em conjunto para o seu inquilino, cria o novo controlo e define como o Acesso Condicional pode dizer se os seus utilizadores realizaram com sucesso a verificação com o fornecedor.

Os controlos personalizados não podem ser utilizados com a automatização da Proteção de Identidade que exija a autenticação de vários fatores ou para elevar as funções no Gestor de Identidade Privilegiado (PIM).

Copie os dados da JSON e cole-os na caixa de texto relacionada. Não faça alterações na JSON a menos que compreenda explicitamente a mudança que está a fazer. Fazer qualquer alteração pode quebrar a ligação entre o fornecedor e a Microsoft e potencialmente bloqueá-lo e aos seus utilizadores fora das suas contas.

A opção de criar um controlo personalizado está na secção **Gerir** a página **de Acesso Condicional.**

![Controlo](./media/controls/82.png)

Clicando em **Novo controlo personalizado,** abre uma lâmina com uma caixa de texto para os dados JSON do seu controlo.  

![Controlo](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Apagar controlos personalizados

Para eliminar um controlo personalizado, tem primeiro de garantir que não está a ser utilizado em nenhuma política de Acesso Condicional. Uma vez concluído:

1. Ir para a lista de controlos personalizados
1. Clique...  
1. Selecione **Eliminar**.

## <a name="editing-custom-controls"></a>Edição de controlos personalizados

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
