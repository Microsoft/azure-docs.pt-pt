---
title: Conecte dados de proteção de identidade Azure AD ao Azure Sentinel
description: Saiba como ligar dados de Proteção de Identidade Azure AD ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: b82ddfef57efaaca0ae43750cd306a63a772b911
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616832"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Conecte dados da Proteção de Identidade da AD Azure



Pode transmitir registos da [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) para o Azure Sentinel para transmitir alertas para o Azure Sentinel para ver dashboards, criar alertas personalizados e melhorar a investigação. A Azure Ative Directory Identity Protection proporciona uma visão consolidada dos utilizadores de risco, deteções de risco e vulnerabilidades, com a capacidade de remediar imediatamente o risco, e definir políticas para remediar automaticamente eventos futuros. O serviço baseia-se na experiência da Microsoft em proteger as identidades dos consumidores e ganha uma precisão tremenda a partir do sinal de mais de 13 mil milhões de logins por dia. 


## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma [licença Azure Ative Directory Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Utilizador com permissões de administrador global ou administrador de segurança


## <a name="connect-to-azure-ad-identity-protection"></a>Ligue-se à Proteção de Identidade Azure AD

Se já tem Proteção de Identidade Azure AD, certifique-se de que está [ativado na sua rede](../active-directory/identity-protection/overview-identity-protection.md).
Se a Azure AD Identity Protection for implementada e obter dados, os dados de alerta podem ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no azulejo **azure AD Identity Protection.**

2. Clique em **Connect** para começar a transmitir eventos de Proteção de Identidade Azure AD em Azure Sentinel.


6. Para utilizar o esquema relevante no Log Analytics para os alertas de Proteção de Identidade Azure AD, procure **SecurityAlert**.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar a Proteção de Identidade Azure AD ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
