---
title: Utilizar análise de alterações de aplicações no Azure Monitor para localizar problemas de aplicação web | Documentos da Microsoft
description: Utilize análise de alterações de aplicações no Azure Monitor para resolver problemas de aplicativos em sites ao vivo no App Service do Azure.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 2a31131b662d01f9841a3f1c5b0a6c459a117e77
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075372"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Utilizar a análise de alterações de aplicações (pré-visualização) no Azure Monitor

Quando um problema de site em direto ou a falha ocorre, rapidamente, determinar a causa raiz é essencial. Soluções de monitorização padrão poderão alertá-lo a um problema. Pode até mesmo indicar que componente está a falhar. Mas este alerta não sempre imediatamente explica a causa da falha. Sabe-se ao seu site trabalhou há cinco minutos e, agora está quebrado. O que mudou nos últimos cinco minutos? Esta é a pergunta que a análise de alterações de aplicação é criada para responder no Azure Monitor. 

Aproveitando o poder da [gráfico de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), análise de alterações fornece informações sobre as alterações de aplicação do Azure para aumentar observability e reduzir o MTTR (tempo médio para reparar).

> [!IMPORTANT]
> Análise de alterações está atualmente em pré-visualização. Esta versão de pré-visualização é fornecida sem um contrato de nível de serviço. Esta versão não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades restringidas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Descrição geral

Análise de alteração detecta vários tipos de alterações, da camada de infraestrutura até à implementação de aplicação. É um fornecedor de recursos do Azure de nível de assinatura que verifica as alterações de recursos na subscrição. Análise de alterações fornece dados de várias ferramentas de diagnóstico ajudar os utilizadores a compreender quais alterações poderão ter causado problemas.

O diagrama seguinte ilustra a arquitetura de análise de alteração:

![Diagrama da arquitetura de como alterar análise obtém os dados de alteração e fornece-o para ferramentas de cliente](./media/change-analysis/overview.png)

Atualmente a análise de alterações é integrado a **diagnosticar e resolver problemas** experiência na aplicação de web do serviço de aplicações. Para ativar a deteção de alteração e ver as alterações na aplicação web, consulte a *Analysis alterar para a funcionalidade aplicações Web* seção mais adiante neste artigo.

### <a name="azure-resource-manager-deployment-changes"></a>Alterações de implementação do Azure Resource Manager

Usando [gráfico de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), análise de alterações fornece um registo histórico de como os recursos do Azure que alojam a sua aplicação mudaram ao longo do tempo. Por exemplo, pode detetar a análise de alteração, alterações em regras de configuração de IP, identidades geridas e as definições de SSL. Portanto, se uma etiqueta for adicionada a uma aplicação web, análise alterar reflete a alteração. Esta informação estiver disponível, desde o `Microsoft.ChangeAnalysis` fornecedor de recursos está ativado na subscrição do Azure.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Alterações na implementação de aplicações web e de configuração

Análise de alteração captura o estado de implementação e configuração de uma aplicação a cada 4 horas. Pode detetar, por exemplo, as alterações nas variáveis de ambiente de aplicativo. A ferramenta calcula as diferenças e apresenta o que foi alterado. Ao contrário das alterações do Gestor de recursos, informações de alteração de implementação de código podem não estar disponíveis imediatamente na ferramenta. Para ver as alterações mais recentes na análise de alterações, selecione **análise altera-se agora**.

