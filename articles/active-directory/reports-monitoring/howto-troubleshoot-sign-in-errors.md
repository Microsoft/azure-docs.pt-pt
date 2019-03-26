---
title: Como resolver problemas de erros de início de sessão através de relatórios do Azure Active Directory | Documentos da Microsoft
description: Saiba como resolver erros de início de sessão a utilização de relatórios do Azure Active Directory no portal do Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26a3594b00f19c2790e9dfd5f09fbdc7d73d478f
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434896"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Como: Resolver problemas de erros de início de sessão através de relatórios do Azure Active Directory

O [relatório de inícios de sessão](concept-sign-ins.md) no Azure Active Directory (Azure AD) permite-lhe encontrar respostas para perguntas sobre gerir o acesso as aplicações na sua organização, incluindo:

- O que é o padrão de início de sessão de um utilizador?
- Quantos utilizadores iniciaram sessão ao longo de uma semana?
- Qual é o estado destes inícios de sessão?


Além disso, o relatório de inícios de sessão pode também ajudar a resolver problemas de falhas de início de sessão para utilizadores na sua organização. Neste guia, irá aprender a isolar uma falha de início de sessão no relatório de inícios de sessão e utilizá-lo para compreender a causa da falha.

## <a name="prerequisites"></a>Pré-requisitos

É necessário:

* Um inquilino do Azure AD com uma licença premium (P1/P2). Ver [introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição do Azure Active Directory.
* Um utilizador, o que está a **administrador global**, **administrador de segurança**, **leitor de segurança**, ou **leitor de relatório** função para o inquilino. Além disso, qualquer usuário pode acessar seus próprios inícios de sessão. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Resolver erros de início de sessão usando o relatório de inícios de sessão

1. Navegue para o [portal do Azure](https://portal.azure.com) e selecione o seu diretório.
2. Selecione **do Azure Active Directory** e selecione **inícios de sessão** partir os **monitorização** secção. 
3. Utilize os filtros de fornecido para restringir a falha, o identificador de nome de utilizador ou o objeto, o nome da aplicação ou a data. Além disso, selecione **falha** partir do **estado** pendente para apresentar apenas os falhadas inícios de sessão. 

    ![Filtrar os resultados](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identifique o Falha ao início de sessão que pretende investigar. Selecione-o para abrir a janela detalhes adicionais com mais informações sobre o início de sessão-in com falha. Tome nota da **início de sessão do código de erro** e **motivo da falha**. 

    ![Selecione o registo](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Também pode encontrar estas informações no **resolução de problemas e suporte** separador na janela de detalhes.

    ![Resolução de problemas e suporte](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. O motivo da falha descreve o erro. Por exemplo, no cenário acima, o motivo da falha é **nome de utilizador inválido ou palavra-passe ou nome de utilizador inválido no local ou palavra-passe**. A correção é simplesmente início de sessão novamente com o nome de utilizador correto e a palavra-passe.

7. Pode obter informações adicionais, incluindo ideias para atualização, ao procurar o código de erro **50126** neste exemplo, no [referência de códigos de erro dos inícios de sessão](reference-sign-ins-error-codes.md). 

8. Se tudo o resto falhar, ou se o problema persistir, apesar do procedimento recomendado, a demorar [abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) seguir os passos a **resolução de problemas e suporte** separador. 

## <a name="next-steps"></a>Passos Seguintes

* [Referenciam de códigos de erro de inícios de sessão](reference-sign-ins-error-codes.md)
* [Descrição geral do relatório de inícios de sessão](concept-sign-ins.md)
