---
title: Problemas de suturação de problemas Da conta Azure Automation
description: Este artigo diz como resolver problemas e resolver problemas com uma conta Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: bebc89514da7f28015da5cb145ae50bf3fccdb6a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680077"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Problemas de suturação de problemas Da conta Azure Automation

Este artigo discute soluções para problemas que poderá encontrar quando utiliza uma conta De Automação Azure. Para obter informações gerais sobre contas de Automação, consulte a visão geral da [autenticação da conta Azure Automation](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: Incapaz de registar Fornecedor de Recursos Automation para subscrições

### <a name="issue"></a>Problema

Quando trabalha com funcionalidades de gestão, por exemplo, Gestão de Atualizações, na sua conta De automação, encontra-se o seguinte erro:

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
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) . Esta é a conta oficial do Microsoft Azure para ligar a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/), e selecione **Obter Suporte**.