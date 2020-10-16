---
title: Acesso Condicional - Autenticação do legado do bloco - Diretório Ativo Azure
description: Criar uma política de acesso condicional personalizado para bloquear protocolos de autenticação de legados
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2499a89907eb63f01adbc80a6d206911c09c1259
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91265957"
---
# <a name="conditional-access-block-legacy-authentication"></a>Acesso Condicional: Autenticação do legado do bloco

Devido ao risco acrescido associado aos protocolos de autenticação, a Microsoft recomenda que as organizações bloqueiem pedidos de autenticação utilizando estes protocolos e exijam a autenticação moderna.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

As seguintes etapas ajudarão a criar uma política de acesso condicional para bloquear pedidos de autenticação legado. Esta política é colocada no [modo report-only](howto-conditional-access-insights-reporting.md) para iniciar para que os administradores possam determinar o impacto que terão nos utilizadores existentes. Quando os administradores estão confortáveis que a política se aplica como pretendem, podem mudar para **Ligar** ou encenar a implementação adicionando grupos específicos e excluindo outros.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Conditional Access**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores**.
   1. Em **'Excluir',** selecione **Utilizadores e grupos** e escolha quaisquer contas que mantenham a capacidade de utilizar a autenticação antiga. Exclua pelo menos uma conta para evitar que esteja trancado. Se não excluir nenhuma conta, não poderá criar esta política.
   1. Selecione **Concluído**.
1. Em **aplicativos ou ações cloud**, selecione **Todas as aplicações cloud**.
   1. Selecione **Concluído**.
1. Em **Condições**  >  **As aplicações do Cliente**, definir **Configurar** para **Sim**.
   1. Verifique apenas as caixas **Exchange ActiveSync clientes** e **outros clientes.**
   1. Selecione **Concluído**.
1. Sob **controlos de acesso**  >  **Grant**, selecione **Acesso ao Bloco**.
   1. Selecione **Selecionar**.
1. Confirme as suas definições e defina **Ativar** a política **apenas para reportar**.
1. Selecione **Criar** para criar para ativar a sua política.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto utilizando o modo de relatório de acesso condicional](howto-conditional-access-insights-reporting.md)

[Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)

[Como configurar um dispositivo ou aplicação multifunções para enviar e-mail usando o Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
