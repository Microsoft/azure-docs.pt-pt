---
title: Acesso Condicional de resolução de problemas utilizando a ferramenta What If - Azure Ative Directory
description: Onde encontrar que políticas de Acesso Condicional foram aplicadas e porquê
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661afc08c76c6cde61b02a29a55b4a8bec932e21
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73175788"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Resolução de problemas Acesso Condicional utilizando a ferramenta What If

A [ferramenta What If](what-if-tool.md) in Conditional Access é poderosa quando se tenta perceber porque é que uma apólice foi ou não aplicada a um utilizador numa circunstância específica ou se uma política se aplicaria num estado conhecido.

A ferramenta What If is located in the **Azure portal** > **Azure Ative Diretório Diretório** > **Conditional Access** > **What If**.

![Acesso Condicional O que se a ferramenta em estado padrão](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> A ferramenta What If atualmente não avalia as políticas no modo apenas para relatórios.

## <a name="gathering-information"></a>Recolha de informação

O What Se a ferramenta requer apenas um **Utilizador** para começar. 

As seguintes informações adicionais são opcionais, mas ajudarão a reduzir o âmbito para casos específicos.

* Aplicações na app ou ações
* Endereço IP 
* País
* Plataforma de dispositivo
* Aplicativos de clientes (pré-visualização)
* Estado do dispositivo (pré-visualização) 
* Risco de inscrição

Estas informações podem ser recolhidas a partir do utilizador, do seu dispositivo ou do registo de login sessão da AD Azure.

## <a name="generating-results"></a>Gerar resultados

Insera os critérios recolhidos na secção anterior e selecione **O Que Se** gerar uma lista de resultados. 

Em qualquer momento, pode selecionar **Reset** para limpar qualquer entrada de critérios e voltar ao estado padrão.

## <a name="evaluating-results"></a>Avaliação dos resultados

### <a name="policies-that-will-apply"></a>Políticas que se aplicarão

Esta lista mostrará quais as políticas de Acesso Condicional que seriam aplicadas dadas as condições. A lista incluirá tanto os controlos de subvenção como os controlos de sessão que se aplicam. Exemplos incluem exigir a autenticação de vários fatores para aceder a uma aplicação específica.

### <a name="policies-that-will-not-apply"></a>Políticas que não se aplicarão

Esta lista mostrará políticas de Acesso Condicional que não se aplicariam se as condições se aplicassem. A lista incluirá quaisquer políticas e a razão pela qual não se aplicam. Exemplos incluem utilizadores e grupos que podem ser excluídos de uma política.

## <a name="use-case"></a>Caso de utilização

Muitas organizações criam políticas baseadas em localizações de rede, permitindo localizações fidedignas e bloqueando locais onde o acesso não deve ocorrer.

Para validar que uma configuração foi feita adequadamente, um administrador poderia usar a ferramenta What If para imitar o acesso, a partir de um local que deve ser permitido e de um local que deve ser negado.

![E se a ferramenta mostrar resultados com acesso ao Bloco](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

Neste caso, o utilizador estaria impedido de aceder a qualquer aplicação em nuvem na sua viagem à Coreia do Norte, uma vez que Contoso bloqueou o acesso a partir desse local.

Este teste poderia ser alargado para incorporar outros pontos de dados para reduzir o âmbito.

## <a name="next-steps"></a>Passos seguintes

* [O que é o Acesso Condicional?](overview.md)
* [O que é o Azure Active Directory Identity Protection?](../identity-protection/overview-v2.md)
* [O que é uma identidade de dispositivo?](../devices/overview.md)
* [Como funciona: Multi-Factor Authentication do Azure](../authentication/concept-mfa-howitworks.md)
