---
title: Como resolver relatórios de erros de sessão de resolução de problemas Microsoft Docs
description: Saiba como resolver erros de sessão de resolução de problemas utilizando relatórios do Azure Ative Directory no portal Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de9f5a21f38f741dcf65d285446d9482716d4f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85608131"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Como: Erros de sessão de resolução de problemas utilizando relatórios do Azure Ative Directory

O [relatório de inscrições](concept-sign-ins.md) no Azure Ative Directory (Azure AD) permite-lhe encontrar respostas para questões em torno da gestão do acesso às aplicações na sua organização, incluindo:

- O que é o padrão de início de sessão de um utilizador?
- Quantos utilizadores iniciaram sessão ao longo de uma semana?
- Qual é o estado destes inícios de sessão?


Além disso, o relatório de inscrições também pode ajudá-lo a resolver falhas de sessão de sessão para utilizadores na sua organização. Neste guia, aprende-se a isolar uma falha de inscrição no relatório de inscrição e a usá-lo para compreender a causa principal da falha.

## <a name="prerequisites"></a>Pré-requisitos

É necessário:

* Um inquilino AZure AD com uma licença premium (P1/P2). Veja [como começar com o Azure Ative Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição do Azure Ative Directory.
* Um utilizador, que esteja no **administrador global,** **administrador de segurança,** **leitor de segurança,** ou **reporte** o papel de leitor para o arrendatário. Além disso, qualquer utilizador pode aceder às suas próprias insiná-lo. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Erros de sessão de resolução de problemas utilizando o relatório de inscrições

1. Navegue até ao [portal Azure](https://portal.azure.com) e selecione o seu diretório.
2. Selecione **Azure Ative Directory** e selecione **Iniciar sing-ins** na secção **de Monitorização.** 
3. Utilize os filtros fornecidos para reduzir a falha, quer pelo nome de utilizador, quer pelo identificador de objetos, nome de aplicação ou data. Além disso, selecione **Falha** da redução do **estado** para mostrar apenas as insuposições falhadas. 

    ![Filtrar os resultados](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identifique a inscrição falhada que quer investigar. Selecione-o para abrir a janela de detalhes adicionais com mais informações sobre o sinse-in falhado. Note o **código de erro de iniciar** e a razão de Falha de **inscrição**. 

    ![Selecione registo](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Também pode encontrar esta informação no **separador Troubleshooting e suporte** na janela de detalhes.

    ![Resolução de problemas e apoio](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. A razão de falha descreve o erro. Por exemplo, no cenário acima referido, a razão de falha é **nome de utilizador ou senha inválido ou nome de utilizador ou senha iniciais iniciais.** A correção é simplesmente iniciar sedução novamente com o nome de utilizador e senha correta.

7. Pode obter informações adicionais, incluindo ideias para remediação, procurando o código de erro, **50126** neste exemplo, na [referência de códigos de erro de inscrição](reference-sign-ins-error-codes.md). 

8. Se tudo o resto falhar, ou o problema persistir apesar de tomar o curso de ação recomendado, [abra um bilhete](../fundamentals/active-directory-troubleshooting-support-howto.md) de apoio seguindo os passos no **separador Troubleshooting e support.** 

## <a name="next-steps"></a>Passos seguintes

* [Referência de códigos de erro de inscrição](reference-sign-ins-error-codes.md)
* [Relatório de inscrição relatório geral](concept-sign-ins.md)
