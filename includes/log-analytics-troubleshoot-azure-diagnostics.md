---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 44febf95c660601df78047fc473f61e0d3169890
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52271682"
---
### <a name="troubleshoot-azure-diagnostics"></a>Resolver Problemas do Diagnóstico do Azure

Se receber a seguinte mensagem de erro, o fornecedor de recursos Microsoft.insights não está registado:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Para registar o fornecedor de recursos, execute os seguintes passos no portal do Azure:

1.  No painel de navegação à esquerda, clique em *Subscrições*
2.  Selecione a subscrição identificada na mensagem de erro
3.  Clique em *Fornecedores de Recursos*
4.  Encontre o fornecedor *Microsoft.insights*
5.  Clique na ligação *Registar*

![Registe o fornecedor de recursos do microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Assim que o fornecedor de recursos do *Microsoft.insights* estiver registado, repita o diagnóstico de configuração.


No PowerShell, se receber a seguinte mensagem de erro, terá de atualizar a versão do PowerShell:

`Set-AzureRmDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Atualizar a versão do PowerShell para Novembro de 2016 (v2.3.0) ou versão posterior, com as instruções no [introdução aos cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) artigo.
