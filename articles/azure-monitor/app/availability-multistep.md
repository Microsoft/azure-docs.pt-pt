---
title: Monitor com testes web em várias etapas - Azure Application Insights
description: Confiúdedo de testes web em várias etapas para monitorizar as suas aplicações web com Azure Application Insights
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: 0031971dc2ccd2a9e681220dd835f46d33e36dbb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934860"
---
# <a name="multi-step-web-tests"></a>Testes Web com vários passos

Pode monitorizar uma sequência gravada de URLs e interações com um website através de testes web em várias etapas. Este artigo irá acompanhá-lo através do processo de criação de um teste web em várias etapas com a Visual Studio Enterprise.

> [!NOTE]
> Os testes web em várias etapas dependem de ficheiros de webtest do Visual Studio. Foi [anunciado](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) que o Visual Studio 2019 será a última versão com a funcionalidade webtest. É importante entender que, apesar de não serem adicionadas novas funcionalidades, a funcionalidade do Webtest no Visual Studio 2019 ainda se encontra suportada e continuará a ser suportada durante o ciclo de suporte do produto. A equipa de produtos do Azure Monitor abordou questões sobre o futuro dos testes de disponibilidade em várias etapas [aqui.](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)  
> </br>
> Os testes web em várias etapas **não são suportados** na nuvem [do Governo Azure.](../../azure-government/index.yml)


## <a name="pre-requisites"></a>Pré-requisitos

* Visual Studio 2017 Enterprise ou maior.
* Visual Studio web performance e ferramentas de teste de carga.

Para localizar as ferramentas de teste, é pré-requisito. Lançar o **Instalador de Estúdio Visual**  >  **Componentes Individuais**  >  **Depurar e testar** o desempenho web e  >  **ferramentas de teste de carga**.

