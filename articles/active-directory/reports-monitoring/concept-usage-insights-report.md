---
title: Relatório de utilização e insights / Microsoft Docs
description: Introdução ao relatório de utilização e insights no portal Azure Ative Directory
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74008272"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Relatório de utilização e insights no portal azure ative diretório

Com o relatório de utilização e insights, pode obter uma visão centrada na aplicação dos seus dados de inscrição. Pode encontrar respostas para as seguintes perguntas:

*   Quais são as aplicações mais usadas na minha organização?
*   Que aplicações têm os sign-ins mais falhados? 
*   Quais são os principais erros de inscrição para cada aplicação?

## <a name="prerequisites"></a>Pré-requisitos 

Para aceder aos dados do relatório de utilização e insights, é necessário:

* Um inquilino da AD Azure
* Uma licença azure AD premium (P1/P2) para visualizar os dados de entrada
* Um utilizador no administrador global, administrador de segurança, leitor de segurança ou papel de leitor de relatórios. Além disso, qualquer utilizador (não administradores) pode aceder aos seus próprios sign-ins. 

## <a name="access-the-usage-and-insights-report"></a>Aceda ao relatório de utilização e insights

1. Navegue para o [portal Azure.](https://portal.azure.com)
2. Selecione o diretório certo, em seguida, selecione **O Diretório Ativo Azure** e escolha **as aplicações enterprise**.
3. A partir da secção **Atividade,** selecione **Usage & insights** para abrir o relatório. 

![Relatório de Utilização e Informações](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Use o relatório

O relatório de utilização e insights mostra a lista de aplicações com um ou mais sinais na tentativa, e permite-lhe classificar pelo número de inscrições bem sucedidas, inscrições falhadas e a taxa de sucesso.

Clicar mais na carga na parte inferior da lista permite-lhe visualizar aplicações adicionais na página. Pode selecionar o intervalo de datas para visualizar todas as aplicações que tenham sido utilizadas dentro do intervalo.

Também pode definir o foco numa aplicação específica. Selecione **a atividade de início de sessão** para ver o sinal de atividade ao longo do tempo para a aplicação, bem como os erros de topo.  

Quando seleciona um dia no gráfico de utilização da aplicação, obtém uma lista detalhada das atividades de inscrição para a aplicação.  

![Relatório de Utilização e Informações](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Passos seguintes

* [Relatório de inscrição](concept-sign-ins.md)