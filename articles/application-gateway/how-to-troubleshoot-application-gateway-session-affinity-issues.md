---
title: Problemas de afinidade da sessão de resolução de problemas
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como resolver problemas de afinidade na Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 548bda36ed2b167c159d32a575b63ecbf10b16dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397574"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Problemas de resolução de aplicações Azure Gateway problemas de afinidade

Saiba como diagnosticar e resolver problemas de afinidade de sessão com o Azure Application Gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

A funcionalidade de afinidade de sessão com base em cookies é útil quando pretende manter uma sessão de utilizador no mesmo servidor. Ao utilizar cookies geridos por um gateway, o Gateway de Aplicação pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento. Isto é importante nos casos em que o estado da sessão é guardado localmente no servidor para uma sessão de utilizador.

## <a name="possible-problem-causes"></a>Possíveis causas problemáticas

O problema na manutenção da afinidade da sessão baseada em cookies pode acontecer devido às seguintes razões principais:

- A definição "Affinity baseada em cookies" não está ativada
- A sua aplicação não pode lidar com a afinidade baseada em cookies
- A aplicação está a usar afinidade baseada em cookies, mas os pedidos ainda saltam entre servidores de back-end

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Verifique se a definição "Afinidade baseada em cookies" está ativada

Por vezes, os problemas de afinidade da sessão podem ocorrer quando se esquece de ativar a definição de "afinidade baseada em cookies". Para determinar se ativou a definição "Afinidade baseada em Cookies" no separador DEFINIÇÕES HTTP no portal Azure, siga as instruções:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. No painel de **navegação à esquerda,** clique em **Todos os recursos.** Clique no nome do gateway de aplicação na lâmina de todos os recursos. Se a subscrição que selecionou já tiver vários recursos, pode introduzir o nome de gateway de aplicação no **Filtro pelo nome...** para aceder facilmente ao gateway de aplicação.

