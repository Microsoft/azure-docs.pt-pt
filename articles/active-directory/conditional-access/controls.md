---
title: Controlos personalizados no Acesso Condicional Azure AD
description: Saiba como funcionam os controlos personalizados no Acesso Condicional do Diretório Ativo Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8c149279a755eb186a3fdc7891e9b511d18c7f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80050537"
---
# <a name="custom-controls-preview"></a>Controlos personalizados (pré-visualização)

Os controlos personalizados são uma capacidade de pré-visualização do Diretório Ativo Azure. Ao utilizar controlos personalizados, os seus utilizadores são redirecionados para um serviço compatível para satisfazer os requisitos de autenticação fora do Diretório Ativo do Azure. Para satisfazer este controlo, o navegador de um utilizador é redirecionado para o serviço externo, executa qualquer autenticação necessária e é depois redirecionado de volta para o Diretório Ativo do Azure. O Diretório Ativo azure verifica a resposta e, se o utilizador foi autenticado ou validado com sucesso, o utilizador continua no fluxo de Acesso Condicional.

> [!NOTE]
> Para mais informações sobre alterações estamos a planear a capacidade de Controlo Personalizado, consulte a [nova atualização](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls)de fevereiro de 2020.

## <a name="creating-custom-controls"></a>Criação de controlos personalizados

A Custom Controls funciona com um conjunto limitado de fornecedores de autenticação aprovados. Para criar um controlo personalizado, deve contactar primeiro o fornecedor que pretende utilizar. Cada fornecedor não Microsoft tem o seu próprio processo e requisitos para se inscrever, subscrever ou tornar-se parte do serviço, e indicar que pretende integrar-se com o Acesso Condicional. Nessa altura, o fornecedor fornecer-lhe-á um bloco de dados no formato JSON. Estes dados permitem ao fornecedor e ao Acesso Condicional trabalhar em conjunto para o seu inquilino, cria o novo controlo e define como o Acesso Condicional pode dizer se os seus utilizadores realizaram com sucesso a verificação com o fornecedor.

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

## <a name="known-limitations"></a>Limitações conhecidas

Os controlos personalizados não podem ser utilizados com a automatização da Proteção de Identidade que exija a autenticação de multi-factores Azure, reposição de senha de autosserviço Azure AD (SSPR), satisfazendo os requisitos de reclamação de autenticação de vários fatores, ou para elevar funções no Gestor de Identidade Privilegiado (PIM).

## <a name="next-steps"></a>Passos seguintes

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

- [Modo só de relatório](concept-conditional-access-report-only.md)

- [Simular o sinal de comportamento usando a ferramenta de acesso condicional O que se a ferramenta](troubleshoot-conditional-access-what-if.md)
