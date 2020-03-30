---
title: Ligue os dados do ATP azure ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar dados atp do Azure ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588591"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Ligar dados da Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> O conector de dados azure advanced threat protection em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Pode transmitir registos de [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) para Azure Sentinel com um único clique.

## <a name="prerequisites"></a>Pré-requisitos

- Utilizador com permissões de administrador global ou administrador de segurança
- Deve ser um cliente de pré-visualização do Azure ATP e permitir a integração entre o Azure ATP e o Microsoft Cloud App Security. Para mais informações, consulte [a Integração avançada de Proteção do Azure.](https://docs.microsoft.com/cloud-app-security/aatp-integration)

## <a name="connect-to-azure-atp"></a>Ligue-se ao ATP Azure

Certifique-se de que a versão de pré-visualização DO ATP Azure está [ativada na sua rede](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Se o ATP Azure for implantado e ingerir os seus dados, os alertas suspeitos podem ser facilmente transmitidos para o Azure Sentinel. Pode levar até 24 horas para os alertas começarem a fluir para O Sentinel a dispor.


1. Para ligar o Azure ATP ao Azure Sentinel, tem primeiro de permitir a integração entre o Azure ATP e o Microsoft Cloud App Security. Para obter informações sobre como fazê-lo, consulte a integração de [Proteção contra Ameaças Avançadas do Azure.](https://docs.microsoft.com/cloud-app-security/aatp-integration)

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no azulejo **Azure Advanced Threat Protection (Preview).**

1. Pode selecionar se pretende que os alertas do AZURE ATP gerem automaticamente incidentes no Azure Sentinel. Em caso **de criar incidentes,** selecione **Ativar** para ativar a regra analítica padrão que cria incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Em seguida, pode editar esta regra de acordo com **as regras** **Analytics** e, em seguida, Ative .

1. Clique em **Ligar**.

1. Para utilizar o esquema relevante no Log Analytics para os alertas ATP Azure, procure **securityAlert**.

> [!NOTE]
> Se os alertas forem superiores a 30 KB, o Azure Sentinel deixa de exibir o campo Das Entidades nos alertas.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar a Azure Advanced Threat Protection ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