3. Selecione o separador **definições HTTP** em **DEFINIÇÕES**.

   ![A screenshot mostra DEFINIÇÕES com as definições de H T T P selecionadas.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Clique **em aplicativoSgatewayBackendHttpSettings** no lado direito para verificar se selecionou **Enabled** for Cookie based affinity.

   ![O Screenshot mostra as definições de gateway para um gateway de aplicações, inlcuiding se a afinidade baseada em Cookie é selecionada.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Também pode verificar o valor da "**CookieBasedAffinity**" definido para *Ativar* em "**backendHttpSettingsCollection**" utilizando um dos seguintes métodos:

- Executar [Get-AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) em PowerShell
- Procure através do ficheiro JSON utilizando o modelo do Gestor de Recursos Azure

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>A aplicação não pode lidar com a afinidade baseada em cookies

#### <a name="cause"></a>Causa

O gateway de aplicações só pode realizar afinidade baseada em sessão usando um cookie.

#### <a name="workaround"></a>Solução

Se a aplicação não conseguir lidar com afinidade baseada em cookies, deve utilizar um equilibrador de carga externo ou interno ou outra solução de terceiros.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>A aplicação está a usar afinidade baseada em cookies, mas os pedidos ainda saltam entre servidores de back-end

#### <a name="symptom"></a>Sintoma

Ativou a definição de Affinity baseada em Cookies, quando acede ao Gateway de Aplicações utilizando um URL de nome curto no Internet Explorer, por exemplo: `http://website` , o pedido ainda está a saltar entre servidores de back-end.

Para identificar este problema, siga as instruções:

1. Faça um rastreio de depurador web no "Cliente" que está a ligar-se à aplicação por trás do Gateway de aplicação (Estamos a usar o Fiddler neste exemplo).
    **Ponta** Se não sabe como usar o Violinista, verifique a opção " Quero recolher o **tráfego de rede e analisá-lo usando o depurador web**" na parte inferior.

2. Verifique e analise os registos de sessão, para determinar se os cookies fornecidos pelo cliente têm os detalhes da ARRAffinity. Se não encontrar os detalhes da ARRAffinity, tais como "**ARRAffinity=** *ARRAffinityValue*" dentro do conjunto de cookies, isso significa que o cliente não está respondendo com o cookie ARRA, que é fornecido pelo Gateway de aplicação.
    Por exemplo:

    ![A screenshot mostra um registo de sessão com uma única entrada realçada.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![Screenshot mostra cabeçalhos de pedido para H T T, incluindo informações sobre cookies.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

A aplicação continua a tentar definir o cookie em cada pedido até obter resposta.

#### <a name="cause"></a>Causa

Este problema ocorre porque o Internet Explorer e outros navegadores podem não armazenar ou usar o cookie com um URL de nome curto.

#### <a name="resolution"></a>Resolução

Para corrigir este problema, aceda ao Gateway de Aplicação através de um FQDN. Por exemplo, usar [http://website.com](https://website.com/) ou [http://appgw.website.com](http://website.com/) . .

## <a name="additional-logs-to-troubleshoot"></a>Troncos adicionais para resolução de problemas

Pode recolher registos adicionais e analisá-los para resolver problemas relacionados com a afinidade da sessão baseada em cookies

### <a name="analyze-application-gateway-logs"></a>Analisar registos de Gateway de Aplicação

Para recolher os registos do Gateway de Aplicação, siga as instruções:

Ativar o registo através do portal do Azure

1. No [portal Azure,](https://portal.azure.com/)encontre o seu recurso e, em seguida, clique em **registos de diagnóstico**.

   Para o Gateway de Aplicações, estão disponíveis três registos: log de acesso, registo de desempenho, registo de firewall

2. Para começar a recolher dados, clique **em Ligar os diagnósticos**.

   ![A screenshot mostra um gateway de aplicação com registos de Diagnóstico selecionados.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. A lâmina **de definição de diagnóstico** fornece as definições para os registos de diagnóstico. Neste exemplo, o Log Analytics armazena os registos. Clique em **Configurar** em **Log Analytics** para definir o seu espaço de trabalho. Também pode utilizar os hubs de eventos e uma conta de armazenamento para guardar os registos de diagnóstico.

   ![A screenshot mostra o painel de definições de Diagnóstico com configuração de Log Analytics selecionada.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Confirme as definições e clique em **Guardar**.

   ![A screenshot mostra o painel de definições de Diagnóstico com o Save selecionado.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Ver e analisar os registos de acesso do Gateway de Aplicação

1. No portal Azure sob a vista de recursos do Gateway de Aplicação, selecione **registos de diagnóstico** na secção **MONITOR .**

   ![A screenshot mostra MONITORING com registos de Diagnóstico selecionados.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. No lado direito, selecione "**ApplicationGatewayAccessLog**" na lista de drop-down nas **categorias de Log.**  

   ![O Screenshot mostra a lista de categorias de Registo com o ApplicationGatewayAccessLog selecionado.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Na lista de Registos de Acesso de Gateway de Aplicação, clique no registo que pretende analisar e exportar e, em seguida, exporte o ficheiro JSON.

4. Converta o ficheiro JSON que exportou no ficheiro passo 3 para O CSV e veja-o no Excel, Power BI ou em qualquer outra ferramenta de visualização de dados.

5. Consulte os seguintes dados:

- **ClientIP**– Este é o endereço IP do cliente do cliente que liga.
- **ClientPort** - Esta é a porta de origem do cliente de ligação para o pedido.
- **PedidoQuery** – Isto indica o servidor de destino que o pedido é recebido.
- **Server-Routed**: Instância de piscina de back-end que o pedido é recebido.
- **X-AzureApplicationGateway-LOG-ID**: ID de correlação utilizado para o pedido. Pode ser usado para resolver problemas de tráfego nos servidores de back-end. Por exemplo: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **ESTADO DO SERVIDOR**: Código de resposta HTTP que o Gateway de aplicação recebeu a partir da parte de trás.

  ![O Screenshot mostra o estado do servidor em texto simples, na sua maioria obscurecido, com o clientePort e SERVER-ROUTED em destaque.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Se vir dois itens provenientes da mesma Porta clienteIP e cliente, e são enviados para o mesmo servidor back-end, isso significa que o Gateway de Aplicação configurado corretamente.

Se vir dois itens provenientes da mesma Porta clienteIP e cliente, e são enviados para os diferentes servidores back-end, o que significa que o pedido está a saltar entre servidores de backend, selecione "**A aplicação está a usar afinidade baseada em cookies, mas os pedidos ainda saltam entre servidores de back-end**" na parte inferior para resolver os problemas.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Utilize o depurar web para capturar e analisar os tráfegos HTTP ou HTTPS

Ferramentas de depurar web como o Fiddler, podem ajudá-lo a depurar aplicações web capturando o tráfego de rede entre a Internet e computadores de teste. Estas ferramentas permitem-lhe inspecionar os dados de entrada e saída à medida que o navegador os recebe/envia. O Fiddler, neste exemplo, tem a opção de reprodução HTTP que pode ajudá-lo a resolver problemas do lado do cliente com aplicações web, especialmente para o tipo de problema de autenticação.

Use o depurar web da sua escolha. Nesta amostra utilizaremos o Fiddler para capturar e analisar tráfegos http ou https, seguir as instruções:

1. Descarregue a ferramenta Fiddler em <https://www.telerik.com/download/fiddler> .

    > [!NOTE]
    > Escolha o Fiddler4 se o computador de captura tiver .NET 4 instalado. Caso contrário, escolha Fiddler2.

2. Clique em linha re execução e corra como administrador para instalar.

    ![O Screenshot mostra o programa de configuração da ferramenta Fiddler com um menu contextual com Run como administrador selecionado.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Quando abrir o Violinista, deve começar automaticamente a capturar o tráfego (note a captura no canto inferior esquerdo). Prima f12 para iniciar ou parar a captura de tráfego.

    ![A screenshot mostra o Fiddler Web Debugger com o indicador de Captura realçado.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. O mais provável é que esteja interessado no tráfego HTTPS desencriptado e pode ativar a desencriptação HTTPS selecionando Opções de Violino **de Ferramentas**  >  , e verificar a caixa " **Desencriptar o tráfego HTTPS**".

    ![O Screenshot mostra opções no Violino com o tráfego de H T T P selecionado e desencripto https selecionado.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Pode remover sessões não relacionadas anteriores antes de reproduzir o problema clicando em  **X** (ícone) > **Remover Tudo** como seguinte screenshot: 

    ![A screenshot mostra o ícone X selecionado, que mostra a opção Remover todas as opções.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Depois de reproduzir o problema, guarde o ficheiro para revisão selecionando **File**  >  **Save**  >  **All Sessions..** 

    ![A screenshot mostra a opção 'Guardar todos os ficheiros' selecionada.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Verifique e analise os registos de sessão para determinar qual é o problema.

    Por exemplo:

- **Exemplo A:** Encontra um registo de sessão que o pedido é enviado do cliente, e vai para o endereço IP público do Gateway de Aplicação, clique neste registo para ver os detalhes.  No lado direito, os dados na caixa inferior são o que o Gateway de aplicação está a devolver ao cliente. Selecione o separador "RAW" e determine se o cliente está a receber um "**Set-Cookie: ARRAffinity=** *ARRAffinityValue*." Se não houver cookie, a afinidade da sessão não está definida, ou o Gateway de Aplicação não está a aplicar cookie de volta ao cliente.

   > [!NOTE]
   > Este valor ARRAffinity é o id cookie-id, que o Gateway de aplicação define para o cliente ser enviado para um determinado servidor back-end.

   ![O screenshot mostra um exemplo de detalhes de uma entrada de registo com o valor Set-Cookie realçado.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Exemplo B:** O registo da próxima sessão seguido pelo anterior é o cliente que responde de volta ao Gateway de Aplicação, que definiu o ARRAAFFINITY. Se o cookie-id ARRAffinity corresponder, o pacote deve ser enviado para o mesmo servidor de back-end que foi usado anteriormente. Consulte as próximas linhas de comunicação http para ver se o cookie ARRAffinity do cliente está a mudar.

   ![A screenshot mostra um exemplo de detalhes de uma entrada de registo com um cookie realçado.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Para a mesma sessão de comunicação, o cookie não deve mudar. Verifique a caixa superior no lado direito, selecione o separador "Cookies" para ver se o cliente está a usar o cookie e enviá-lo de volta para o Gateway de Aplicações. Caso contrário, o navegador cliente não está a guardar e a usar o cookie para conversas. Às vezes, o cliente pode mentir.

 

## <a name="next-steps"></a>Passos seguintes

Se os passos anteriores não resolverem o problema, abra um [bilhete de apoio](https://azure.microsoft.com/support/options/).