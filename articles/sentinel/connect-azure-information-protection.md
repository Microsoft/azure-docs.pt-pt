---
title: Recolha de dados do Azure Information Protection na pré-visualização de sentinela de Azure | Documentos da Microsoft
description: Saiba como recolher dados do Azure Information Protection na sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 7c5866d3096823f91a70b28c7c5dd1790e1b3bf8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537172"
---
# <a name="collect-data-from-azure-information-protection"></a>Recolher dados do Azure Information Protection

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode transmitir em fluxo registos a partir [do Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) em Azure sentinela com um único clique. O Azure Information Protection ajuda a proteger os dados, quer estejam armazenado na cloud ou em infraestruturas no local e controlo e ajuda a proteger e-mails, documentos e dados confidenciais que partilha fora da sua empresa. Desde a classificação fácil para embedded etiquetas e às permissões, melhore a proteção de dados em todos os momentos com o Azure Information Protection. Quando liga do Azure Information Protection para Azure sentinela, transmitir todos os alertas do Azure Information Protection para Azure sentinela.


## <a name="prerequisites"></a>Pré-requisitos

- Utilizador com o administrador global, administrador de segurança ou as permissões de proteção de informações


## <a name="connect-to-azure-information-protection"></a>Ligar ao Azure Information Protection

Se já tiver o Azure Information Protection, certifique-se de que é [ativada na sua rede](https://docs.microsoft.com/azure/information-protection/activate-service).
Se o Azure Information Protection é implementado e obtenção de dados, os dados de alertas podem facilmente ser transmitidos em sentinela do Azure.


1. No Azure sentinela, selecione **recolha de dados** e, em seguida, clique nas **do Azure Information Protection** mosaico.

2. Vá para o [portal do Azure Information Protection](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade) 

3. Sob **conexão**, configure a transmissão em fluxo de registos do Azure Information Protection para Azure sentinela clicando [Configure a análise](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade)

4. Selecione a área de trabalho no qual implementou sentinela do Azure. 

5. Clique em **OK**.

6. Para utilizar o esquema relevante no Log Analytics para os alertas do Azure Information Protection, procure **InformationProtectionLogs_CL**.




## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar o Azure Information Protection para Azure sentinela. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).
