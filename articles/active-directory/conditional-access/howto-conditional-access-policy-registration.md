---
title: Acesso Condicional - Informações combinadas de segurança - Diretório Ativo Azure
description: Criar uma política personalizada de acesso condicional para registo de informações de segurança
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
ms.openlocfilehash: 4f69a94e17155ff93510d09f666bce12f628274f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295162"
---
# <a name="conditional-access-securing-security-info-registration"></a>Acesso Condicional: Assegurar o registo de informações de segurança

A segurança quando e como os utilizadores se registam para a Autenticação multi-factor Do Azure e o reset de palavra-passe self-service é agora possível com as ações do utilizador na política de Acesso Condicional. Esta funcionalidade de pré-visualização está disponível para organizações que permitiram a [pré-visualização](../authentication/concept-registration-mfa-sspr-combined.md)combinada do registo. Esta funcionalidade pode ser ativada em organizações onde pretendam utilizar condições como a localização da rede fidedigna para restringir o acesso ao registo para a Autenticação multi-factor do Azure e reposição de senhade autosserviço (SSPR). Para mais informações sobre as condições utilizáveis, consulte o artigo [Acesso Condicional: Condições](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registo a partir de um local de confiança

A seguinte política aplica-se a todos os utilizadores selecionados, que tentam registar-se utilizando a experiência de registo combinado, e bloqueiam o acesso a menos que estejam a ligar-se a partir de um local marcado como rede fidedigna.

1. No **portal Azure,** navegue até ao**Acesso Condicional**de**Segurança** >  **do Diretório** > Ativo azure.
1. Selecione **Nova política.**
1. Em Nome, insira um nome para esta política. Por exemplo, Registo combinado de **Informações de Segurança em Redes Fidedignas**.
1. Em **Atribuições**, selecione **Utilizadores e grupos**, e selecione os utilizadores e grupos a que pretende que esta política se aplique.

   > [!WARNING]
   > Os utilizadores devem estar ativados para a [pré-visualização combinada](../authentication/howto-registration-mfa-sspr-combined.md)do registo .

1. Em **aplicações ou ações cloud**, selecione as ações do **Utilizador,** verifique **registar informações de segurança (pré-visualização)**.
1. Em **condições** > **locais**.
   1. Configurar **Sim.**
   1. Incluir **qualquer local**.
   1. Excluir **todos os locais fidedignos.**
   1. Selecione **Feito** na lâmina De Localização.
   1. Selecione **Feito** na lâmina Condições.
1. Em **condições,** > **as aplicações do Cliente (Pré-visualização)**, definir **Configurar** para **Sim,** e selecionar **Feito**.
1. Sob **controlos** > de acesso**Grant**.
   1. Selecione **acesso ao bloco**.
   1. Em seguida, clique em **Selecionar**.
1. Defina **Permitir política** como **Ativado**.
1. Em seguida, selecione **Guardar**.

No passo 6 desta política, as organizações têm escolhas que podem fazer. A política acima requer o registo de uma localização de rede fidedigna. As organizações podem optar por utilizar quaisquer condições disponíveis no lugar dos **Locais.** Lembre-se que esta política é uma política de blocos, pelo que tudo incluído está bloqueado e tudo o que não corresponde ao incluído é permitido. 

Alguns podem optar por utilizar o estado do dispositivo em vez da localização no passo 6 acima:

6. Em **condições** > **Estado do dispositivo (Pré-visualização)**.
   1. Configurar **Sim.**
   1. Incluir **todo o estado do dispositivo.**
   1. Excluir **dispositivo Hybrid Azure AD aderiu** e/ou **Dispositivo marcado como conforme**
   1. Selecione **Feito** na lâmina De Localização.
   1. Selecione **Feito** na lâmina Condições.

> [!WARNING]
> Se utilizar o estado do dispositivo como condição na sua política, isso poderá afetar os utilizadores convidados no diretório. [O modo apenas de relatório](concept-conditional-access-report-only.md) pode ajudar a determinar o impacto das decisões políticas.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determine o impacto utilizando o modo apenas de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular o sinal de comportamento usando a ferramenta de acesso condicional O que se a ferramenta](troubleshoot-conditional-access-what-if.md)
