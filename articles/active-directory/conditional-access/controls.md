---
title: Controlos personalizados no Acesso Condicionado AD Azure
description: Saiba como funcionam os controlos personalizados no Azure Ative Directory Conditional Access.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7585c91e42b2d3591532756c1ead9ea60b7035e
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837588"
---
# <a name="custom-controls-preview"></a>Controlos personalizados (pré-visualização)

Os controlos personalizados são uma capacidade de pré-visualização do Diretório Ativo Azure. Ao utilizar controlos personalizados, os seus utilizadores são redirecionados para um serviço compatível para satisfazer os requisitos de autenticação fora do Diretório Ativo Azure. Para satisfazer este controlo, o navegador de um utilizador é redirecionado para o serviço externo, executa qualquer autenticação necessária e é depois redirecionado de volta para o Diretório Ativo Azure. O Azure Ative Directory verifica a resposta e, se o utilizador foi autenticado ou validado com sucesso, o utilizador continua no fluxo de Acesso Condicional.

> [!NOTE]
> Para obter mais informações sobre as alterações que estamos a planear para a capacidade de Controlo Personalizado, consulte o Arquivo de fevereiro de 2020 [para as novidades.](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls)

## <a name="creating-custom-controls"></a>Criação de controlos personalizados

Os Controlos Personalizados funcionam com um conjunto limitado de fornecedores de autenticação aprovados. Para criar um controlo personalizado, deve primeiro contactar o fornecedor que pretende utilizar. Cada fornecedor não Microsoft tem o seu próprio processo e requisitos para se inscrever, subscrever ou de outra forma tornar-se parte do serviço, e indicar que deseja integrar-se com o Acesso Condicional. Nessa altura, o fornecedor fornecer-lhe-á um bloco de dados em formato JSON. Estes dados permitem ao fornecedor e ao Acesso Condicional trabalhar em conjunto para o seu inquilino, cria o novo controlo e define como o Acesso Condicional pode dizer se os seus utilizadores realizaram com sucesso a verificação com o fornecedor.

Copie os dados JSON e, em seguida, cole-os na caixa de texto relacionada. Não faça alterações no JSON a menos que compreenda explicitamente a mudança que está a fazer. Fazer qualquer alteração pode quebrar a ligação entre o fornecedor e a Microsoft e potencialmente bloqueá-lo a si e aos seus utilizadores fora das suas contas.

A opção de criar um controlo personalizado está na secção **Gerir** a página **de Acesso Condicional.**

![Interface de controlos personalizados em Acesso Condicional](./media/controls/custom-controls-conditional-access.png)

Clicando em **Novo controlo personalizado,** abre uma lâmina com uma caixa de texto para os dados JSON do seu controlo.  

![Novo controlo personalizado](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>Apagar controlos personalizados

Para eliminar um controlo personalizado, tem primeiro de se certificar de que não está a ser utilizado em nenhuma política de Acesso Condicional. Uma vez concluído:

1. Vá à lista de controlos personalizados
1. Clique...  
1. Selecione **Eliminar**.

## <a name="editing-custom-controls"></a>Edição de controlos personalizados

Para editar um controlo personalizado, deve eliminar o controlo de corrente e criar um novo controlo com as informações atualizadas.

## <a name="known-limitations"></a>Limitações conhecidas

Os controlos personalizados não podem ser utilizados com a automatização da Proteção de Identidade que requer a autenticação multi-factor Azure AD, o reset da palavra-passe de autosserviço AZure AD (SSPR), satisfazendo os requisitos de reclamação de autenticação de vários fatores, para elevar as funções no Privileged Identity Manager (PIM), como parte da inscrição do dispositivo Intune, ou ao juntar dispositivos à Azure AD.

## <a name="next-steps"></a>Passos seguintes

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

- [Modo só de relatório](concept-conditional-access-report-only.md)

- [Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)
