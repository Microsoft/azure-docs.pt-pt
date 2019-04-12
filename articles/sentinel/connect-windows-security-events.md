---
title: Ligar a dados de eventos de segurança do Windows para a pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Saiba como ligar a dados de eventos de segurança do Windows para sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d131d39a9d9770046fa3f9368250204e4d0b0162
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491957"
---
# <a name="connect-windows-security-events"></a>Ligar aos eventos de segurança do Windows 

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode transmitir em fluxo todos os eventos de segurança de servidores do Windows ligado à sua área de trabalho de sentinela do Azure. Esta ligação permite-lhe ver os dashboards e criar alertas personalizados e a melhorar a investigação. Isso dá-lhe mais informações sobre a rede da sua organização e melhora as capacidades de operação de segurança.  Pode selecionar os eventos para transmitir em fluxo:

- **Todos os eventos** -segurança de todos os Windows e eventos do AppLocker.
- **Comuns** -um conjunto padrão de eventos para fins de auditoria.
- **Mínimo** -um pequeno conjunto de eventos que possam indicar a potenciais ameaças. Ao ativar esta opção, não será possível ter um registo de auditoria completo.
- **Nenhum** -sem segurança ou eventos do AppLocker.

> [!NOTE]
> 
> - Os dados serão armazenados na localização geográfica da área de trabalho no qual está a executar sentinela do Azure.

## <a name="set-up-the-windows-security-events-connector"></a>Configurar o conector de eventos de segurança do Windows

Para integrar totalmente os eventos de segurança do Windows com o Azure sentinela:

1. No portal do Azure sentinela, selecione **conectores de dados** e, em seguida, clique nas **eventos de segurança do Windows** mosaico. 
1. Selecione os tipos de dados que pretende transmitir em fluxo.
1. Clique em **Atualizar**.
6. Para utilizar o esquema relevante no Log Analytics para os eventos de segurança do Windows, procure **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode demorar cerca de 20 minutos até que os seus registos começam a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar eventos de segurança do Windows para sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

