---
title: Recolher dados do Office 365 em pré-visualização de sentinela de Azure | Documentos da Microsoft
description: Saiba como recolher dados do Office 365 no Azure sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 7cd90b987550dc60b30d3aa0cd1016b681eec85f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547038"
---
# <a name="collect-data-from-office-365-logs"></a>Recolher dados de registos do Office 365

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode transmitir em fluxo registos de auditoria a partir [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) em Azure sentinela com um único clique. Pode transmitir em fluxo registos de auditoria a partir de vários inquilinos para uma área de trabalho única sentinela do Azure. O conector de registo de atividade do Office 365 fornece informações sobre atividades de utilizador em curso. Irá obter informações sobre várias utilizador administrador, sistema e as ações de política e eventos do Office 365. Ao ligar os registos do Office 365 no Azure sentinela pode utilizar estes dados para ver os dashboards, criar alertas personalizados e melhorar o processo de investigação.


## <a name="prerequisites"></a>Pré-requisitos

- Tem de ser um administrador global ou administrador de segurança no seu inquilino
- Para adicionar um inquilino, abra a porta 4433 ao tráfego da web no computador do qual fazer a ligação.

## <a name="connect-to-office-365"></a>Ligar ao Office 365

1. No Azure sentinela, selecione **recolha de dados** e, em seguida, clique nas **do Office 365** mosaico.

2. Se ainda não ativou a ele, em **conexão** utilizar o **ativar** botão para ativar a solução do Office 365. Se já foi ativado, serão identificado no ecrã de ligação, como já foi ativado.
1. Office 365 permite-lhe transmitir dados a partir de vários inquilinos para sentinela do Azure. Para cada inquilino que pretende ligar, adicionar o inquilino sob **ligue-se os inquilinos ao Azure sentinela**. 
1. É apresentado um ecrã de Active Directory. É solicitado para autenticar com um utilizador de administrador global no cada inquilino que pretende ligar ao Azure sentinela e fornecer permissões ao Azure sentinela para ler os respetivos registos. 
5. Em registos de atividades do Stream Office 365, clique em **selecione** para escolher que tipos de registo que pretende transmitir em fluxo para sentinela do Azure. Atualmente, Azure sentinela suporta Exchange e SharePoint.

4. Clique em **aplicar alterações**.

3. Para utilizar o esquema relevante no Log Analytics para os registos do Office 365, procure **OfficeActivity**.


## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar o Office 365 para sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

