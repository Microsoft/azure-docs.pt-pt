---
title: Trabalhar com vários inquilinos para a Azure Sentinel para prestadores de serviços MSSP Microsoft Docs
description: Como trabalhar com vários inquilinos para a Azure Sentinel para prestadores de serviços MSSP.
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
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79476020"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Trabalhar com vários inquilinos em Azure Sentinel 

Se você é um prestador de serviços de segurança gerido (MSSP) e você está usando [O Farol Azure](../lighthouse/overview.md) para gerir os centros de operações de segurança dos seus clientes (SOC), você será capaz de gerir os recursos Azure Sentinel dos seus clientes sem ligar diretamente ao inquilino do cliente, a partir do seu próprio inquilino Azure. 

## <a name="prerequisites"></a>Pré-requisitos
- [A bordo do Farol de Azure](../lighthouse/how-to/onboard-customer.md)
- Para que funcione corretamente, o seu inquilino deve estar registado no Registo do Fornecedor de Recursos Sentinela Azure em pelo menos uma subscrição. Se tem um Azure Sentinel registado no seu inquilino, está pronto para começar. Caso contrário, **selecione Subscrições** do portal Azure, seguidas por **fornecedores de recursos**.  Em seguida, a partir do ecrã **SOC - Fornecedores de recursos,** procure e selecione `Microsoft.OperationalInsights` e , e `Microsoft.SecurityInsights` **selecione Register**.
   ![Consulte os fornecedores de recursos](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Como aceder a Azure Sentinel de outros inquilinos
1. Sob **a subscrição Do Diretório +** selecione os diretórios delegados e as subscrições onde estão localizados os espaços de trabalho Azure Sentinel do seu cliente.

   ![Gerar incidentes de segurança](media/multiple-tenants-service-providers/directory-subscription.png)

1. Abra o Azure Sentinel. Você verá todos os espaços de trabalho nas subscrições selecionadas, e você será capaz de trabalhar com eles perfeitamente, como qualquer espaço de trabalho em seu próprio inquilino.

> [!NOTE]
> Não será capaz de implantar conectores em Azure Sentinel dentro de um espaço de trabalho gerido. Para implantar um conector, tem de iniciar um contrato direto com o arrendatário no qual pretende implantar um conector e autenticar lá com as permissões necessárias.





## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a gerir vários inquilinos do Azure Sentinel de forma perfeita. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