![Screenshot do instalador visual Doe uI com componentes individuais selecionados com uma caixa de verificação ao lado do item para o desempenho web e ferramentas de teste de carga](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Os testes web em várias etapas têm custos adicionais associados a eles. Para saber mais consulte o [guia oficial de preços.](https://azure.microsoft.com/pricing/details/application-insights/)

## <a name="record-a-multi-step-web-test"></a>Grave um teste web em várias etapas 

> [!WARNING]
> Já não recomendamos a utilização do gravador em várias etapas. O gravador foi desenvolvido para páginas estáticas de HTML com interações básicas, e não fornece uma experiência funcional para páginas web modernas.

Para obter orientações sobre a criação de testes web do Visual Studio consulte a documentação oficial do [Visual Studio 2019](/visualstudio/test/how-to-create-a-web-service-test).

## <a name="upload-the-web-test"></a>Faça o upload do teste web

1. No portal 'Insights de Aplicações' no painel de disponibilidade **selecione Criar** teste de teste de teste web  >  **tipo** de  >  **teste multi-passo .**

2. Desa cos assimte os locais de teste, a frequência e os parâmetros de alerta.

### <a name="frequency--location"></a>Localização & de frequência

|Definição| Explicação
|----|----|----|
|**Frequência de teste**| Define a frequência com que o teste é executado a partir de cada local de teste. Com uma frequência predefinida de cinco minutos e cinco localizações de teste, o site é testado, em média, a cada minuto.|
|**Locais de teste**| São os locais de onde os nossos servidores enviam pedidos web para o seu URL. **O nosso número mínimo de locais de teste recomendados é de cinco** para garantir que pode distinguir problemas no seu website de problemas de rede. Pode selecionar até 16 localizações.

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
|**Clássico** | Já não recomendamos o uso de alertas clássicos para novos testes de disponibilidade.|
|**Limiar de localização de alerta**|Recomendamos um mínimo de 3/5 locais. A relação ideal entre o limiar de localização de alerta e o número de locais de teste é o número de **localização** de alerta  =  **dos locais de teste - 2, com um mínimo de cinco locais de ensaio.**|

## <a name="configuration"></a>Configuração

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Ligar o tempo e os números aleatórios ao seu teste

Suponhamos que está a testar uma ferramenta que obtém dados dependentes da hora, tais como stocks de um feed externo. Ao gravar o teste Web, tem de utilizar horas específicas, embora sejam definidas como parâmetros do teste, StartTime e EndTime.

![A minha incrível imagem de aplicativo de stock](./media/availability-multistep/app-insights-72webtest-parameters.png)

Quando executa o teste, gostaria que a EndTime fosse sempre a hora atual e a StartTime a hora de há 15 minutos.

O Plugin de Data de Teste Web fornece a forma de lidar com os tempos de parametrização.

1. Adicione um plug-in de teste Web para cada valor variável de parâmetro que pretende. Na barra de ferramentas do teste Web, selecione **Adicionar Plug-in de Teste Web**.
    
    ![Adicionar plug-in de teste web](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    Neste exemplo, utilizamos duas instâncias do Plug-in Data/Hora. Uma instância para “há 15 minutos” e outra para “agora”.

2. Abra as propriedades de cada plug-in. Atribua um nome e defina-o para utilizar a hora atual. Para uma das propriedades, defina Adicionar Minutos = -15.

    ![Parâmetros de contexto](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Nos parâmetros do teste Web, utilize {{nome do plug-in}} para fazer referência a um nome de plug-in.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Agora, carregue o teste no portal. Os valores dinâmicos passarão a ser utilizados sempre que o teste for executado.

### <a name="dealing-with-sign-in"></a>Lidar com início de sessão

Se os seus utilizadores iniciarem sessão na sua aplicação, terá várias opções para simular o início de sessão, de modo a poder testar páginas depois do início de sessão. A abordagem que utiliza depende do tipo de segurança fornecida pela aplicação.

Em todos os casos, deve criar uma conta na sua aplicação apenas para efeitos de teste. Se possível, restrinja as permissões da conta neste teste, para que não haja nenhuma possibilidade de os testes Web afetarem os utilizadores reais.

**Nome de utilizador simples e senha** Grave um teste web da maneira habitual. Elimine primeiro os cookies.

**Autenticação SAML**

|Nome da propriedade| Descrição|
|----|-----|
| Público Uri | O público URI para o símbolo SAML.  Este é o URI para o Access Control Service (ACS) – incluindo o espaço de nome ACS e o nome de anfitrião. |
| Senha de certificado | A palavra-passe para o certificado do cliente que dará acesso à chave privada incorporada. |
| Certificado de Cliente  | O valor do certificado de cliente com chave privada no formato codificado Base64. |
| Identificador de Nome | O identificador de nome para o símbolo |
| Não Após | O tempo para o qual o símbolo será válido.  O padrão é de 5 minutos. |
| Não Antes | O tempo para o qual um símbolo criado no passado será válido (para abordar os distorções de tempo).  O predefinido é (negativo) 5 minutos. |
| Nome do parâmetro do contexto do alvo | O parâmetro de contexto que receberá a afirmação gerada. |


**Segredo do cliente** Se a sua aplicação tiver uma rota de login que envolva um segredo de cliente, use essa rota. O Azure Active Directory (AAD) é um exemplo de um serviço que fornece um início de sessão com segredo do cliente. No AAD, o segredo do cliente é a Chave da Aplicação.

Eis um exemplo de teste Web de uma aplicação Web do Azure com uma chave de aplicação:

![Screenshot da amostra](./media/availability-multistep/client-secret.png)

Obtenha o token do AAD utilizando o segredo do cliente (AppKey).
Extraia o token de portador a partir da resposta.
Chame a API com o token de portador no cabeçalho de autorização.
Certifique-se de que o teste web é um cliente real - ou seja, tem a sua própria app em AAD - e use a sua chave de aplicação clientId + . O seu serviço em teste também tem a sua própria aplicação em AAD: o appID URI desta aplicação reflete-se no teste web no campo de recursos.

### <a name="open-authentication"></a>Autenticação Aberta
Um exemplo de Autenticação Aberta é iniciar sessão com a conta Microsoft ou Google. Muitas aplicações que utilizam a OAuth fornecem uma alternativa ao segredo do cliente. Por isso, a primeira tática deve ser investigar essa possibilidade.

Se o teste tiver de iniciar sessão com a OAuth, a abordagem geral será:

Utilizar uma ferramenta como o Fiddler para examinar o tráfego entre o browser, o site de autenticação e a aplicação.
Iniciar duas sessões ou mais com computadores ou browsers diferentes ou com longos intervalos (para permitir a expiração dos tokens).
Ao comparar as diversas sessões, identificar o token devolvido pelo site de autenticação, que é de seguida transmitido ao servidor da aplicação depois do início de sessão.
Gravar um teste Web com o Visual Studio.
Parametrizar os tokens definindo o parâmetro quando o token é devolvido do autenticador e utilizando-o na consulta do site. (O Visual Studio tenta parametrizar o teste, mas não parametriza corretamente os tokens.)

## <a name="troubleshooting"></a>Resolução de problemas

Artigo dedicado [à resolução de problemas.](troubleshoot-availability.md)

## <a name="next-steps"></a>Próximos passos

* [Alertas de Disponibilidade](availability-alerts.md)
* [Testes web de url ping](monitor-web-app-availability.md)
