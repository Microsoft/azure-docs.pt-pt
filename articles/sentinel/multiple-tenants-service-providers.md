---
title: Trabalhar com vários inquilinos para o Azure Sentinel para prestadores de serviços da MSSP Microsoft Docs
description: Como trabalhar com vários inquilinos para o Azure Sentinel para prestadores de serviços MSSP.
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
ms.openlocfilehash: caa79b572d0024b93abd2d32ca99d92cc2a8b4bb
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582080"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Trabalho com vários inquilinos em Azure Sentinel 

Se é um prestador de serviços de segurança gerido (MSSP) e está a usar o [Farol Azure](../lighthouse/overview.md) para gerir os centros de operações de segurança dos seus clientes (SOC), poderá gerir os recursos azure Sentinel dos seus clientes sem se ligar diretamente ao inquilino do cliente, do seu próprio inquilino Azure. 

## <a name="prerequisites"></a>Pré-requisitos
- [Farol Azure a bordo](../lighthouse/how-to/onboard-customer.md)
- Para que isto funcione corretamente, o seu inquilino deve estar registado no Azure Sentinel Resource Provider em pelo menos uma subscrição. Se tiver um Azure Sentinel registado no seu inquilino, está pronto para começar. Caso contrário, no portal Azure, selecione **Subscrições seguidas** por **fornecedores** de Recursos e, em seguida, procure `Microsoft.Security.Insights` e selecione **Register**.
   ![Verificar os fornecedores de recursos](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Como aceder ao Azure Sentinel de outros inquilinos
1. No âmbito do **Diretório + subscrição,** selecione os diretórios delegados e as subscrições onde estão localizados os espaços de trabalho do seu cliente Azure Sentinel.

   ![Gerar incidentes de segurança](media/multiple-tenants-service-providers/directory-subscription.png)

1. Open Azure Sentinel. Você verá todos os espaços de trabalho nas subscrições selecionadas, e você será capaz de trabalhar com eles perfeitamente, como qualquer espaço de trabalho no seu próprio inquilino.

> [!NOTE]
> Não poderá implantar conectores em Azure Sentinel dentro de um espaço de trabalho gerido. Para implementar um conector, deve assinar diretamente no inquilino no qual pretende implantar um conector e autenticar lá com as permissões necessárias.





## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a gerir vários inquilinos Azure Sentinel sem problemas. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

