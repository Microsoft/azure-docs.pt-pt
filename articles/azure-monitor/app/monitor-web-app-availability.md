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
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: abe55007aa8a8719d6b6f1659e00a089a2e28771
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303747"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorizar a disponibilidade de qualquer Web site

Depois de implementar a aplicação web/Web site, pode configurar testes recorrentes para monitorizar a disponibilidade e capacidade de resposta. O [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) envia regularmente pedidos Web para a sua aplicação a partir de pontos em todo o mundo. Ele pode alertá-lo se seu aplicativo não está a responder ou se responde muito lenta.

Pode configurar testes de disponibilidade para qualquer ponto final HTTP ou HTTPS que seja acessível a partir da Internet pública. Não tem de fazer alterações no Web site que está a testar. Na verdade, até mesmo não tem de ser um site que é proprietário. Pode testar a disponibilidade de uma API REST que depende do seu serviço.

### <a name="types-of-availability-tests"></a>Tipos de testes de disponibilidade:

Existem três tipos de testes de disponibilidade:

* [Teste de ping do URL](#create-a-url-ping-test): um teste simples que pode criar no Portal do Azure.
* [Teste web de vários passos](availability-multistep.md): Uma gravação de uma seqüência de solicitações de web, o que pode ser reproduzido para testar cenários mais complexos. Testes web de vários passos são criados no Visual Studio Enterprise e carregados para o portal para execução.
* [Testes de disponibilidade de controle personalizado](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): O `TrackAvailability()` método pode ser utilizado para criar seus próprios testes de disponibilidade personalizado.

**Pode criar até 100 testes de disponibilidade por recurso do Application Insights.**

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de disponibilidade, tem primeiro de criar um recurso do Application Insights. Se já tiver criado um recurso, avance para a secção seguinte para [criar um teste de Ping do URL](#create-a-url-ping-test).

A partir do portal do Azure, selecione **criar um recurso** > **ferramentas de programação** > **Application Insights** e [criar um Recurso do Application Insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Criar um teste de ping do URL

O nome "teste de ping do URL" é um pouco de um nome inapropriado. Para ser claro, esse teste não está fazendo uso de ICMP (Internet Control Message Protocol) para verificar a disponibilidade do seu site. Em vez disso, ele usa mais avançada funcionalidade de pedido HTTP para validar se um ponto final está a responder. Também mede o desempenho associado com que a resposta e adiciona a capacidade de definir critérios de sucesso personalizado juntamente com funcionalidades mais avançadas, como pedidos dependentes de análise e permitindo para repetições.

Para criar o primeiro pedido de disponibilidade, abra o painel de disponibilidade e selecione **criar teste**.

![Indique, pelo menos, o URL do seu site](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Criar um teste

|Definição| Explicação
|----|----|----|
|**URL** |  O URL pode ser qualquer página da web que pretende testar, mas tem de estar visível a partir da internet pública. O URL pode incluir uma cadeia de consulta. Desta forma, pode, por exemplo, testar um pouco a base de dados. Se o URL remeter para um redirecionamento, iremos segui-lo até dez redirecionamentos.|
|**Analisar pedidos dependentes**| Teste pede imagens, scripts, ficheiros de estilo e outros ficheiros que fazem parte da página da web em teste. O tempo de resposta gravado inclui o tempo necessário para obter estes ficheiros. O teste falhará se qualquer um desses recursos não podem ser transferidos com êxito no tempo limite para o teste completo. Se a opção não estiver selecionada, o teste pede apenas o ficheiro no URL especificado. A ativar o resultado opção é uma verificação mais estritas. O teste poderá falhar por casos, o que não podem ser perceptíveis ao navegar manualmente o site.
|**Permitir repetição de tentativas**|Quando o teste falhar, será tentada novamente após um curto intervalo. Uma falha só é comunicada após três tentativas falhadas sucessivas. Os testes subsequentes são realizados à frequência habitual de teste. A repetição encontra-se temporariamente suspensa até ao próximo êxito. Esta regra é aplicada de forma independente em cada localização de teste. **Recomendamos esta opção**. Em média, cerca de 80% das falhas desaparecem aquando da repetição.|
|**Frequência de teste**| Define a frequência com que a execução do teste em cada localização de teste. Com uma frequência predefinida de cinco minutos e cinco localizações de teste, o site é testado, em média, a cada minuto.|
|**Localizações de teste**| São os locais de onde os nossos servidores enviam pedidos web ao seu URL. **Nosso número mínimo de localizações de teste recomendado é cinco** e assim garantir que pode distinguir problemas no seu Web site dos problemas de rede. Pode selecionar até 16 localizações.

**Se o URL não é visível a partir da internet pública, pode optar por abrir seletivamente a firewall para permitir que apenas as transações de teste por meio de**. Para saber mais sobre as exceções de firewall para nossos agentes de teste de disponibilidade, consulte a [guia de endereços IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Recomendamos vivamente que teste a partir de múltiplas localizações com **um mínimo de cinco locais**. Este procedimento impede que os alarmes falsos que pode ser resultado de problemas transitórios com uma localização específica. Além disso Achamos que a configuração ideal é fazer com que o **número de localizações de teste de ser igual ao limiar de alerta de localização + 2**.

### <a name="success-criteria"></a>Critérios de êxito

|Definição| Explicação
|----|----|----|
| **Tempo limite de teste** |Reduza este valor para ser alertado de repostas lentas. O teste será contabilizado como uma falha se as respostas do seu site não foram recebidas durante este período. Se tiver selecionado **Pedidos dependentes de análise**, todas as imagens, ficheiros de estilos, scripts e outros recursos dependentes terão de ser recebidos durante este período.|
| **Resposta HTTP** | O código de estado devolvido é contabilizado como um sucesso. 200 é o código que indica que foi devolvida uma página Web normal.|
| **Correspondência do conteúdo** | Uma cadeia de caracteres, como "Bem-vindo!" Podemos testar que uma correspondência sensíveis às maiúsculas e minúsculas ocorre em cada resposta. Tem de ser uma cadeia simples, sem carateres universais. Não se esqueça de que se alterar o conteúdo da página, poderá ter de a atualizar. **Apenas carateres em inglês são suportadas com correspondência do conteúdo** |

### <a name="alerts"></a>Alertas

|Definição| Explicação
|----|----|----|
|**Quase em tempo real (pré-visualização)** | Recomendamos que utilize os alertas de tempo quase real. Configurar este tipo de alerta é feita após a criação de seu teste de disponibilidade.  |
|**Clássico** | Nós já não é recomendado utilizar alertas clássicos para novos testes de disponibilidade.|
|**Limiar de alerta de localização**|Recomendamos um mínimo de 3/5 localizações. A relação ideal entre o limiar de alerta de localização e o número de localizações de teste é **limiar de localização de alerta** = **número de localizações de teste - 2, com um mínimo de cinco localizações de teste.**|

## <a name="see-your-availability-test-results"></a>Ver os resultados do teste de disponibilidade

Resultados do teste de disponibilidade podem ser visualizados com exibições de desenho de linha e de dispersão.

Após alguns minutos, clique em **atualizar** para ver os resultados do teste.

![Vista de linha](./media/monitor-web-app-availability/availability-refresh-002.png)

A vista de gráfico de dispersão mostra exemplos dos resultados do teste que tenham o detalhe da etapa de teste de diagnóstico nos mesmos. O motor de testes armazena os detalhes de diagnóstico dos testes que têm falhas. Relativamente aos testes bem-sucedidos, são armazenados os detalhes de diagnóstico de um subconjunto das execuções. Coloque o cursor sobre qualquer um dos pontos verdes/vermelhos para ver o teste, nome e localização de teste.

![Vista de linha](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Selecione um teste ou uma localização específica ou reduza o período de tempo para ver mais resultados relativos ao período de tempo relevante. Utilize o Explorador de Pesquisa para ver resultados de todas as execuções ou utilize consultas do Analytics para executar relatórios personalizados nestes dados.

## <a name="inspect-and-edit-tests"></a>Inspecionar e editar testes

Para editar, desativar temporariamente ou eliminar um teste de clique nas reticências junto a um nome de teste. Poderá demorar até 20 minutos para que as alterações de configuração a serem propagadas para todos os agentes de teste depois que for feita uma alteração.

![Ver detalhes do teste. Editar e desativar um teste web](./media/monitor-web-app-availability/edit.png)

Quando realizar tarefas de manutenção ao seu serviço, poderá querer desativar os testes de disponibilidade ou as regras de alerta associadas aos mesmos.

## <a name="if-you-see-failures"></a>Se vir falhas

Clique num ponto vermelho.

![Clicar num ponto vermelho](./media/monitor-web-app-availability/open-instance-3.png)

A partir de um resultado de teste de disponibilidade, pode ver os detalhes de transações em todos os componentes. Aqui, pode:

* Inspecionar a resposta recebida do seu servidor.
* Diagnosticar falhas com a telemetria correlacionada do lado do servidor recolhida durante o processamento o teste de disponibilidade falhou.
* Inicie sessão um problema ou item de trabalho no Git ou do Azure quadros para controlar o problema. O erro irá conter uma ligação para este evento.
* Abra o resultado do teste da Web no Visual Studio.

Saber mais sobre os diagnósticos de transação de ponta a ponta experiência [aqui](../../azure-monitor/app/transaction-diagnostics.md).

Clique na linha da exceção para ver os detalhes da exceção do lado do servidor que causou o teste de disponibilidade sintético efetuar a ativação. Também pode obter o [instantâneo de depuração](../../azure-monitor/app/snapshot-debugger.md) para diagnóstico ao nível de código mais sofisticado.

![Diagnóstico do lado do servidor](./media/monitor-web-app-availability/open-instance-4.png)

Além dos resultados em bruto, também pode ver duas métricas de disponibilidade de chaves no [Explorador de métricas](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. Disponibilidade: Percentagem dos testes que foram bem-sucedidos, em todas as execuções de teste.
2. Duração do teste: Duração média do teste em todas as execuções de teste.

## <a name="automation"></a>Automatização

* [Utilize scripts do PowerShell para configurar um teste de disponibilidade](../../azure-monitor/app/powershell.md#add-an-availability-test) automaticamente.
* Configure um [webhook](../../azure-monitor/platform/alerts-webhooks.md) que será chamado sempre que for gerado um alerta.

## <a name="troubleshooting"></a>Resolução de problemas

Dedicado [artigo de resolução de problemas](troubleshoot-availability.md).

## <a name="next-steps"></a>Passos Seguintes

* [Alertas de disponibilidade](availability-alerts.md)
* [Testes web de vários passos](availability-multistep.md)


