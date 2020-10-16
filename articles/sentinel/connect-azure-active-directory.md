---
title: Ligue os dados do Azure Ative Directory ao Azure Sentinel Microsoft Docs
description: Saiba como recolher dados do Azure Ative Directory e transmitir registos de login azure AD e registos de auditoria no Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 279f54c3de964580cc37d1288a6e1b7726348e10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88208627"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Ligar dados do Azure Ative Directory (Azure AD)



Pode utilizar o conector incorporado do Azure Sentinel para recolher dados do [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmiti-lo para o Azure Sentinel. O conector permite-lhe transmitir [registos de login](../active-directory/reports-monitoring/concept-sign-ins.md) e [registos](../active-directory/reports-monitoring/concept-audit-logs.md)de auditoria .

## <a name="prerequisites"></a>Pré-requisitos


- Qualquer licença AD Azure (Grátis/O365/P1/P2) é suficiente para ingerir registos de entrada no Azure Sentinel. Podem ser aplicadas taxas adicionais por gigabyte para o Azure Monitor (Log Analytics) e para o Azure Sentinel.

- O seu utilizador deve ser atribuído ao contributo do Azure Sentinel no espaço de trabalho.

- O seu utilizador deve ser atribuído às funções de Administrador Global ou Administrador de Segurança no inquilino a partir do quais pretende transmitir os registos.

- O seu utilizador deve ter lido e escrito permissões para as definições de diagnóstico AZure AD para poder ver o estado da ligação. 


## <a name="connect-to-azure-active-directory"></a>Ligue-se ao Diretório Ativo Azure

1. Em Azure Sentinel, selecione **Conectores** de dados do menu de navegação.

1. Na galeria de conectores de dados, selecione **Azure Ative Directory** e, em seguida, selecione **Abrir a página do conector**.

1. Marque as caixas de verificação ao lado dos registos que pretende transmitir para Azure Sentinel e clique em **Connect**.

1. Pode selecionar se deseja que os alertas do Azure AD gerem automaticamente incidentes em Azure Sentinel. Em **Configurar incidentes** selecione Ativar para **ativar** a regra de análise padrão que cria incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Em seguida, pode editar esta regra sob **a análise** e, em seguida, **as regras Ative**.

1. Para utilizar o esquema relevante no Log Analytics para consulta de alertas Azure AD, tipo `SigninLogs` ou na janela de `AuditLogs` consulta.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Azure Ative Directory ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
