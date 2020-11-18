---
title: Ligue os dados do Azure Defender ao Azure Sentinel
description: Saiba como ligar os alertas do Azure Defender do Azure Security Center e transmita-os para o Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: b37675d46d501fc43446eafc83d6c0a02f238177
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655957"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Ligue os dados de alerta do Azure Defender do Azure Security Center

Utilize o conector de alerta Azure Defender para ingerir os alertas do Azure Defender do [Azure Security Center](../security-center/security-center-introduction.md) e transmiti-los para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- O seu utilizador deve ter a função De Leitor de Segurança na subscrição dos registos que transmite.

- Terá de ativar o Azure Defender dentro do Centro de Segurança Azure. (O nível padrão já não existe e já não é um requisito de licença.)

## <a name="connect-to-azure-defender"></a>Ligue-se ao Azure Defender

1. Em Azure Sentinel, selecione **Conectores** de dados do menu de navegação.

1. Na galeria de conectores de dados, selecione **alertas do Azure Defender da ASC** (ainda pode ser chamado de Centro de Segurança Azure) e clique no botão de página do **conector Open.**

1. Em **Configuração**, clique em **Ligar** ao lado de cada subscrição cujos alertas pretende transmitir para Azure Sentinel. O botão Ligar só estará disponível se tiver as permissões necessárias.

1. Pode selecionar se deseja que os alertas do Azure Defender gerem automaticamente incidentes em Azure Sentinel. Em **In Create incidents**, selecione **Ativado** para ativar a regra de análise predefinitiva que cria automaticamente incidentes a partir de alertas. Em seguida, pode editar esta regra no **âmbito de Analytics,** no separador **Regras Ative.**

1. Para utilizar o esquema relevante no Log Analytics para os alertas do Azure Defender, procure por **SecurityAlert**.

## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar o Azure Defender ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)