![Captura de ecrã do botão "Análise de alterações agora"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Alterações de dependência

Alterações às dependências de recursos também podem causar problemas numa aplicação web. Por exemplo, se uma aplicação web chama-se para uma cache de Redis, a cache de Redis SKU pode afetar o desempenho das aplicações web. Para detectar alterações nas dependências, análise de alterações verifica o registo DNS da aplicação web. Dessa forma, ele identifica as alterações em todos os componentes de aplicações que podem causar problemas.

## <a name="change-analysis-for-the-web-apps-feature"></a>Análise de alteração para a funcionalidade aplicações Web

No Azure Monitor, a análise de alterações atualmente está incluída no Self-Service **diagnosticar e resolver problemas** experiência. Aceder a esta experiência do **descrição geral** página da sua aplicação de serviço de aplicações.

![Captura de ecrã do botão "Descrição geral" e o "diagnosticar e resolver problemas" botão](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Ativar a análise de alterações em de diagnóstico e resolver a ferramenta de problemas

1. Selecione **disponibilidade e desempenho**.

    ![Captura de ecrã do "Disponibilidade e desempenho" Opções de resolução de problemas](./media/change-analysis/availability-and-performance.png)

1. Selecione **panes de aplicativos**.

   ![Captura de ecrã do botão "Falha de aplicação"](./media/change-analysis/application-crashes-tile.png)

1. Para ativar a análise de alterações, selecione **ativar agora**.

   ![Captura de ecrã das opções de "Falha de aplicação"](./media/change-analysis/application-crashes.png)

1. Para tirar partido de todas as funcionalidades de análise de alteração, ative **alteração Analysis**, **verificar a existência de alterações de código**, e **sempre no**. Em seguida, selecione **Guardar**.

    ![Captura de ecrã da interface do usuário de "Ativar a análise de alteração"](./media/change-analysis/change-analysis-on.png)

    - Ativar **alteração análise** para detectar alterações de nível de recursos. 
    - Ativar **verificar a existência de alterações de código** para ver ficheiros de implementação e as alterações de configuração do site. 
    - Ativar **sempre no** para otimizar o desempenho de verificação de alteração. Mas tenha em atenção que esta definição poderá resultar em encargos adicionais de faturas.

1. Para aceder a análise de alterações, selecione **diagnosticar e resolver problemas** > **disponibilidade e desempenho** > **falha aplicação**. Verá um gráfico que resume o tipo de alterações ao longo do tempo, juntamente com detalhes sobre essas mudanças:

     ![Captura de ecrã da vista de diferença de alteração](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Ativar análise de alterações em escala

Se a sua subscrição inclui várias aplicações web, permitindo que o serviço ao nível da aplicação web será ineficiente. Neste caso, siga estas instruções alternativas.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Registar o fornecedor de recursos de análise de alteração para a sua subscrição

1. Registre-se o sinalizador de funcionalidade de análise de alterações (pré-visualização). Porque o sinalizador de funcionalidade está em pré-visualização, tem de registá-lo para torná-lo visível à sua subscrição:

   1. Abra o [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

      ![Captura de ecrã da alteração do Cloud Shell](./media/change-analysis/cloud-shell.png)

   1. Altere o tipo de shell para **PowerShell**.

      ![Captura de ecrã da alteração do Cloud Shell](./media/change-analysis/choose-powershell.png)

   1. Execute o seguinte comando do PowerShell:

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```
    
1. Registe o fornecedor de recursos de análise de alteração para a subscrição.

   - Aceda a **subscrições**e selecione a subscrição que pretende ativar no serviço de alteração. Em seguida, selecione os fornecedores de recursos:

        ![Captura de ecrã que mostra como registar o fornecedor de recursos de análise de alteração](./media/change-analysis/register-rp.png)

       - Selecione **Microsoft.ChangeAnalysis**. Em seguida, na parte superior da página, selecione **registar**.

       - Depois do fornecedor de recursos estiver ativado, pode definir uma etiqueta oculta na aplicação web para detetar alterações no nível da implementação. Para definir uma etiqueta ocultada, siga as instruções em **não é possível obter as informações de análise de alteração**.

   - Em alternativa, pode utilizar um script do PowerShell para registar o fornecedor de recursos:

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration
    
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        Para utilizar o PowerShell para definir uma etiqueta oculta numa aplicação web, execute o seguinte comando:
    
        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > Depois de adicionar a marca ocultada, ainda poderá ter de esperar até quatro horas antes de começar a ver as alterações. Os resultados são atrasados porque a análise de alteração analisa a sua aplicação web apenas a cada 4 horas. A agenda de 4 horas limita o impacto de desempenho a análise.

## <a name="next-steps"></a>Passos Seguintes

- Monitorizar o serviço de aplicações, com mais eficiência [a ativação de funcionalidades do Application Insights](azure-web-apps.md) no Azure Monitor.
- Saiba mais sobre [gráfico de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), que ajuda a análise de alteração de energia.
