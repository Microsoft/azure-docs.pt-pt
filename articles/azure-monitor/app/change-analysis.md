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
ms.openlocfilehash: 5bd3816e65398283de85b4551a137b3f97db4cc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226287"
---
# <a name="application-change-analysis-public-preview"></a>Análise de Alterações da Aplicação (pré-visualização pública)

Quando ocorre uma problema/indisponibilidade do site em direto, é fundamental determinar rapidamente a causa principal. Padrão de soluções de monitorização pode ajudá-lo a identificar rapidamente o que existe um problema e, muitas vezes, mesmo que componente está a falhar. Mas isso sempre não levar a uma explicação imediata para por que motivo a falha está ocorrendo. Seu site trabalhou cinco minutos atrás, agora ele está quebrado. O que mudou nos últimos cinco minutos? Esta é a pergunta que a nova funcionalidade do Azure Monitor, análise de alterações de aplicação é criada para responder. Com a criação do poder dos [gráfico de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) análise de alterações de aplicações fornece informações sobre as alterações de aplicação do Azure para aumentar observability e reduzir o MTTR (tempo médio para reparar).

> [!IMPORTANT]
> Análise de alterações de aplicações do Azure Monitor está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-change-analysis-service"></a>Descrição geral do serviço de análise de alteração
O serviço de análise de alteração detecta vários tipos de alterações da camada de infraestrutura até à implementação de aplicação. É um fornecedor de recursos do Azure ao nível de subscrição que examina as alterações de recursos na subscrição e fornece dados de várias ferramentas de diagnóstico ajudar os utilizadores a compreender quais alterações poderão ter causado problemas.

O diagrama seguinte ilustra a arquitetura do serviço de análise de alteração: ![Diagrama de arquitetura para como o serviço de análise de alteração obtém dados de alteração e fornecer dados para ferramentas de cliente](./media/change-analysis/overview.png)

Atualmente a ferramenta está integrada nos serviços de aplicação web de aplicação diagnosticar e resolver problemas de experiência. Ver *serviço de análise de alteração para a aplicação de Web de serviços de aplicação* secção sobre como ativar e ver as alterações feitas numa aplicação web.

### <a name="azure-resource-manager-deployment-changes"></a>Alterações de implementação do Azure Resource Manager
Tirar partido [gráfico de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) a ferramenta de análise de alterações fornece um registo histórico de como os recursos do Azure que alojam a sua aplicação mudaram ao longo do tempo. Por exemplo, se uma aplicação web tivesse uma etiqueta adicionada a ele, a alteração será refletida na ferramenta de análise de alterações.
Essas informações estão sempre disponíveis enquanto o `Microsoft.ChangeAnalysis` fornecedor de recursos é carregada para a subscrição do Azure.

### <a name="web-application-deployment-and-configuration-changes"></a>Alterações de configuração e implementação da aplicação Web
Ferramenta de análise de alteração captura o estado de implementação e configuração de um aplicativo a cada 4 horas para computar as diferenças e apresentar o que foi alterado. Exemplos de tais alterações incluem alterações de variável de ambiente de aplicativo, alterações de regra de configuração de IP, alterações de identidade do serviço gerido, alterações de definições de SSL e assim por diante.
Ao contrário das alterações do Resource Manager, este tipo de informações de alteração pode não estar disponível imediatamente na ferramenta. Para ver as alterações mais recentes, utilize o botão "Análise de alterações agora" na ferramenta.

