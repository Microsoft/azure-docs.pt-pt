---
title: Aplicação do Azure Monitor alterar análise - detetar as alterações que possam afetar o site em direto problemas/falhas com a aplicação do Azure Monitor alterar análise | Documentos da Microsoft
description: Resolver problemas de site em direto de aplicações nos serviços de aplicações do Azure com a análise de alterações de aplicações do Azure Monitor
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: b04bd57c66b52e9a2c14d43c9e89d7c54fc48ae2
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415867"
---
# <a name="azure-monitor-application-change-analysis-public-preview"></a>O Azure Monitor alteração análise das aplicações (pré-visualização pública)

Quando ocorre uma problema/indisponibilidade do site em direto, é fundamental determinar rapidamente a causa principal. Padrão de soluções de monitorização pode ajudá-lo a identificar rapidamente o que existe um problema e, muitas vezes, mesmo que componente está a falhar. Mas isso sempre não levar a uma explicação imediata para por que motivo a falha está ocorrendo. Seu site trabalhou cinco minutos atrás, agora ele está quebrado. O que mudou nos últimos cinco minutos? Esta é a pergunta que a nova funcionalidade do Azure Monitor, análise de alterações de aplicação é criada para responder. Com a criação do poder dos [gráfico de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) análise de alterações de aplicações fornece informações sobre as alterações de aplicação do serviço de aplicações do Azure para aumentar observability e reduzir o MTTR (tempo médio para reparar).

> [!IMPORTANT]
> Análise de alterações de aplicações do Azure Monitor está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-do-i-use-application-change-analysis"></a>Como posso utilizar a análise de alterações de aplicações?

Análise de alterações de aplicações do Azure Monitor baseia-se atualmente para o Self-Service **diagnosticar e resolver problemas** experiência, que podem ser acedido a partir do **descrição geral** secção do seu serviço de aplicações do Azure aplicação:

![Página com as caixas vermelhas em torno do botão de descrição geral de descrição geral de captura de ecrã do serviço de aplicações do Azure e diagnosticar e resolver o botão de problemas](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis"></a>Ativar a análise de alteração

1. Selecione **disponibilidade e desempenho**

    ![captura de ecrã das opções de resolução de problemas de desempenho e disponibilidade](./media/change-analysis/availability-and-performance.png)

2. Clique nas **falha aplicação** mosaico.

   ![Captura de ecrã com mosaico de falhas de aplicação](./media/change-analysis/application-crashes-tile.png)

3. Para habilitar **alteração Analysis** selecionar **ativar agora**.

   ![captura de ecrã das opções de resolução de problemas de desempenho e disponibilidade](./media/change-analysis/application-crashes.png)

4. Para tirar partido de toda a alterar o conjunto de funcionalidades de análise **alterar Analysis**, **verificar a existência de alterações de código**, e **sempre no** para **no** e selecione **guardar**.

    ![Captura de ecrã da interface de usuário de análise de alteração de enable de App Service do Azure](./media/change-analysis/change-analysis-on.png)

    Se **alteração análise** é ativado, será capaz de detetar as alterações de nível de recursos. Se **verificar a existência de alterações de código** é ativada, também verá arquivos de implantação e as alterações de configuração do site. Habilitando **sempre no** otimizará a alteração de análise de desempenho, mas pode implicar custos adicionais de uma perspectiva de faturação.

5.  Quando tudo estiver ativada, selecionando **diagnosticar e resolver problemas** > **disponibilidade e desempenho** > **falha aplicação** permitirá que acesse a experiência de análise de alterações. O gráfico será summerize o tipo de alterações que aconteceu ao longo do tempo, juntamente com detalhes sobre essas mudanças:

     ![Captura de ecrã da vista de diferença de alteração](./media/change-analysis/change-view.png)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unable-to-fetch-change-analysis-information"></a>Não é possível obter as informações de análise de alterações.

Este é um problema temporário com a experiência de integração de pré-visualização atual. A solução alternativa consiste em definir uma etiqueta oculta na sua aplicação web e, em seguida, atualizar a página:

   ![Captura de ecrã da marca de alteração ocultada](./media/change-analysis/hidden-tag.png)

Para definir a etiqueta oculta com o PowerShell:

1. Abra o Azure Cloud Shell:

    ![Captura de ecrã da alteração do Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

2. Altere o tipo de shell para o PowerShell:

   ![Captura de ecrã da alteração do Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

3. Execute o seguinte comando:

```powershell
$webapp=Get-AzWebApp -Name <name_of_your_webapp>
$tags = $webapp.Tags
$tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
```

> [!NOTE]
> Assim que for adicionada a marca ocultada, ainda poderá inicialmente esperar até quatro horas para conseguir ver primeiro as alterações. Isso se deve a freqeuncy de 4 horas que o serviço de análise de alterações usa para verificar a aplicação web, limitar o impacto no desempenho da verificação.

## <a name="next-steps"></a>Passos Seguintes

- Melhorar a monitorização dos serviços de aplicações do Azure [, permitindo que os recursos do Application Insights](azure-web-apps.md) do Azure Monitor.
- Melhorar a compreensão dos [gráfico de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) que ajuda a aplicação do Azure Monitor power alterar analysis. 
