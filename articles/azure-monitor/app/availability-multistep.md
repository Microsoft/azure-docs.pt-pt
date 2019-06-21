---
title: Monitorizar a sua aplicação web com testes web de vários passos e o Azure Application Insights | Documentos da Microsoft
description: Testes web de vários passos de configuração para monitorizar as aplicações web com o Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d8bfe92af4e8afc4edae76efb2e1cb7b287c7aa9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305177"
---
# <a name="multi-step-web-tests"></a>Testes Web com vários passos

Pode monitorar uma seqüência gravada de URLs e as interações com um Web site através de testes web de vários passos. Este artigo irá guiá-lo pelo processo de criação de um teste web de vários passos com o Visual Studio Enterprise.

> [!NOTE]
> Testes web de vários passos têm custos adicionais associados a eles. Para saber mais consulte a [guia oficial de preços](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="pre-requisites"></a>Pré-requisitos

* Visual Studio 2017 Enterprise ou superior.
* Ferramentas de teste de carga e desempenho de web do Visual Studio.

Para localizar o teste pré-requisito de ferramentas. Iniciar o **instalação do Visual Studio** > **componentes individuais** > **depuração e teste**  >   **Desempenho da Web e ferramentas de teste de carga**.

![Captura de ecrã do instalador do Visual Studio da interface do Usuário com componentes individuais selecionados com uma caixa de verificação junto ao item para desempenho da Web e ferramentas de teste de carga](./media/availability-multistep/web-performance-load-testing.png)

## <a name="record-a-multi-step-web-test"></a>Grave um teste web de vários passos

Para criar um teste com vários passos, registe o cenário com o Visual Studio Enterprise e, em seguida, carregue o registo no Application Insights. Application Insights replays o cenário em intervalos definidos e verifica a resposta.

> [!IMPORTANT]
> * Não pode utilizar funções codificadas ou ciclos no seus testes. O teste tem de estar contido completamente no script .webtest. No entanto, pode utilizar plug-ins standard.
> * Os testes web com vários passos são suportados apenas com carateres em inglês. Se utilizar o Visual Studio noutras linguagens, atualize o ficheiro de definição de teste web para traduzir/excluir carateres não ingleses.

Utilize o Visual Studio Enterprise para guardar uma sessão Web.

1. Crie um desempenho da Web e o projeto de teste de carga. **Arquivo** > **novo** > **projeto** > **Visual C#**   >  **teste**

    ![Novo projeto do Visual Studio da interface do Usuário](./media/availability-multistep/vs-web-performance-and-load-test.png)

2. Abra o `.webtest` de ficheiros e iniciar a gravação.

    ![Teste do Visual Studio gravação da interface do Usuário](./media/availability-multistep/open-web-test.png)

3. Clique nos passos que pretende que o teste para simular como parte da gravação.

    ![Interface do Usuário de gravação do browser](./media/availability-multistep/record.png)

4. Edite o teste para:

    * Adicionar validações para verificar o texto recebido e os códigos de reposta.
    * Remova qualquer interações uneccesary. Também pode remover pedidos dependentes de imagens ou adicionar controlo de sites que não é relevante para si considerar seu teste de sucesso.
    
    Tenha em atenção que só é possível editar o script de teste – pode adicionar código personalizado ou chamar outros testes web. Não insira ciclos no teste. Pode utilizar os plug-ins do teste Web padrão.

5. Execute o teste no Visual Studio para validar e verificar se funciona.

    A execução de testes Web abre um browser e repete as ações que gravou. Certifique-se de que tudo funciona conforme o esperado.

## <a name="upload-the-web-test"></a>Carregar o teste web

1. No portal do Application Insights no painel de disponibilidade, selecione **criar testar** > **tipo de teste** > **teste web de vários passos**.

2. Defina as localizações de teste, a frequência e parâmetros de alerta.

### <a name="frequency--location"></a>Frequência & de localização

|Definição| Explicação
|----|----|----|
|**Frequência de teste**| Define a frequência com que a execução do teste em cada localização de teste. Com uma frequência predefinida de cinco minutos e cinco localizações de teste, o site é testado, em média, a cada minuto.|
|**Localizações de teste**| São os locais de onde os nossos servidores enviam pedidos web ao seu URL. **Nosso número mínimo de localizações de teste recomendado é cinco** e assim garantir que pode distinguir problemas no seu Web site dos problemas de rede. Pode selecionar até 16 localizações.

### <a name="success-criteria"></a>Critérios de êxito

|Definição| Explicação
|----|----|----|
| **Tempo limite de teste** |Reduza este valor para ser alertado de repostas lentas. O teste será contabilizado como uma falha se as respostas do seu site não foram recebidas durante este período. Se tiver selecionado **Pedidos dependentes de análise**, todas as imagens, ficheiros de estilos, scripts e outros recursos dependentes terão de ser recebidos durante este período.|
| **Resposta HTTP** | O código de estado devolvido é contabilizado como um sucesso. 200 é o código que indica que foi devolvida uma página Web normal.|
| **Correspondência do conteúdo** | Uma cadeia de caracteres, como "Bem-vindo!" Podemos testar que uma correspondência sensíveis às maiúsculas e minúsculas ocorre em cada resposta. Tem de ser uma cadeia simples, sem carateres universais. Não se esqueça de que se alterar o conteúdo da página, poderá ter de a atualizar. **Apenas carateres em inglês são suportadas com correspondência do conteúdo** |

### <a name="alerts"></a>Alertas

|Definição| Explicação
|----|----|----|
|**Quase em tempo real (pré-visualização)** | Recomendamos que utilize os alertas de tempo quase real. Configurar este tipo de alerta é feita após a criação de seu teste de disponibilidade.  |
|**Clássico** | Nós já não é recomendado utilizar alertas clássicos para novos testes de disponibilidade.|
|**Limiar de alerta de localização**|Recomendamos um mínimo de 3/5 localizações. A relação ideal entre o limiar de alerta de localização e o número de localizações de teste é **limiar de localização de alerta** = **número de localizações de teste - 2, com um mínimo de cinco localizações de teste.**|

## <a name="advanced-configuration"></a>Configuração Avançada

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Ligar a hora e números aleatórios em seu teste

Suponhamos que está a testar uma ferramenta que obtém dados dependentes da hora, tais como stocks de um feed externo. Ao gravar o teste Web, tem de utilizar horas específicas, embora sejam definidas como parâmetros do teste, StartTime e EndTime.

![Meu captura de ecrã da aplicação das ações formidável](./media/availability-multistep/app-insights-72webtest-parameters.png)

Quando executa o teste, gostaria que a EndTime fosse sempre a hora atual e a StartTime a hora de há 15 minutos.

O plug-in da hora de data de teste de Web proporciona a maneira de lidar com parametrizar tempos.

1. Adicione um plug-in de teste Web para cada valor variável de parâmetro que pretende. Na barra de ferramentas do teste Web, selecione **Adicionar Plug-in de Teste Web**.
    
    ![Adicionar plug-in de teste Web](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    Neste exemplo, utilizamos duas instâncias do Plug-in Data/Hora. Uma instância para “há 15 minutos” e outra para “agora”.

2. Abra as propriedades de cada plug-in. Atribua um nome e defina-o para utilizar a hora atual. Para uma das propriedades, defina Adicionar Minutos = -15.

    ![Parâmetros de contexto](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Nos parâmetros do teste Web, utilize {{nome do plug-in}} para fazer referência a um nome de plug-in.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Agora, carregue o teste no portal. Os valores dinâmicos passarão a ser utilizados sempre que o teste for executado.

### <a name="dealing-with-sign-in"></a>Lidar com início de sessão

Se os seus utilizadores iniciarem sessão na sua aplicação, terá várias opções para simular o início de sessão, de modo a poder testar páginas depois do início de sessão. A abordagem que utiliza depende do tipo de segurança fornecida pela aplicação.

Em todos os casos, deve criar uma conta na sua aplicação apenas para efeitos de teste. Se possível, restrinja as permissões da conta neste teste, para que não haja nenhuma possibilidade de os testes Web afetarem os utilizadores reais.

**Nome de utilizador Simple e a palavra-passe** grave um teste web como habitualmente. Elimine primeiro os cookies.

**Autenticação SAML** utilizar o plug-in SAML disponível para testes web. Acessar o plug-in por...

**Segredo do cliente** se a aplicação tiver uma rota de início de sessão que envolva um segredo do cliente, utilize esse caminho. O Azure Active Directory (AAD) é um exemplo de um serviço que fornece um início de sessão com segredo do cliente. No AAD, o segredo do cliente é a Chave da Aplicação.

Eis um exemplo de teste Web de uma aplicação Web do Azure com uma chave de aplicação:

![Captura de ecrã de exemplo](./media/availability-multistep/client-secret.png)

Obtenha o token do AAD utilizando o segredo do cliente (AppKey).
Extraia o token de portador a partir da resposta.
Chame a API com o token de portador no cabeçalho de autorização.
Certifique-se de que o teste web é um cliente real – ou seja, ele tem sua própria aplicação no AAD - e utilize o clientId + aplicação chave. O serviço em teste também tem sua própria aplicação no AAD: o appID URI desta aplicação é refletido no teste web no campo de recursos.

### <a name="open-authentication"></a>Autenticação Aberta
Um exemplo de Autenticação Aberta é iniciar sessão com a conta Microsoft ou Google. Muitas aplicações que utilizam a OAuth fornecem uma alternativa ao segredo do cliente. Por isso, a primeira tática deve ser investigar essa possibilidade.

Se o teste tiver de iniciar sessão com a OAuth, a abordagem geral será:

Utilizar uma ferramenta como o Fiddler para examinar o tráfego entre o browser, o site de autenticação e a aplicação.
Iniciar duas sessões ou mais com computadores ou browsers diferentes ou com longos intervalos (para permitir a expiração dos tokens).
Ao comparar as diversas sessões, identificar o token devolvido pelo site de autenticação, que é de seguida transmitido ao servidor da aplicação depois do início de sessão.
Gravar um teste Web com o Visual Studio.
Parametrizar os tokens definindo o parâmetro quando o token é devolvido do autenticador e utilizando-o na consulta do site. (O Visual Studio tenta parametrizar o teste, mas não parametriza corretamente os tokens.)

## <a name="troubleshooting"></a>Resolução de problemas

Dedicado [artigo de resolução de problemas](troubleshoot-availability.md).

## <a name="next-steps"></a>Passos Seguintes

* [Alertas de disponibilidade](availability-alerts.md)
* [Testes de web de ping do URL](monitor-web-app-availability.md)
