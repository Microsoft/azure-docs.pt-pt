---
title: Monitorizar a disponibilidade e a capacidade de resposta de qualquer site | Microsoft Docs
description: Configurar testes Web no Application Insights. Receber alertas se um site ficar indisponível ou responder lentamente.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/22/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: b5bca3b4c9f5fbe4c9d7dd917b4e2f0767abf145
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341022"
---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Monitorizar a disponibilidade e a capacidade de resposta de qualquer site
Depois de implementar a aplicação Web ou o Web site em qualquer servidor, pode configurar testes para monitorizar a respetiva disponibilidade e capacidade de resposta. O [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) envia regularmente pedidos Web para a sua aplicação a partir de pontos em todo o mundo. Este ferramenta alerta-o se a aplicação não responder ou responder lentamente.

Pode configurar testes de disponibilidade para qualquer ponto final HTTP ou HTTPS que seja acessível a partir da Internet pública. Não tem de adicionar nada ao Web site que está a testar. Nem sequer tem de estar no seu site. Pode testar um serviço de API REST do qual dependa.

Existem dois tipos de testes de disponibilidade:

* [Teste de ping do URL](#create): um teste simples que pode criar no Portal do Azure.
* [Teste Web de vários passos](#multi-step-web-tests): pode criá-lo no Visual Studio Enterprise e carregá-lo no Portal.

Pode criar até 100 testes de disponibilidade por recurso de aplicação.


## <a name="create"></a>Abra um recurso para os relatórios de teste de disponibilidade

**Se já tiver configurado o Application Insights configurado** para a sua aplicação Web, abra o recurso do Application Insights no [portal do Azure](https://portal.azure.com).

**Ou, se quiser ver os relatórios num novo recurso,** aceda ao [portal do Azure](https://portal.azure.com) e crie um recurso do Application Insights.

![Criar um recurso > Ferramentas de programação > Application Insights](./media/monitor-web-app-availability/1create-resource-appinsights.png)

Clique em **Todos os recursos** para abrir o painel Descrição geral do novo recurso.

## <a name="setup"></a>Criar um teste de ping do URL
Abra o painel Disponibilidade e adicione um teste.

![Indique, pelo menos, o URL do seu site](./media/monitor-web-app-availability/2addtest-url.png)

* **O URL** pode ser qualquer página Web que pretender testar, mas tem de estar visível a partir da Internet pública. O URL pode incluir uma cadeia de consulta. Desta forma, pode, por exemplo, testar um pouco a base de dados. Se o URL remeter para um redirecionamento, iremos segui-lo até dez redirecionamentos.
* **Analisar pedidos dependentes**: Se esta opção estiver marcada, o teste pede imagens, scripts, ficheiros de estilo e outros ficheiros que fazem parte da página da web em teste. O tempo de resposta gravado inclui o tempo necessário para obter estes ficheiros. O teste falha se todos estes recursos não puderem ser transferidos com êxito no tempo limite para a realização do teste completo. Se a opção não estiver selecionada, o teste pede apenas o ficheiro no URL especificado.

* **Permitir repetição de tentativas**:  Se esta opção estiver marcada, quando o teste falhar, será tentada novamente após um curto intervalo. Uma falha só é comunicada após três tentativas falhadas sucessivas. Os testes subsequentes são realizados à frequência habitual de teste. A repetição encontra-se temporariamente suspensa até ao próximo êxito. Esta regra é aplicada de forma independente em cada localização de teste. Recomendamos esta opção. Em média, cerca de 80% das falhas desaparecem aquando da repetição.

* **Frequência de teste**: Define a frequência com que a execução do teste em cada localização de teste. Com uma frequência predefinida de cinco minutos e cinco localizações de teste, o site é testado, em média, a cada minuto.

* As **Localizações de teste** são os locais a partir de onde os nossos servidores enviam pedidos Web ao seu URL. Nosso número mínimo de localizações de teste recomendado é cinco e para assim garantir que pode distinguir problemas no seu Web site dos problemas de rede. Pode selecionar até 16 localizações.

> [!NOTE]
> * Recomendamos vivamente que teste a partir de vários locais com um mínimo de cinco locais. Este procedimento impede que os alarmes falsos que pode ser resultado de problemas transitórios com uma localização específica. Além disso Descobrimos que a configuração ideal é fazer com que o número de localizações de teste de ser igual ao limiar de alerta de localização + 2. 
> * Permitir que os resultados de opção "Analisar pedidos dependentes" numa verificação mais estritas. O teste poderá falhar por casos que não podem ser perceptíveis ao navegar manualmente o site.

* **Critérios de êxito**:

    **Tempo limite de teste**: Reduza este valor para ser alertado de repostas lentas. O teste será contabilizado como uma falha se as respostas do seu site não foram recebidas durante este período. Se tiver selecionado **Pedidos dependentes de análise**, todas as imagens, ficheiros de estilos, scripts e outros recursos dependentes terão de ser recebidos durante este período.

    **Resposta HTTP**: O código de estado devolvido é contabilizado como um sucesso. 200 é o código que indica que foi devolvida uma página Web normal.

    **Correspondência do conteúdo**: uma cadeia, como “Bem-vindo!” Podemos testar que uma correspondência sensíveis às maiúsculas e minúsculas ocorre em cada resposta. Tem de ser uma cadeia simples, sem carateres universais. Não se esqueça de que se alterar o conteúdo da página, poderá ter de a atualizar.

* **Limiar de alerta de localização**: Recomendamos um mínimo de 3/5 localizações. A relação ideal entre o limiar de alerta de localização e o número de localizações de teste é **limiar de localização de alerta** = **número de localizações de teste** - 2, com um mínimo de cinco testar localizações.

## <a name="multi-step-web-tests"></a>Testes Web com vários passos
Pode monitorizar um cenário que envolva uma sequência de URLs. Por exemplo, se estiver a monitorizar um site de vendas, pode testar se a adição de artigos no carrinho de compras funciona corretamente.

> [!NOTE]
> Os testes Web de vários passos estão sujeitos a um custo. [Esquema do escalão de preço](https://azure.microsoft.com/pricing/details/application-insights/).
> 

Para criar um teste com vários passos, registe o cenário com o Visual Studio Enterprise e, em seguida, carregue o registo no Application Insights. O Application Insights reproduz o cenário de tempos a tempos e verifica as respostas.

> [!NOTE]
> * Não pode utilizar funções codificadas ou ciclos no seus testes. O teste tem de estar contido completamente no script .webtest. No entanto, pode utilizar plug-ins standard.
> * Os testes web com vários passos são suportados apenas com carateres em inglês. Se utilizar o Visual Studio noutras linguagens, atualize o ficheiro de definição de teste web para traduzir/excluir carateres não ingleses.
>

#### <a name="1-record-a-scenario"></a>1. Registar um cenário
Utilize o Visual Studio Enterprise para guardar uma sessão Web.

1. Crie um projeto de teste de desempenho da Web.

    ![Na edição Visual Studio Enterprise, crie um projeto a partir do modelo Desempenho da Web e Carregar Teste.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

 * *Não vê o modelo de Desempenho da Web e de Teste de Carga?* - Feche o Visual Studio Enterprise. Abra o **Instalador do Visual Studio** para modificar a instalação do Visual Studio Enterprise. Em **Componentes Individuais**, selecione **ferramentas de Desempenho da Web e de Teste de Carga**.

2. Abra o ficheiro .webtest e comece a gravar.

    ![Abrir o ficheiro .webtest e clicar em Gravar.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Execute as ações de utilizador que pretende simular no teste: abrir o site, adicionar um produto ao carrinho e assim sucessivamente. Em seguida, pare o teste.

    ![O gravador de teste Web é executado no Internet Explorer.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Não crie um cenário longo. Existe um limite de 100 passos e de 2 minutos.
4. Edite o teste para:

   * Adicionar validações para verificar o texto recebido e os códigos de reposta.
   * Remova todas as interações supérfluas. Também pode remover pedidos dependentes de imagens ou adicionar ou controlar sites.

     Lembre-se de que apenas pode editar o script de teste, não pode adicionar código personalizado ou chamar outros testes Web. Não insira ciclos no teste. Pode utilizar os plug-ins do teste Web padrão.
5. Execute o teste no Visual Studio para verificar se funciona.

    A execução de testes Web abre um browser e repete as ações que gravou. Verifique se funciona como esperado.

    ![No Visual Studio, abra o ficheiro .webtest e clique em Executar.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. Carregar o teste Web no Application Insights
1. No portal do Application Insights sobre a disponibilidade painel, clique em Adicionar teste.

    ![No painel de disponibilidade, escolha Adicionar teste.](./media/monitor-web-app-availability/3addtest-web.png)
2. Selecione o teste com vários passos e carregue o ficheiro .webtest.

    Defina as localizações do teste, a frequência e os parâmetros de alerta da mesma forma como para os testes de ping.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Ligar a hora e números aleatórios ao teste com vários passos
Suponhamos que está a testar uma ferramenta que obtém dados dependentes da hora, tais como stocks de um feed externo. Ao gravar o teste Web, tem de utilizar horas específicas, embora sejam definidas como parâmetros do teste, StartTime e EndTime.

![Um teste Web com parâmetros.](./media/monitor-web-app-availability/appinsights-72webtest-parameters.png)

Quando executa o teste, gostaria que a EndTime fosse sempre a hora atual e a StartTime a hora de há 15 minutos.

Os Plug-ins de Teste Web permitem-lhe parametrizar tempos.

1. Adicione um plug-in de teste Web para cada valor variável de parâmetro que pretende. Na barra de ferramentas do teste Web, selecione **Adicionar Plug-in de Teste Web**.

    ![Escolher Adicionar Plug-in de Teste Web e selecionar um tipo.](./media/monitor-web-app-availability/appinsights-72webtest-plugins.png)

    Neste exemplo, utilizamos duas instâncias do Plug-in Data/Hora. Uma instância para “há 15 minutos” e outra para “agora”.
2. Abra as propriedades de cada plug-in. Atribua um nome e defina-o para utilizar a hora atual. Para uma das propriedades, defina Adicionar Minutos = -15.

    ![Definir um nome, Utilizar Hora Atual e Adicionar Minutos.](./media/monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. Nos parâmetros do teste Web, utilize {{nome do plug-in}} para fazer referência a um nome de plug-in.

    ![No parâmetro de teste, utilize {{nome do plug-in}}.](./media/monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Agora, carregue o teste no portal. Os valores dinâmicos são utilizados sempre que o teste for executado.


## <a name="monitor"></a>Ver os resultados do teste de disponibilidade

A guia visão geral mostra a taxa de êxito dos testes, enquanto o separador de detalhes mostra o gráfico de dispersão e de grade de detalhes específicos.

Após alguns minutos, clique em **Atualizar** para ver os resultados do teste.

![Gráfico de dispersão no painel de detalhes](./media/monitor-web-app-availability/4refresh.png)

O gráfico de dispersão mostra exemplos dos resultados do teste que contêm detalhes de passos de teste de diagnóstico. O motor de testes armazena os detalhes de diagnóstico dos testes que têm falhas. Relativamente aos testes bem-sucedidos, são armazenados os detalhes de diagnóstico de um subconjunto das execuções. Passe o cursor sobre um dos pontos verdes/vermelhos para ver o carimbo de data/hora, a duração, a localização e o nome do teste. Clique em qualquer ponto do gráfico de dispersão para ver os detalhes do resultado do teste.  

Selecione um teste ou uma localização específica ou reduza o período de tempo para ver mais resultados relativos ao período de tempo relevante. Utilize o Explorador de Pesquisa para ver resultados de todas as execuções ou utilize consultas do Analytics para executar relatórios personalizados nestes dados.

Para além de resultados em bruto, existem duas métricas de Disponibilidade no Explorador de Métricas: 

1. Disponibilidade: Percentagem dos testes que foram bem-sucedidos, em todas as execuções de teste. 
2. Duração do teste: Duração média do teste em todas as execuções de teste.

Pode aplicar filtros no nome e na localização dos testes para analisar tendências de testes e/ou localizações específicas.

## <a name="edit"></a> Inspecionar e editar testes

Na guia detalhes, num teste específico, selecione as reticências na extremidade direita para editar, temporariamente desativar, eliminar ou transferir teste web.

Selecione **ver detalhes do teste** de um teste específico para ver o gráfico de dispersão e detalhes de localização de teste específico.

![Ver detalhes do teste, editar e desativar um teste web](./media/monitor-web-app-availability/5viewdetails.png)

Quando realizar tarefas de manutenção ao seu serviço, poderá querer desativar os testes de disponibilidade ou as regras de alerta associadas aos mesmos.

![Desativar um teste web](./media/monitor-web-app-availability/6disable.png)
![editar teste](./media/monitor-web-app-availability/8edittest.png)

## <a name="failures"></a>Se vir falhas
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

## <a name="alerts"></a> Alertas de disponibilidade
Pode ter os seguintes tipos de regras de alerta em dados de disponibilidade com a experiência de alertas clássicos:
1. X fora de localizações de Y reportando falhas num período de tempo
2. Percentagem de agregação de disponibilidade cai abaixo do limiar de um
3. Aumentos de duração média de teste para além de um limiar

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Alerta sobre X fora de geração de relatórios de falhas de locais de Y
O X fora de localizações de Y regra de alerta está ativada por predefinição no [experiência de novos alertas unificadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), ao criar um novo teste de disponibilidade. Pode optar por, selecionando a opção "clássica" ou optar por desabilitar a regra de alerta.

![Criar experiência](./media/monitor-web-app-availability/appinsights-71webtestUpload.png)

> [!NOTE]
>  Com o [novos alertas unificadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), as preferências de gravidade e a notificação de regra de alerta com [grupos de ação](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **tem de ser** configurado na experiência de alertas. Sem os seguintes passos, apenas receberá notificações no portal.

1. Depois de guardar o teste de disponibilidade, nos detalhes de separador clique nas reticências pelo teste que acabou de criar. Clique em "Editar alerta".
![Editar depois de guardar](./media/monitor-web-app-availability/9editalert.png)

2. Defina o nível de gravidade pretendidos, a descrição da regra e mais importante, o grupo de ação com as preferências de notificação que pretende utilizar para esta regra de alerta.
![Editar depois de guardar](./media/monitor-web-app-availability/10editalert.png)


> [!NOTE]
> * Configure os grupos de ação para receber notificações quando o alerta for acionado, seguindo os passos acima. Sem essa etapa, receberá apenas notificações no portal quando a regra for acionada.
>

### <a name="alert-on-availability-metrics"></a>Alerta relativamente a métricas de disponibilidade
Utilizar o [novos alertas unificadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), pode alertar sobre a disponibilidade de agregação segmentada e testar também as métricas de duração:

1. Selecione um recurso do Application Insights a experiência de métricas e selecione uma métrica de disponibilidade:  ![Seleção de métricas de disponibilidade](./media/monitor-web-app-availability/selectmetric.png)

2. Configure alertas de opção no menu leva-o para a nova experiência onde pode selecionar localizações para configurar a regra de alerta sobre ou de testes específicos. Também pode configurar os grupos de ação para esta regra de alerta aqui.
    ![Configuração de alertas de disponibilidade](./media/monitor-web-app-availability/availabilitymetricalert.png)

### <a name="alert-on-custom-analytics-queries"></a>Alerta sobre consultas de análise personalizada
Utilizar o [novos alertas unificadas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), pode alertar relativamente [consultas de registo personalizado](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Com consultas personalizadas, pode alertar relativamente a quaisquer condições de arbitrária que o ajuda a obter o sinal de mais confiável de problemas de disponibilidade. Isso também é particularmente aplicável, se estiverem a enviar os resultados de disponibilidade personalizado com o SDK de TrackAvailability. 

> [!Tip]
> * As métricas em dados de disponibilidade incluem quaisquer resultados de disponibilidade personalizado que pode enviar ao chamar o nosso SDK TrackAvailability. Pode utilizar alertas em métricas de suporte para o alerta, nos resultados de disponibilidade personalizado.
>

## <a name="dealing-with-sign-in"></a>Lidar com início de sessão
Se os seus utilizadores iniciarem sessão na sua aplicação, terá várias opções para simular o início de sessão, de modo a poder testar páginas depois do início de sessão. A abordagem que utiliza depende do tipo de segurança fornecida pela aplicação.

Em todos os casos, deve criar uma conta na sua aplicação apenas para efeitos de teste. Se possível, restrinja as permissões da conta neste teste, para que não haja nenhuma possibilidade de os testes Web afetarem os utilizadores reais.

### <a name="simple-username-and-password"></a>Nome de utilizador e palavra-passe simples
Grave um teste Web como habitualmente. Elimine primeiro os cookies.

### <a name="saml-authentication"></a>Autenticação SAML
Utilize o plug-in SAML disponível para testes Web.

### <a name="client-secret"></a>Segredo do cliente
Se a sua aplicação tiver uma rota de início de sessão que envolva um segredo do cliente, utilize-a. O Azure Active Directory (AAD) é um exemplo de um serviço que fornece um início de sessão com segredo do cliente. No AAD, o segredo do cliente é a Chave da Aplicação.

Eis um exemplo de teste Web de uma aplicação Web do Azure com uma chave de aplicação:

![Exemplo de segredo do cliente](./media/monitor-web-app-availability/110.png)

1. Obtenha o token do AAD utilizando o segredo do cliente (AppKey).
2. Extraia o token de portador a partir da resposta.
3. Chame a API com o token de portador no cabeçalho de autorização.

Certifique-se de que o teste Web é um cliente real: ou seja, tem a sua própria aplicação no AAD - e utilize o clientId + appkey. O serviço em teste também tem sua própria aplicação no AAD: o appID URI desta aplicação é refletido no teste web no campo de recursos.

### <a name="open-authentication"></a>Autenticação Aberta
Um exemplo de Autenticação Aberta é iniciar sessão com a conta Microsoft ou Google. Muitas aplicações que utilizam a OAuth fornecem uma alternativa ao segredo do cliente. Por isso, a primeira tática deve ser investigar essa possibilidade.

Se o teste tiver de iniciar sessão com a OAuth, a abordagem geral será:

* Utilizar uma ferramenta como o Fiddler para examinar o tráfego entre o browser, o site de autenticação e a aplicação.
* Iniciar duas sessões ou mais com computadores ou browsers diferentes ou com longos intervalos (para permitir a expiração dos tokens).
* Ao comparar as diversas sessões, identificar o token devolvido pelo site de autenticação, que é de seguida transmitido ao servidor da aplicação depois do início de sessão.
* Gravar um teste Web com o Visual Studio.
* Parametrizar os tokens definindo o parâmetro quando o token é devolvido do autenticador e utilizando-o na consulta do site.
  (O Visual Studio tenta parametrizar o teste, mas não parametriza corretamente os tokens.)

## <a name="performance-tests"></a>Testes de desempenho
Pode executar um teste de carga no seu Website. Como o teste de disponibilidade, pode enviar pedidos simples ou pedidos com vários passos a partir dos nossos pontos a nível mundial. Ao contrário de um teste de disponibilidade, são enviados muitos pedidos, simulando vários utilizadores em simultâneo.

Sob **configurar**, aceda à **testes de desempenho** e clique em novo para criar um teste.

![Criar um novo teste de desempenho](./media/monitor-web-app-availability/11new-performance-test.png)

Quando o teste estiver concluído, são-lhe apresentados tempos de resposta e taxas de êxito.

![Resultados do teste de desempenho](./media/monitor-web-app-availability/12performance-test.png)

> [!TIP]
> Para observar os efeitos de um teste de desempenho, utilize o [Live Stream](../../azure-monitor/app/live-stream.md) e o [Gerador de perfis](../../azure-monitor/app/profiler.md).
>

## <a name="automation"></a>Automatização
* [Utilize scripts do PowerShell para configurar um teste de disponibilidade](../../azure-monitor/app/powershell.md#add-an-availability-test) automaticamente.
* Configure um [webhook](../../azure-monitor/platform/alerts-webhooks.md) que será chamado sempre que for gerado um alerta.

## <a name="qna"></a> FAQ

* *Site está okay mas vejo falhas de teste? Por que motivo é que o Application Insights-me é alertas?*

    * O seu teste tem "Analisar pedidos dependentes" ativados? Que resulta numa verificação rigorosa nos recursos, como scripts, imagens, etc. Esses tipos de falhas podem não ser evidentes num navegador. Verifique todas as imagens, scripts, folhas de estilo e outros ficheiros carregados pela página. Se qualquer um deles falhar, o teste é reportado como falhado, mesmo se a página principal HTML carregar corretamente. Para dessensibilizar o teste para tais falhas recursos, basta desmarcar "Analisar Pedidos Dependentes" da configuração do teste. 

    * Para reduzir as probabilidades de ruído de blips de rede transitórios etc., certifique-se de que a configuração "Permitir repetição de falhas de teste" está marcada. Também pode testar a partir de mais localizações e gerir o limiar de regra de alerta em conformidade para evitar problemas específicos da localização que causam alertas indevidos.

    * Clique em qualquer um dos vermelhos a experiência de disponibilidade do ou qualquer falha de disponibilidade a partir do Explorador de pesquisa para ver os detalhes de por que estamos reportou que a falha. O resultado do teste, juntamente com a telemetria correlacionada do lado do servidor (se ativada) deve ajudar a compreender o motivo pelo qual o teste falhou. Causas comuns de problemas transitórios problemas de rede ou a ligação. 

    * Foi o limite de tempo de teste? Podemos abortar testes depois de 2 minutos. Se o ping ou o teste com vários passos demora mais de 2 minutos, vamos relatá-lo como uma falha. Considere dividir o teste em vários aqueles que podem ser concluídas em durações mais curtas.

    * Todas as localizações comunicaram falha, ou apenas alguns deles? Se apenas algumas comunicadas falhas, pode ser devido a problemas de rede de CDN. Novamente, ao clicar nos pontos vermelhos deve ajudar a compreender por que a localização comunicadas falhas.

* *Eu não recebeu uma mensagem de e-mail quando o alerta acionado ou resolvido ou ambos?*

    Verifique a configuração de alertas clássicos para confirmar o seu e-mail é diretamente aparece na lista ou uma lista de distribuição que estiver a utilizar está configurada para receber notificações. Se for, em seguida, verifique a configuração de lista de distribuição para confirmar a pode receber mensagens de e-mail externas. Também pode verificar se o seu administrador de correio pode ter qualquer políticas configuradas, que podem fazer com que este problema.

* *Eu não recebeu a notificação de webhook?*

    Verifique se o aplicativo receber a notificação de webhook está disponível e processa com êxito os pedidos de webhook. Ver [isso](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) para obter mais informações.

* *Falha de teste intermitente com um erro de violação do protocolo?*

    O erro ("violação do protocolo.. CR tem de ser seguido por LF") indica um problema com o servidor (ou dependências). Isto acontece quando há cabeçalhos mal formados estão definidos na resposta. Pode ser causado por balanceadores de carga ou CDNs. Especificamente, alguns cabeçalhos poderão não estar a utilizar CRLF para indicar o fim de linha, que viola a especificação de HTTP e, por conseguinte, a falha de validação a nível de .NET WebRequest. Inspecione a resposta a cabeçalhos spot que poderá estar em violação.
    
    Nota: O URL não pode falhar em browsers que tenham uma validação simples de cabeçalhos HTTP. Veja esta mensagem de blogue para uma explicação detalhada deste problema: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  
    
* *Não vejo qualquer telemetria de lado do servidor relacionada para diagnosticar falhas do teste?*
    
    Se tiver o Application Insights configurado para a sua aplicação do lado do servidor, poderá dever-se ao facto de a [amostragem](../../azure-monitor/app/sampling.md) estar em curso. Selecione um resultado de disponibilidade diferente.

* *Posso chamar um código a partir do meu teste Web?*

    Não. Os passos do teste devem estar no ficheiro .webtest. E não pode chamar outros testes Web nem utilizar ciclos. No entanto, existem vários plug-ins que poderão ser úteis.

* *Suporta HTTPS?*

    Suportamos TLS 1.1 e TLS 1.2. Atualmente não verificamos os erros de certificado HTTPS.  

* *Existe alguma diferença entre “testes Web” e “testes de disponibilidade”?*

    Ambos os termos podem ser utilizados alternadamente. “Testes de disponibilidade” é um termo mais genérico que inclui os testes de ping de URL individuais, para além dos testes Web de vários passos.
    
* *Gostaria de utilizar testes de disponibilidade no nosso servidor interno, que é executado protegido por uma firewall.*

    Existem duas soluções possíveis:
    
    * Configure a firewall para permitir pedidos recebidos a partir dos [endereços IP dos nossos agentes de teste Web](../../azure-monitor/app/ip-addresses.md).
    * Escreva o seu próprio código para testar periodicamente o seu servidor interno. Execute o código como um processo em segundo plano num servidor de teste atrás da firewall. O processo de teste pode enviar os resultados para o Application Insights através da API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) no pacote SDK core. Isto requer que o servidor de teste tenha acesso de envio para o ponto final de ingestão do Application Insights, mas é um risco de segurança muito inferior do que a alternativa de permitir pedidos recebidos. Os resultados não aparecem nos painéis de testes Web de disponibilidade, mas aparecem como resultados de disponibilidade no Analytics, no Search e no Explorador de Métricas.

* *O carregamento de um teste Web de vários passos falha*

    Alguns motivos pelos quais que isto pode acontecer:
    * Existe um limite de tamanho de 300 K.
    * Os ciclos não são suportados.
    * As referências a outros testes Web não são suportadas.
    * As origens de dados não são suportadas.

* *O meu teste com vários passos não é concluído*

    Existe um limite de 100 pedidos por teste. Além disso, o teste é interrompido se for executado durante mais de dois minutos.

* *Como executar um teste com certificados de cliente?*

    Essa função não é suportada.

## <a name="who-receives-the-classic-alert-notifications"></a>Quem receberá notificações de alerta (clássicas)?

Esta secção apenas se aplica a alertas clássicos e irá ajudá-lo a otimizar suas notificações de alerta para garantir que apenas os destinatários desejados recebem notificações. Para saber mais sobre a diferença entre [alertas clássicos](../platform/alerts-classic.overview.md)e a nova experiência de alertas, consulte a [artigo de descrição geral de alertas](../platform/alerts-overview.md). Para controlar o alerta de notificação no novos alertas experiência de utilização [grupos de ação](../platform/action-groups.md).

* Recomendamos a utilização de destinatários específicos para as notificações de alerta clássicas.

* Para alertas em falhas de X fora de localizações de Y, o **em massa/grupo** opção de caixa de verificação, se estiver ativada, envia para os utilizadores com funções de administrador/coadministrador.  Essencialmente _todos os_ administradores da _subscrição_ irão receber notificações.

* Para alertas em disponibilidade métricas (ou qualquer métricas do Application Insights para esse fim) a **em massa/grupo** opção de caixa de verificação se estiver ativada, envia para os utilizadores com funções de proprietário, contribuinte ou leitor na subscrição. Na verdade, _todos os_ os utilizadores com acesso à subscrição do recurso do Application Insights estão no âmbito e irá receber notificações. 

> [!NOTE]
> Se utilizar atualmente o **em massa/grupo** opção de caixa de verificação e desativá-la, não será capaz de reverter a alteração.

Utilize os nova experiência/perto-realtime os alertas do alerta se tiver de notificar os utilizadores com base nas suas funções. Com o [grupos de ação](../platform/action-groups.md), pode configurar notificações por e-mail aos utilizadores com qualquer uma das funções Contribuidor/proprietário/leitor (não combinadas em conjunto como uma única opção).



## <a name="next"></a>Passos seguintes
[Pesquisar registos de diagnóstico][diagnostic]

[Resolução de problemas][qna]

[IP addresses of web test agents (Endereços IP dos agentes de testes Web)](../../azure-monitor/app/ip-addresses.md)

<!--Link references-->

[azure-availability]: ../../insights-create-web-tests.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
