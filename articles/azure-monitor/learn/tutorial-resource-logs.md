---
title: Coletar logs de recursos de um recurso do Azure e analisá-los com Azure Monitor
description: Tutorial para definir as configurações de diagnóstico para coletar logs de recursos de um recurso do Azure para um Log Analytics espaço de trabalho onde eles podem ser analisados com uma consulta de log.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 90b2a9bc9e3e8aa6297f02a46163717a2bf58a22
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75533560"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Tutorial: coletar e analisar logs de recursos de um recurso do Azure

Os logs de recursos fornecem informações sobre a operação detalhada de um recurso do Azure e são úteis para monitorar sua integridade e disponibilidade. Os recursos do Azure geram logs de recursos automaticamente, mas você deve configurar onde eles devem ser coletados. Este tutorial orienta você pelo processo de criação de uma configuração de diagnóstico para coletar logs de recursos para um recurso em sua assinatura do Azure e analisá-los com uma consulta de log.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um espaço de trabalho Log Analytics no Azure Monitor
> * Criar uma configuração de diagnóstico para coletar logs de recursos 
> * Criar uma consulta de log simples para analisar os logs


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de um recurso do Azure para monitorar. Você pode usar qualquer recurso em sua assinatura do Azure que dê suporte às configurações de diagnóstico. Para determinar se um recurso dá suporte a configurações de diagnóstico, acesse seu menu na portal do Azure e verifique se há uma opção de **configurações de diagnóstico** na seção **monitoramento** do menu.


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-workspace"></a>Criar áreas de trabalho
Um espaço de trabalho Log Analytics no Azure Monitor coleta e indexa dados de log de uma variedade de fontes e permite uma análise avançada usando uma linguagem de consulta eficiente. O espaço de trabalho do Log Analytics precisa existir antes de criar uma configuração de diagnóstico para enviar dados para ela. Você pode usar um espaço de trabalho existente em sua assinatura do Azure ou criar um com o procedimento a seguir. 

> [!NOTE]
> Embora você possa trabalhar com dados em espaços de trabalho do Log Analytics no menu **Azure monitor** , crie e gerencie espaços de trabalho no menu **log Analytics espaços de trabalho** .

1. Em **todos os serviços**, selecione **log Analytics espaços de trabalho**.
2. Clique em **Adicionar** na parte superior da tela e forneça os seguintes detalhes para o espaço de trabalho:
   - **Espaço de trabalho log Analytics**: nome do novo espaço de trabalho. Esse nome deve ser globalmente exclusivo em todas as assinaturas de Azure Monitor.
   - **Assinatura**: selecione a assinatura para armazenar o espaço de trabalho. Isso não precisa ser a mesma assinatura que o recurso que está sendo monitorado.
   - **Grupo de recursos**: selecione um grupo de recursos existente ou clique em **criar novo** para criar um novo. Isso não precisa ser o mesmo grupo de recursos que o recurso que está sendo monitorado.
   - **Local**: selecione uma região do Azure ou crie uma nova. Isso não precisa ser o mesmo local que o recurso que está sendo monitorado.
   - **Tipo de preço**: selecione *gratuito* , que manterá 7 dias de dados. Você pode alterar esse tipo de preço mais tarde. Clique no link **log Analytics preços** para saber mais sobre diferentes tipos de preço.

    ![Nova área de trabalho](media/tutorial-resource-logs/new-workspace.png)

3. Clique em **OK** para criar o espaço de trabalho.

## <a name="create-a-diagnostic-setting"></a>Criar uma configuração de diagnóstico
[As configurações de diagnóstico](../platform/diagnostic-settings.md) definem onde os logs de recursos devem ser enviados para um recurso específico. Uma única configuração de diagnóstico pode ter vários [destinos](../platform/diagnostic-settings.md#destinations), mas usaremos apenas um espaço de trabalho log Analytics neste tutorial.

1. Na seção **monitoramento** do menu do recurso, selecione configurações de **diagnóstico**.
2. Você deve ter uma mensagem "nenhuma configuração de diagnóstico definida". Clique em **Adicionar configuração de diagnóstico**.

    ![Definições de diagnóstico](media/tutorial-resource-logs/diagnostic-settings.png)

3. Cada configuração de diagnóstico tem três partes básicas:
 
   - **Nome**: isso não tem nenhum efeito significativo e deve ser simplesmente descritivo para você.
   - **Destinos**: um ou mais destinos para enviar os logs. Todos os serviços do Azure compartilham o mesmo conjunto de três destinos possíveis. Cada configuração de diagnóstico pode definir um ou mais destinos, mas não mais de um destino de um tipo específico. 
   - **Categorias**: categorias de logs para enviar a cada um dos destinos. O conjunto de categorias irá variar para cada serviço do Azure.

4. Selecione **Enviar para log Analytics espaço de trabalho** e, em seguida, selecione o espaço de trabalho que você criou.
5. Selecione as categorias que você deseja coletar. Consulte a documentação de cada serviço para obter uma definição de suas categorias disponíveis.

    ![Configuração de diagnóstico](media/tutorial-resource-logs/diagnostic-setting.png)

6. Clique em **salvar** para salvar as configurações de diagnóstico.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Usar uma consulta de log para recuperar logs
Os dados são recuperados de um espaço de trabalho Log Analytics usando uma consulta de log escrita na linguagem de consulta Kusto (KQL). As informações e soluções no Azure Monitor fornecerão consultas de log para recuperar dados de um serviço específico, mas você pode trabalhar diretamente com consultas de log e seus resultados na portal do Azure com Log Analytics. 

1. Na seção **monitoramento** do menu do recurso, selecione **logs**.
2. Log Analytics é aberto com uma janela de consulta vazia com o escopo definido para seu recurso. Todas as consultas incluirão somente os registros desse recurso.

    > [!NOTE]
    > Se você tiver aberto os logs no menu Azure Monitor, o escopo será definido como o espaço de trabalho Log Analytics. Nesse caso, todas as consultas incluirão todos os registros no espaço de trabalho.
   
    ![Registos](media/tutorial-resource-logs/logs.png)

4. O serviço mostrado no exemplo grava logs de recursos na tabela **AzureDiagnostics** , mas outros serviços podem gravar em outras tabelas. Consulte [serviços, esquemas e categorias com suporte para logs de recursos do Azure](../platform/diagnostic-logs-schema.md) para tabelas usadas por diferentes serviços do Azure.

    > [!NOTE]
    > Vários serviços gravam logs de recursos na tabela AzureDiagnostics. Se você iniciar Log Analytics no menu Azure Monitor, precisará adicionar uma instrução `where` com a coluna `ResourceProvider` para especificar seu serviço específico. Quando você inicia Log Analytics no menu de um recurso, o escopo é definido como somente registros desse recurso, de modo que essa coluna não é necessária. Consulte a documentação do serviço para obter exemplos de consultas.


5. Digite uma consulta e clique em **executar** para inspecionar os resultados. 
6. Consulte Introdução [às consultas de log no Azure monitor](../log-query/get-started-queries.md) para obter um tutorial sobre como escrever consultas de log.

    ![Consulta de log](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Passos seguintes
Agora que você aprendeu como coletar logs de recursos em um espaço de trabalho Log Analytics, conclua um tutorial sobre como escrever consultas de log para analisar esses dados.

> [!div class="nextstepaction"]
> [Introdução às consultas de log no Azure Monitor](../log-query/get-started-queries.md)
