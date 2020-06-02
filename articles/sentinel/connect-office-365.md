---
title: Ligar registos do Office 365 ao Azure Sentinel Microsoft Docs
description: Saiba como ligar os dados do Office 365 ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: yelevin
ms.openlocfilehash: bcd00247486faeea47ef4a4a43fa1df5420321e6
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248945"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Ligar registos do Office 365 ao Azure Sentinel

O conector de log [office 365](https://docs.microsoft.com/office/) traz informações sobre o Azure Sentinel sobre as atividades de utilizador e administração em curso em **Exchange** and **SharePoint** (incluindo **o OneDrive).** Esta informação inclui detalhes de ações como transferências de ficheiros, pedidos de acesso enviados, alterações em eventos de grupo e operações de caixa de correio, bem como os detalhes do utilizador que realizou as ações. Ligar os registos do Office 365 ao Azure Sentinel permite-lhe visualizar e analisar estes dados nos seus livros, consultar-se para criar alertas personalizados e incorporá-los para melhorar o seu processo de investigação, dando-lhe mais informações sobre a segurança do seu Office 365.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no seu espaço de trabalho Azure Sentinel.

- Deve ser administrador global ou administrador de segurança do seu inquilino.

- O seu escritório 365 deve estar no mesmo inquilino que o seu espaço de trabalho Azure Sentinel.

> [!IMPORTANT]
> - Para que o conector possa aceder aos dados através da API de Atividade de Gestão 365, deve ter **registo de auditoria unificado** ativado no seu Office 365. Dependendo do tipo de licença Do Office 365 / Microsoft 365 que tem, pode ou não ser ativada por padrão. Consulte o [Centro de Segurança e Conformidade do Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) para verificar o estado de registo de auditoria unificado de acordo com o seu tipo de licença.
> - Também pode ativar, desativar e verificar o estado atual do registo de auditoria unificado do Office 365. Para obter instruções, consulte [o Registo de auditoria do Turn Office 365 para ligar ou desligar](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Para mais informações, consulte a [referência API da Atividade de Gestão 365.](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)


   > [!NOTE]
   > Como referido acima, e como pode ver na página de conector nos **tipos de dados,** o conector Azure Sentinel Office 365 suporta atualmente a ingestão de registos de auditoria apenas a partir de Microsoft Exchange e SharePoint (incluindo OneDrive). No entanto, existem algumas soluções externas se estiver interessado em trazer [dados de equipas](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761) ou [outros dados do Office](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) para o Azure Sentinel. 

## <a name="enable-the-office-365-log-connector"></a>Ativar o conector de registo do Office 365

1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Na lista de **conectores de dados,** clique **no Office 365**e, em seguida, no botão de página do **conector Open** no direito inferior.

1. Sob a secção **configuração**marcada, marque as caixas de verificação dos registos de atividade do Office 365 que pretende ligar ao Azure Sentinel, e clique em **Alterar Alterações**. 

   > [!NOTE]
   > Se já tinha ligado vários inquilinos ao Azure Sentinel, utilizando uma versão mais antiga do conector do Office 365 que o suportava, poderá visualizar e modificar os registos que recolhe de cada inquilino. Você não será capaz de adicionar inquilinos adicionais, mas você pode remover inquilinos previamente adicionados.

1. Para consultar os dados de registo do Office 365 no Log Analytics, `OfficeActivity` digite na primeira linha da janela de consulta.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Office 365 ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa a detetar ameaças com o Azure Sentinel, utilizando regras [incorporadas](tutorial-detect-threats-built-in.md) ou [personalizadas.](tutorial-detect-threats-custom.md)

