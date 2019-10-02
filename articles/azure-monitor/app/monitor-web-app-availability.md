---
title: Monitorizar a disponibilidade e a capacidade de resposta de qualquer site | Microsoft Docs
description: Configurar testes Web no Application Insights. Receber alertas se um site ficar indisponível ou responder lentamente.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 3c7ba10525dedf213a416d9ce6b55c80539fedd7
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71812209"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorar a disponibilidade de qualquer site

Depois de implantar seu aplicativo Web/site, você pode configurar testes recorrentes para monitorar a disponibilidade e a capacidade de resposta. O [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) envia regularmente pedidos Web para a sua aplicação a partir de pontos em todo o mundo. Ele poderá alertá-lo se seu aplicativo não estiver respondendo ou se responder muito devagar.

Pode configurar testes de disponibilidade para qualquer ponto final HTTP ou HTTPS que seja acessível a partir da Internet pública. Você não precisa fazer nenhuma alteração no site que está testando. Na verdade, nem precisa ser um site que você possui. Você pode testar a disponibilidade de uma API REST da qual seu serviço depende.

### <a name="types-of-availability-tests"></a>Tipos de testes de disponibilidade:

Há três tipos de testes de disponibilidade:

* [Teste de ping do URL](#create-a-url-ping-test): um teste simples que pode criar no Portal do Azure.
* [Teste na Web de várias etapas](availability-multistep.md): Uma gravação de uma sequência de solicitações da Web, que pode ser reproduzida para testar cenários mais complexos. Os testes na Web de várias etapas são criados no Visual Studio Enterprise e carregados no portal para execução.
* [Testes de disponibilidade de acompanhamento personalizado](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): Se você decidir criar um aplicativo personalizado para executar testes de disponibilidade, o método `TrackAvailability()` poderá ser usado para enviar os resultados para Application Insights.

**Você pode criar até 100 testes de disponibilidade por recurso de Application Insights.**

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de disponibilidade, primeiro você precisa criar um recurso de Application Insights. Se você já tiver criado um recurso, vá para a próxima seção para [criar um teste de ping de URL](#create-a-url-ping-test).

No portal do Azure, selecione **criar um recurso** > **ferramentas para desenvolvedores** > **Application insights** e [criar um recurso de Application insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Criar um teste de ping do URL

O nome "teste de ping de URL" é um pouco de um nome. Para ser claro, esse teste não faz uso do ICMP (protocolo de mensagem de controle da Internet) para verificar a disponibilidade do site. Em vez disso, ele usa a funcionalidade de solicitação HTTP mais avançada para validar se um ponto de extremidade está respondendo. Ele também mede o desempenho associado a essa resposta e adiciona a capacidade de definir critérios de êxito personalizados acoplados a recursos mais avançados, como a análise de solicitações dependentes, além de permitir novas tentativas.

Para criar sua primeira solicitação de disponibilidade, abra o painel disponibilidade e selecione **criar teste**.

![Indique, pelo menos, o URL do seu site](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Criar um teste

|Definição| Explicação
|----|----|----|
|**URL** |  A URL pode ser qualquer página da Web que você deseja testar, mas ela deve estar visível na Internet pública. O URL pode incluir uma cadeia de consulta. Desta forma, pode, por exemplo, testar um pouco a base de dados. Se o URL remeter para um redirecionamento, iremos segui-lo até dez redirecionamentos.|
|**Analisar solicitações dependentes**| Solicitações de teste imagens, scripts, arquivos de estilo e outros arquivos que fazem parte da página da Web em teste. O tempo de resposta gravado inclui o tempo necessário para obter estes ficheiros. O teste falhará se qualquer um desses recursos não puder ser baixado com êxito dentro do tempo limite para o teste inteiro. Se a opção não estiver selecionada, o teste pede apenas o ficheiro no URL especificado. A habilitação dessa opção resulta em uma verificação mais estrita. O teste pode falhar para casos, o que pode não ser perceptível ao navegar manualmente no site.
|**Habilitar novas tentativas**|Quando o teste falha, ele é repetido após um curto intervalo. Uma falha só é comunicada após três tentativas falhadas sucessivas. Os testes subsequentes são realizados à frequência habitual de teste. A repetição encontra-se temporariamente suspensa até ao próximo êxito. Esta regra é aplicada de forma independente em cada localização de teste. **Recomendamos essa opção**. Em média, cerca de 80% das falhas desaparecem aquando da repetição.|
|**Frequência de teste**| Define a frequência com que o teste é executado a partir de cada local de teste. Com uma frequência predefinida de cinco minutos e cinco localizações de teste, o site é testado, em média, a cada minuto.|
|**Locais de teste**| São os locais de onde nossos servidores enviam solicitações da Web para sua URL. **Nosso número mínimo de locais de teste recomendados é cinco** para garantir que você possa distinguir problemas em seu site por meio de problemas de rede. Pode selecionar até 16 localizações.

**Se a URL não estiver visível na Internet pública, você poderá optar por abrir seletivamente o firewall para permitir apenas as transações de teste**. Para saber mais sobre as exceções de firewall para nossos agentes de teste de disponibilidade, consulte o [Guia de endereço IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> É altamente recomendável testar de vários locais com **um mínimo de cinco locais**. Isso é para evitar alarmes falsos que podem resultar de problemas transitórios com um local específico. Além disso, descobrimos que a configuração ideal é ter o **número de locais de teste igual ao limite de local do alerta + 2**.

### <a name="success-criteria"></a>Critérios de êxito

|Definição| Explicação
|----|----|----|
| **Tempo limite do teste** |Diminua esse valor para ser alertado sobre respostas lentas. O teste será contabilizado como uma falha se as respostas do seu site não foram recebidas durante este período. Se tiver selecionado **Pedidos dependentes de análise**, todas as imagens, ficheiros de estilos, scripts e outros recursos dependentes terão de ser recebidos durante este período.|
| **Resposta HTTP** | O código de status retornado que é contado como êxito. 200 é o código que indica que foi devolvida uma página Web normal.|
| **Correspondência de conteúdo** | Uma cadeia de caracteres, como "bem-vindo!" Podemos testar que uma correspondência sensíveis às maiúsculas e minúsculas ocorre em cada resposta. Tem de ser uma cadeia simples, sem carateres universais. Não se esqueça de que se alterar o conteúdo da página, poderá ter de a atualizar. **Somente caracteres em inglês têm suporte com correspondência de conteúdo** |

### <a name="alerts"></a>Alertas

|Definição| Explicação
|----|----|----|
|**Quase em tempo real (visualização)** | É recomendável usar alertas quase em tempo real. A configuração desse tipo de alerta é feita após a criação do teste de disponibilidade.  |
|**Clássico** | Não recomendamos o uso de alertas clássicos para novos testes de disponibilidade.|
|**Limite de local de alerta**|Recomendamos um mínimo de 3/5 locais. A relação ideal entre o limite de local de alerta e o número de locais de teste é o número **limite** = **de local de alerta de locais de teste-2, com um mínimo de cinco locais de teste.**|

## <a name="see-your-availability-test-results"></a>Ver os resultados do teste de disponibilidade

Os resultados do teste de disponibilidade podem ser visualizados com exibições de gráfico de linha e dispersão.

Depois de alguns minutos, clique em **Atualizar** para ver os resultados do teste.

![Exibição de linha](./media/monitor-web-app-availability/availability-refresh-002.png)

A exibição dispersão mostra exemplos dos resultados de teste que têm detalhes da etapa de teste de diagnóstico neles. O motor de testes armazena os detalhes de diagnóstico dos testes que têm falhas. Relativamente aos testes bem-sucedidos, são armazenados os detalhes de diagnóstico de um subconjunto das execuções. Passe o mouse sobre qualquer um dos pontos verdes/vermelhos para ver o teste, o nome do teste e o local.

![Exibição de linha](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Selecione um teste ou uma localização específica ou reduza o período de tempo para ver mais resultados relativos ao período de tempo relevante. Utilize o Explorador de Pesquisa para ver resultados de todas as execuções ou utilize consultas do Analytics para executar relatórios personalizados nestes dados.

## <a name="inspect-and-edit-tests"></a>Inspecionar e editar testes

Para editar, desabilitar temporariamente ou excluir um teste, clique nas reticências ao lado de um nome de teste. Pode levar até 20 minutos para que as alterações de configuração sejam propagadas para todos os agentes de teste depois que uma alteração é feita.

![Exibir detalhes do teste. Editar e desabilitar um teste na Web](./media/monitor-web-app-availability/edit.png)

Quando realizar tarefas de manutenção ao seu serviço, poderá querer desativar os testes de disponibilidade ou as regras de alerta associadas aos mesmos.

## <a name="if-you-see-failures"></a>Se vir falhas

Clique num ponto vermelho.

![Clicar num ponto vermelho](./media/monitor-web-app-availability/open-instance-3.png)

A partir de um resultado do teste de disponibilidade, você pode ver os detalhes da transação em todos os componentes. Aqui você pode:

* Inspecionar a resposta recebida do seu servidor.
* Diagnosticar falha com a telemetria correlacionada do lado do servidor coletada durante o processamento do teste de disponibilidade com falha.
* Registre um problema ou item de trabalho no git ou Azure Boards para acompanhar o problema. O erro irá conter uma ligação para este evento.
* Abra o resultado do teste da Web no Visual Studio.

Saiba mais sobre a experiência de diagnóstico de transação de ponta a ponta [aqui](../../azure-monitor/app/transaction-diagnostics.md).

Clique na linha de exceção para ver os detalhes da exceção do lado do servidor que causou a falha do teste de disponibilidade sintética. Você também pode obter o [instantâneo de depuração](../../azure-monitor/app/snapshot-debugger.md) para obter um diagnóstico de nível de código mais rico.

![Diagnóstico do lado do servidor](./media/monitor-web-app-availability/open-instance-4.png)

Além dos resultados brutos, você também pode exibir duas métricas de disponibilidade principais no [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. Ininterrupta Porcentagem dos testes que foram bem-sucedidos, em todas as execuções de teste.
2. Duração do teste: Duração média do teste em todas as execuções de teste.

## <a name="automation"></a>Automatização

* [Utilize scripts do PowerShell para configurar um teste de disponibilidade](../../azure-monitor/app/powershell.md#add-an-availability-test) automaticamente.
* Configure um [webhook](../../azure-monitor/platform/alerts-webhooks.md) que será chamado sempre que for gerado um alerta.

## <a name="troubleshooting"></a>Resolução de problemas

[Artigo de solução de problemas](troubleshoot-availability.md)dedicado.

## <a name="next-steps"></a>Passos seguintes

* [Alertas de disponibilidade](availability-alerts.md)
* [Testes na Web de várias etapas](availability-multistep.md)


