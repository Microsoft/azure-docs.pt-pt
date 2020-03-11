---
title: Proteja o backend da SPA com o OAuth 2.0 utilizando o Azure Ative Directory B2C e a Azure API Management.
description: Proteja um API com OAuth 2.0 utilizando o Azure Ative Directory B2C, Azure API Management e Easy Auth para ser chamado de um JavaScript SPA.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new
ms.openlocfilehash: fde48d63bd343fbed1f82e60819131ffb043a795
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967638"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Proteja o backend da SPA com o OAuth 2.0, o Diretório Ativo Azure B2C e a Azure API Management

Este cenário mostra-lhe como configurar a sua instância de Gestão API Azure para proteger uma API.
Usaremos o protocolo OAuth 2.0 com o Azure AD B2C, ao lado da API Management para garantir um backend de funções Azure usando a EasyAuth.

## <a name="aims"></a>Objetivos
Vamos ver como a API Management pode ser usada num cenário simplificado com funções Azure e Azure AD B2C. Irá criar uma aplicação JavaScript (JS) chamando uma API, que assina em utilizadores com Azure AD B2C. Em seguida, você usará as funcionalidades de política validada-jwt da API Management para proteger a API Backend.

Para defesa em profundidade, usamos a EasyAuth para validar o token novamente dentro da API traseira.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir os passos deste artigo, deve ter:
* Uma conta de armazenamento V2 de propósito geral (StorageV2) para acolher a aplicação de página única JS frontal
* Uma instância de Gestão API Azure 
* Uma aplicação vazia da Função Azure (execução do tempo de funcionamento do Núcleo V2.NET, num Plano de Consumo do Windows) para acolher a chamada API
* Um inquilino Azure AD B2C, ligado a uma subscrição 

Embora, na prática, utilize recursos na mesma região em cargas de trabalho de produção, para este artigo como fazer a região de implantação não é importante.

