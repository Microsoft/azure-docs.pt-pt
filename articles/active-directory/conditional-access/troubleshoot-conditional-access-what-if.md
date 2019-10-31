---
title: Solucionar problemas de acesso condicional usando a ferramenta de What If-Azure Active Directory
description: Onde encontrar quais políticas de acesso condicional foram aplicadas e por que
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
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175788"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Solucionando problemas de acesso condicional usando a ferramenta de What If

A [ferramenta de What If](what-if-tool.md) no acesso condicional é eficiente ao tentar entender por que uma política foi ou não foi aplicada a um usuário em uma circunstância específica ou se uma política se aplicaria em um estado conhecido.

A ferramenta de What If está localizada na **portal do Azure** > **Azure Active Directory** > **acesso condicional** > **What If.**

![Ferramenta de What If de acesso condicional no estado padrão](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> Atualmente, a ferramenta de What If não avalia políticas no modo somente de relatório.

## <a name="gathering-information"></a>Coletando informações

A ferramenta de What If requer apenas um **usuário** para começar. 

As informações adicionais a seguir são opcionais, mas ajudarão a restringir o escopo para casos específicos.

* Aplicativos de nuvem ou ações
* Endereço IP 
* País
* Plataforma do dispositivo
* Aplicativos cliente (visualização)
* Estado do dispositivo (versão prévia) 
* Risco de entrada

Essas informações podem ser coletadas do usuário, de seu dispositivo ou do log de entradas do Azure AD.

## <a name="generating-results"></a>Gerando resultados

Insira os critérios coletados na seção anterior e selecione **What If** para gerar uma lista de resultados. 

A qualquer momento, você pode selecionar **Redefinir** para limpar qualquer entrada de critério e retornar ao estado padrão.

## <a name="evaluating-results"></a>Avaliando resultados

### <a name="policies-that-will-apply"></a>Políticas que serão aplicadas

Esta lista mostrará quais políticas de acesso condicional se aplicam de acordo com as condições. A lista incluirá os controles de concessão e de sessão que se aplicam. Os exemplos incluem exigir a autenticação multifator para acessar um aplicativo específico.

### <a name="policies-that-will-not-apply"></a>Políticas que não serão aplicadas

Esta lista mostrará políticas de acesso condicional que não se aplicarão se as condições forem aplicadas. A lista incluirá todas as políticas e o motivo pelo qual elas não se aplicam. Os exemplos incluem usuários e grupos que podem ser excluídos de uma política.

## <a name="use-case"></a>Caso de utilização

Muitas organizações criam políticas com base em locais de rede, permitindo locais confiáveis e locais de bloqueio onde o acesso não deve ocorrer.

Para validar que uma configuração foi feita adequadamente, um administrador pode usar a ferramenta de What If para imitar o acesso, de um local que deve ser permitido e de um local que deve ser negado.

![Ferramenta de What If mostrando resultados com acesso de bloqueio](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

Nessa instância, o usuário será impedido de acessar qualquer aplicativo de nuvem em sua viagem para a Coreia do Norte, pois a contoso bloqueou o acesso desse local.

Esse teste pode ser expandido para incorporar outros pontos de dados para restringir o escopo.

## <a name="next-steps"></a>Passos seguintes

* [O que é o acesso condicional?](overview.md)
* [O que é Azure Active Directory Identity Protection?](../identity-protection/overview-v2.md)
* [O que é uma identidade de dispositivo?](../devices/overview.md)
* [Como funciona: Multi-Factor Authentication do Azure](../authentication/concept-mfa-howitworks.md)
