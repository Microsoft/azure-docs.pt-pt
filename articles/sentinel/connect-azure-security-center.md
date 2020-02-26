---
title: Conecte dados do Centro de Segurança Azure ao Azure Sentinel
description: Saiba como ligar os dados do Azure Security Center ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588540"
---
# <a name="connect-data-from-azure-security-center"></a>Ligar dados do Centro de Segurança Azure





O Azure Sentinel permite-lhe ligar alertas do Centro de [Segurança Azure](../security-center/security-center-intro.md) e transmiti-los para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Para exportar alertas do Azure Security Center, deve ter o papel do Leitor de Segurança na subscrição dos registos que transmite.

- Deve ter o nível Padrão do [Azure Security Center](../security-center/security-center-pricing.md) standard a funcionar na subscrição. Caso contrário, [atualize a sua subscrição de forma padrão](https://azure.microsoft.com/pricing/details/security-center/).



## <a name="connect-to-azure-security-center"></a>Ligue-se ao Centro de Segurança Azure

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no azulejo **do Azure Security Center.**

1. À direita, clique em **Connect** ao lado de cada subscrição cujos alertas pretende transmitir para O Sentinel a dispor. Certifique-se de atualizar cada subscrição para o nível Padrão do Azure Security Center standard para transmitir alertas para o Azure Sentinel.

1. Pode selecionar se pretende que os alertas do Azure Security Center gerem automaticamente incidentes em Azure Sentinel. Em caso **de criar incidentes,** selecione **Ativar** para ativar a regra analítica padrão que cria incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Em seguida, pode editar esta regra de acordo com **as regras** **Analytics** e, em seguida, Ative .

3. Clique em **Ligar**.

4. Para utilizar o esquema relevante no Log Analytics para os alertas do Centro de Segurança Azure, procure **securityAlert**.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Azure Security Center ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
