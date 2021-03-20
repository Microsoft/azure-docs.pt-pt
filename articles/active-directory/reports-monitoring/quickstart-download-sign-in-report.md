---
title: 'Início Rápido: Transferir um relatório de início de sessão no portal do Azure | Microsoft Docs'
description: Aprender a transferir um relatório de início de sessão no portal do Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86bc72f69903134afa3750ad6b72486a713b6cc0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011919"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Início Rápido: Transferir um relatório de início de sessão no portal do Azure

Neste início rápido, vai aprender a transferir os dados de início de sessão do seu inquilino das últimas 24 horas. Você pode baixar até 250.000 registos a partir do portal Azure. Os registos são classificados pela mais recente, por padrão, obtém-se os mais recentes 250.000 registos. 

## <a name="prerequisites"></a>Pré-requisitos

É necessário:

* Um inquilino do Azure Active Directory, com uma licença Premium para ver o relatório de atividade de início de sessão. Veja [como começar com o Azure Ative Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição do Azure Ative Directory. Note que se não tiver quaisquer dados de atividades antes da atualização, levará alguns dias para que os dados apareçam nos relatórios depois de atualizar para uma licença premium.
* Um utilizador, que esteja no **Administrador de Segurança,** **Leitor de Segurança,** **Leitor de Relatório** ou **Administrador Global** para o arrendatário. Além disso, qualquer utilizador no inquilino pode aceder aos seus inícios de sessão.

## <a name="quickstart-download-a-sign-in-report"></a>Início Rápido: Transferir um relatório de início de sessão

1. Navegue até ao [portal Azure.](https://portal.azure.com)
2. Selecione **Azure Active Directory** no painel de navegação esquerdo e utilize o botão **Trocar diretório** para selecionar o Active Directory.
3. No dashboard, selecione **Azure Active Directory** e, em seguida, selecione **Inícios de sessão**. 
4. Escolha **últimas 24 horas** no filtro pendente **Data** e selecione **Aplicar** para ver os inícios de sessão das últimas 24 horas. 
5. Selecione o botão **Descarregar,** selecione **CSV** como formato de ficheiro e especifique um nome de ficheiro para descarregar um ficheiro CSV contendo os registos filtrados. 

![Relatórios](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Passos seguintes

* [Relatórios de atividade de início de sessão no portal do Azure Active Directory](concept-sign-ins.md)
* [Retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md)
* [Latências dos relatórios do Azure Active Directory](reference-reports-latencies.md)
