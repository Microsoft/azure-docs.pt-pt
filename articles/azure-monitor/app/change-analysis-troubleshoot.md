---
title: Análise de alteração de aplicação de resolução de problemas - Monitor Azure
description: Saiba como resolver problemas na Análise de Alterações de Aplicações.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100521187"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>Análise de alteração de aplicação de resolução de problemas (pré-visualização)

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>Está a ter problemas em registar a Microsoft. Alterar fornecedor de recursos de análise do separador De alterar histórico

Se for a primeira vez que vê o histórico change após a sua integração com a Análise de Alterações de Aplicações, verá que registará automaticamente um fornecedor de recursos **Microsoft.ChangeAnalysis**. Em casos raros, pode falhar pelas seguintes razões:

- **Não tem permissões suficientes para registar o fornecedor de recursos Microsoft.ChangeAnalysis.** Esta mensagem de erro significa que a sua função na subscrição atual não tem o **microsoft.Support/register/action** scope associado a ela. Isto pode acontecer se não for o proprietário de uma subscrição e obtiver permissões de acesso partilhado através de um colega de trabalho (isto é, ver acesso a um grupo de recursos). Para corrigir isto, pode contactar o proprietário da sua subscrição para registar o fornecedor de recursos **Microsoft.ChangeAnalysis.** Isto pode ser feito no portal Azure através **de Subscrições | Fornecedores de recursos** e pesquisa ```Microsoft.ChangeAnalysis``` e registo na UI, ou através da Azure PowerShell ou Azure CLI.

    Registar fornecedor de recursos através do PowerShell:
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Falhou no registo do fornecedor de recursos Microsoft.ChangeAnalysis**. Esta mensagem significa que algo falhou imediatamente, pois a UI enviou um pedido de registo do fornecedor de recursos, e não está relacionado com a questão da permissão. É provável que seja um problema temporário de conectividade na Internet. Tente refrescar a página e verifique a sua ligação à Internet. Se o erro persistir, contacte changeanalysishelp@microsoft.com

- **Isto está a demorar mais do que o esperado.** Esta mensagem significa que o registo está a demorar mais de 2 minutos. Isto é invulgar, mas não significa necessariamente que algo correu mal. Pode ir a **Subscrições | Fornecedor de recursos** para verificar o estado de registo do fornecedor de recursos **Microsoft.ChangeAnalysis.** Pode tentar utilizar o UI para não registar, reregistar ou refrescar para ver se ajuda. Se o problema persistir, contacte changeanalysishelp@microsoft.com para apoio.
    ![Registo de RP de resolução de problemas demorando muito tempo](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![Screenshot da ferramenta Diagnose and Solve Problems para uma Máquina Virtual com ferramentas de resolução de problemas selecionadas.](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Screenshot do azulejo para a ferramenta de resolução de problemas de alterações recentes para uma máquina virtual.](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>A subscrição do Farol Azure não é suportada

- Não tendo consultado o fornecedor de **recursos microsoft.ChangeAnalysis** com a *subscrição do farol Azure não é suportado, as alterações só estão disponíveis no inquilino da subscrição*. Existe neste momento uma limitação para que o fornecedor de recursos change analysis seja registado através da subscrição do Azure Lighthouse para utilizadores que não estejam no arrendatário doméstico. Estamos a trabalhar para resolver esta limitação. Se esta é uma questão de bloqueio para si, existe uma solução alternativa que envolve a criação de um principal de serviço e a atribuição explícita do papel para permitir o acesso.  Contacto changeanalysishelp@microsoft.com para saber mais sobre isso.

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Ocorreu um erro durante a obtenção de alterações. Por favor, refresque esta página ou volte mais tarde para ver as alterações

Esta é a mensagem de erro geral apresentada pelo serviço de Análise de Alterações de Aplicação quando as alterações não puderam ser carregadas. Algumas causas conhecidas são:

- Erro de conectividade da Internet a partir do dispositivo cliente
- O serviço de Análise de Alteração está temporariamente indisponível Refrescando a página após alguns minutos normalmente corrige este problema. Se o erro persistir, contacte changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>Não tem permissões suficientes para ver algumas mudanças. Contacte o seu administrador de subscrição da Azure

Esta é a mensagem de erro geral não autorizada, explicando que o utilizador atual não tem permissões suficientes para visualizar a alteração. Pelo menos o acesso ao leitor é necessário no recurso para visualizar as alterações de infraestrutura devolvidas pelo Azure Resource Graph e pelo Azure Resource Manager. Para alterações de ficheiros e alterações de configuração de aplicações web, é necessária uma função de pelo menos contribuinte.

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Falhou no registo do fornecedor de recursos Microsoft.ChangeAnalysis

Esta mensagem significa que algo falhou imediatamente, pois a UI enviou um pedido de registo do fornecedor de recursos, e não está relacionado com a questão da permissão. É provável que seja um problema temporário de conectividade na Internet. Tente refrescar a página e verifique a sua ligação à Internet. Se o erro persistir, contacte changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Não tem permissões suficientes para registar o fornecedor de recursos Microsoft.ChangeAnalysis. Contacte o seu administrador de subscrição da Azure

Esta mensagem de erro significa que a sua função na subscrição atual não tem o **microsoft.Support/register/action** scope associado a ela. Isto pode acontecer se não for o proprietário de uma subscrição e obtiver permissões de acesso partilhado através de um colega de trabalho (isto é, ver acesso a um grupo de recursos). Para corrigir isto, pode contactar o proprietário da sua subscrição para registar o fornecedor de recursos **Microsoft.ChangeAnalysis.** Isto pode ser feito no portal Azure através **de Subscrições | Fornecedores de recursos** e pesquisa ```Microsoft.ChangeAnalysis``` e registo na UI, ou através da Azure PowerShell ou Azure CLI.

Registar fornecedor de recursos através do PowerShell:

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Resource Graph](../../governance/resource-graph/overview.md), que ajuda a power Change Analysis.