![Captura de ecrã da análise para que as alterações agora botão no diagnosticar e resolver a ferramenta de problemas com a integração de análise de alteração para a aplicação serviço de aplicações web](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Alterações de dependência
Recursos de dependências também podem ser a causa dos problemas. Por exemplo, se uma aplicação web chama-se para uma cache de Redis, o desempenho da aplicação web pode ser afetado pela cache de Redis SKU. Ao examinar o registo DNS de aplicação web altere analysis service também apresentar as dependências dos informações de alteração para identificar alterações em todos os componentes de uma aplicação que pode ter causado problemas.


## <a name="change-analysis-service-for-app-services-web-app"></a>Alterar o Analysis service para aplicações Web de serviços de aplicações

Análise de alterações de aplicações do Azure Monitor baseia-se atualmente para o Self-Service **diagnosticar e resolver problemas** experiência, que podem ser acedido a partir do **descrição geral** secção do seu serviço de aplicações do Azure aplicação:

![Página com as caixas vermelhas em torno do botão de descrição geral de descrição geral de captura de ecrã do serviço de aplicações do Azure e diagnosticar e resolver o botão de problemas](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-diagnose-and-solve-problems-tool"></a>Ativar a análise de alterações em diagnosticar e resolver a ferramenta de problemas

1. Selecione **disponibilidade e desempenho**

    ![captura de ecrã das opções de resolução de problemas de desempenho e disponibilidade](./media/change-analysis/availability-and-performance.png)

2. Clique nas **falha aplicação** mosaico.

   ![Captura de ecrã com mosaico de falhas de aplicação](./media/change-analysis/application-crashes-tile.png)

3. Para habilitar **alteração Analysis** selecionar **ativar agora**.

   ![captura de ecrã das opções de resolução de problemas de desempenho e disponibilidade](./media/change-analysis/application-crashes.png)

4. Para tirar partido de toda a alterar o conjunto de funcionalidades de análise **alterar Analysis**, **verificar a existência de alterações de código**, e **sempre no** para **no** e selecione **guardar**.

    ![Captura de ecrã da interface de usuário de análise de alteração de enable de App Service do Azure](./media/change-analysis/change-analysis-on.png)

    Se **alteração análise** é ativado, será capaz de detetar as alterações de nível de recursos. Se **verificar a existência de alterações de código** é ativada, também verá arquivos de implantação e as alterações de configuração do site. Habilitando **sempre no** otimizará a alteração de análise de desempenho, mas pode implicar custos adicionais de uma perspectiva de faturação.

5.  Quando tudo estiver ativada, selecionando **diagnosticar e resolver problemas** > **disponibilidade e desempenho** > **falha aplicação** permitirá que acesse a experiência de análise de alterações. O gráfico resume o tipo de alterações que aconteceu ao longo do tempo, juntamente com detalhes sobre essas mudanças:

     ![Captura de ecrã da vista de diferença de alteração](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-service-at-scale"></a>Ativar o serviço de análise de alterações em escala
Se tiver muitas das aplicações web na sua subscrição, ativar o serviço, por nível de aplicação web será ineficiente. Aqui estão algumas instruções de inclusão alternativo.

#### <a name="registering-change-analysis-resource-provider-for-your-subscription"></a>Registar o fornecedor de recursos de análise de alteração para a sua subscrição

1. Registar o sinalizador de funcionalidade de pré-visualização de análise de alteração

    Uma vez que esta funcionalidade está em pré-visualização, terá de registar em primeiro lugar o sinalizador de funcionalidade para que ele ficasse visível à sua subscrição.
    - Abra o [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

    ![Captura de ecrã da alteração do Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

    - Altere o tipo de shell para o PowerShell:

    ![Captura de ecrã da alteração do Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

    - Execute o seguinte comando do PowerShell:

    ``` PowerShell

    Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to onboard
    Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
    Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag

    ```

2. Registar o fornecedor de recursos de análise de alteração para a subscrição

    - Navegue para subscrições, selecione a subscrição que pretende integrar o serviço de alteração, em seguida, clique em fornecedores de recursos:

        ![Captura de ecrã por se registrar RP de análise de alterações a partir do painel de subscrições](./media/change-analysis/register-rp.png)

    - Selecione *Microsoft.ChangeAnalysis* e clique em *registar* no topo da página.

    - Assim que o fornecedor de recursos é carregada, siga as instruções em *não é possível obter as informações de análise de alterar* abaixo para definir etiqueta oculta na aplicação web para ativar a implementação de alterações no nível de deteção na aplicação web.

3. Como alternativa para o passo 2 acima, pode se registrar para o fornecedor de recursos por meio de script do PowerShell:

    ```PowerShell
    Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
    ```

4. Para definir uma etiqueta oculta numa aplicação web com o PowerShell, execute o seguinte comando:

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
