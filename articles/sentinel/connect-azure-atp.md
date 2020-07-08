---
title: Ligue os dados do Azure ATP ao Azure Sentinel. Microsoft Docs
description: Aprenda a transmitir registos do Azure Advanced Threat Protection (ATP) para a Azure Sentinel com um único clique.
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
ms.openlocfilehash: f58c38ccfa234752a80c05c300d245c6c9e97cf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559177"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Ligue dados da Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> O conector de dados Azure Advanced Threat Protection em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode transmitir registos de [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) para Azure Sentinel com um único clique.

## <a name="prerequisites"></a>Pré-requisitos

- Utilizador com permissões globais de administrador ou administrador de segurança
- Você deve ser um cliente de pré-visualização do Azure ATP e permitir a integração entre Azure ATP e Microsoft Cloud App Security. Para mais informações, consulte [a Azure Advanced Protection Integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Ligue-se ao Azure ATP

Certifique-se de que a versão de pré-visualização Azure ATP está [ativada na sua rede](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Se o Azure ATP for implantado e ingerir os seus dados, os alertas suspeitos podem ser facilmente transmitidos para o Azure Sentinel. Pode levar até 24 horas para que os alertas comecem a fluir para Azure Sentinel.


1. Para ligar o Azure ATP ao Azure Sentinel, tem primeiro de permitir a integração entre o Azure ATP e o Microsoft Cloud App Security. Para obter informações sobre como fazê-lo, consulte a [integração da Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. Em Azure Sentinel, selecione **os conectores de dados** e, em seguida, clique no azulejo **Azure Advanced Threat Protection (Preview).**

1. Pode selecionar se deseja que os alertas do Azure ATP gerem automaticamente incidentes no Azure Sentinel. No **âmbito do Create incidents** select **Enable** to enable the pred pred alytic rule that creates incidents automaticly from alerts gerados no serviço de segurança conectado. Em seguida, pode editar esta regra sob **a análise** e, em seguida, **as regras Ative**.

1. Clique em **Ligar**.

1. Para utilizar o esquema relevante no Log Analytics para os alertas ATP Azure, procure por **SecurityAlert**.

> [!NOTE]
> Se os alertas forem superiores a 30 KB, o Azure Sentinel deixa de exibir o campo Entidades nos alertas.

## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar a Azure Advanced Threat Protection ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