## <a name="overview"></a>Descrição geral
Aqui está uma ilustração dos componentes em uso e do fluxo entre eles uma vez que este processo está concluído.
![Componentes em uso e fluxo](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Componentes em uso e fluxo")

Aqui está uma visão geral rápida dos passos:

1. Criar as aplicações Azure AD B2C (Frontend, API Management) e API com âmbitos e conceder acesso à API
1. Crie o signo ou inscreva-se em políticas para permitir que os utilizadores se inscrevam com o Azure AD B2C 
1. Configure API Management com os novos IDs e chaves do cliente Azure AD B2C para ativar a autorização do utilizador OAuth2 na Consola de Desenvolvimento
1. Construir a API função
1. Configure a Função API para ativar a EasyAuth com o novo Id e Chaves do Cliente Azure AD AD B2C e bloqueie a APIM VIP 
1. Construir a Definição API em Gestão API
1. Configurar o Oauth2 para a configuração da API Management API
1. Estabeleça a política **CORS** e adicione a política **validada-jwt** para validar o token OAuth para cada pedido de entrada
1. Construir a aplicação de chamada para consumir a API
1. Faça upload da amostra JS SPA
1. Configure a App de Cliente JS da amostra com os novos ID e chaves do cliente Azure AD AD B2C 
1. Testar a Aplicação do Cliente

## <a name="configure-azure-ad-b2c"></a>Configure Azure AD B2C 
Abra a lâmina Azure AD B2C no portal e faça os seguintes passos.
1. Selecione o separador **Aplicações** 
1. Clique no botão 'Adicionar' e crie três aplicações para os seguintes fins
   * O Cliente Frontend.
   * A API função backend.
   * [Opcional] O portal de desenvolvimento da API Management (a menos que esteja a gerir a Azure API Management no nível de consumo, mais sobre este cenário mais tarde).
1. Definir WebApp / Web API para todas as 3 aplicações e definir 'Permitir fluxo implícito' para sim apenas para o Cliente Frontend.
1. Agora detete o App ID URI, escolha algo único e relevante para o serviço que está sendo criado.
1. Utilize os espaços reservados para os urls de resposta por enquanto, como https://localhost, atualizaremos esses urls mais tarde.
1. Clique em 'Criar', e depois repita os passos 2-5 para cada uma das três aplicações acima, registando o AppID URI, nome e ID de aplicação para posterior utilização para as três aplicações.
1. Abra a Aplicação do Portal de Desenvolvimento de Gestão da API a partir da lista de aplicações e selecione o separador *Keys* (em geral) e, em seguida, clique em 'Gerar Chave' para gerar uma chave auth
1. Ao clicar em guardar, grave a chave em algum lugar seguro para posterior utilização - note que este lugar é a única hipótese que você terá de visualizar e copiar esta chave.
1. Selecione agora o separador *de âmbitos publicados* (sob acesso API)
1. Crie e nomeie uma margem para a sua API função e grave o Scope e povoou o Valor total do âmbito e, em seguida, clique em 'Guardar'.
   > [!NOTE]
   > Os âmbitos Do Azure AD B2C são efetivamente permissões dentro da sua API a que outras aplicações podem solicitar acesso através da lâmina de acesso API a partir das suas aplicações, efetivamente acabou de criar permissões de aplicação para a sua chamada API.
1. Abra as outras duas aplicações e, em seguida, olhe sob o separador *API Access.*
1. Conceda-lhes acesso ao âmbito da API backend e ao padrão que já lá estava ("Aceda ao perfil do utilizador").
1. Gere-lhes uma chave cada uma selecionando o separador *Keys* em 'General' para gerar uma tecla auth e gravar essas teclas em algum lugar seguro para mais tarde.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Criar um fluxo de utilizador "Inscreva-se ou inscreva-se"
1. Volte à raiz (ou 'Visão geral') da lâmina Azure AD B2C 
1. Em seguida, selecione "Fluxos de utilizador (Políticas)" e clique em "Novo fluxo de utilizador"
1. Escolha o tipo de fluxo do utilizador 'Inscreva-se e inscreva-se'
1. Dê um nome à apólice e grave-o para mais tarde.
1. Em seguida, em 'Fornecedores de identidade', verifique 'User ID sign up' (isto pode dizer 'E-mail inscreva-se') e clique OK. 
1. Em 'Atributos e reclamações do utilizador', clique em 'Mostrar Mais...' em seguida, escolha as opções de reclamação que deseja que os seus utilizadores entrem e tenham devolvido no token. Verifique pelo menos 'Display Name' e 'Email Address' para recolher e devolver, e clique em 'OK', em seguida, clique em 'Criar'.
1. Selecione a política que criou na lista e, em seguida, clique no botão 'Executar o fluxo do utilizador'.
1. Esta ação abrirá a lâmina de fluxo do utilizador de execução, selecionará a aplicação frontend e gravará o endereço do domínio b2clogin.com que é mostrado sob o dropdown para 'Select domain'.
1. Clique no link na parte superior para abrir o "conhecido ponto final de configuração aberta", e grave os valores authorization_endpoint e token_endpoint bem como o valor do próprio link como o conhecido ponto final de configuração aberta.

   > [!NOTE]
   > As Políticas B2C permitem-lhe expor os pontos finais de login Azure AD B2C para poder capturar diferentes componentes de dados e iniciar sessão nos utilizadores de diferentes formas. Neste caso, configuramos um sinal ou sinal em endpoint, que expôs um ponto final de configuração bem conhecido, especificamente a nossa política criada foi identificada no URL pelo parâmetro p=.
   > 
   > Uma vez feito isto – tem agora uma plataforma funcional de identidade Business to Consumer que irá contratar utilizadores em várias aplicações. 
   > Se quiser clicar no botão 'Executar o fluxo do utilizador' aqui (para passar pelo sinal de inscrição ou iniciar o processo) e sentir o que fará na prática, mas o passo de reorientação no final falhará uma vez que a aplicação ainda não foi implementada.

## <a name="build-the-function-api"></a>Construir a função API
1. Volte para o seu inquilino AD Azure padrão no portal Azure para que possamos configurar itens na sua subscrição novamente 
1. Vá à lâmina de Aplicações de Funções do portal Azure, abra a sua aplicação de função vazia e, em seguida, crie uma nova função in-Portal 'Webhook + API' através do arranque rápido.
1. Colhe o código da amostra a partir de baixo para Run.csx sobre o código existente que aparece.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > O código de função de script c# que acabou de colar simplesmente regista uma linha para os registos de funções, e devolve o texto "Hello World" com alguns dados dinâmicos (a data e a hora).

3. Selecione "Integrar" a partir da lâmina da esquerda e, em seguida, selecione 'Editor Avançado' no canto superior direito do painel.
4. Colhe o código da amostra abaixo sobre o json existente.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. Volte para o separador HttpTrigger1, clique em 'Obter URL', e depois copie o URL que aparece.

   > [!NOTE]
   > As encadernações que acabou de criar basta dizer às Funções para responder em pedidos anónimos http GET para o URL que acabou de copiar. (https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey) Agora temos um API https sem servidor escalável, que é capaz de devolver uma carga útil muito simples.
   > Agora pode testar chamar este API de um navegador web usando o URL acima, também pode despir a porção ?code=secret do URL e provar que as Funções Azure devolverão um erro de 401.

## <a name="configure-and-secure-the-function-api"></a>Configure e fixe a função API
1. Duas áreas extra na aplicação de funções precisam de ser configuradas (Restrições Auth e Rede).
1. Em primeiro lugar, vamos configurar a Autenticação /Autorização, por isso clique no nome da aplicação de funções (ao lado do ícone de funções &lt;Z&gt;) para mostrar a página de visão geral.
1. Em seguida, selecione o separador 'Funcionalidades da Plataforma' e selecione 'Autenticação / Autorização'.
1. Ligue a função de autenticação do serviço de aplicações.
1. Em 'Fornecedores de Autenticação' escolha 'Azure Ative Directory', e escolha 'Advanced' do interruptor Modo de Gestão.
1. Colar o ID de aplicação da Backend Function API (de Azure AD B2C na caixa 'ID do cliente')
1. Colar o ponto final de configuração de id aberto bem conhecido a partir do signup ou iniciar a política na caixa URL emitente (gravámos esta configuração anteriormente).
1. Selecione OK.
1. Detete a Ação a tomar quando o pedido não for autenticado para "Iniciar sessão com o Diretório Ativo Azure", em seguida, clique em Guardar.

   > [!NOTE]
   > Agora a sua API função é implementada e deve lançar 401 respostas se a chave correta não for fornecida, e deve devolver os dados quando um pedido válido for apresentado.
   > Adicionou segurança adicional em profundidade na EasyAuth, configurando a opção 'Login With Azure AD' para lidar com pedidos não autenticados. Esteja ciente de que isso irá alterar o comportamento de pedido não autorizado entre a App de Funções Backend e o Frontend SPA, uma vez que a EasyAuth emitirá um redirecionamento 302 para a AAD em vez de uma resposta não autorizada 401, corrigiremos isso usando a API Management mais tarde.
   > Ainda não temos nenhuma segurança IP aplicada, se você tem uma chave válida e token OAuth2, qualquer um pode chamá-lo de qualquer lugar - idealmente queremos forçar todos os pedidos a vir através da API Management.
   > Se estiver a utilizar o nível de consumo de Gestão API, não poderá efetuar este bloqueio por VIP, uma vez que não existe um IP estático dedicado para esse nível, terá de confiar no método de bloquear as suas chamadas API através da chave de função secreta partilhada , por isso os passos 11-13 não serão possíveis.

1. Feche a lâmina 'Autenticação / Autorização' 
1. Selecione 'Networking' e, em seguida, selecione 'Restrições de acesso'
1. Em seguida, bloqueie os IPs da aplicação de função permitida para a instância DE Gestão API VIP. Este VIP é mostrado na gestão da API - secção de visão geral do portal.
1. Se quiser continuar a interagir com o portal de funções e a realizar os passos opcionais abaixo, deve adicionar o seu próprio endereço IP público ou gama CIDR também aqui.
1. Uma vez que há uma entrada permitida na lista, Azure adiciona uma regra implícita de negação para bloquear todos os outros endereços. 

Você precisará adicionar blocos de endereços formatados CIDR ao painel de restrições IP. When you need to add a single address such as the API Management VIP, you need to add it in the format xx.xx.xx.xx.

   > [!NOTE]
   > Agora a sua API função não deve ser chamada de outro lugar que não seja através da gestão da API, ou do seu endereço.
   
## <a name="import-the-function-app-definition"></a>Importar a definição de aplicação de função
1. Abra a *lâmina de Gestão API*e abra *a sua instância.*
1. Selecione a lâmina APIs da secção de Gestão API da sua instância.
1. A partir do painel 'Adicionar um Novo API', escolha 'Function App', e, em seguida, selecione 'Full' a partir do topo do popup.
1. Clique em Navegar, escolha a aplicação de funções que está a hospedar a API no seu interior e clique em selecionar.
1. Dê à API um nome e descrição para a utilização interna da API Management e adicione-o ao Produto "ilimitado".
1. Certifique-se de que grava o URL base para posterior utilização e, em seguida, clique em criar.

## <a name="configure-oauth2-for-api-management"></a>Configure Oauth2 para Gestão de API

1. Em seguida, Selecione a lâmina Oauth 2.0 do Separador de Segurança e clique em 'Adicionar'
1. Dê valores para *o nome* e *descrição* do ponto final de Oauth adicionado (estes valores aparecerão no próximo passo como ponto final oAuth2).
1. Pode introduzir qualquer valor no URL da página de registo do Cliente, uma vez que este valor não será utilizado.
1. Verifique o tipo de Concessão *Implícita auth* e deixe verificado o tipo de concessão de código de autorização verificado.
1. Mova-se para os campos *de ponto* final de Autorização e *Token,* e introduza os valores capturados a partir do conhecido documento de configuração xml anteriormente.
1. Percorra para baixo e monte um *parâmetro adicional* do corpo chamado "recurso" com o ID do cliente API da Função Backend a partir do registo da App Azure AD B2C
1. Selecione 'Credenciais de cliente', detete o ID do Cliente para o ID da aplicação da consola Developer - ignore este passo se utilizar o modelo de gestão api de consumo.
1. Desloque o Segredo do Cliente à chave que gravou anteriormente - ignore este passo se utilizar o modelo de gestão api de consumo.
1. Por último, registe agora a redirect_uri da concessão de código auth da API Management para posterior utilização.

## <a name="set-up-oauth2-for-your-api"></a>Configurar o Oauth2 para a sua API
1. A sua API aparecerá no lado esquerdo do portal sob a secção 'All APIs', abra a Sua API clicando nele.
1. Selecione o separador 'Definições'.
1. Atualize as suas definições selecionando "Oauth 2.0" do botão de rádio de autorização do utilizador.
1. Selecione o servidor Oauth que definiu anteriormente.
1. Verifique a caixa de verificação 'Override scope' e introduza o âmbito registado para a chamada de API de backend mais cedo.

   > [!NOTE]
   > Agora temos uma instância de Gestão API que sabe como obter fichas de acesso do Azure AD B2C para autorizar pedidos e compreende a nossa configuração Oauth2 Azure Ative Directory B2C.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Configurar as políticas **CORS** e **validar-jwt**

> Devem ser seguidas as seguintes secções, independentemente da utilizada do nível APIM. 

1. Volte para o separador de design e escolha "All APIs", em seguida, clique no botão de visualização de código para mostrar ao editor de política.
1. Editar a secção de entrada e colar a xml abaixo para que se leia como a seguinte.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. Editar o url openid-config para combinar com o seu conhecido ponto final Azure AD AD B2C para o signo ou assinar na política.
1. Editar o valor da reclamação para corresponder ao ID de aplicação válido, também conhecido como ID do cliente para a aplicação API de backend e guardar.

   > [!NOTE]
   > Agora, a gestão da API é capaz de responder aos pedidos de origem cruzada para aplicações JS SPA, e irá realizar estrangulamento, limitação de taxas e pré-validação do símbolo jWT auth sendo passado antes de reencaminhar o pedido para a API função.

   > [!NOTE]
   > A secção seguinte é opcional e não se aplica ao nível **de Consumo,** que não suporta o portal de desenvolvimento.
   > Se não pretender utilizar o portal do desenvolvedor, ou não puder utilizá-lo uma vez que está a utilizar o nível de Consumo, salte este passo e salte diretamente para ["Construa o JavaScript SPA para consumir a API".](##build-the-javascript-spa-to-consume-the-api)

## <a name="optional-configure-the-developer-portal"></a>[Opcional] Configure o portal de desenvolvimento

1. Abra a lâmina Azure AD B2C e navegue para o registo de candidatura saem do Portal do Desenvolvimento
1. Delineie a entrada 'Answer URL' na que observou quando configurar o redirect_uri da concessão de código auth na API Management anteriormente.

   Agora que a autorização de utilizador OAuth 2.0 está ativada no `Echo API`, a Consola de Desenvolvimento obtém um sinal de acesso para o utilizador, antes de ligar para a API.

1. Navegue em qualquer operação sob o `Echo API` no portal do desenvolvedor e selecione **Experimente-a** para o levar à Consola de Desenvolvimento.
1. Note um novo item na secção **de Autorização,** correspondente ao servidor de autorização que acabou de adicionar.
1. Selecione código de **autorização** da lista de autorização e é solicitado que inscreva-se no inquilino da AD Azure. Se já assinou a conta, pode não ser solicitado.
1. Após o sucesso da entrada, um cabeçalho `Authorization: Bearer` é adicionado ao pedido, com um sinal de acesso do Azure AD B2C codificado na Base64. 
1. Selecione **Enviar** e pode ligar para a API com sucesso.

   > [!NOTE]
   > Agora, a gestão da API é capaz de adquirir fichas para o portal de desenvolvimento testar a sua API e é capaz de entender a sua definição e tornar a página de teste apropriada no portal de v.

1. A partir da lâmina geral do portal de gestão da API, clique em 'Developer Portal' para iniciar sessão como administrador da API.
1. Aqui, você e outros consumidores selecionados da sua API podem testá-los e chamá-los de uma consola.
1. Selecione 'Products', depois escolha 'Ilimitado', em seguida, escolha a API que criamos anteriormente e clique em 'TRY IT'
1. Desconeça a chave de subscrição da API e copie-a para um local seguro juntamente com o url de pedido que necessitará mais tarde.
1. Selecione Também Implicit, a partir do oauth auth dropdown e você pode ter que autenticar aqui com um popup.
1. Clique em 'Enviar' e se estiver tudo bem, a sua App de Funções deve responder com uma mensagem olá através da gestão da API com uma mensagem de 200 OK e algum JSON.

   > [!NOTE]
   > Parabéns, tem agora o Azure AD B2C, a API Management e o Azure Functions a trabalhar em conjunto para publicar, proteger e consumir uma API. Deve ter reparado que a API está de facto protegida duas vezes utilizando este método, uma vez com o Cabeçalho chave de assinatura OCP de Gestão API e uma vez com a Autorização: Bearer JWT.
   > Você estaria correto, uma vez que este exemplo é uma Aplicação de Página Única JavaScript, usamos a Chave de Gestão API apenas para chamadas de limitação de taxas e faturação.
   > A Autorização e Autenticação efetivas é tratada pelo Azure AD B2C, e é encapsulada no JWT, que é validado duas vezes, uma pela API Management, e depois pela Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Construa o JavaScript SPA para consumir a API
1. Abra a lâmina das contas de armazenamento no portal Azure 
1. Selecione a conta que criou e selecione a lâmina 'Site Estático' da secção Definições (se não vir uma opção 'Site Estático', verifique se criou uma conta V2).
1. Detete a funcionalidade de hospedagem estátática da web para 'activada', e coloque o nome do documento do índice em 'index.html', em seguida, clique em 'guardar'.
1. Note o conteúdo do Ponto Final Primário, uma vez que este local é onde o site frontend será hospedado. 

   > [!NOTE]
   > Pode utilizar o Azure Blob Storage + CDN rewrite, ou o Azure App Service - mas a funcionalidade de hospedagem do Site Estática do Blob Storage dá-nos um recipiente predefinido para servir conteúdo estático da web / html /js / css do Armazenamento Azure e inferirá uma página predefinida para nós para trabalho zero.

## <a name="upload-the-js-spa-sample"></a>Faça upload da amostra JS SPA
1. Ainda na lâmina da conta de armazenamento, selecione a lâmina 'Blobs' da secção Blob Service e clique no recipiente $web que aparece no painel da direita.
1. Guarde o código abaixo para um ficheiro localmente na sua máquina como index.html e, em seguida, carregue o índice de ficheiros.html para o recipiente $web.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Navegue no Ponto Final primário do Site Estático que armazenou anteriormente na última secção.

   > [!NOTE]
   > Parabéns, acabaste de implementar uma app javaScript single page para o Armazenamento Azure Uma vez que ainda não configurámos a aplicação JS com as suas chaves para o api ou configuramos a aplicação JS com os seus dados Azure AD AD B2C – a página ainda não funcionará se a abrir.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Configure o JS SPA para Azure AD B2C
1. Agora sabemos onde está tudo: podemos configurar o SPA com o endereço API Management API apropriado e a aplicação/IDs do cliente Azure AD B2C correto
1. Volte para a lâmina de armazenamento do portal Azure e clique no index.html, em seguida, escolha 'Editar Blob' 
1. Atualize os detalhes auth para corresponder à sua aplicação frontal que registou no B2C anteriormente, observando que os valores 'b2cScopes' são para o backend da API.
1. A chave webApi e o url api podem ser encontrados no painel de teste de Gestão API para a operação API.
1. Crie uma chave de subscrição APIM, dirigindo-se à Gestão API de volta à lâmina de Gestão API, selecionando 'Subscrições', e clicando em 'Adicionar Subscrição' e, em seguida, guardar o registo. Clicar na Ellipsis (...) junto à linha criada permitir-lhe-á mostrar as teclas para que possa copiar a chave principal.
1. Deve parecer algo como o código abaixo: -  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. Clicar em Guardar

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Desinte as URIs de redirecionamento para a aplicação frontend Azure AD B2C
1. Abra a lâmina Azure AD B2C e navegue para o registo de inscrição para a Aplicação JavaScript Frontend
1. Delineie o URL de redirecionamento para o que observou quando configurao previamente o ponto final primário do site estático acima

   > [!NOTE] 
   > Esta configuração resultará em um cliente da aplicação frontend a receber um sinal de acesso com reclamações apropriadas do Azure AD B2C.
   > O SPA poderá adicioná-lo como um token portador no cabeçalho https na chamada para a API de backend.
   > A API Management irá pré-validar o token, as chamadas limite de taxa para o ponto final pela chave de subscrição, antes de passar o pedido para a API função azure recetora.
   > O SPA irá dar a resposta no navegador.

   > *Parabéns, configuraste o Azure AD B2C, a Azure API Management, o Azure Functions, a Azure App Service Authorization para trabalhar em perfeita harmonia!*

   > [!NOTE]
   > Agora temos uma aplicação simples com uma API simples e segura, vamos testá-la.

## <a name="test-the-client-application"></a>Testar a aplicação do cliente
1. Abra o URL da aplicação de amostra que você notou a partir da conta de armazenamento que criou anteriormente
1. Clique em "Iniciar sessão" no canto superior direito, este clique aparecerá no seu Azure AD B2C ou inscreva-se no perfil.
1. O registo de correio na secção "Inado como" do ecrã será preenchido a partir do seu JWT.
1. Agora clique em "Call Web Api", e a página deve atualizar com os valores enviados de volta da sua API segura.

## <a name="and-were-done"></a>E estamos a fazer
Os passos acima podem ser adaptados e editados para permitir muitas utilizações diferentes do Azure AD B2C com a API Management.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [o Azure Ative Directory e o OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre a Gestão da API.
* Para outras formas de garantir o seu serviço de back-end, consulte a [autenticação do Certificado Mútuo](api-management-howto-mutual-certificates.md).
* Considere usar a API do Gráfico Azure AD para atribuir reclamações personalizadas e usar uma política de Gestão API para validar que estão presentes no símbolo.

* Criar uma instância de [serviço de Gestão API.](get-started-create-service-instance.md)

* [Gerencie a sua primeira API.](import-and-publish.md)
