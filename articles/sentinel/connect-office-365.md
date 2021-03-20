---
title: Ligar os registos do Office 365 ao | de Azure Sentinel Microsoft Docs
description: Aprenda a utilizar o conector de log office 365 para obter informações sobre as atividades de utilizador e administração em curso em Exchange, Teams e SharePoint, incluindo o OneDrive.
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
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 05848eb2761284669e659b3875e96acdfa71f90f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632187"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Ligar os Registos do Office 365 ao Azure Sentinel

O conector de log [office 365](/office/) traz informações sobre o Azure Sentinel sobre as atividades de utilizador e administração em curso em **Exchange** and **SharePoint** (incluindo **OneDrive),** e agora também em **Equipas.** Esta informação inclui detalhes de ações como transferências de ficheiros, pedidos de acesso enviados, alterações em eventos de grupo, operações de caixa de correio, eventos de Equipas (como eventos de chat, equipa, membro e canal), bem como os detalhes do utilizador que realizou as ações. Ligar os registos do Office 365 ao Azure Sentinel permite-lhe visualizar e analisar estes dados nos seus livros, consultar-se para criar alertas personalizados e incorporá-los para melhorar o seu processo de investigação, dando-lhe mais informações sobre a segurança do seu Office 365.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no seu espaço de trabalho Azure Sentinel.

- Deve ser administrador global ou administrador de segurança do seu inquilino.

- O seu escritório 365 deve estar no mesmo inquilino que o seu espaço de trabalho Azure Sentinel.

> [!IMPORTANT]
> - Para que o conector possa aceder aos dados através da API de Atividade de Gestão 365, deve ter **registo de auditoria unificado** ativado no seu Office 365. Dependendo do tipo de licença Do Office 365 / Microsoft 365 que tem, pode ou não ser ativada por padrão. Consulte o [Centro de Segurança e Conformidade do Office 365](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) para verificar o estado de registo de auditoria unificado de acordo com o seu tipo de licença.
> - Também pode ativar, desativar e verificar o estado atual do registo de auditoria unificado do Office 365. Para obter instruções, consulte [o Registo de auditoria do Turn Office 365 para ligar ou desligar](/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Para mais informações, consulte a [referência API da Atividade de Gestão 365.](/office/office-365-management-api/office-365-management-activity-api-reference)


   > [!NOTE]
   > Como referido acima, e como pode ver na página de conector nos **tipos de dados,** o conector Azure Sentinel Office 365 suporta atualmente a ingestão de registos de auditoria apenas a partir do Microsoft Exchange e SharePoint (incluindo o OneDrive), **e agora também de Equipas.** No entanto, existem algumas soluções externas se estiver interessado em trazer [outros dados do Office](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) para o Azure Sentinel. 

## <a name="enable-the-office-365-log-connector"></a>Ativar o conector de registo do Office 365

### <a name="instructions-tab"></a>Separador de instruções

1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Na galeria **de conectores de dados,** selecione **o Office 365** e, em seguida, selecione Abrir a página do **conector** no painel de pré-visualização.

1. Sob a secção **configuração** marcada, marque as caixas de verificação dos registos de atividade do Office 365 que pretende ligar ao Azure Sentinel, e clique em **Alterar Alterações**. 

   > [!NOTE]
   > Se já tinha ligado vários inquilinos ao Azure Sentinel, utilizando uma versão mais antiga do conector do Office 365 que o suportava, poderá visualizar e modificar os registos que recolhe de cada inquilino. Você não será capaz de adicionar inquilinos adicionais, mas você pode remover inquilinos previamente adicionados.

### <a name="next-steps-tab"></a>Separador de passos seguintes

- Consulte os livros de trabalho recomendados, amostras de consulta e modelos de regras de análise que estão agregados com o conector de log **office 365,** para obter informações sobre os dados de registo do SharePoint, OneDrive, Exchange e Teams.

- Para consultar manualmente os dados de registo do Office 365 em **Logs,** introduza `OfficeActivity` na primeira linha da janela de consulta.
   - Para filtrar a consulta de um tipo de registo específico, insira `| where OfficeWorkload == "<logtype>"` na segunda linha da consulta, onde *\<logtype\>* `SharePoint` `OneDrive` está, ou `Exchange` `MicrosoftTeams` .

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Office 365 ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa a detetar ameaças com o Azure Sentinel, utilizando regras [incorporadas](tutorial-detect-threats-built-in.md) ou [personalizadas.](tutorial-detect-threats-custom.md)