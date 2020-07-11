---
title: Problemas na conta da Azure Automation
description: Este artigo diz como resolver problemas e resolver problemas com uma conta Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: edd57d3d77432f3bb37872ee26f414b56398cae0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187290"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Problemas na conta da Azure Automation

Este artigo discute soluções para problemas que poderá encontrar quando utilizar uma conta Azure Automation. Para obter informações gerais sobre contas de Automação, consulte [a visão geral da autenticação da conta Azure Automation](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: Não conseguir registar Fornecedor de Recursos de Automação para subscrições

### <a name="issue"></a>Problema

Quando trabalha com funcionalidades de gestão, por exemplo, Gestão de Atualização, na sua conta de Automação, encontra o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

O Fornecedor de Recursos de Automação não está registado na assinatura.

### <a name="resolution"></a>Resolução

Para registar o Fornecedor de Recursos de Automação, siga estes passos no portal Azure:

1. A partir do seu navegador, aceda ao [portal Azure.](https://portal.azure.com)

2. Vá a **Subscrições**e selecione a sua subscrição.   

3. Em **Definições**, selecione **Fornecedores de Recursos**.

4. A partir da lista de fornecedores de recursos, verifique se o fornecedor de recursos **Microsoft.Automation** está registado.

5. Se o fornecedor não estiver listado, registe-o como descrito no [Resolve errors for resource provider registration](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="next-steps"></a>Passos seguintes

Se este artigo não resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) . Esta é a conta oficial da Microsoft Azure para ligar a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Apoio**.
