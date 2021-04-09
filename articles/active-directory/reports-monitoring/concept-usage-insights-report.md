---
title: O relatório de utilização e insights | Microsoft Docs
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
ms.openlocfilehash: 54bce5e839786862a6dac9aeb685dd364547a09a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685038"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Relatório de utilização e insights no portal Azure Ative Directory

Com o relatório de utilização e insights, pode obter uma visão centrada na aplicação dos seus dados de inscrição. Pode encontrar respostas para as seguintes perguntas:

*   Quais são as aplicações mais usadas na minha organização?
*   Que aplicações têm as inscrições mais falhadas? 
*   Quais são os principais erros de inscrição para cada aplicação?

## <a name="prerequisites"></a>Pré-requisitos 

Para aceder aos dados a partir do relatório de utilização e insights, é necessário:

* Um inquilino da AD AZure
* Uma licença Azure AD premium (P1/P2) para visualizar os dados de inscrição
* Um utilizador no administrador global, administrador de segurança, leitor de segurança ou funções de leitor de relatórios. Além disso, qualquer utilizador (não administradores) pode aceder às suas próprias entradas. 

## <a name="access-the-usage-and-insights-report"></a>Aceda ao relatório de utilização e insights

1. Navegue até ao [portal Azure.](https://portal.azure.com)
2. Selecione o diretório certo, em seguida, selecione **Azure Ative Directory** e escolha **aplicações Enterprise**.
3. A partir da secção **De Atividade,** selecione **Usage & insights** para abrir o relatório. 

![A screenshot mostra o uso & insights selecionados a partir da secção Atividade.](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Use o relatório

O relatório de utilização e insights mostra a lista de aplicações com uma ou mais tentativas de inscrição, e permite-lhe classificar pelo número de entradas bem sucedidas, inserções falhadas e a taxa de sucesso.

Clicar **mais** na parte inferior da lista permite-lhe visualizar aplicações adicionais na página. Pode selecionar o intervalo de datas para visualizar todas as aplicações que tenham sido utilizadas dentro do intervalo.

![O Screenshot mostra o uso & insights para a atividade da Aplicação, onde pode selecionar uma atividade de visualização de gama e visualização de s-in para diferentes aplicações.](./media/concept-usage-insights-report/usage-and-insights-report.png)

Também pode definir o foco numa aplicação específica. Selecione **a atividade de início de sposição** para ver a atividade de início ao longo do tempo para a aplicação, bem como os erros de topo.  

Quando seleciona um dia no gráfico de utilização da aplicação, obtém uma lista detalhada das atividades de inscrição para a aplicação.  

:::image type="content" source="./media/concept-usage-insights-report/usage-and-insights-application-report.png" alt-text="A screenshot mostra o uso & insights para uma aplicação específica onde você pode ver um gráfico para a atividade de iniciar sposição.":::

## <a name="next-steps"></a>Passos seguintes

* [Relatório de inscrições](concept-sign-ins.md)
