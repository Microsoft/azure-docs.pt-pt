---
title: Monitor com testes na Web de várias etapas-insights de Aplicativo Azure
description: Configurar testes na Web de várias etapas para monitorar seus aplicativos Web com o Aplicativo Azure insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/23/2019
ms.reviewer: sdash
ms.openlocfilehash: 8e630f324a7a0ebdfcc74941e760b80fabefa8d3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928958"
---
# <a name="multi-step-web-tests"></a>Testes Web de vários passos

Você pode monitorar uma sequência registrada de URLs e interações com um site por meio de testes na Web de várias etapas. Este artigo explicará o processo de criação de um teste na Web de várias etapas com Visual Studio Enterprise.

> [!NOTE]
> Testes na Web de várias etapas dependem de arquivos do Visual Studio WebTest. Foi [anunciado](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) que o Visual Studio 2019 será a última versão com a funcionalidade WebTest. É importante entender que, embora nenhum novo recurso seja adicionado, a funcionalidade do WebTest no Visual Studio 2019 ainda é suportada e continuará a ter suporte durante o ciclo de vida do suporte do produto. A equipe de produto Azure Monitor resolveu perguntas sobre o futuro de testes de disponibilidade de várias etapas [aqui](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101).  

## <a name="pre-requisites"></a>Pré-requisitos

* Visual Studio 2017 Enterprise ou superior.
* Ferramentas de teste de carga e desempenho na Web do Visual Studio.

Para localizar o pré-requisito das ferramentas de teste. Inicie o **Instalador do Visual Studio** > **componentes individuais** > **depuração e teste** > **ferramentas de teste de carga e desempenho da Web**.

