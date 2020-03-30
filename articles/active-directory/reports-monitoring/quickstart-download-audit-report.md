---
title: 'Início Rápido: Transferir um relatório de auditoria no portal do Azure | Microsoft Docs'
description: Aprender a transferir um relatório de auditoria no portal do Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6cbea49fe39c92c8a2fc50e501cb4ef5cff74b1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "68989683"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Início Rápido: Transferir um relatório de auditoria no portal do Azure

Neste arranque rápido, você aprende a descarregar um ficheiro CSV dos registos de auditoria para o seu inquilino nas últimas 24 horas. Você pode baixar até 250.000 registos do portal Azure. Os registos são classificados pela mais recente, por isso, por padrão, obtém-se os mais recentes 250.000 registos. 

## <a name="prerequisites"></a>Pré-requisitos

É necessário:

* Um inquilino do Azure Active Directory. 
* Um utilizador, que está no papel de Administrador de **Segurança,** **Leitor de Segurança,** ou **Administrador Global** para o inquilino. Além disso, qualquer utilizador no inquilino pode aceder aos seus próprios registos de auditoria.

## <a name="quickstart-download-an-audit-report"></a>Início Rápido: Transferir um relatório de auditoria

1. Navegue para o [portal Azure.](https://portal.azure.com)
2. Selecione **Azure Active Directory** no painel de navegação esquerdo e utilize o botão **Trocar diretório** para selecionar o Active Directory.
3. No dashboard, selecione **Azure Active Directory** e, em seguida, selecione **Registos de auditoria**. 
4. Escolha **últimas 24 horas** no filtro pendente **Intervalo de datas** e selecione **Aplicar** para ver os registos de auditoria das últimas 24 horas. 
5. Selecione o botão **Descarregamento,** selecione **CSV** como formato de ficheiro e especifique um nome de ficheiro para descarregar um ficheiro CSV contendo os registos filtrados. 

![Relatórios](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Passos seguintes

* [Relatórios de atividade de início de sessão no portal do Azure Active Directory](concept-sign-ins.md)
* [Retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md)
* [Latências dos relatórios do Azure Active Directory](reference-reports-latencies.md)
