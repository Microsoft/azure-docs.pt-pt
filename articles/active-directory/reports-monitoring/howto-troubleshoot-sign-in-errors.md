---
title: Como solucionar problemas de erros de entrada usando relatórios de Azure Active Directory | Microsoft Docs
description: Saiba como solucionar problemas de erros de entrada usando Azure Active Directory relatórios no portal do Azure
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c901395436c8ed660c50b7342a804143d57db3c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988175"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Como: Solucionar problemas de erros de entrada usando relatórios de Azure Active Directory

O [relatório de entradas](concept-sign-ins.md) no Azure Active Directory (Azure AD) permite que você encontre respostas para perguntas sobre o gerenciamento de acesso aos aplicativos em sua organização, incluindo:

- O que é o padrão de início de sessão de um utilizador?
- Quantos utilizadores iniciaram sessão ao longo de uma semana?
- Qual é o estado destes inícios de sessão?


Além disso, o relatório de entradas também pode ajudá-lo a solucionar problemas de falhas de entrada para usuários em sua organização. Neste guia, você aprende a isolar uma falha de entrada no relatório de entradas e a usá-la para entender a causa raiz da falha.

## <a name="prerequisites"></a>Pré-requisitos

É necessário:

* Um locatário do Azure AD com uma licença Premium (P1/P2). Consulte [introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar sua edição do Azure Active Directory.
* Um usuário, que está na função **administrador global**, **administrador de segurança**, **leitor de segurança**ou **leitor de relatório** para o locatário. Além disso, qualquer usuário pode acessar suas próprias entradas. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Solucionar problemas de erros de conexão usando o relatório de entradas

1. Navegue até a [portal do Azure](https://portal.azure.com) e selecione seu diretório.
2. Selecione **Azure Active Directory** e as **entradas** na seção **monitoramento** . 
3. Use os filtros fornecidos para restringir a falha, seja pelo identificador de nome de usuário ou objeto, nome do aplicativo ou data. Além disso, selecione **falha** na lista suspensa **status** para exibir somente as entradas com falha. 

    ![Filtrar os resultados](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identifique a entrada com falha que você deseja investigar. Selecione-o para abrir a janela detalhes adicionais com mais informações sobre a entrada com falha. Anote o **código de erro de entrada** e o **motivo da falha**. 

    ![Selecionar registro](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Você também pode encontrar essas informações na guia **solução de problemas e suporte** na janela de detalhes.

    ![Resolução de problemas e suporte](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. O motivo da falha descreve o erro. Por exemplo, no cenário acima, o motivo da falha é **nome de usuário ou senha inválido ou nome de usuário ou senha local inválido**. A correção é simplesmente entrar novamente com o nome de usuário e a senha corretos.

7. Você pode obter informações adicionais, incluindo ideias para correção, pesquisando o código de erro **50126** neste exemplo, na [referência de códigos de erro de entradas](reference-sign-ins-error-codes.md). 

8. Se tudo falhar, ou o problema persistir, apesar de fazer o curso de ação recomendado, [abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) seguindo as etapas na guia **solução de problemas e suporte** . 

## <a name="next-steps"></a>Passos seguintes

* [Referência de códigos de erro de entradas](reference-sign-ins-error-codes.md)
* [Visão geral do relatório de entradas](concept-sign-ins.md)
