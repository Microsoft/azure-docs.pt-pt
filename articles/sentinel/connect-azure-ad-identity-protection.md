---
title: Ligar os dados do Azure AD Identity Protection para a pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Saiba como ligar a dados do Azure AD Identity Protection para Azure sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d8306056655809c69af2ed39b5bbf8efebe05d3f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787540"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Ligar dados a partir do Azure AD Identity Protection

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode transmitir em fluxo registos a partir [do Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) em Azure sentinela a alertas de fluxo para o Azure sentinela para ver os dashboards e criar alertas personalizados e a melhorar a investigação. O Azure Active Directory Identity Protection fornece uma vista consolidada de utilizadores em risco, eventos de risco e vulnerabilidades, com a capacidade de remediar riscos imediatamente e definir políticas para remediar automaticamente eventos futuros. O serviço baseia-se na experiência da Microsoft proteger identidades e ganha uma maior precisão a partir do sinal de mais de 13 mil milhões de logons por dia. 


## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter um [licença do Azure Active Directory Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Utilizador com permissões de administrador de segurança ou de administrador global


## <a name="connect-to-azure-ad-identity-protection"></a>Ligar ao Azure AD Identity Protection

Se já tiver o Azure AD Identity Protection, certifique-se de que é [ativada na sua rede](../active-directory/identity-protection/enable.md).
Se o Azure AD Identity Protection é implementado e obtenção de dados, os dados de alertas podem facilmente ser transmitidos em sentinela do Azure.


1. No Azure sentinela, selecione **conectores de dados** e, em seguida, clique nas **Azure AD Identity Protection** mosaico.

2. Clique em **Connect** para iniciar a transmissão em fluxo de eventos do Azure AD Identity Protection para Azure sentinela.


6. Para utilizar o esquema relevante no Log Analytics para os alertas do Azure AD Identity Protection, procure **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar o Azure AD Identity Protection para Azure sentinela. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).
