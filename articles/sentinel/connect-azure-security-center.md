---
title: Ligue os dados do Centro de Segurança Azure ao Azure Sentinel
description: Aprenda a ligar alertas a partir do nível standard do Azure Security Center (ASC) e transmita-os para o Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 2fc7744600a9652ad43fd0aae8d886dc94acd58f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559153"
---
# <a name="connect-data-from-azure-security-center-asc"></a>Ligar dados do Azure Security Center (ASC)

O Azure Sentinel permite-lhe ligar alertas do Centro de [Segurança Azure](../security-center/security-center-intro.md) e transmiti-los para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Para exportar alertas do Azure Security Center, deve ter o papel de Leitor de Segurança na subscrição dos registos que transmite.

- Você deve ter o [nível padrão do Azure Security Center](../security-center/security-center-pricing.md) em execução na subscrição. Caso contrário, [atualize a sua subscrição de série](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="connect-to-azure-security-center"></a>Ligue-se ao Centro de Segurança Azure

1. Em Azure Sentinel, selecione **Conectores** de dados do menu de navegação.

1. Na galeria de conectores de dados, selecione **O Centro de Segurança Azure**e clique no botão de página do **conector Open.**

1. Em **Configuração**, clique em **Ligar** ao lado de cada subscrição cujos alertas pretende transmitir para Azure Sentinel. O botão Connect só estará disponível se tiver as permissões necessárias e a subscrição de nível ASC Standard.

1. Pode selecionar se deseja que os alertas do Azure Security Center gerem automaticamente incidentes em Azure Sentinel. Em **In Create incidents**, selecione **Ativado** para ativar a regra de análise predefinitiva que cria automaticamente incidentes a partir de alertas. Em seguida, pode editar esta regra no **âmbito de Analytics,** no separador **Regras Ative.**

1. Para utilizar o esquema relevante no Log Analytics para os alertas do Centro de Segurança Azure, procure por **SecurityAlert**.

## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar o Centro de Segurança Azure ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
