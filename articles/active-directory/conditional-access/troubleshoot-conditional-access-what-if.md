---
title: Resolução de problemas Acesso Condicional usando a ferramenta What If - Azure Ative Directory
description: Onde encontrar que políticas de acesso condicional foram aplicadas e porquê
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0112ab53c501d639d3f8e0d09d82ef3a12cb93a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94837251"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Resolução de problemas Acesso Condicional usando a ferramenta E Se

A [ferramenta What If](what-if-tool.md) in Conditional Access é poderosa quando tenta entender por que uma política foi ou não aplicada a um utilizador em uma circunstância específica ou se uma política seria aplicada em um estado conhecido.

A ferramenta What If está localizada no **portal Azure**  >  **Ative Directy**  >  **Acesso Condicionado** E  >  **Se**.

![Acesso condicional O que se a ferramenta estiver em estado predefinido](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> A ferramenta What If não avalia atualmente as políticas no modo apenas de relatório.

## <a name="gathering-information"></a>Recolha de informação

A ferramenta What If requer apenas um **Utilizador** para começar. 

As seguintes informações adicionais são facultativas, mas ajudarão a reduzir o âmbito de aplicação de casos específicos.

* Aplicações na app ou ações
* Endereço IP 
* País/Região
* Plataforma de dispositivo
* Aplicativos de cliente (pré-visualização)
* Estado do dispositivo (pré-visualização) 
* Risco de início de sessão

Estas informações podem ser recolhidas a partir do utilizador, do seu dispositivo ou do registo de login AZure AD.

## <a name="generating-results"></a>Geração de resultados

Insira os critérios recolhidos na secção anterior e selecione **E Se** gerar uma lista de resultados. 

Em qualquer ponto, pode selecionar **Reset** para limpar qualquer entrada de critérios e voltar ao estado predefinido.

## <a name="evaluating-results"></a>Avaliação de resultados

### <a name="policies-that-will-apply"></a>Políticas que serão aplicadas

Esta lista mostrará quais as políticas de acesso condicional aplicadas dadas as condições. A lista incluirá tanto os controlos de concessão como de sessão que se aplicam. Exemplos incluem exigir a autenticação de vários fatores para aceder a uma aplicação específica.

### <a name="policies-that-will-not-apply"></a>Políticas que não se aplicarão

Esta lista mostrará políticas de Acesso Condicional que não se aplicam se as condições aplicadas. A lista incluirá quaisquer políticas e a razão pela qual não se aplicam. Exemplos incluem utilizadores e grupos que podem ser excluídos de uma política.

## <a name="use-case"></a>Caso de utilização

Muitas organizações criam políticas baseadas em localizações de rede, permitindo localizações fidedignas e bloqueando locais onde o acesso não deve ocorrer.

Para validar que uma configuração foi feita adequadamente, um administrador poderia usar a ferramenta What If para imitar o acesso, a partir de um local que deve ser permitido e a partir de um local que deve ser negado.

[![E se a ferramenta mostrar resultados com acesso ao Bloco](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png#lightbox)

Neste caso, o utilizador estaria impedido de aceder a qualquer aplicação em nuvem na sua viagem à Coreia do Norte, uma vez que Contoso bloqueou o acesso a partir desse local.

Este teste poderia ser expandido para incorporar outros pontos de dados para reduzir o âmbito.

## <a name="next-steps"></a>Passos seguintes

* [O que é o Acesso Condicional?](overview.md)
* [O que é o Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)
* [O que é um ID do dispositivo?](../devices/overview.md)
* [Como funciona: Autenticação multi-factor Azure AD](../authentication/concept-mfa-howitworks.md)