---
title: Problemas de afinidade da sessão de resolução de problemas
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como resolver problemas de afinidade em azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 9f14521c15c3497bed4ffbeba44cb5d78ee4df7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74047991"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Problemas de sessão de aplicação Azure Gateway questões de afinidade

Saiba diagnosticar e resolver problemas de afinidade da sessão com o Portal de Aplicação Azure.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

A funcionalidade de afinidade de sessão com base em cookies é útil quando pretende manter uma sessão de utilizador no mesmo servidor. Ao utilizar cookies geridos por um gateway, o Gateway de Aplicação pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento. Isto é importante nos casos em que o estado da sessão é guardado localmente no servidor para uma sessão de utilizador.

## <a name="possible-problem-causes"></a>Possíveis causas problemáticas

O problema na manutenção da afinidade da sessão baseada em cookies pode acontecer devido às seguintes principais razões:

- A definição "Affinity baseada em cookies" não está ativada
- A sua aplicação não pode lidar com afinidade baseada em cookies
- A aplicação está a usar afinidade baseada em cookies, mas os pedidos ainda saltam entre servidores de back-end

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Verifique se a definição "Affinity baseada em Cookies" está ativada

Por vezes, podem ocorrer problemas de afinidade da sessão quando se esquece de ativar a definição de "afinidade baseada em Cookies". Para determinar se ativou a definição de "afinidade baseada em Cookies" no separador DEFINIÇões HTTP no portal Azure, siga as instruções:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. No painel **de navegação à esquerda,** clique em **todos os recursos.** Clique no nome do gateway da aplicação na lâmina de todos os recursos. Se a subscrição que selecionou já tiver vários recursos, pode introduzir o nome de gateway da aplicação no **Filtro pelo nome...** para aceder facilmente ao gateway de aplicação.

