---
title: Aumentar a resiliência da autenticação e autorização nas aplicações do cliente que desenvolve
titleSuffix: Microsoft identity platform
description: Orientação para aumentar a resiliência da autenticação e autorização na aplicação do cliente utilizando a plataforma de identidade da Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: bc3b041e44fad66a4edc6ff34c0e534dc423de86
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226595"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>Aumentar a resiliência da autenticação e autorização nas aplicações do cliente que desenvolve

Esta secção fornece orientações sobre a construção de resiliência em aplicações de clientes que utilizam a plataforma de identidade da Microsoft e o Azure Ative Directory para assinar nos utilizadores e realizar ações em nome desses utilizadores.

## <a name="use-the-microsoft-authentication-library-msal"></a>Utilize a Biblioteca de Autenticação microsoft (MSAL)

A [Microsoft Authentication Library (MSAL)](../develop/msal-overview.md) é uma parte chave da plataforma de identidade da [Microsoft.](../develop/index.yml) Simplifica e gere a aquisição, gestão, caching e tokens refrescantes, e utiliza as melhores práticas para a resiliência. O MSAL foi concebido para permitir uma solução segura sem que os desenvolvedores tenham de se preocupar com os detalhes da implementação.

MSAL caches tokens e usa um padrão de aquisição de token silencioso. Também serializa automaticamente a cache simbólica em plataformas que fornecem armazenamento seguro de si mesmos como Windows UWP, iOS e Android. Os desenvolvedores podem personalizar o comportamento de serialização ao [utilizarem o Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization), [MSAL.NET,](../develop/msal-net-token-cache-serialization.md) [MSAL para Java](../develop/msal-java-token-cache-serialization.md)e [MSAL para Python](../develop/msal-python-token-cache-serialization.md).

