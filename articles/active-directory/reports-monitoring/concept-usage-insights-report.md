---
title: Relatório de utilização e informações no portal do Azure Active Directory | Documentos da Microsoft
description: Introdução ao relatório de utilização e informações no portal do Azure Active Directory
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
ms.openlocfilehash: 3fe1e72d277bffd4bc9b38ac377e33b341967e17
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806360"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Relatório de utilização e informações no portal do Azure Active Directory

Com o relatório de utilização e informações, pode obter uma vista centrada na aplicação dos seus dados de início de sessão. Pode encontrar respostas às seguintes perguntas:

*   O que estão as principais aplicações utilizadas na minha organização?
*   As aplicações tem os mais falhas inícios de sessão? 
*   Quais são os erros principais início de sessão para cada aplicativo?

## <a name="prerequisites"></a>Pré-requisitos 

Para acessar os dados do relatório de utilização e informações, precisa:

* Um inquilino do Azure AD
* Uma licença do Azure AD premium (P1/P2) para ver os dados de início de sessão
* Um utilizador no administrador global, administrador de segurança, leitor de segurança ou funções do leitor de relatório. Além disso, qualquer utilizador (não administrador) pode aceder aos seus próprios inícios de sessão. 

## <a name="access-the-usage-and-insights-report"></a>Aceder ao relatório de utilização e informações

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Selecione o diretório correto, em seguida, selecione **do Azure Active Directory** e escolha **aplicações empresariais**.
3. Do **atividade** secção, selecione **utilização & insights** para abrir o relatório. 

![Relatório de utilização e informações](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Utilize o relatório

O relatório de utilização e informações mostra a lista de aplicações com início de sessão de um ou mais em tentativa e permite-lhe classificar pelo número de inícios de sessão com êxito, inícios de sessão falhados e a taxa de êxito.

Clicando em carga mais na parte inferior da lista permite-lhe ver aplicações adicionais na página. Pode selecionar o intervalo de datas para ver todas as aplicações que foram utilizadas dentro do intervalo.

Também pode definir o foco numa aplicação específica. Selecione **ver a atividade de início de sessão** para ver o início de sessão na atividade ao longo do tempo para o aplicativo, bem como os erros principais.  

Quando seleciona um dia no gráfico de utilização do aplicativo, obtém uma lista detalhada das atividades de início de sessão para a aplicação.  

![Relatório de utilização e informações](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Passos Seguintes

* [Relatório de inícios de sessão](concept-sign-ins.md)