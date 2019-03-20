---
title: Ver histórico de auditoria para funções de diretório do Azure AD no PIM | Documentos da Microsoft
description: Saiba como ver o histórico de auditoria para funções de diretório do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b91e2c15e86cdda4daab27892fa7a2b6ed0c0e0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008914"
---
# <a name="view-audit-history-for-azure-ad-directory-roles-in-pim"></a>Ver o histórico de auditoria para funções de diretório do Azure AD no PIM
Pode utilizar o histórico de auditoria do Privileged Identity Management (PIM) para ver todas as atribuições de utilizador e ativações dentro de um determinado período de tempo para funções de contas com privilégios. Se pretender ver o histórico de auditoria de atividade no seu inquilino, incluindo administrador, o utilizador final e atividade de sincronização, pode utilizar o [relatórios de acesso e utilização do Azure Active Directory.](../reports-monitoring/overview-reports.md)

## <a name="navigate-to-audit-history"></a>Navegue para o histórico de auditorias
Partir do [portal do Azure](https://portal.azure.com) dashboard, selecione o **Azure AD Privileged Identity Management** aplicação. A partir daí, acessar o histórico de auditoria clicando **gerir funções com privilégios** > **histórico de auditorias** no dashboard do PIM.

![Histórico de auditorias](media/azure-ad-pim-approval-workflow/image021.png)

> [!NOTE]
> Pode classificar os dados por ação e procure "Ativação aprovada"


## <a name="audit-history-graph"></a>Gráfico do histórico de auditoria
Pode utilizar o histórico de auditoria para ver o total de ativações, ativações máximas por dia e ativações média por dia num gráfico de linha.  Também pode filtrar os dados por função, se houver mais de uma função no histórico de auditorias.

Utilize o **tempo**, **ação**, e **função** botões para ordenar o histórico.

## <a name="audit-history-list"></a>Lista de histórico de auditoria
As colunas na lista de histórico de auditoria são:

* **Requerente** -o utilizador que pediu a ativação de função ou a alteração.  Se o valor for "Azure System", verifique o histórico de auditoria do Azure para obter mais informações.
* **Utilizador** -o utilizador que está atribuído a uma função ou a ativação.
* **Função** -a função atribuída ou ativada pelo utilizador.
* **Ação** - as ações executadas pelo requerente. Isto pode incluir a atribuição, unassignment, ativação ou desativação.
* **Tempo** - quando ocorreu a ação.
* **Raciocinando** -se qualquer texto foi inserido no campo motivo durante a ativação, irá aparecer aqui.
* **Expiração** - relevante apenas para a ativação de funções.

## <a name="filter-audit-history"></a>Histórico de auditoria de filtro
Pode filtrar as informações que aparece no histórico de auditorias, clicando a **filtro** botão.  O **painel de parâmetros do gráfico de atualização** aparecerá.

Depois de definir os filtros, clique em **atualização** para filtrar os dados na história.  Se os dados não aparecerem imediatamente, atualize a página.

### <a name="change-the-date-range"></a>Alterar o intervalo de datas
Utilize o **hoje**, **semana passada**, **último mês**, ou **personalizado** botões para alterar o intervalo de tempo do histórico de auditorias.

Quando escolhe o **personalizada** botão, terá a chance um **da** campo de data e um **para** campo de data para especificar um intervalo de datas para o histórico.  Pode introduzir as datas no formato DD/MM/AAAA ou clique nas **calendário** ícone e selecione a data de um calendário.

### <a name="change-the-roles-included-in-the-history"></a>Alterar as funções incluídas no histórico
Marque ou desmarque os **função** caixa de verificação junto a cada função para incluir ou exclui-lo a partir do histórico de.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos Seguintes

- [Ver histórico de atividade e auditoria para funções de recursos do Azure no PIM](azure-pim-resource-rbac.md)
