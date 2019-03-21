---
title: Resolução de problemas de afinidade de sessão de Gateway de aplicação do Azure
description: Este artigo fornece informações sobre como resolver problemas de afinidade de sessão no Gateway de aplicação do Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 157cbd9b05f7f2af58df732a1ca0329926a200da
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123220"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Resolver problemas de afinidade de sessão de Gateway de aplicação do Azure

Saiba como diagnosticar e resolver problemas de afinidade de sessão com o Gateway de aplicação do Azure.

## <a name="overview"></a>Descrição geral

A funcionalidade de afinidade de sessão com base em cookies é útil quando pretende manter uma sessão de utilizador no mesmo servidor. Ao utilizar cookies geridos por um gateway, o Gateway de Aplicação pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento. Isto é importante nos casos em que o estado da sessão é guardado localmente no servidor para uma sessão de utilizador.

## <a name="possible-problem-causes"></a>Causas possível problema

O problema na manutenção de afinidade de sessão com base no cookie pode acontecer devido ao seguinte principal:

- Definição de "afinidade com base no cookie" não está ativada
- A aplicação não consegue processar a afinidade com base no cookie
- Aplicação está a utilizar com base no cookie de afinidade, mas ainda saltando entre servidores back-end de pedidos

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Verifique se a definição de "afinidade com base no Cookie" está ativada

Por vezes, os problemas de afinidade de sessão podem ocorrer quando se esqueça de ativar a definição de "Com base no Cookie afinidade". Para determinar se tiver ativado a definição "Com base no Cookie afinidade", no separador Definições de HTTP no portal do Azure, siga as instruções:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Na **deixado navegação** painel, clique em **todos os recursos**. Clique no nome do gateway de aplicação no painel todos os recursos. Se a subscrição que selecionou já tiver vários recursos, pode introduzir o nome do gateway de aplicação no **filtrar por nome...** para aceder facilmente ao gateway de aplicação.

