---
title: Solucionar problemas de afinidade de sessão
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como solucionar problemas de afinidade de sessão no gateway Aplicativo Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 9f14521c15c3497bed4ffbeba44cb5d78ee4df7b
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047991"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Solucionar problemas de afinidade de sessão de gateway Aplicativo Azure

Saiba como diagnosticar e resolver problemas de afinidade de sessão com Aplicativo Azure gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

A funcionalidade de afinidade de sessão com base em cookies é útil quando pretende manter uma sessão de utilizador no mesmo servidor. Ao utilizar cookies geridos por um gateway, o Gateway de Aplicação pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento. Isto é importante nos casos em que o estado da sessão é guardado localmente no servidor para uma sessão de utilizador.

## <a name="possible-problem-causes"></a>Possíveis causas de problema

O problema na manutenção da afinidade de sessão baseada em cookies pode ocorrer devido aos seguintes motivos principais:

- A configuração "afinidade baseada em cookie" não está habilitada
- Seu aplicativo não pode manipular a afinidade baseada em cookie
- O aplicativo está usando afinidade baseada em cookie, mas as solicitações ainda estão saltando entre os servidores back-end

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Verifique se a configuração "afinidade baseada em cookies" está habilitada

Às vezes, podem ocorrer problemas de afinidade de sessão quando você se esquece de habilitar a configuração "afinidade com base em cookie". Para determinar se você habilitou a configuração "afinidade baseada em cookie" na guia Configurações de HTTP no portal do Azure, siga as instruções:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. No painel de **navegação esquerdo** , clique em **todos os recursos**. Clique no nome do gateway de aplicativo na folha todos os recursos. Se a assinatura que você selecionou já tiver vários recursos, você poderá inserir o nome do gateway de aplicativo no **filtro por nome..** . para aceder facilmente ao gateway de aplicação.

