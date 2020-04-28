---
title: Como resolver os erros de registo de sessão relata [ Microsoft Docs
description: Saiba como resolver erros de entrada usando relatórios do Azure Ative Directory no portal Azure
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
ms.openlocfilehash: ec5fe7f62e8537a7f687202d365eb37d43b48b78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74008066"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Como: Problemas de sessão de registo utilizando relatórios do Diretório Ativo do Azure

O [relatório de inscrição](concept-sign-ins.md) no Azure Ative Directory (Azure AD) permite-lhe encontrar respostas a questões sobre a gestão do acesso às aplicações na sua organização, incluindo:

- O que é o padrão de início de sessão de um utilizador?
- Quantos utilizadores iniciaram sessão ao longo de uma semana?
- Qual é o estado destes inícios de sessão?


Além disso, o relatório de inscrição também pode ajudá-lo a resolver falhas de sessão para utilizadores na sua organização. Neste guia, aprende-se a isolar uma falha de inscrição no relatório de inscrição e a usá-lo para compreender a causa principal da falha.

## <a name="prerequisites"></a>Pré-requisitos

É necessário:

* Um inquilino DaAzure Com licença premium (P1/P2). Ver [Começar com o Azure Ative Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição de Diretório Ativo Azure.
* Um utilizador, que está no **administrador global**, administrador de **segurança,** leitor de **segurança,** ou **reporta** o papel do leitor para o inquilino. Além disso, qualquer utilizador pode aceder aos seus próprios sign-ins. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Erros de sessão de problemas utilizando o relatório de inscrição

1. Navegue para o [portal Azure](https://portal.azure.com) e selecione o seu diretório.
2. Selecione **Diretório Ativo Azure** e selecione **Sign-ins** da secção **de Monitorização.** 
3. Utilize os filtros fornecidos para reduzir a falha, quer pelo nome de utilizador quer pelo identificador de objetos, nome da aplicação ou data. Além disso, selecione **Falha** a partir da queda de **Estado** para exibir apenas os insines falhados. 

    ![Filtrar os resultados](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identifique o sinal falhado que quer investigar. Selecione-a para abrir a janela de detalhes adicionais com mais informações sobre o insessão falhado. Note o **código de erro de entrada** e a **razão**de falha . 

    ![Selecione o registo](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Também pode encontrar esta informação no separador De resolução de **problemas e suporte** na janela de detalhes.

    ![Resolução de problemas e apoio](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. A razão da falha descreve o erro. Por exemplo, no cenário acima, a razão de falha é **o nome de utilizador ou palavra-passe inválido ou o nome de utilizador ou palavra-passe inválidono no local.** A correção é simplesmente iniciar sessão com o nome de utilizador e senha corretos.

7. Pode obter informações adicionais, incluindo ideias para a reparação, procurando o código de erro, **50126** neste exemplo, na referência dos [códigos de erro de inscrição](reference-sign-ins-error-codes.md). 

8. Se tudo o resto falhar, ou o problema persistir, apesar de tomar o curso de ação recomendado, [abra um bilhete](../fundamentals/active-directory-troubleshooting-support-howto.md) de apoio seguindo os passos no separador de resolução de **problemas e suporte.** 

## <a name="next-steps"></a>Passos seguintes

* [Referência de códigos de erro de iniciar sessão](reference-sign-ins-error-codes.md)
* [Visão geral do relatório de inscrição](concept-sign-ins.md)
