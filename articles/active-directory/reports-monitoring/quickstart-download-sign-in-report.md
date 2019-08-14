---
title: 'Início Rápido: Transferir um relatório de início de sessão no portal do Azure | Microsoft Docs'
description: Aprender a transferir um relatório de início de sessão no portal do Azure
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cbd71696c3508a464b1343d552cba4a3391066f
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989661"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Início rápido: Baixar um relatório de entrada usando o portal do Azure

Neste início rápido, vai aprender a transferir os dados de início de sessão do seu inquilino das últimas 24 horas. Você pode baixar até 250.000 registros do portal do Azure. Os registros são classificados pela mais recente por padrão; você obtém os registros 250.000 mais recentes. 

## <a name="prerequisites"></a>Pré-requisitos

É necessário:

* Um inquilino do Azure Active Directory, com uma licença Premium para ver o relatório de atividade de início de sessão. Consulte [introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar sua edição do Azure Active Directory. Observe que, se você não tiver dados de atividades antes da atualização, levará alguns dias para que os dados sejam exibidos nos relatórios após a atualização para uma licença Premium.
* Um usuário, que está no **administrador de segurança**, no **leitor de segurança**, no **leitor de relatório** ou na função de **administrador global** para o locatário. Além disso, qualquer utilizador no inquilino pode aceder aos seus inícios de sessão.

## <a name="quickstart-download-a-sign-in-report"></a>Início rápido: Transferir um relatório de início de sessão

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** no painel de navegação esquerdo e utilize o botão **Trocar diretório** para selecionar o Active Directory.
3. No dashboard, selecione **Azure Active Directory** e, em seguida, selecione **Inícios de sessão**. 
4. Escolha **últimas 24 horas** no filtro pendente **Data** e selecione **Aplicar** para ver os inícios de sessão das últimas 24 horas. 
5. Selecione o botão **baixar** , selecione **CSV** como o formato de arquivo e especifique um nome de arquivo para baixar um arquivo CSV contendo os registros filtrados. 

![Relatórios](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Passos Seguintes

* [Relatórios de atividade de início de sessão no portal do Azure Active Directory](concept-sign-ins.md)
* [Retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md)
* [Latências dos relatórios do Azure Active Directory](reference-reports-latencies.md)