3. Selecione a guia **configurações de http** em **configurações**.

   ![solução de problemas – afinidade de sessão-problemas-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Clique em **appgatewaybackendhttp** no lado direito para verificar se você selecionou **habilitado** para afinidade baseada em cookie.

   ![solução de problemas – afinidade de sessão-problemas-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Você também pode verificar se o valor de "**CookieBasedAffinity**" está definido como *habilitado*em "**backendHttpSettingsCollection**" usando um dos seguintes métodos:

- Executar [Get-AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) no PowerShell
- Examinar o arquivo JSON usando o modelo de Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>O aplicativo não pode tratar a afinidade baseada em cookie

#### <a name="cause"></a>Causa

O gateway de aplicativo só pode executar a afinidade baseada em sessão usando um cookie.

#### <a name="workaround"></a>Solução

Se o aplicativo não puder lidar com a afinidade baseada em cookie, você deverá usar um balanceador de carga do Azure externo ou interno ou outra solução de terceiros.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>O aplicativo está usando afinidade baseada em cookie, mas as solicitações ainda estão saltando entre os servidores back-end

#### <a name="symptom"></a>Sintoma

Você habilitou a configuração de afinidade baseada em cookie, ao acessar o gateway de aplicativo usando uma URL de nome curto no Internet Explorer, por exemplo: [http://website](http://website/) , a solicitação ainda está saltando entre os servidores back-end.

Para identificar esse problema, siga as instruções:

1. Faça um rastreamento do depurador da Web no "cliente" que está se conectando ao aplicativo por trás do gateway de aplicativo (estamos usando o Fiddler neste exemplo).
    **Dica** Se você não souber como usar o Fiddler, marque a opção "**eu quero coletar o tráfego de rede e analisá-lo usando o depurador da Web**" na parte inferior.

2. Verifique e analise os logs de sessão para determinar se os cookies fornecidos pelo cliente têm os detalhes de ARRAffinity. Se você não encontrar os detalhes do ARRAffinity, como "**ARRAffinity =** *ARRAffinityValue*" dentro do conjunto de cookies, isso significa que o cliente não está respondendo com o cookie ARRA, que é fornecido pelo gateway de aplicativo.
    Por exemplo:

    ![solução de problemas – afinidade de sessão-problemas-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![solução de problemas – afinidade de sessão-problemas-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

O aplicativo continua a tentar definir o cookie em cada solicitação até receber a resposta.

#### <a name="cause"></a>Causa

Esse problema ocorre porque o Internet Explorer e outros navegadores podem não armazenar ou usar o cookie com uma URL de nome curta.

#### <a name="resolution"></a>Resolução

Para corrigir este problema, aceda ao Gateway de Aplicação através de um FQDN. Por exemplo, use [http://website.com](https://website.com/) ou [http://appgw.website.com](http://appgw.website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Logs adicionais para solucionar problemas

Você pode coletar logs adicionais e analisá-los para solucionar problemas relacionados à afinidade de sessão baseada em cookies

### <a name="analyze-application-gateway-logs"></a>Analisar logs do gateway de aplicativo

Para coletar os logs do gateway de aplicativo, siga as instruções:

Ativar o registo através do portal do Azure

1. No [portal do Azure](https://portal.azure.com/), localize o recurso e clique em **logs de diagnóstico**.

   Para o gateway de aplicativo, três logs estão disponíveis: log de acesso, log de desempenho, log de firewall

2. Para começar a coletar dados, clique em **Ativar diagnóstico**.

   ![solução de problemas – afinidade de sessão-problemas-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. A folha **configurações de diagnóstico** fornece as configurações para os logs de diagnóstico. Neste exemplo, Log Analytics armazena os logs. Clique em **Configurar** em **log Analytics** para definir seu espaço de trabalho. Também pode utilizar os hubs de eventos e uma conta de armazenamento para guardar os registos de diagnóstico.

   ![solução de problemas – afinidade de sessão-problemas-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Confirme as configurações e clique em **salvar**.

   ![solução de problemas – afinidade de sessão-problemas-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Exibir e analisar os logs de acesso do gateway de aplicativo

1. Na portal do Azure na exibição de recursos do gateway de aplicativo, selecione **logs de diagnóstico** na seção **monitoramento** .

   ![solução de problemas – afinidade de sessão-problemas-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. No lado direito, selecione "**ApplicationGatewayAccessLog**" na lista suspensa em **categorias de log.**  

   ![solução de problemas – afinidade de sessão-problemas-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Na lista log de acesso do gateway de aplicativo, clique no log que você deseja analisar e exportar e, em seguida, exporte o arquivo JSON.

4. Converta o arquivo JSON que você exportou na etapa 3 para arquivo CSV e exiba-os no Excel, Power BI ou em qualquer outra ferramenta de visualização de dados.

5. Verifique os seguintes dados:

- **Clientip**– esse é o endereço IP do cliente do cliente que está se conectando.
- **ClientPort** -essa é a porta de origem do cliente que está se conectando para a solicitação.
- **RequestQuery** – indica o servidor de destino que a solicitação foi recebida.
- **Servidor-roteado**: instância de pool de back-end em que a solicitação é recebida.
- **X-AzureApplicationGateway-log-ID**: ID de correlação usada para a solicitação. Ele pode ser usado para solucionar problemas de tráfego nos servidores back-end. Por exemplo: X-AzureApplicationGateway-CACHE-TECLE = 0 & SERVER-ROUTEd = 10.0.2.4.

  - **Servidor-status**: código de resposta http que o gateway de aplicativo recebeu do back-end.

  ![solução de problemas – afinidade de sessão-problemas-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Se você vir dois itens provenientes do mesmo ClientIP e da porta do cliente, e eles forem enviados para o mesmo servidor de back-end, isso significará que o gateway de aplicativo foi configurado corretamente.

Se você vir dois itens provenientes do mesmo ClientIP e da porta do cliente, e eles forem enviados para os diferentes servidores de back-end, isso significa que a solicitação está saltando entre os servidores de back-end, selecione "o**aplicativo está usando afinidade baseada em cookie, mas as solicitações ainda estão chegando entre os servidores back-ends**" na parte inferior para solucionar o problema.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Usar o depurador da Web para capturar e analisar os tráfegos HTTP ou HTTPS

Ferramentas de depuração da Web como o Fiddler podem ajudá-lo a depurar aplicativos Web capturando o tráfego de rede entre os computadores de teste e Internet. Essas ferramentas permitem inspecionar dados de entrada e saída à medida que o navegador os recebe/envia. O Fiddler, neste exemplo, tem a opção HTTP Replay que pode ajudá-lo a solucionar problemas do lado do cliente com aplicativos Web, especialmente para o tipo de problema de autenticação.

Use o depurador da Web de sua escolha. Neste exemplo, usaremos o Fiddler para capturar e analisar os tráfegos http ou HTTPS, siga as instruções:

1. Baixe a ferramenta Fiddler em <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Escolha Fiddler4 se o computador de captura tiver o .NET 4 instalado. Caso contrário, escolha fiddler2.

2. Clique com o botão direito do mouse no executável de instalação e execute como administrador para instalar.

    ![solução de problemas – afinidade de sessão-problemas-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Quando você abre o Fiddler, ele deve iniciar automaticamente a captura de tráfego (Observe a captura no canto inferior esquerdo). Pressione F12 para iniciar ou parar a captura de tráfego.

    ![solução de problemas – afinidade de sessão-problemas-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Provavelmente, você estará interessado em tráfego HTTPS descriptografado e poderá habilitar a descriptografia de HTTPS selecionando **ferramentas** > **Opções Fiddler**e marcando a caixa " **descriptografar tráfego HTTPS**".

    ![solução de problemas – afinidade de sessão-problemas-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Você pode remover sessões não relacionadas anteriores antes de reproduzir o problema clicando em **X** (ícone) > **remover tudo** como a seguinte captura de tela: 

    ![solução de problemas – afinidade de sessão-problemas-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Depois de reproduzir o problema, salve o arquivo para revisão selecionando **arquivo** > **salvar** > **todas as sessões..** . 

    ![solução de problemas – afinidade de sessão-problemas-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Verifique e analise os logs de sessão para determinar qual é o problema.

    Para obter exemplos:

- **Exemplo A:** Você encontra um log de sessão que a solicitação é enviada do cliente e vai para o endereço IP público do gateway de aplicativo, clique nesse log para exibir os detalhes.  No lado direito, os dados na caixa inferior são o que o gateway de aplicativo está retornando ao cliente. Selecione a guia "RAW" e determine se o cliente está recebendo um "**Set-Cookie: ARRAffinity =** *ARRAffinityValue*". Se não houver nenhum cookie, a afinidade de sessão não está definida ou o gateway de aplicativo não está aplicando o cookie de volta ao cliente.

   > [!NOTE]
   > Esse valor de ARRAffinity é o cookie-ID, que o gateway de aplicativo define para que o cliente seja enviado para um servidor de back-end específico.

   ![solução de problemas-afinidade de sessão-problemas-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Exemplo B:** O próximo log de sessão seguido pelo anterior é o cliente que responde de volta ao gateway de aplicativo, que definiu o ARRAAFFINITY. Se o ARRAffinity cookie-id corresponder, o pacote deverá ser enviado para o mesmo servidor back-end que foi usado anteriormente. Verifique as próximas várias linhas da comunicação http para ver se o cookie ARRAffinity do cliente está sendo alterado.

   ![solução de problemas – afinidade de sessão-problemas-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Para a mesma sessão de comunicação, o cookie não deve ser alterado. Marque a caixa superior no lado direito, selecione a guia "cookies" para ver se o cliente está usando o cookie e enviando-o de volta para o gateway de aplicativo. Caso contrário, o navegador do cliente não está mantendo e usando o cookie para conversas. Às vezes, o cliente pode estar.

 

## <a name="next-steps"></a>Passos seguintes

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).
