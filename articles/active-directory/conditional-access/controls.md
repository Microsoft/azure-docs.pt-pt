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
ms.openlocfilehash: 3fff08690eb2807fbbd50f297761c57d3fef88fe
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671850"
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

## <a name="next-steps"></a>Passos seguintes

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

- [Modo apenas de relatório](concept-conditional-access-report-only.md)

- [Simular o sinal de comportamento usando a ferramenta de acesso condicional O que se a ferramenta](troubleshoot-conditional-access-what-if.md)
