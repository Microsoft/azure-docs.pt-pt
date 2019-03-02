---
title: Recolha de dados do Centro de segurança do Azure em pré-visualização sentinela do Azure | Documentos da Microsoft
description: Saiba como recolher dados do Centro de segurança do Azure no Azure sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f59c5f6a9f497a6420172996f9f327f16ffd26f9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242280"
---
# <a name="collect-data-from-azure-security-center"></a>Recolher dados a partir do Centro de segurança do Azure

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Sentinel do Azure permite-lhe recolher alertas a partir [Centro de segurança do Azure](../security-center/security-center-intro.md) e transmiti-los em sentinela do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Se quiser exportar alertas do Centro de segurança do Azure, tem de ser um Contribuidor na subscrição cujos registos transmitir em fluxo.

- Tem de ter o [escalão Standard do Azure Security Center](../security-center/security-center-pricing.md) em execução na subscrição. Caso contrário, [Atualize sua assinatura padrão](https://azure.microsoft.com/pricing/details/security-center/).

- Tem de iniciar sessão com um utilizador que tenha permissões de administrador de segurança ou de administrador global em cada subscrição que pretende ligar.


## <a name="connect-to-azure-security-center"></a>Ligar ao centro de segurança do Azure

1. No Azure sentinela, selecione **recolha de dados** e, em seguida, clique nas **Centro de segurança do Azure** mosaico.
1. No lado direito, clique em **Connect** junto a cada subscrição cujas alertas pretende transmitir para o Azure sentinela. Certifique-se atualizar cada subscrição para o escalão Standard de centro de segurança do Azure para alertas de fluxo para sentinela do Azure.

3. Clique em **Ligar**.

4. Para utilizar o esquema relevante no Log Analytics para os alertas do Centro de segurança do Azure, procure **SecurityEvent**.

## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar o Centro de segurança do Azure ao Azure sentinela. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).