![Captura de tela da interface do usuário do instalador do Visual Studio com componentes individuais selecionados com uma caixa de seleção ao lado do item para ferramentas de teste de carga e desempenho na Web](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Testes na Web de várias etapas têm custos adicionais associados a eles. Para saber mais, consulte o [Guia de preços oficial](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Registrar um teste na Web de várias etapas 

> [!WARNING]
> Não é mais recomendável usar o gravador de várias etapas. O gravador foi desenvolvido para páginas HTML estáticas com interações básicas e não fornece uma experiência funcional para páginas da Web modernas.

Para obter orientação sobre como criar testes na Web do Visual Studio, consulte a [documentação oficial do visual studio 2019](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019).

## <a name="upload-the-web-test"></a>Carregar o teste na Web

1. No portal de Application Insights no painel disponibilidade, selecione **criar teste** > **tipo de teste** > **teste na Web de várias etapas**.

2. Defina os locais de teste, a frequência e os parâmetros de alerta.

### <a name="frequency--location"></a>Local de & de frequência

|Definição| Explicação
|----|----|----|
|**Frequência de teste**| Define a frequência com que o teste é executado a partir de cada local de teste. Com uma frequência predefinida de cinco minutos e cinco localizações de teste, o site é testado, em média, a cada minuto.|
|**Locais de teste**| São os locais de onde nossos servidores enviam solicitações da Web para sua URL. **Nosso número mínimo de locais de teste recomendados é cinco** para garantir que você possa distinguir problemas em seu site por meio de problemas de rede. Pode selecionar até 16 localizações.

### <a name="success-criteria"></a>Critérios de sucesso

|Definição| Explicação
|----|----|----|
| **Tempo limite do teste** |Diminua esse valor para ser alertado sobre respostas lentas. O teste será contabilizado como uma falha se as respostas do seu site não foram recebidas durante este período. Se tiver selecionado **Pedidos dependentes de análise**, todas as imagens, ficheiros de estilos, scripts e outros recursos dependentes terão de ser recebidos durante este período.|
| **Resposta HTTP** | O código de status retornado que é contado como êxito. 200 é o código que indica que foi devolvida uma página Web normal.|
| **Correspondência de conteúdo** | Uma cadeia de caracteres, como "bem-vindo!" Podemos testar que uma correspondência sensíveis às maiúsculas e minúsculas ocorre em cada resposta. Tem de ser uma cadeia simples, sem carateres universais. Não se esqueça de que se alterar o conteúdo da página, poderá ter de a atualizar. **Somente caracteres em inglês têm suporte com correspondência de conteúdo** |

### <a name="alerts"></a>Alertas

|Definição| Explicação
|----|----|----|
|**Quase em tempo real (visualização)** | É recomendável usar alertas quase em tempo real. A configuração desse tipo de alerta é feita após a criação do teste de disponibilidade.  |
|**Clássico** | Não recomendamos o uso de alertas clássicos para novos testes de disponibilidade.|
|**Limite de local de alerta**|Recomendamos um mínimo de 3/5 locais. A relação ideal entre o limite de local de alerta e o número de locais de teste é o **limite de local de alerta** = **número de locais de teste-2, com um mínimo de cinco locais de teste.**|

## <a name="configuration"></a>Configuração

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Tempo de conexão e números aleatórios em seu teste

Suponhamos que está a testar uma ferramenta que obtém dados dependentes da hora, tais como stocks de um feed externo. Ao gravar o teste Web, tem de utilizar horas específicas, embora sejam definidas como parâmetros do teste, StartTime e EndTime.

![Captura de tela de meu aplicativo de ações incríveis](./media/availability-multistep/app-insights-72webtest-parameters.png)

Quando executa o teste, gostaria que a EndTime fosse sempre a hora atual e a StartTime a hora de há 15 minutos.

O plug-in de data e hora do teste na Web fornece a maneira de lidar com os tempos de parametrização.

1. Adicione um plug-in de teste Web para cada valor variável de parâmetro que pretende. Na barra de ferramentas do teste Web, selecione **Adicionar Plug-in de Teste Web**.
    
    ![Adicionar plug-in de teste na Web](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    Neste exemplo, utilizamos duas instâncias do Plug-in Data/Hora. Uma instância para “há 15 minutos” e outra para “agora”.

2. Abra as propriedades de cada plug-in. Atribua um nome e defina-o para utilizar a hora atual. Para uma das propriedades, defina Adicionar Minutos = -15.

    ![Parâmetros de contexto](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Nos parâmetros do teste Web, utilize {{nome do plug-in}} para fazer referência a um nome de plug-in.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Agora, carregue o teste no portal. Os valores dinâmicos passarão a ser utilizados sempre que o teste for executado.

### <a name="dealing-with-sign-in"></a>Lidar com início de sessão

Se os seus utilizadores iniciarem sessão na sua aplicação, terá várias opções para simular o início de sessão, de modo a poder testar páginas depois do início de sessão. A abordagem que utiliza depende do tipo de segurança fornecida pela aplicação.

Em todos os casos, deve criar uma conta na sua aplicação apenas para efeitos de teste. Se possível, restrinja as permissões da conta neste teste, para que não haja nenhuma possibilidade de os testes Web afetarem os utilizadores reais.

**Nome de usuário e senha simples** Registre um teste na Web da maneira usual. Elimine primeiro os cookies.

**Autenticação SAML**

|Nome da propriedade| Descrição|
|----|-----|
| URI do público | O URI do público-alvo do token SAML.  Esse é o URI para o serviço de controle de acesso (ACS) – incluindo o nome do host e o namespace do ACS. |
| Senha do certificado | A senha para o certificado de cliente que concederá acesso à chave privada inserida. |
| Certificado do cliente  | O valor do certificado de cliente com a chave privada no formato codificado em base64. |
| Identificador de Nome | O identificador de nome para o token |
| Não Após | O TimeSpan para o qual o token será válido.  O padrão é 5 minutos. |
| Não Antes | O TimeSpan para o qual um token criado no passado será válido (para endereçar distorções de tempo).  O padrão é (negativo) 5 minutos. |
| Nome do parâmetro de contexto de destino | O parâmetro de contexto que receberá a declaração gerada. |


**Segredo do cliente** Se seu aplicativo tiver uma rota de entrada que envolva um segredo do cliente, use essa rota. O Azure Active Directory (AAD) é um exemplo de um serviço que fornece um início de sessão com segredo do cliente. No AAD, o segredo do cliente é a Chave da Aplicação.

Eis um exemplo de teste Web de uma aplicação Web do Azure com uma chave de aplicação:

![Captura de tela de exemplo](./media/availability-multistep/client-secret.png)

Obtenha o token do AAD utilizando o segredo do cliente (AppKey).
Extraia o token de portador a partir da resposta.
Chame a API com o token de portador no cabeçalho de autorização.
Verifique se o teste na Web é um cliente real, ou seja, se ele tem seu próprio aplicativo no AAD, e use sua chave de aplicativo clientId +. Seu serviço em teste também tem seu próprio aplicativo no AAD: o URI do appID deste aplicativo é refletido no teste na Web no campo de recurso.

### <a name="open-authentication"></a>Autenticação Aberta
Um exemplo de Autenticação Aberta é iniciar sessão com a conta Microsoft ou Google. Muitas aplicações que utilizam a OAuth fornecem uma alternativa ao segredo do cliente. Por isso, a primeira tática deve ser investigar essa possibilidade.

Se o teste tiver de iniciar sessão com a OAuth, a abordagem geral será:

Utilizar uma ferramenta como o Fiddler para examinar o tráfego entre o browser, o site de autenticação e a aplicação.
Iniciar duas sessões ou mais com computadores ou browsers diferentes ou com longos intervalos (para permitir a expiração dos tokens).
Ao comparar as diversas sessões, identificar o token devolvido pelo site de autenticação, que é de seguida transmitido ao servidor da aplicação depois do início de sessão.
Gravar um teste Web com o Visual Studio.
Parametrizar os tokens definindo o parâmetro quando o token é devolvido do autenticador e utilizando-o na consulta do site. (O Visual Studio tenta parametrizar o teste, mas não parametriza corretamente os tokens.)

## <a name="troubleshooting"></a>Resolução de problemas

[Artigo de solução de problemas](troubleshoot-availability.md)dedicado.

## <a name="next-steps"></a>Passos seguintes

* [Alertas de disponibilidade](availability-alerts.md)
* [Testes da Web de ping de URL](monitor-web-app-availability.md)
