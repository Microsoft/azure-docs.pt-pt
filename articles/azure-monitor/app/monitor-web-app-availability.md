---
title: Monitorizar a disponibilidade e a capacidade de resposta de qualquer web site - Azure Monitor
description: Configurar testes de ping em Application Insights. Receber alertas se um site ficar indisponível ou responder lentamente.
ms.topic: conceptual
ms.date: 04/15/2021
ms.reviewer: sdash
ms.openlocfilehash: ecfd4ffee3582ff37411e59c75d8be8fca5e945f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516627"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorizar a disponibilidade de qualquer site

O nome "teste de ping URL" é um pouco de um erro. Para ser claro, estes testes não estão a fazer qualquer uso do ICMP (Internet Control Message Protocol) para verificar a disponibilidade do seu site. Em vez disso, utilizam uma funcionalidade de pedido HTTP mais avançada para validar se um ponto final está a responder. Eles também medem o desempenho associado a essa resposta, e adiciona a capacidade de definir critérios de sucesso personalizados juntamente com funcionalidades mais avançadas como analisar pedidos dependentes, e permitir retrações.

Existem dois tipos de teste de ping de URL que pode criar, testes básicos e padrão de ping.

> [!NOTE]
> Os testes básicos e standard de ping estão atualmente em visualização pública. Estas versões de pré-visualização são fornecidas sem um acordo de nível de serviço. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.

Básico vs Standard:

- O básico é restrito a cinco locais por teste.
- Os testes padrão podem ter cabeçalhos personalizados ou solicitar o corpo.
- Os testes padrão podem utilizar qualquer método de pedido HTTP enquanto o básico só pode ser `GET` utilizado.
- A verificação de vida do certificado SSL alerta-o de um período de tempo definido antes do seu certificado expirar.
- Os testes padrão são uma característica paga.

> [!NOTE]
> Atualmente, não existem custos adicionais para os testes standard ping da funcionalidade de pré-visualização. Os preços das funcionalidades que estão em pré-visualização serão anunciados no futuro e um aviso fornecido antes do início da faturação. Caso opte por continuar a utilizar os testes Standard Ping após o período de pré-aviso, será cobrado à taxa aplicável.

## <a name="create-a-url-ping-test"></a>Criar um teste de ping do URL

Para criar um teste de disponibilidade, precisa de utilizar um recurso de Insight de Aplicação existente ou [criar um recurso Application Insights](create-new-resource.md).

Para criar o seu primeiro pedido de disponibilidade, abra o painel de disponibilidade e selecione Create Test & escolha o seu SKU de teste.

:::image type="content" source="./media/monitor-web-app-availability/create-basic-test.png" alt-text="Screenshot de criar um teste básico de url ping no Portal Azure":::

|Definição | Explicação |
|--------|-------------|
|**URL** |  O URL pode ser qualquer página Web que pretender testar, mas tem de estar visível a partir da Internet pública. O URL pode incluir uma cadeia de consulta. Desta forma, pode, por exemplo, testar um pouco a base de dados. Se o URL remeter para um redirecionamento, iremos segui-lo até dez redirecionamentos.|
|**Pedidos dependentes de parse**| O teste solicita imagens, scripts, ficheiros de estilo e outros ficheiros que fazem parte da página web em teste. O tempo de resposta gravado inclui o tempo necessário para obter estes ficheiros. O teste falha se algum destes recursos não puder ser descarregado com sucesso dentro do intervalo para todo o teste. Se a opção não estiver selecionada, o teste pede apenas o ficheiro no URL especificado. Ativar esta opção resulta numa verificação mais rigorosa. O teste pode falhar em casos, o que pode não ser percetível quando navega manualmente no site. |
|**Permitir recauchutagens**| Quando o teste falha, é novamente julgado após um curto intervalo. Uma falha só é comunicada após três tentativas falhadas sucessivas. Os testes subsequentes são realizados à frequência habitual de teste. A repetição encontra-se temporariamente suspensa até ao próximo êxito. Esta regra é aplicada de forma independente em cada localização de teste. **Recomendamos esta opção.** Em média, cerca de 80% das falhas desaparecem aquando da repetição.|
| **Teste de validação de certificado SSL** | Pode verificar o certificado SSL no seu website para se certificar de que está corretamente instalado, válido, confiável e não dá quaisquer erros a nenhum dos seus utilizadores. |
| **Verificação proativa da vida útil** | Isto permite-lhe definir um período de tempo definido antes do seu certificado SSL expirar. Uma vez expirado, o teu teste falhará. |
|**Frequência de teste**| Define a frequência com que o teste é executado a partir de cada local de teste. Com uma frequência predefinida de cinco minutos e cinco localizações de teste, o site é testado, em média, a cada minuto.|
|**Locais de teste**| São os locais de onde os nossos servidores enviam pedidos web para o seu URL. **O nosso número mínimo de locais de teste recomendados é de cinco** para garantir que pode distinguir problemas no seu website de problemas de rede. Pode selecionar mais de cinco locais com teste padrão e até 16 locais.|

