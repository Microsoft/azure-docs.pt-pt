---
title: Monitorizar a disponibilidade e a capacidade de resposta de qualquer site | Microsoft Docs
description: Configurar testes Web no Application Insights. Receber alertas se um site ficar indisponível ou responder lentamente.
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 61358051a8ddc32bc01ec5e231f4c28ebfa18ee0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670037"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorize a disponibilidade de qualquer website

Depois de ter implementado a sua aplicação/website web, pode configurar testes recorrentes para monitorizar a disponibilidade e a capacidade de resposta. O [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) envia regularmente pedidos Web para a sua aplicação a partir de pontos em todo o mundo. Pode alertá-lo se a sua aplicação não estiver a responder, ou se responder muito lentamente.

Pode configurar testes de disponibilidade para qualquer ponto final HTTP ou HTTPS que seja acessível a partir da Internet pública. Não precisa de fazer alterações no site que está a testar. Na verdade, nem precisa ser um site que tenha. Pode testar a disponibilidade de uma API REST de que o seu serviço depende.

### <a name="types-of-availability-tests"></a>Tipos de testes de disponibilidade:

Existem três tipos de testes de disponibilidade:

* [Teste de ping do URL](#create-a-url-ping-test): um teste simples que pode criar no Portal do Azure.
* [Teste web em várias etapas](availability-multistep.md): Uma gravação de uma sequência de pedidos web, que pode ser reproduzido de volta para testar cenários mais complexos. Os testes web em várias etapas são criados na Visual Studio Enterprise e enviados para o portal para execução.
* [Testes personalizados](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet)de disponibilidade de faixas : Se decidir criar `TrackAvailability()` uma aplicação personalizada para executar testes de disponibilidade, o método pode ser usado para enviar os resultados para Insights de Aplicação.

**Pode criar até 100 testes de disponibilidade por recurso Application Insights.**

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de disponibilidade, primeiro é necessário criar um recurso Application Insights. Se já criou um recurso, dirija-se à secção seguinte para [criar um teste URL Ping](#create-a-url-ping-test).

A partir do portal Azure, selecione **Criar um recurso** > **developer Tools** > **Application Insights** e criar um recurso De insights de [aplicação](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Criar um teste de ping do URL

O nome "URL ping test" é um pouco errado. Para ser claro, este teste não está a fazer qualquer uso do ICMP (Protocolo de Mensagem de Controlo de Internet) para verificar a disponibilidade do seu site. Em vez disso, utiliza uma funcionalidade de pedido http mais avançada para validar se um ponto final está a responder. Também mede o desempenho associado a essa resposta, e adiciona a capacidade de definir critérios de sucesso personalizados juntamente com funcionalidades mais avançadas, como analisar pedidos dependentes, e permitir repetições.

Para criar o seu primeiro pedido de disponibilidade, abra o painel de disponibilidade e selecione **Create Test**.

![Indique, pelo menos, o URL do seu site](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Criar um teste

|Definição| Explicação
|----|----|----|
|**URL** |  O URL pode ser qualquer página Web que pretender testar, mas tem de estar visível a partir da Internet pública. O URL pode incluir uma cadeia de consulta. Desta forma, pode, por exemplo, testar um pouco a base de dados. Se o URL remeter para um redirecionamento, iremos segui-lo até dez redirecionamentos.|
|**Pedidos dependentes da Parse**| O teste solicita imagens, scripts, ficheiros de estilo e outros ficheiros que fazem parte da página web em teste. O tempo de resposta gravado inclui o tempo necessário para obter estes ficheiros. O teste falha se algum destes recursos não puder ser descarregado com sucesso dentro do prazo para todo o teste. Se a opção não estiver selecionada, o teste pede apenas o ficheiro no URL especificado. Permitir esta opção resulta numa verificação mais rigorosa. O teste pode falhar em casos que podem não ser percetíveis ao navegar manualmente no site.
|**Ativar repetições**|quando o teste falha, é novamente experimentado após um curto intervalo. Uma falha só é comunicada após três tentativas falhadas sucessivas. Os testes subsequentes são realizados à frequência habitual de teste. A repetição encontra-se temporariamente suspensa até ao próximo êxito. Esta regra é aplicada de forma independente em cada localização de teste. **Recomendamos esta opção.** Em média, cerca de 80% das falhas desaparecem aquando da repetição.|
|**Frequência de teste**| Define a frequência com que o teste é executado a partir de cada local de teste. Com uma frequência predefinida de cinco minutos e cinco localizações de teste, o site é testado, em média, a cada minuto.|
|**Locais de teste**| São os locais de onde os nossos servidores enviam pedidos web para o seu URL. **O nosso número mínimo de locais de teste recomendados é de cinco,** de forma a garantir que pode distinguir problemas no seu website a partir de problemas de rede. Pode selecionar até 16 localizações.

**Se o seu URL não for visível a partir da internet pública, pode optar por abrir seletivamente a sua firewall para permitir apenas as transações**de teste através de . Para saber mais sobre as exceções à firewall para os nossos agentes de teste de disponibilidade, consulte o guia de [endereços IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Recomendamos vivamente os testes de vários locais com **um mínimo de cinco locais.** Isto é para evitar alarmes falsos que possam resultar de problemas transitórios com uma localização específica. Além disso, descobrimos que a configuração ideal é ter o **número de locais de teste igual ao limiar de localização de alerta + 2**.

### <a name="success-criteria"></a>Critérios de sucesso

|Definição| Explicação
|----|----|----|
| **Tempo de teste** |Diminua este valor para ser alertado sobre respostas lentas. O teste será contabilizado como uma falha se as respostas do seu site não foram recebidas durante este período. Se tiver selecionado **Pedidos dependentes de análise**, todas as imagens, ficheiros de estilos, scripts e outros recursos dependentes terão de ser recebidos durante este período.|
| **Resposta HTTP** | O código de estado devolvido que é contado como um sucesso. 200 é o código que indica que foi devolvida uma página Web normal.|
| **Correspondência de conteúdo** | Uma corda, como "Bem-vindo!" Podemos testar que uma correspondência sensíveis às maiúsculas e minúsculas ocorre em cada resposta. Tem de ser uma cadeia simples, sem carateres universais. Não se esqueça de que se alterar o conteúdo da página, poderá ter de a atualizar. **Apenas os caracteres ingleses são suportados com correspondência de conteúdo** |

### <a name="alerts"></a>Alertas

|Definição| Explicação
|----|----|----|
|**Quase em tempo real (Pré-visualização)** | Recomendamos a utilização de alertas quase em tempo real. Configurar este tipo de alerta é feito após a criação do seu teste de disponibilidade.  |
|**Clássica** | Já não recomendamos a utilização de alertas clássicos para novos testes de disponibilidade.|
|**Limiar de localização de alerta**|Recomendamos um mínimo de 3/5 locais. A relação ideal entre o limiar de localização do alerta e o número de locais de teste é o número **de pontos-limite** = de localização de localização**de pontos de ensaio - 2, com um mínimo de cinco locais de teste.**|

## <a name="see-your-availability-test-results"></a>Ver os resultados do teste de disponibilidade

Os resultados dos testes de disponibilidade podem ser visualizados com vistas de enredo de linha e dispersão.

Após alguns minutos, clique em **Refresh** para ver os resultados dos seus testes.

![Vista de linha](./media/monitor-web-app-availability/availability-refresh-002.png)

A visão de dispersão mostra amostras dos resultados dos testes que têm detalhes de teste de diagnóstico. O motor de testes armazena os detalhes de diagnóstico dos testes que têm falhas. Relativamente aos testes bem-sucedidos, são armazenados os detalhes de diagnóstico de um subconjunto das execuções. Passe por cima de qualquer um dos pontos verdes/vermelhos para ver o teste, o nome do teste e a localização.

![Vista de linha](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Selecione um teste ou uma localização específica ou reduza o período de tempo para ver mais resultados relativos ao período de tempo relevante. Utilize o Explorador de Pesquisa para ver resultados de todas as execuções ou utilize consultas do Analytics para executar relatórios personalizados nestes dados.

## <a name="inspect-and-edit-tests"></a> Inspecionar e editar testes

Para editar, desativar temporariamente ou apagar um teste clique nas elipses ao lado de um nome de teste. Pode levar até 20 minutos para que as alterações de configuração se propaguem a todos os agentes de teste após a alteração.

![Ver detalhes do teste. Editar e Desativar um teste web](./media/monitor-web-app-availability/edit.png)

Quando realizar tarefas de manutenção ao seu serviço, poderá querer desativar os testes de disponibilidade ou as regras de alerta associadas aos mesmos.

## <a name="if-you-see-failures"></a>Se vir falhas

Clique num ponto vermelho.

![Clicar num ponto vermelho](./media/monitor-web-app-availability/open-instance-3.png)

A partir de um resultado de teste de disponibilidade, pode ver os detalhes da transação em todos os componentes. Aqui pode:

* Inspecionar a resposta recebida do seu servidor.
* Diagnosticar falha com telemetria do lado do servidor correlacionada recolhida durante o processamento do teste de disponibilidade falhado.
* Faça loga num item de trabalho ou em Git ou Azure Boards para acompanhar o problema. O erro irá conter uma ligação para este evento.
* Abra o resultado do teste da Web no Visual Studio.

Saiba mais sobre o fim da [experiência](../../azure-monitor/app/transaction-diagnostics.md)de diagnóstico de transações aqui.

Clique na linha de exceção para ver os detalhes da exceção do lado do servidor que fez com que o teste de disponibilidade sintética falhasse. Também pode obter o [instantâneo de depuração](../../azure-monitor/app/snapshot-debugger.md) para diagnósticos de nível de código mais ricos.

![Diagnósticos do lado do servidor](./media/monitor-web-app-availability/open-instance-4.png)

Além dos resultados brutos, também pode ver duas métricas de disponibilidade chave no [Metrics Explorer:](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

1. Disponibilidade: percentagem dos testes que foram bem-sucedidos, em todas as execuções de testes.
2. Duração do Teste: duração média dos testes em todas as execuções de testes.

## <a name="automation"></a>Automatização

* [Utilize scripts do PowerShell para configurar um teste de disponibilidade](../../azure-monitor/app/powershell.md#add-an-availability-test) automaticamente.
* Configure um [webhook](../../azure-monitor/platform/alerts-webhooks.md) que será chamado sempre que for gerado um alerta.

## <a name="troubleshooting"></a>Resolução de problemas

Artigo dedicado à resolução de [problemas.](troubleshoot-availability.md)

## <a name="next-steps"></a>Passos seguintes

* [Alertas de Disponibilidade](availability-alerts.md)
* [Testes Web com vários passos](availability-multistep.md)


