---
title: Acesso Condicional - Autenticação do legado do bloco - Diretório Ativo Azure
description: Criar uma política personalizada de acesso condicional para bloquear protocolos de autenticação do legado
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a868c8199ac34a498a280e2522d6b1e4c7ec370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295221"
---
# <a name="conditional-access-block-legacy-authentication"></a>Acesso Condicional: Autenticação do legado do bloco

Devido ao risco acrescido associado aos protocolos de autenticação do legado, a Microsoft recomenda que as organizações bloqueiem os pedidos de autenticação utilizando estes protocolos e exijam a autenticação moderna.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

Os seguintes passos ajudarão a criar uma política de Acesso Condicional para bloquear pedidos de autenticação do legado. Esta política é colocada no [modo apenas](howto-conditional-access-report-only.md) para iniciar o relatório para começar para que os administradores possam determinar o impacto que terão nos utilizadores existentes. Quando os administradores se sentem confortáveis em que a política se aplique como pretendem, podem mudar para **On** ou encenar a implantação adicionando grupos específicos e excluindo outros.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até ao**Acesso Condicional**de**Segurança** >  **do Diretório** > Ativo do Azure.
1. Selecione **Nova política.**
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir,** selecione **Todos os utilizadores**.
   1. Em **Excluir,** selecione **Utilizadores e grupos** e escolha quaisquer contas que mantenham a capacidade de utilizar a autenticação do legado. Exclua pelo menos uma conta para evitar que seja bloqueado. Se não excluir nenhuma conta, não poderá criar esta política.
   1. Selecione **Done** (Concluído).
1. Em **aplicativos ou ações cloud**, selecione Todas as **aplicações em nuvem**.
   1. Selecione **Done** (Concluído).
1. Em **Condições** > **As aplicações do Cliente (pré-visualização)**, definir **Configure** para **Sim**.
   1. Verifique apenas as caixas **Aplicações móveis e clientes** > de desktop**Outros clientes**.
   1. Selecione **Done** (Concluído).
1. Sob **controlos** > de acesso**Grant**, selecione acesso **ao bloco**.
   1. Selecione **Selecionar**.
1. Confirme as suas definições e ajuste **a política de ativação** **apenas**para reportar .
1. Selecione **Criar** para criar para ativar a sua política.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determine o impacto utilizando o modo apenas de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular o sinal de comportamento usando a ferramenta de acesso condicional O que se a ferramenta](troubleshoot-conditional-access-what-if.md)