**Se o seu URL não for visível a partir da internet pública, pode optar por abrir seletivamente a sua firewall para permitir apenas as transações de teste através** de . Para saber mais sobre as exceções à firewall para os nossos agentes de teste de disponibilidade, consulte o [guia de endereços IP](./ip-addresses.md#availability-tests).

> [!NOTE]
> Recomendamos vivamente testes de vários locais com **um mínimo de cinco locais.** Isto é para evitar falsos alarmes que possam resultar de problemas transitórios com uma localização específica. Além disso, descobrimos que a configuração ideal é ter o **número de locais de teste igual ao limiar de localização de alerta + 2**.

## <a name="standard-test"></a>Teste Padrão

:::image type="content" source="./media/monitor-web-app-availability/standard-test-post.png" alt-text="Screenshot do separador de informações padrão de teste." border="false":::

|Definição | Explicação |
|--------|-------------|
| **Cabeçalhos personalizados** | Os pares de valor chave que definem os parâmetros operacionais. |
| **HTTP solicitar verbo** | Indique que medidas gostaria de tomar com o seu pedido. Se o verbo escolhido não estiver disponível na UI, pode implementar um teste padrão utilizando o Azure Resource Monitor com a escolha pretendida. |
| **Corpo de pedido** | Dados personalizados associados ao seu pedido HTTP. Pode fazer upload de ficheiros próprios de tipo próprio no seu conteúdo ou desativar esta funcionalidade. Para conteúdo sonoro suportamos TEXTO, JSON, HTML, XML e JavaScript. |

## <a name="success-criteria"></a>Critérios de sucesso

|Definição| Explicação
|----|----|----|
| **Tempo limite de teste** |Diminua este valor para ser alertado sobre respostas lentas. O teste será contabilizado como uma falha se as respostas do seu site não foram recebidas durante este período. Se tiver selecionado **Pedidos dependentes de análise**, todas as imagens, ficheiros de estilos, scripts e outros recursos dependentes terão de ser recebidos durante este período.|
| **Resposta HTTP** | O código de estado devolvido que é contado como um sucesso. 200 é o código que indica que foi devolvida uma página Web normal.|
| **Correspondência de conteúdo** | Uma corda, como "Bem-vindo!" Podemos testar que uma correspondência sensíveis às maiúsculas e minúsculas ocorre em cada resposta. Tem de ser uma cadeia simples, sem carateres universais. Não se esqueça de que se alterar o conteúdo da página, poderá ter de a atualizar. **Apenas os caracteres ingleses são suportados com correspondência de conteúdo** |

## <a name="alerts"></a>Alertas

|Definição| Explicação
|----|----|----|
|**Quase em tempo real (Pré-visualização)** | Recomendamos a utilização de alertas de quase-tempo real. Configurar este tipo de alerta é feito após a criação do teste de disponibilidade.  |
|**Limiar de localização de alerta**|Recomendamos um mínimo de 3/5 locais. A relação ideal entre o limiar de localização de alerta e o número de locais de teste é o número de **localização** de alerta  =  **dos locais de teste - 2, com um mínimo de cinco locais de ensaio.**|

## <a name="location-population-tags"></a>Etiquetas populacionais de localização

As seguintes etiquetas populacionais podem ser usadas para o atributo geolocalização ao implementar um teste de ping URL de disponibilidade usando O Gestor de Recursos Azure.

#### <a name="azure-gov"></a>Azure gov

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

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="A screenshot mostra a página Disponibilidade com o botão Refresh realçado.":::

A vista de dispersão mostra amostras dos resultados dos testes que têm detalhes de teste de diagnóstico neles. O motor de testes armazena os detalhes de diagnóstico dos testes que têm falhas. Relativamente aos testes bem-sucedidos, são armazenados os detalhes de diagnóstico de um subconjunto das execuções. Passe sobre qualquer um dos pontos verdes/vermelhos para ver o teste, o nome do teste e a localização.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Vista de linha." border="false":::

Selecione um teste ou uma localização específica ou reduza o período de tempo para ver mais resultados relativos ao período de tempo relevante. Utilize o Explorador de Pesquisa para ver resultados de todas as execuções ou utilize consultas do Analytics para executar relatórios personalizados nestes dados.

## <a name="inspect-and-edit-tests"></a> Inspecionar e editar testes

Para editar, desativar temporariamente ou apagar um teste clique nas elipses ao lado de um nome de teste. Pode levar até 20 minutos para que as alterações de configuração se propaguem a todos os agentes de teste após a alteração.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Ver detalhes do teste. Editar e desativar um teste web." border="false":::

Quando realizar tarefas de manutenção ao seu serviço, poderá querer desativar os testes de disponibilidade ou as regras de alerta associadas aos mesmos.

## <a name="if-you-see-failures"></a>Se vir falhas

Selecione um ponto vermelho.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Screenshot do separador de detalhes de transações de ponta a ponta." border="false":::

A partir de um resultado de teste de disponibilidade, você pode ver os detalhes da transação em todos os componentes. Aqui pode:

* Reveja o relatório de resolução de problemas para determinar o que pode ter causado a falha do seu teste, mas a sua aplicação ainda está disponível.
* Inspecionar a resposta recebida do seu servidor.
* Diagnosticar falha com telemetria correlacionada do lado do servidor recolhida durante o processamento do teste de disponibilidade falhado.
* Faça um sessão de problemas ou artigos de trabalho em Git ou Azure Boards para rastrear o problema. O erro irá conter uma ligação para este evento.
* Abra o resultado do teste da Web no Visual Studio.

Para saber mais sobre o fim da experiência de diagnóstico de transação, visite a documentação de [diagnóstico de transação.](./transaction-diagnostics.md)

Clique na linha de exceção para ver os detalhes da exceção do lado do servidor que fez com que o teste de disponibilidade sintética falhasse. Também pode obter o [instantâneo de depurg](./snapshot-debugger.md) para diagnósticos de código mais ricos.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Diagnósticos do lado do servidor.":::

Além dos resultados brutos, pode também ver duas métricas chave de disponibilidade no [Metrics Explorer:](../essentials/metrics-getting-started.md)

1. Disponibilidade: percentagem dos testes que foram bem-sucedidos, em todas as execuções de testes.
2. Duração do Teste: duração média dos testes em todas as execuções de testes.

## <a name="automation"></a>Automatização

* [Utilize scripts do PowerShell para configurar um teste de disponibilidade](./powershell.md#add-an-availability-test) automaticamente.
* Configure um [webhook](../alerts/alerts-webhooks.md) que será chamado sempre que for gerado um alerta.


## <a name="next-steps"></a>Passos seguintes

* [Alertas de Disponibilidade](availability-alerts.md)
* [Testes web em várias etapas](availability-multistep.md)
* [Resolução de problemas](troubleshoot-availability.md)