![Imagem de dispositivo com e aplicação usando MSAL para ligar para a Identidade do Microsoft](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

Ao utilizar o MSAL, a aquisição simbólica, refrescante e silenciosa é suportada automaticamente. Você pode usar padrões simples para adquirir os tokens necessários para a autenticação moderna. Apoiamos muitas línguas e pode encontrar uma amostra que corresponda ao seu idioma e cenário na nossa página [Samples.](../develop/sample-v2-code.md)

## <a name="c"></a>[C#](#tab/csharp)

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
return myMSALObj.acquireTokenSilent(request).catch(error => {
    console.warn("silent token acquisition fails. acquiring token using redirect");
    if (error instanceof msal.InteractionRequiredAuthError) {
        // fallback to interaction when silent call fails
        return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
            console.log(tokenResponse);

            return tokenResponse;
        }).catch(error => {
            console.error(error);
        });
    } else {
        console.warn(error);
    }
});
```

---

A MSAL pode, em alguns casos, renovar proativamente as fichas. Quando a Microsoft Identity emite um símbolo de longa data, pode enviar informações ao cliente durante o momento ideal para refrescar o token ("refresh \_ in"). A MSAL irá atualizar proativamente o símbolo com base nesta informação. A aplicação continuará a funcionar enquanto o token antigo é válido, mas terá um prazo mais longo para fazer outra aquisição de token bem sucedida.

### <a name="stay-up-to-date"></a>Fique atualizado

Os desenvolvedores devem ter um processo de atualização para a versão mais recente do MSAL. A autenticação faz parte da segurança da sua aplicação e a sua aplicação precisa de se manter atual com as melhorias de segurança contidas nas novas versões MSAL. Esta é geralmente uma boa prática para bibliotecas em desenvolvimento contínuo e fazê-lo irá garantir que você tem o código mais atualizado no que diz respeito à resiliência da app. À medida que a Microsoft Identity continua a inovar sobre formas de as aplicações serem mais resistentes, as aplicações que utilizam o mais recente MSAL serão as mais preparadas para construir sobre estas inovações.

[Verifique a versão mais recente MSAL.js e vertas de lançamento](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Consulte as últimas notas de lançamento do MSAL .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[Consulte a versão mais recente do MSAL Python e vertas de lançamento](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[Consulte a versão mais recente do MSAL Java e vertas de lançamento](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[Consulte as últimas versões msal iOS e macOS e as notas de lançamento](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[Confira a mais recente versão do MSAL Android e vertas de lançamento](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[Consulte a mais recente versão MSAL Angular e ver notas de lançamento](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Consulte a versão mais recente do Microsoft.Identity.Web e ver notas de lançamento](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="use-resilient-patterns-for-token-handling"></a>Use padrões resilientes para manuseamento de símbolos

Se não estiver a utilizar o MSAL, pode utilizar estes padrões resilientes para o manuseamento de fichas. Estas boas práticas são implementadas automaticamente pela biblioteca MSAL. 

Em geral, uma aplicação que utilize a autenticação moderna chamará um ponto final para recuperar fichas que autenticam o utilizador ou autorizar a aplicação a chamar APIs protegidas. A MSAL destina-se a lidar com os detalhes da autenticação e implementa vários padrões para melhorar a resiliência deste processo. Utilize as orientações nesta secção para implementar as melhores práticas se optar por utilizar uma biblioteca diferente da MSAL. Se utilizar o MSAL, obtém todas estas boas práticas gratuitamente, uma vez que a MSAL as implementa automaticamente.

### <a name="cache-tokens"></a>Fichas de cache

As aplicações devem ser adequadamente cache tokens recebidos da Microsoft Identity. Quando a sua aplicação recebe fichas, a resposta HTTP que contém os tokens também contém uma propriedade "expires_in" que diz à aplicação quanto tempo para cache, e reutilizar, o token. É importante que as aplicações utilizem a propriedade "expires_in" para determinar o tempo de vida útil do token. A aplicação nunca deve tentar descodificar um token de acesso API.

![Uma aplicação que faz uma chamada para a identidade da Microsoft, mas a chamada passa por uma cache simbólica no dispositivo que executa a aplicação](media/resilience-client-app/token-cache.png)

A utilização do token em cache impede o tráfego desnecessário entre a sua app e a Microsoft Identity, e torna a sua app menos suscetível a falhas de aquisição simbólicas, reduzindo o número de chamadas de aquisição simbólicas. Os tokens em cache também melhoram o desempenho da sua aplicação, uma vez que a app precisa de bloquear a aquisição de fichas menos. O seu utilizador pode manter-se inscrito na sua aplicação durante a duração da vida útil desse símbolo.

### <a name="serialize-and-persist-tokens"></a>Serialize e persista fichas

As aplicações devem serializar de forma segura a sua cache simbólica para persistirem os tokens entre as instâncias da aplicação. As fichas podem ser reutilizadas desde que estejam dentro da sua vida útil. [Os tokens de atualização](../develop/v2-oauth2-auth-code-flow.md#refresh-the-access-token)- e, cada vez mais, [os tokens de acesso](../develop/access-tokens.md)- são emitidos por muitas horas. Este tempo válido pode abranger um utilizador que inicia a sua aplicação muitas vezes. Quando a sua aplicação começar, deverá verificar se existe um acesso válido ou um token de atualização que possa ser usado. Isto irá aumentar a resiliência e desempenho da aplicação, uma vez que evita quaisquer chamadas desnecessárias para a Identidade da Microsoft.

![Uma aplicação que faz uma chamada para a identidade da Microsoft, mas a chamada passa por uma cache simbólica, bem como uma loja simbólica no dispositivo que executa a aplicação](media/resilience-client-app/token-store.png)

O armazenamento de fichas persistentes deve ser acedido e encriptado ao utilizador ou identidade do processo. Em plataformas como mobile, Windows e Mac, o desenvolvedor deve aproveitar as capacidades incorporadas para armazenar credenciais.

### <a name="acquire-tokens-silently"></a>Adquira fichas silenciosamente

O processo de autenticação de um utilizador ou de recuperação de autorização para chamar uma API pode requerer vários passos na Identidade da Microsoft. Por exemplo, quando o utilizador se inscreve pela primeira vez, poderá ter de introduzir credenciais e efetuar uma autenticação multi-factor através de uma mensagem de texto. Cada passo adiciona uma dependência do recurso que fornece esse serviço. A melhor experiência para o utilizador, e para aquele com menos dependências, é tentar adquirir um símbolo silenciosamente para evitar estes passos extra antes de solicitar a interação do utilizador.

![Diagrama mostrando os vários serviços dentro da Identidade da Microsoft que podem ter de ser executados para completar o processo de autenticação ou autorização de um utilizador](media/resilience-client-app/external-dependencies.png)

A aquisição de um símbolo começa silenciosamente com um token válido da cache simbólica da aplicação. Se não houver um token válido disponível, a aplicação deve tentar adquirir um token usando um token de atualização, se disponível, e o ponto final simbólico. Se nenhuma destas opções estiver disponível, a aplicação deve adquirir um token usando o parâmetro "prompt=none". Isto utilizará o ponto final de autorização, mas não mostrará qualquer UI ao utilizador. Se a Microsoft Identity puder fornecer um símbolo à aplicação sem interagir com o utilizador, irá fornecer. Se nenhum destes métodos resultar num token, então um utilizador terá de reautenticar-se interactivamente.

> [!NOTE]
> Em geral, as aplicações devem evitar o uso de solicitações como "login" e "consentimento", uma vez que forçarão a interação do utilizador mesmo quando não é necessária qualquer interação.

## <a name="handle-service-responses-properly"></a>Lidar corretamente com as respostas de serviço

Embora as aplicações devam lidar com todas as respostas de erro, existem algumas respostas que podem ter impacto na resiliência. Se a sua aplicação receber um código de resposta HTTP 429, Demasiados Pedidos, a Microsoft Identity está a estrangular os seus pedidos. Se a sua aplicação continuar a fazer muitos pedidos, continuará a ser estrangulada impedindo que a sua app receba fichas. A sua aplicação não deve tentar adquirir um token novamente até que, em segundos, no campo de resposta Retry-After tenha passado. Receber uma resposta 429 é muitas vezes uma indicação de que a aplicação não está a caching e a reutilizar tokens corretamente. Os desenvolvedores devem rever como os tokens são em cache e reutilizados na aplicação.

Quando uma aplicação recebe um código de resposta HTTP 5xx, a aplicação não deve introduzir um ciclo de repetição rápida. Quando presente, o pedido deve honrar o mesmo Retry-After manuseamento como faz para uma resposta 429. Se não for fornecida Retry-After cabeçalho pela resposta, recomendamos a implementação de uma retração exponencial com a primeira repetição pelo menos 5 segundos após a resposta.

Quando um pedido de pedidos não deve voltar a tentar imediatamente. Implemente uma retração exponencial com a primeira repetição pelo menos 5 segundos após a resposta.

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>Avaliar opções para recuperar informações relacionadas com a autorização

Muitas aplicações e APIs precisam de informações específicas sobre o utilizador para tomar decisões de autorização. Há algumas maneiras de uma aplicação obter esta informação. Cada método tem as suas vantagens e desvantagens. Os desenvolvedores devem pesá-los para determinar qual a melhor estratégia para a resiliência na sua app.

### <a name="tokens"></a>Tokens

Fichas de identidade (ID) e fichas de acesso contêm alegações padrão que fornecem informações sobre o assunto. Estes estão [documentados em fichas de identificação da plataforma](../develop/id-tokens.md) de identidade da Microsoft e [fichas de acesso à plataforma de identidade da Microsoft.](../develop/access-tokens.md) Se a informação de que a sua aplicação precisa já se encontra no token, então a técnica mais eficiente para recuperar esses dados é usar as alegações simbólicas, uma vez que irá guardar a escuta de uma chamada de rede adicional para recuperar informações separadamente. Menos chamadas de rede significam maior resiliência global para a aplicação.

> [!NOTE]
> Algumas aplicações ligam para o ponto final do UserInfo para recuperar reclamações sobre o utilizador que autenticou. A informação disponível no token de ID que a sua aplicação pode receber é um superconjunto da informação que pode obter do ponto final do UserInfo. A sua aplicação deve utilizar o token de ID para obter informações sobre o utilizador em vez de ligar para o ponto final do UserInfo.

Um desenvolvedor de aplicações pode aumentar as reclamações padrão de token com [reivindicações opcionais.](../develop/active-directory-optional-claims.md) Uma reivindicação opcional comum são [os grupos.](../develop/active-directory-optional-claims.md#configuring-groups-optional-claims) Existem várias formas de adicionar reivindicações de grupo. A opção "Grupo de Aplicação" inclui apenas grupos designados para a aplicação. As opções "Todos" ou "Grupos de segurança" incluem grupos de todas as aplicações no mesmo inquilino, que podem adicionar muitos grupos ao token. É importante avaliar o efeito no seu caso, uma vez que pode potencialmente anular a eficiência obtida por solicitar grupos no token, causando inchaço simbólico e até mesmo exigindo chamadas adicionais para obter a lista completa de grupos.

Em vez de usar grupos no seu token, pode em vez disso usar e incluir funções de aplicações. Os desenvolvedores podem definir [funções](../develop/howto-add-app-roles-in-azure-ad-apps.md) de aplicativo para as suas apps e APIs que o cliente pode gerir a partir do seu diretório usando o portal ou APIs. Os IT Pros podem então atribuir funções a diferentes utilizadores e grupos para controlar quem tem acesso a que conteúdo e funcionalidade. Quando um token é emitido para a aplicação ou API, as funções atribuídas ao utilizador estarão disponíveis na reivindicação de funções no token. Obter esta informação diretamente em um símbolo pode salvar chamadas de APIs adicionais.

Finalmente, os administradores de TI também podem adicionar reclamações com base em informações específicas de um inquilino. Por exemplo, uma empresa pode ter uma extensão para ter um ID de utilizador específico da empresa.

Em todos os casos, a adição de informação do diretório diretamente a um token pode ser eficiente e aumentar a resiliência das aplicações, reduzindo o número de dependências que a app tem. Por outro lado, não aborda quaisquer questões de resiliência por não poder adquirir um símbolo. Só deve adicionar reclamações opcionais para os principais cenários da sua aplicação. Se a aplicação necessitar de informação apenas para a funcionalidade de administração, então o melhor é que a aplicação obtenha essa informação apenas conforme necessário.

### <a name="microsoft-graph"></a>Microsoft Graph

O Microsoft Graph fornece um ponto final unificado da API para aceder aos dados da Microsoft 365 que descrevem os padrões de produtividade, identidade e segurança numa organização. As aplicações que utilizam o Microsoft Graph podem potencialmente utilizar qualquer uma das informações através do Microsoft 365 para decisões de autorização.

As aplicações requerem apenas um único token para aceder a todos os Microsoft 365. Isto é mais resistente do que usar as APIs mais antigas que são específicas para componentes da Microsoft 365, como o Microsoft Exchange ou o Microsoft SharePoint, onde são necessários vários tokens.

Ao utilizar as APIs do Microsoft Graph, sugerimos que utilize um [Microsoft Graph SDK](/graph/sdks/sdks-overview). Os SDKs do Microsoft Graph são projetados para simplificar a construção de aplicações de alta qualidade, eficientes e resistentes que acedem ao Microsoft Graph.

Para decisões de autorização, os desenvolvedores devem considerar quando usar as reclamações disponíveis num token como uma alternativa a algumas chamadas do Microsoft Graph. Como mencionado acima, os desenvolvedores poderiam solicitar grupos, funções de aplicação e reivindicações opcionais nos seus tokens. Em termos de resiliência, a utilização do Microsoft Graph para autorização requer chamadas de rede adicionais que dependem da Identidade do Microsoft (para obter o token para aceder ao Microsoft Graph) bem como o próprio Microsoft Graph. No entanto, se a sua aplicação já se baseia no Microsoft Graph como a sua camada de dados, então confiar no Gráfico para autorização não é um risco adicional a assumir.

## <a name="use-broker-authentication-on-mobile-devices"></a>Utilize a autenticação do corretor em dispositivos móveis

Nos dispositivos móveis, a utilização de um corretor de autenticação como o Microsoft Authenticator melhorará a resiliência. O corretor adiciona benefícios acima do que está disponível com outras opções, como o navegador do sistema ou um WebView incorporado. O corretor de autenticação pode utilizar um [token de atualização primária](../devices/concept-primary-refresh-token.md) (PRT) que contém reclamações sobre o utilizador e o dispositivo e pode ser usado para obter fichas de autenticação para aceder a outras aplicações a partir do dispositivo. Quando um PRT é usado para solicitar o acesso a uma aplicação, o seu dispositivo e as alegações de MFA são confiáveis pela Azure AD. Isto aumenta a resiliência evitando passos adicionais para autenticar novamente o dispositivo. Os utilizadores não serão desafiados com várias solicitações de MFA no mesmo dispositivo, aumentando assim a resiliência, reduzindo as dependências dos serviços externos e melhorando a experiência do utilizador.

![Uma aplicação que faz uma chamada para a identidade da Microsoft, mas a chamada passa por uma cache simbólica, bem como uma loja simbólica e um Corretor de Autenticação no dispositivo que executa a aplicação](media/resilience-client-app/authentication-broker.png)

A autenticação do corretor é suportada automaticamente pela MSAL. Pode encontrar mais informações sobre a utilização da autenticação intermediada nas seguintes páginas:

- [Configure SSO em macOS e iOS](../develop/single-sign-on-macos-ios.md#sso-through-authentication-broker-on-ios)
- [Como ativar o SSO cross-app no Android usando o MSAL](../develop/msal-android-single-sign-on.md)

## <a name="adopt-continuous-access-evaluation"></a>Adotar Avaliação de Acesso Contínuo

[A Avaliação contínua de Acesso (CAE)](../conditional-access/concept-continuous-access-evaluation.md) é um desenvolvimento recente que pode aumentar a segurança e resiliência da aplicação com fichas de longa duração. O CAE é um padrão emergente da indústria que está a ser desenvolvido no Grupo de Trabalho de Sinais e Eventos Partilhados da Fundação OpenID. Com o CAE, um símbolo de acesso pode ser revogado com base em [eventos críticos](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) e [avaliação de políticas](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview), em vez de depender de uma vida útil curta. Para algumas APIs de recursos, porque o risco e a política são avaliados em tempo real, o CAE pode aumentar substancialmente a vida útil simbólica até 28 horas. À medida que as APIs de recursos e as aplicações adotam o CAE, a Microsoft Identity poderá emitir tokens de acesso que sejam revogáveis e sejam válidos por longos períodos de tempo. Estes tokens de longa duração serão proactivamente renovados pela MSAL.

Enquanto o CAE está em fase inicial, é possível [desenvolver aplicações de clientes hoje que beneficiarão do CAE](../develop/app-resilience-continuous-access-evaluation.md) quando os recursos (APIs) a aplicação utiliza a AE. À medida que mais recursos adotam CAE, a sua aplicação poderá adquirir fichas ativadas cae para esses recursos também. A Microsoft Graph API e [microsoft Graph SDKs](/graph/sdks/sdks-overview), irá pré-visualizar a capacidade CAE no início de 2021. Se quiser participar na pré-visualização pública do Microsoft Graph com CAE, pode informar-nos que está interessado aqui: [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview) .

Se desenvolver APIs de recursos, encorajamo-lo a participar nos [Sinais e Eventos Partilhados WG.](https://openid.net/wg/sse/) Estamos a trabalhar com este grupo para permitir a partilha de eventos de segurança entre a Microsoft Identity e os fornecedores de recursos.

## <a name="next-steps"></a>Passos seguintes

- [Como utilizar a Avaliação contínua de Acessos permitiu APIs nas suas aplicações](../develop/app-resilience-continuous-access-evaluation.md)
- [Construir resiliência em aplicações daemon](resilience-daemon-app.md)
- [Construa resiliência na sua infraestrutura de gestão de identidade e acessos](resilience-in-infrastructure.md)
- [Construa resiliência nos seus sistemas CIAM](resilience-b2c.md)
