---
title: Recolher dados do Azure AD na pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Saiba como recolher dados do Azure Active Directory no Azure sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: 315b18feb74862bbeca6ff8265ee003fbad48595
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242321"
---
# <a name="collect-data-from-azure-active-directory"></a>Recolher dados do Azure Active Directory

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sentinel do Azure permite-lhe recolher dados a partir [do Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmiti-la em sentinela do Azure. Pode escolher para o stream [registos de início de sessão](../active-directory/reports-monitoring/concept-sign-ins.md) e [registos de auditoria](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Pré-requisitos

- Se quiser exportar dados de início de sessão a partir do Active Directory, tem de ter uma licença do Azure AD P1 ou P2.

- Utilizador com permissões global de administrador de segurança ou de administrador no inquilino que pretende que os registos a partir do stream.


## <a name="connect-to-azure-ad"></a>Ligar ao Azure AD

1. No Azure sentinela, selecione **recolha de dados** e, em seguida, clique nas **Azure Active Directory** mosaico.

2. Junto aos registos que pretende transmitir para o Azure sentinela, clique em **Connect**.






## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar-se do Azure AD para sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).
