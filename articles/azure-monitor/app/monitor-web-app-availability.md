---
title: Monitorizar a disponibilidade e a capacidade de resposta de qualquer site | Microsoft Docs
description: Configurar testes Web no Application Insights. Receber alertas se um site ficar indisponível ou responder lentamente.
ms.topic: conceptual
ms.date: 03/10/2021
ms.reviewer: sdash
ms.openlocfilehash: d7c610e374dcb7b97850d815ba8bb927cdebacfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012569"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorize a disponibilidade de qualquer website

Depois de ter implementado a sua aplicação web/website, pode configurar testes recorrentes para monitorizar a disponibilidade e capacidade de resposta. O [Azure Application Insights](./app-insights-overview.md) envia regularmente pedidos Web para a sua aplicação a partir de pontos em todo o mundo. Pode alertá-lo se a sua aplicação não estiver a responder, ou se responder muito lentamente.

Pode configurar testes de disponibilidade para qualquer ponto final HTTP ou HTTPS que seja acessível a partir da Internet pública. Não tens de fazer alterações no website que estás a testar. Na verdade, nem sequer tem de ser um site que tens. Pode testar a disponibilidade de uma API REST de que o seu serviço depende.

### <a name="types-of-availability-tests"></a>Tipos de testes de disponibilidade:

Existem três tipos de testes de disponibilidade:

* [Teste de ping do URL](#create-a-url-ping-test): um teste simples que pode criar no Portal do Azure.
* [Teste web em várias etapas](availability-multistep.md): Uma gravação de uma sequência de pedidos web, que pode ser reproduzida para testar cenários mais complexos. Os testes web em várias etapas são criados na Visual Studio Enterprise e enviados para o portal para execução.
* [Testes de disponibilidade de faixas personalizadas](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability): Se decidir criar uma aplicação personalizada para realizar testes de disponibilidade, o `TrackAvailability()` método pode ser usado para enviar os resultados para o Application Insights.

**Pode criar até 100 testes de disponibilidade por recurso Application Insights.**

> [!IMPORTANT]
> Ambos, [teste de URL ping](#create-a-url-ping-test) e teste web em [várias etapas](availability-multistep.md) dependem da infraestrutura pública de DNS da internet para resolver os nomes de domínio dos pontos finais testados. Isto significa que, se estiver a utilizar o DNS Privado, deve certificar-se de que todos os nomes de domínio do seu teste também são resolúveis pelos servidores de nomes de domínio público ou, quando não é possível, pode utilizar [testes de disponibilidade de faixas personalizadas.](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Para criar um teste de disponibilidade, primeiro precisa de criar um recurso Application Insights. Se já criou um recurso, dirija-se à secção seguinte para [criar um teste de URL Ping](#create-a-url-ping-test).

A partir do portal Azure, **selecione Criar um recurso** Developer  >  **Tools**  >  **Application Insights** e criar [um recurso Application Insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Criar um teste de ping do URL

O nome "teste de ping URL" é um pouco de um erro. Para ser claro, este teste não está a fazer qualquer uso do ICMP (Internet Control Message Protocol) para verificar a disponibilidade do seu site. Em vez disso, utiliza uma funcionalidade de pedido HTTP mais avançada para validar se um ponto final está a responder. Também mede o desempenho associado a essa resposta, e adiciona a capacidade de definir critérios de sucesso personalizados juntamente com funcionalidades mais avançadas como analisar pedidos dependentes, e permitir retréis.

Para criar o seu primeiro pedido de disponibilidade, abra o painel de disponibilidade e selecione **Create Test**.

![Indique, pelo menos, o URL do seu site](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Criar um teste

|Definição| Explicação
|----|----|----|
|**URL** |  O URL pode ser qualquer página Web que pretender testar, mas tem de estar visível a partir da Internet pública. O URL pode incluir uma cadeia de consulta. Desta forma, pode, por exemplo, testar um pouco a base de dados. Se o URL remeter para um redirecionamento, iremos segui-lo até dez redirecionamentos.|
|**Pedidos dependentes de parse**| O teste solicita imagens, scripts, ficheiros de estilo e outros ficheiros que fazem parte da página web em teste. O tempo de resposta gravado inclui o tempo necessário para obter estes ficheiros. O teste falha se algum destes recursos não puder ser descarregado com sucesso dentro do intervalo para todo o teste. Se a opção não estiver selecionada, o teste pede apenas o ficheiro no URL especificado. Ativar esta opção resulta numa verificação mais rigorosa. O teste pode falhar em casos, o que pode não ser percetível quando navega manualmente no site.
|**Permitir recauchutagens**|quando o teste falha, é novamente julgado após um curto intervalo. Uma falha só é comunicada após três tentativas falhadas sucessivas. Os testes subsequentes são realizados à frequência habitual de teste. A repetição encontra-se temporariamente suspensa até ao próximo êxito. Esta regra é aplicada de forma independente em cada localização de teste. **Recomendamos esta opção.** Em média, cerca de 80% das falhas desaparecem aquando da repetição.|
|**Frequência de teste**| Define a frequência com que o teste é executado a partir de cada local de teste. Com uma frequência predefinida de cinco minutos e cinco localizações de teste, o site é testado, em média, a cada minuto.|
|**Locais de teste**| São os locais de onde os nossos servidores enviam pedidos web para o seu URL. **O nosso número mínimo de locais de teste recomendados é de cinco** para garantir que pode distinguir problemas no seu website de problemas de rede. Pode selecionar até 16 localizações.

**Se o seu URL não for visível a partir da internet pública, pode optar por abrir seletivamente a sua firewall para permitir apenas as transações de teste através** de . Para saber mais sobre as exceções à firewall para os nossos agentes de teste de disponibilidade, consulte o [guia de endereços IP](./ip-addresses.md#availability-tests).

> [!NOTE]
> Recomendamos vivamente testes de vários locais com **um mínimo de cinco locais.** Isto é para evitar falsos alarmes que possam resultar de problemas transitórios com uma localização específica. Além disso, descobrimos que a configuração ideal é ter o **número de locais de teste igual ao limiar de localização de alerta + 2**.

### <a name="success-criteria"></a>Critérios de sucesso

|Definição| Explicação
|----|----|----|
| **Tempo limite de teste** |Diminua este valor para ser alertado sobre respostas lentas. O teste será contabilizado como uma falha se as respostas do seu site não foram recebidas durante este período. Se tiver selecionado **Pedidos dependentes de análise**, todas as imagens, ficheiros de estilos, scripts e outros recursos dependentes terão de ser recebidos durante este período.|
| **Resposta HTTP** | O código de estado devolvido que é contado como um sucesso. 200 é o código que indica que foi devolvida uma página Web normal.|
| **Correspondência de conteúdo** | Uma corda, como "Bem-vindo!" Podemos testar que uma correspondência sensíveis às maiúsculas e minúsculas ocorre em cada resposta. Tem de ser uma cadeia simples, sem carateres universais. Não se esqueça de que se alterar o conteúdo da página, poderá ter de a atualizar. **Apenas os caracteres ingleses são suportados com correspondência de conteúdo** |

### <a name="alerts"></a>Alertas

|Definição| Explicação
|----|----|----|
|**Quase em tempo real (Pré-visualização)** | Recomendamos a utilização de alertas de quase-tempo real. Configurar este tipo de alerta é feito após a criação do teste de disponibilidade.  |
|**Limiar de localização de alerta**|Recomendamos um mínimo de 3/5 locais. A relação ideal entre o limiar de localização de alerta e o número de locais de teste é o número de **localização** de alerta  =  **dos locais de teste - 2, com um mínimo de cinco locais de ensaio.**|

### <a name="location-population-tags"></a>Etiquetas populacionais de localização

As seguintes etiquetas populacionais podem ser usadas para o atributo geolocalização ao implementar um teste de ping URL de disponibilidade usando O Gestor de Recursos Azure.

#### <a name="azure-gov"></a>Azure Gov

| Nome a Apresentar   | Nome da População     |
|----------------|---------------------|
| USGov Virginia | usgov-va-azr        |
| USGov Arizona  | usgov-phx-azr       |
| USGov Texas    | usgov-tx-azr        |
| USDod Leste     | usgov-ddeast-azr    |
| USDoD Central  | usgov-ddcentral-azr |

#### <a name="azure"></a>Azure

| Nome a Apresentar                           | Nome da População   |
|----------------------------------------|-------------------|
| Austrália Leste                         | emea-au-syd-edge  |
| Sul do Brasil                           | latam-br-gru-edge |
| E.U.A. Central                             | us-fl-mia-edge    |
| Ásia Leste                              | apac-hk-hkn-azr   |
| E.U.A. Leste                                | us-va-ash-azr     |
| França Sul (antiga França Central) | emea-ch-zrh-edge  |
| França Central                         | emea-fr-pra-edge  |
| Leste do Japão                             | apac-jp-kaw-edge  |
| Europa do Norte                           | emea-gb-db3-azr   |
| E.U.A. Centro-Norte                       | us-il-ch1-azr     |
| E.U.A. Centro-Sul                       | us-tx-sn1-azr     |
| Sudeste Asiático                         | apac-sg-sin-azr   |
| Oeste do Reino Unido                                | emea-se-sto-edge  |
| Europa Ocidental                            | emea-nl-ams-azr   |
| E.U.A. Oeste                                | us-ca-sjc-azr     |
| Sul do Reino Unido                               | emea-ru-msa-edge  |

## <a name="see-your-availability-test-results"></a>Ver os resultados do teste de disponibilidade

Os resultados dos testes de disponibilidade podem ser visualizados com vistas de enredo de linha e dispersão.

Após alguns minutos, clique em **Refresh** para ver os resultados dos seus testes.

![A screenshot mostra a página Disponibilidade com o botão Refresh realçado.](./media/monitor-web-app-availability/availability-refresh-002.png)

A vista de dispersão mostra amostras dos resultados dos testes que têm detalhes de teste de diagnóstico neles. O motor de testes armazena os detalhes de diagnóstico dos testes que têm falhas. Relativamente aos testes bem-sucedidos, são armazenados os detalhes de diagnóstico de um subconjunto das execuções. Passe sobre qualquer um dos pontos verdes/vermelhos para ver o teste, o nome do teste e a localização.

![Vista de linha](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Selecione um teste ou uma localização específica ou reduza o período de tempo para ver mais resultados relativos ao período de tempo relevante. Utilize o Explorador de Pesquisa para ver resultados de todas as execuções ou utilize consultas do Analytics para executar relatórios personalizados nestes dados.

## <a name="inspect-and-edit-tests"></a> Inspecionar e editar testes

Para editar, desativar temporariamente ou apagar um teste clique nas elipses ao lado de um nome de teste. Pode levar até 20 minutos para que as alterações de configuração se propaguem a todos os agentes de teste após a alteração.

![Ver detalhes do teste. Editar e desativar um teste web](./media/monitor-web-app-availability/edit.png)

Quando realizar tarefas de manutenção ao seu serviço, poderá querer desativar os testes de disponibilidade ou as regras de alerta associadas aos mesmos.

## <a name="if-you-see-failures"></a>Se vir falhas

Clique num ponto vermelho.

![Clicar num ponto vermelho](./media/monitor-web-app-availability/open-instance-3.png)

A partir de um resultado de teste de disponibilidade, você pode ver os detalhes da transação em todos os componentes. Aqui pode:

* Inspecionar a resposta recebida do seu servidor.
* Diagnosticar falha com telemetria correlacionada do lado do servidor recolhida durante o processamento do teste de disponibilidade falhado.
* Faça um sessão de problemas ou artigos de trabalho em Git ou Azure Boards para rastrear o problema. O erro irá conter uma ligação para este evento.
* Abra o resultado do teste da Web no Visual Studio.

Saiba mais sobre a [experiência](./transaction-diagnostics.md)de diagnóstico de transações final aqui.

Clique na linha de exceção para ver os detalhes da exceção do lado do servidor que fez com que o teste de disponibilidade sintética falhasse. Também pode obter o [instantâneo de depurg](./snapshot-debugger.md) para diagnósticos de código mais ricos.

![Diagnósticos do lado do servidor](./media/monitor-web-app-availability/open-instance-4.png)

Além dos resultados brutos, pode também ver duas métricas chave de disponibilidade no [Metrics Explorer:](../essentials/metrics-getting-started.md)

1. Disponibilidade: percentagem dos testes que foram bem-sucedidos, em todas as execuções de testes.
2. Duração do Teste: duração média dos testes em todas as execuções de testes.

## <a name="automation"></a>Automatização

* [Utilize scripts do PowerShell para configurar um teste de disponibilidade](./powershell.md#add-an-availability-test) automaticamente.
* Configure um [webhook](../alerts/alerts-webhooks.md) que será chamado sempre que for gerado um alerta.

## <a name="troubleshooting"></a>Resolução de problemas

Artigo dedicado [à resolução de problemas.](troubleshoot-availability.md)

## <a name="next-steps"></a>Passos seguintes

* [Alertas de Disponibilidade](availability-alerts.md)
* [Testes Web com vários passos](availability-multistep.md)