3. Selecione **definições de HTTP** separador sob **definições**.

   ![resolução de problemas-sessão-afinidade-problemas de-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Clique em **appGatewayBackendHttpSettings** no lado direito para verificar se tiver selecionado **ativado** para a afinidade com base no Cookie.

   ![resolução de problemas-sessão-afinidade-problemas-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Também pode verificar o valor da "**CookieBasedAffinity**" está definida como *ativado*em "**backendHttpSettingsCollection**" através de um dos seguintes métodos:

- Execute [Get-AzureRmApplicationGatewayBackendHttpSettings](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermapplicationgatewaybackendhttpsettings?view=azurermps-4.1.0) no PowerShell
- Examine o ficheiro JSON com o modelo Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>A aplicação não é possível processar a afinidade com base no cookie

#### <a name="cause"></a>Causa

O gateway de aplicação só pode efetuar a afinidade com base em sessão através de um cookie.

#### <a name="workaround"></a>Solução

Se a aplicação não é possível processar a afinidade com base no cookie, tem de utilizar um balanceador de carga externo ou interno ou outra solução de terceiros.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Aplicação está a utilizar com base no cookie de afinidade, mas ainda saltando entre servidores back-end de pedidos

#### <a name="symptom"></a>Sintoma

Ativar a definição de afinidade baseada em cookies, quando acessa o Gateway de aplicação ao utilizar um URL de nome curto no Internet Explorer, por exemplo: [ http://website ](http://website/) , o pedido é ainda saltando entre servidores back-end.

Para identificar este problema, siga as instruções:

1. Tire um rastreio de depurador da web do "cliente" que está a ligar a aplicação por trás Gateway(We are using Fiddler in this example) o aplicativo.
    **Sugestão** se não sabe como utilizar o Fiddler, marque a opção "**quero coletar o tráfego de rede e analise-os com o depurador da web**" na parte inferior.

2. Verifique e analisar os registos de sessão, para determinar se cookies fornecidos pelo cliente que os detalhes de ARRAffinity. Se não encontrar os detalhes de ARRAffinity, tais como "**ARRAffinity =** *ARRAffinityValue*" no conjunto de cookie, o que significa que o cliente não está respondendo com o cookie ARRA, que é fornecido pela Gateway de aplicação.
    Por exemplo:

    ![resolver problemas relacionados com a sessão-afinidade-problemas-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

        ![troubleshoot-session-affinity-issues-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

A aplicação continua a tentar definir o cookie em cada pedido até que obtenha a resposta.

#### <a name="cause"></a>Causa

Este problema ocorre porque o Internet Explorer e outros navegadores não podem armazenar ou utilizar o cookie com um URL de nome abreviado.

#### <a name="resolution"></a>Resolução

Para corrigir este problema, deve acessar o Gateway de aplicação utilizando um FQDN. Por exemplo, usar [ http://website.com ](http://website.com/) ou [ http://appgw.website.com ](http://appgw.website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Registos adicionais para resolução de problemas

Pode recolher registos adicionais e analisá-las para resolver problemas relacionados com a afinidade de sessão com base no cookie relacionadas de problemas

### <a name="analyze-application-gateway-logs"></a>Analisar registos do Gateway de aplicação

Para recolher os registos do Gateway de aplicação, siga as instruções:

Ativar o registo através do portal do Azure

1. Na [portal do Azure](https://portal.azure.com/), localize o seu recurso e, em seguida, clique em **registos de diagnóstico**.

   Para o Gateway de aplicação, três logs estão disponíveis: Registo de acesso, o registo de desempenho, o log de Firewall

2. Para começar a recolher dados, clique em **ativar os diagnósticos**.

   ![resolver problemas relacionados com a sessão-afinidade-problemas-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. O **as definições de diagnóstico** painel fornece as definições para os registos de diagnóstico. Neste exemplo, o Log Analytics armazena os registos. Clique em **configurar** sob **do Log Analytics** para definir a sua área de trabalho. Também pode utilizar os hubs de eventos e uma conta de armazenamento para guardar os registos de diagnóstico.

   ![resolver problemas relacionados com a sessão-afinidade-problemas-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Confirme as definições e, em seguida, clique em **guardar**.

   ![resolver problemas de sessão-afinidade-problemas-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Ver e analisar os registos de acesso do Gateway de aplicação

1. No portal do Azure sob a exibição de recurso do Gateway de aplicação, selecione **registos de diagnóstico** no **monitorização** secção.

   ![resolver problemas relacionados com a sessão-afinidade-problemas-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. No lado direito, selecione "**ApplicationGatewayAccessLog**" na lista pendente em **categorias de registo.**  

   ![resolver problemas relacionados com a sessão-afinidade-problemas-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Na lista de registo de acesso do Gateway de aplicação, clique no registo de que pretende analisar e exportar e, em seguida, exportar o ficheiro JSON.

4. Converter o ficheiro JSON que exportou no passo 3 para o ficheiro CSV e visualizá-los no Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

5. Verifique os seguintes dados:

- **ClientIP**– este é o endereço IP de cliente do cliente ao ligar.
- **ClientPort** -esta é a porta de origem do cliente ao ligar para o pedido.
- **RequestQuery** – Isto indica que o servidor de destino que o pedido é recebido.
- **Encaminhado por servidor**: Instância de conjunto de back-end que o pedido é recebido.
- **X-AzureApplicationGateway-LOG-ID**: ID de correlação utilizado para o pedido. Ele pode ser usado para resolver problemas de tráfego nos servidores de back-end. Por exemplo: X-AzureApplicationGateway--acerto na CACHE = 0 e ENCAMINHADO por servidor = 10.0.2.4.

  - **SERVER-STATUS**: Código de resposta HTTP que o Gateway de aplicação recebido do back-end.

  ![resolver problemas relacionados com a sessão-afinidade-problemas-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Se vir que os dois itens são provenientes do mesmo ClientIP e porta de cliente e são enviadas para o mesmo servidor de back-end, isso significa que o Gateway de aplicação configurado corretamente.

Se vir que os dois itens são provenientes do mesmo ClientIP e porta de cliente e são enviadas para os diferentes servidores de back-end, que significa que o pedido é saltando entre servidores de back-end, selecionadas "**aplicação está a utilizar com base no cookie de afinidade, mas de pedidos ainda saltando entre servidores back-end**"na parte inferior para resolução de problemas.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Utilizar o depurador da web para capturar e analisar os tráfegos HTTP ou HTTPS

Ferramentas de depuração, como o Fiddler da Web, pode ajudá-lo a depurar aplicativos web ao capturar o tráfego de rede entre os computadores de Internet e de teste. Essas ferramentas permitem-lhe inspecionar os dados de entrada e saídos, como o navegador recebe/envia-os. Fiddler, neste exemplo, tem a opção de repetição HTTP que pode ajudar a resolver problemas do lado do cliente com aplicações web, especialmente para o tipo de autenticação do problema.

Utilize o depurador de web à sua escolha. Neste exemplo, irá utilizar o Fiddler para capturar e analisar os tráfegos http ou https, siga as instruções:

1. Baixar a ferramenta de Fiddler em <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Escolha Fiddler4 se o computador de captura tem o .NET 4 instalado. Caso contrário, escolha o Fiddler2.

2. Clique com o botão direito do rato no executável de configuração e executar como administrador para instalar.

            ![troubleshoot-session-affinity-issues-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Quando abrir o Fiddler, ele deverá ser iniciado automaticamente capturar tráfego (Observe a capturar, no canto inferior esquerdo). Premir a tecla F12 para iniciar ou parar a captura de tráfego.

        ![troubleshoot-session-affinity-issues-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Provavelmente, estará interessado no tráfego HTTPS descriptografado, e pode ativar a desencriptação de HTTPS, selecionando **ferramentas** > **Fiddler opções**e marque a caixa " **desencriptar Tráfego HTTPS**".

        ![troubleshoot-session-affinity-issues-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Pode remover as sessões não relacionadas anteriores antes de reproduzir o problema ao clicar em **X** (ícone) > **Remover tudo** conforme a seguir captura de ecrã: 

        ![troubleshoot-session-affinity-issues-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Depois de ter reproduzida o problema, guarde o ficheiro para revisão selecionando **arquivo** > **guardar** > **todas as sessões....** . 

        ![troubleshoot-session-affinity-issues-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Verifique e analisar os registos de sessão para determinar qual é o problema.

    Para obter exemplos:

- **Exemplo de r:** Encontrar um registo de sessão que é enviado o pedido do cliente, e ele vai para o endereço IP público do Gateway de aplicação, clique neste registo para ver os detalhes.  No lado direito, os dados na caixa na parte inferior são o que o Gateway de aplicação está a devolver ao cliente. Selecione o separador "RAW" e determinar se o cliente está a receber um "**Set-Cookie: ARRAffinity=** *ARRAffinityValue*." Se não houver nenhum cookie, afinidade de sessão não está definida ou o Gateway de aplicação não é aplicar o cookie de volta ao cliente.

   > [!NOTE]
   > Este valor de ARRAffinity é o-id de cookie, que o Gateway de aplicação define para o cliente a serem enviados para um determinado servidor de back-end.

    ![resolver problemas relacionados com a sessão-afinidade-problemas-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Exemplo b:** O registo da sessão seguinte seguido de um é o cliente a responder para o Gateway de aplicação, que definiu o ARRAAFFINITY anterior. Se o id de cookie ARRAffinity corresponder, o pacote deve ser enviado para o mesmo servidor de back-end, que foi utilizado anteriormente. Verifique as seguintes várias linhas de comunicações http para ver se o cookie de ARRAffinity do cliente está mudando.

    ![resolver problemas relacionados com a sessão-afinidade-problemas-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Para a mesma sessão de comunicação, o cookie deve para não alterar. Marque a caixa superior no lado direito, selecione o separador de "Cookies" para ver se o cliente é usando o cookie e enviar para o Gateway de aplicação. Caso contrário, o navegador do cliente não é manter e utilizar o cookie para conversas. Às vezes, o cliente poderá se encontram.

 

## <a name="next-steps"></a>Passos Seguintes

Se os passos anteriores não resolverem o problema, abra um [pedido de suporte](https://azure.microsoft.com/support/options/).