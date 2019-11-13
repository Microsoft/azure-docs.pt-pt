---
title: Relatório de uso e informações | Microsoft Docs
description: Introdução ao relatório de uso e informações no portal de Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b3db86137207ae726c7befc393f62590fd1456d7
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008272"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Relatório de uso e informações no portal de Azure Active Directory

Com o relatório de uso e informações, você pode obter uma exibição centrada no aplicativo de seus dados de entrada. Você pode encontrar respostas para as seguintes perguntas:

*   Quais são os principais aplicativos usados em minha organização?
*   Quais aplicativos têm as entradas mais malsucedidas? 
*   Quais são os erros de entrada mais importantes para cada aplicativo?

## <a name="prerequisites"></a>Pré-requisitos 

Para acessar os dados do relatório de uso e informações, você precisa:

* Um locatário do Azure AD
* Uma licença do Azure AD Premium (P1/P2) para exibir os dados de entrada
* Um usuário nas funções administrador global, administrador de segurança, leitor de segurança ou leitor de relatório. Além disso, qualquer usuário (não administradores) pode acessar suas próprias entradas. 

## <a name="access-the-usage-and-insights-report"></a>Acessar o relatório de uso e informações

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Selecione o diretório certo e, em seguida, selecione **Azure Active Directory** e escolha **aplicativos empresariais**.
3. Na seção **atividade** , selecione **uso & insights** para abrir o relatório. 

![Relatório de Utilização e Informações](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Usar o relatório

O relatório uso e informações mostra a lista de aplicativos com uma ou mais tentativas de entrada e permite que você classifique o número de entradas bem-sucedidas, entradas com falha e a taxa de êxito.

Clicar em carregar mais na parte inferior da lista permite que você exiba aplicativos adicionais na página. Você pode selecionar o intervalo de datas para exibir todos os aplicativos que foram usados dentro do intervalo.

Você também pode definir o foco em um aplicativo específico. Selecione **Exibir atividade de entrada** para ver a atividade de entrada ao longo do tempo para o aplicativo, bem como os principais erros.  

Ao selecionar um dia no grafo de uso do aplicativo, você obtém uma lista detalhada das atividades de entrada para o aplicativo.  

![Relatório de Utilização e Informações](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Passos seguintes

* [Relatório de entradas](concept-sign-ins.md)