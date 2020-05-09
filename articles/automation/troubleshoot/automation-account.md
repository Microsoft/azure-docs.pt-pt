---
title: Resolução de problemas da conta azure Automation
description: Aprenda a resolver problemas e a resolver problemas com uma conta Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 6a7defdaa9b4b0f0b3580a3ac6b1a0487434a97c
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864135"
---
# <a name="troubleshoot-an-azure-automation-account"></a>Resolução de problemas uma conta de Automação Azure

Este artigo discute soluções para problemas que poderá encontrar quando utiliza uma conta De Automação Azure. Para obter informações gerais sobre as contas da Automação, consulte [Criar uma conta Azure](../automation-quickstart-create-account.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: Incapaz de registar Fornecedor de Recursos Automation para subscrições

### <a name="issue"></a>Problema

Quando trabalha com soluções de gestão na sua conta Automation, encontra o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

O Fornecedor de Recursos Automation não está registado na subscrição.

### <a name="resolution"></a>Resolução

Para registar o Fornecedor de Recursos Automation, siga estes passos no portal Azure:

1. Do seu navegador, vá ao [portal Azure.](https://portal.azure.com)

2. Vá a **Assinaturas**e selecione a sua subscrição.   

3. Em **Definições,** selecione **Fornecedores de Recursos**.

4. A partir da lista de fornecedores de recursos, verifique se o fornecedor de recursos **da Microsoft.Automation** está registado.

5. Se o fornecedor não estiver listado, registe-o conforme descrito em Resolver erros para o registo do fornecedor de [recursos.](/azure/azure-resource-manager/resource-manager-register-provider-errors)

## <a name="next-steps"></a>Passos seguintes

Se este artigo não resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport). Esta é a conta oficial do Microsoft Azure para ligar a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/), e selecione **Obter Suporte**.