3. Selecione **as definições de HTTP** em **DEFINIÇÕES**.

   ![problemas-sessão-afinidade-emitas-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Clique em **appGatewayBackendHttpSettings** no lado direito para verificar se selecionou **Enabled** para afinidade baseada em Cookies.

   ![problemas-sessão-afinidade-problemas-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Também pode verificar o valor do "**CookieBasedAffinity**" está definido para *Enabled*em "**backendHttpSettingsCollection**" utilizando um dos seguintes métodos:

- Executar [Get-AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) na PowerShell
- Veja através do ficheiro JSON usando o modelo do Gestor de Recursos Azure

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>A aplicação não pode lidar com a finidade baseada em cookies

#### <a name="cause"></a>Causa

O gateway da aplicação só pode realizar afinidade baseada em sessão usando um cookie.

#### <a name="workaround"></a>Solução

Se a aplicação não conseguir lidar com a finha baseada em cookies, deve utilizar um equilibrador de carga azul ou azul externo ou interno ou outra solução de terceiros.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>A aplicação está a usar afinidade baseada em cookies, mas os pedidos ainda saltam entre servidores de back-end

#### <a name="symptom"></a>Sintoma

Ativou a definição de Affinity baseada em Cookies, quando acede ao Gateway de [http://website](http://website/) aplicação utilizando um URL de nome curto no Internet Explorer, por exemplo: o pedido ainda está a saltar entre servidores de back-end.

Para identificar este problema, siga as instruções:

1. Tome um traço de debugger web no "Cliente" que está ligado à aplicação por trás do Gateway de aplicação (estamos a usar o Fiddler neste exemplo).
    **Dica** Se não sabe usar o Violinista, verifique a opção " Quero recolher o tráfego de**rede e analisá-lo usando web debugger**" na parte inferior.

2. Verifique e analise os registos da sessão, para determinar se os cookies fornecidos pelo cliente têm os detalhes da ARRAffinity. Se não encontrar os detalhes da ARRAffinity, tais como "**ARRAffinity=** *ARRAffinityValue*" dentro do conjunto de cookies, isso significa que o cliente não está a responder com o cookie ARRA, que é fornecido pelo Gateway de Aplicação.
    Por exemplo:

    ![problemas-sessão-afinidade-problemas-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![problemas-sessão-afinidade-problemas-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

A aplicação continua a tentar definir o cookie em cada pedido até obter resposta.

#### <a name="cause"></a>Causa

Este problema ocorre porque o Internet Explorer e outros navegadores podem não armazenar ou usar o cookie com um URL de nome curto.

#### <a name="resolution"></a>Resolução

Para corrigir este problema, aceda ao Gateway de Aplicação através de um FQDN. Por exemplo, [http://website.com](https://website.com/) [http://appgw.website.com](http://appgw.website.com/) utilizar ou .

## <a name="additional-logs-to-troubleshoot"></a>Registos adicionais para resolução de problemas

Você pode recolher registos adicionais e analisá-los para resolver problemas relacionados com afinidade de sessão baseada em cookies

### <a name="analyze-application-gateway-logs"></a>Analisar registos de gateway de aplicação

Para recolher os registos do Gateway da Aplicação, siga as instruções:

Ativar o registo através do portal do Azure

1. No [portal Azure,](https://portal.azure.com/)encontre o seu recurso e, em seguida, clique em **registos de diagnóstico**.

   Para gateway de aplicação, estão disponíveis três registos: Log de acesso, registo de desempenho, registo de firewall

2. Para começar a recolher dados, clique **em Ligar diagnósticos**.

   ![problemas-sessão-afinidade-problemas-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. A lâmina de **definições de Diagnóstico** fornece as definições para os registos de diagnóstico. Neste exemplo, o Log Analytics armazena os registos. Clique em **Configurar** em **Log Analytics** para definir o seu espaço de trabalho. Também pode utilizar os hubs de eventos e uma conta de armazenamento para guardar os registos de diagnóstico.

   ![problemas-sessão-afinidade-problemas-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Confirme as definições e, em seguida, clique em **Guardar**.

   ![problemas-sessão-afinidade-problemas-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Ver e analisar os registos de acesso do Gateway de aplicação

1. No portal Azure sob a vista de recurso Application Gateway, selecione **registos** de diagnóstico na secção **MONITORAmento** .

   ![problemas-sessão-afinidade-problemas-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Do lado direito, selecione "**ApplicationGatewayAccessLog**" na lista de drop-down nas **categorias de Registo.**  

   ![problemas-sessão-afinidade-problemas-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Na lista de registos de acesso ao Gateway application, clique no log que pretende analisar e exportar e, em seguida, exportar o ficheiro JSON.

4. Converta o ficheiro JSON que exportou no ficheiro 3 para CSV e veja-os no Excel, Power BI ou em qualquer outra ferramenta de visualização de dados.

5. Verifique os seguintes dados:

- **ClientIP**– Este é o endereço IP do cliente do cliente de ligação.
- **ClientPort** - Esta é a porta de origem do cliente de ligação para o pedido.
- **RequestQuery** – Isto indica o servidor de destino que o pedido é recebido.
- **Server-Routed**: Back-end pool exemplo que o pedido é recebido.
- **X-AzureApplicationGateway-LOG-ID**: Id de correlação utilizado para o pedido. Pode ser usado para resolver problemas de tráfego nos servidores de back-end. Por exemplo: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVIDOR-STATUS**: Código de resposta HTTP que o Gateway de Aplicação recebeu na parte de trás.

  ![problemas-sessão-afinidade-problemas-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Se vir dois itens estão vindo do mesmo ClientIP e Client Port, e eles são enviados para o mesmo servidor de back-end, isso significa que o Gateway de Aplicação configurado corretamente.

Se vir dois itens provenientes da mesma ClientIP e Client Port, e eles são enviados para os diferentes servidores back-end, isso significa que o pedido está a saltar entre servidores de backend, selecione "**A aplicação está usando afinidade baseada em cookies, mas os pedidos ainda saltando entre servidores de back-end**" na parte inferior para resolver o problema.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Use web debugger para capturar e analisar os tráfegos HTTP ou HTTPS

Ferramentas de depuração web como o Fiddler, podem ajudá-lo a depurar aplicações web capturando o tráfego de rede entre a Internet e computadores de teste. Estas ferramentas permitem-lhe inspecionar os dados de entrada e saída à medida que o navegador os recebe/envia. O Fiddler, neste exemplo, tem a opção de repetição http que pode ajudá-lo a resolver problemas do lado do cliente com aplicações web, especialmente para o tipo de problema de autenticação.

Usa o desbugger web da tua escolha. Nesta amostra usaremos o Fiddler para capturar e analisar os tráfegos http ou https, siga as instruções:

1. Baixe a ferramenta <https://www.telerik.com/download/fiddler>Fiddler em .

    > [!NOTE]
    > Escolha o Fiddler4 se o computador de captura tiver .NET 4 instalado. Caso contrário, escolha o Fiddler2.

2. Clique na configuração executada e corra como administrador para instalar.

    ![problemas-sessão-afinidade-problemas-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Quando abrir o Fiddler, deve começar automaticamente a capturar o tráfego (note a Captura no canto inferior esquerdo). Pressione f12 para iniciar ou parar a captura de tráfego.

    ![problemas-sessão-afinidade-problemas-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Muito provavelmente, estará interessado no tráfego HTTPS desencriptado, e pode ativar a desencriptação HTTPS selecionando **Tools** > **Fiddler Options,** e verificar a caixa " **Decrypt HTTPS tráfego**".

    ![problemas-sessão-afinidade-problemas-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Pode remover sessões não relacionadas anteriores antes de reproduzir o problema clicando em **X** (ícone) > **Remover Tudo** como seguir a imagem: 

    ![problemas-sessão-afinidade-problemas-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Depois de reproduzir o problema, guarde o ficheiro para revisão selecionando **File** > **Save** > **All Sessions..** 

    ![problemas-sessão-afinidade-problemas-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Verifique e analise os registos da sessão para determinar qual é o problema.

    Por exemplo:

- **Exemplo A:** Encontra um registo de sessão que o pedido é enviado do cliente, e vai para o endereço IP público do Gateway de Aplicação, clique neste registo para ver os detalhes.  Do lado direito, os dados na caixa inferior são o que o Gateway de Aplicação está a devolver ao cliente. Selecione o separador "RAW" e determine se o cliente está a receber um "**Set-Cookie: ARRAffinity=** *ARRAffinityValue*." Se não houver cookie, a afinidade da sessão não está definida, ou o Gateway de aplicação não está a aplicar cookies de volta ao cliente.

   > [!NOTE]
   > Este valor ARRAffinity é o cookie-id, que o Gateway de Aplicação define para o cliente ser enviado para um servidor de back-end particular.

   ![problemas-sessão-afinidade-problemas-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Exemplo B:** O registo da sessão seguinte seguido pelo anterior é o cliente que responde ao Gateway de Aplicação, que definiu o ARRAAFFINITY. Se o cookie-id ARRAffinity corresponder, o pacote deve ser enviado para o mesmo servidor de back-end que foi usado anteriormente. Consulte as próximas linhas de comunicação http para ver se o cookie ARRAffinity do cliente está a mudar.

   ![problemas-sessão-afinidade-problemas-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Para a mesma sessão de comunicação, o cookie não deve mudar. Verifique a caixa superior do lado direito, selecione o separador "Cookies" para ver se o cliente está a usar o cookie e enviá-lo de volta para o Gateway da Aplicação. Caso contrário, o navegador cliente não está a guardar e a usar o cookie para conversas. Às vezes, o cliente pode mentir.

 

## <a name="next-steps"></a>Passos seguintes

Se os passos anteriores não resolverem a questão, abra um bilhete de [apoio](https://azure.microsoft.com/support/options/).
