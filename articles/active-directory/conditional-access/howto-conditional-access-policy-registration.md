---
title: Acesso Condicional - Informações combinadas de segurança - Diretório Ativo Azure
description: Crie uma política de acesso condicional personalizado para o registo de informações de segurança
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e99f7466bd3b7ed5517157ca3fa45e7c3241217
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599759"
---
# <a name="conditional-access-securing-security-info-registration"></a>Acesso Condicional: Proteger o registo de informações de segurança

Garantir quando e como os utilizadores se registam para autenticação multi-factor AD Azure e redefinição de senha de autosserviço é agora possível com as ações do utilizador na política de Acesso Condicional. Esta funcionalidade de pré-visualização está disponível para organizações que tenham ativado a [pré-visualização do registo combinado](../authentication/concept-registration-mfa-sspr-combined.md). Esta funcionalidade pode ser ativada em organizações onde pretendem utilizar condições como a localização de rede fidedigna para restringir o acesso ao registo de autenticação multi-factor AD Azure e redefinição de senha de autosserviço (SSPR). Para mais informações sobre condições utilizáveis, consulte o artigo [Acesso Condicionado: Condições.](concept-conditional-access-conditions.md)

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registo a partir de um local de confiança

A seguinte política aplica-se a todos os utilizadores selecionados, que tentam registar-se utilizando a experiência de registo combinado, e bloqueiam o acesso a menos que estejam a ligar-se a partir de um local marcado como rede fidedigna.

1. No **portal Azure,** navegue pelo **Azure Ative Directory**  >    >  **Security Conditional Access**.
1. Selecione **Nova política**.
1. Em Nome, Insira um Nome para esta política. Por exemplo, **Registo combinado de informações de segurança em redes fidedignas**.
1. Em **Atribuições**, selecione **Utilizadores e grupos**, e selecione os utilizadores e grupos a que pretende que esta política se aplique.

   > [!WARNING]
   > Os utilizadores devem estar habilitados para o [registo combinado.](../authentication/howto-registration-mfa-sspr-combined.md)

   1. Em **'Excluir',** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou break-glass da sua organização. 
   1. Selecione **Concluído**.
1. No **âmbito de aplicações ou ações cloud**, selecione as **ações do Utilizador,** consulte **as informações de segurança do Registo**.
1. Em   >  **Locais de Condições**.
   1. Configurar **Sim.**
   1. Incluir **qualquer localização.**
   1. Excluir **todos os locais fidedignos.**
   1. Selecione **Feito** na lâmina de locais.
   1. Selecione **Feito** na lâmina Condições.
1. Em **Condições**  >  **As aplicações do Cliente (Pré-visualização)**, **configurar configurar** para **Sim**, e selecione **Feito**.
1. Sob **controlos de acesso**  >  **Grant**.
   1. Selecione **o acesso ao Bloco**.
   1. Em seguida, clique em **Selecionar**.
1. Defina **Ativar política** como **Ativado**.
1. Em seguida, selecione **Guardar**.

No passo 6 desta política, as organizações têm escolhas que podem fazer. A política acima requer o registo de uma localização de rede fidedigna. As organizações podem optar por utilizar todas as condições disponíveis no lugar das **Localizações.** Lembre-se que esta política é uma política de blocos para que tudo o que estiver incluído seja bloqueado e qualquer coisa que não corresponda ao incluso é permitido. 

Alguns podem optar por utilizar o estado do dispositivo em vez da localização no passo 6 acima:

6. Em **Condições**  >  **Estado do dispositivo (Pré-visualização)**.
   1. Configurar **Sim.**
   1. Incluir **todo o estado do dispositivo**.
   1. Excluir **dispositivo Híbrido Azure AD unidos** e/ou Dispositivo marcado como **conforme**
   1. Selecione **Feito** na lâmina de locais.
   1. Selecione **Feito** na lâmina Condições.

> [!WARNING]
> Se utilizar o estado do dispositivo como condição na sua política, isso poderá ter impacto nos utilizadores convidados no diretório. [O modo apenas de relatório](concept-conditional-access-report-only.md) pode ajudar a determinar o impacto das decisões políticas.
> Note que o modo apenas de relatório não é aplicável para políticas de acesso condicional com âmbito de "Ações do utilizador".

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto utilizando o modo de relatório de acesso condicional](howto-conditional-access-insights-reporting.md)

[Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)
