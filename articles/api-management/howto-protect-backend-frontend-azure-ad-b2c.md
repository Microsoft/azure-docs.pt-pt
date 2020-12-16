---
title: Proteja o backend SPA com o OAuth 2.0 utilizando o Azure Ative Directory B2C e a Azure API Management.
description: Proteja uma API com OAuth 2.0 utilizando o Azure Ative Directory B2C, Azure API Management e Easy Auth para ser chamado de um JavaScript SPA.
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
ms.custom: fasttrack-new, devx-track-js
ms.openlocfilehash: b157200b03bdc89e00bfa5c8264d78baf24875c1
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609127"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Proteja o backend SPA com AAuth 2.0, Azure Ative Directory B2C e Azure API Management

Este cenário mostra-lhe como configurar o seu exemplo de Gestão API Azure para proteger uma API.
Usaremos o protocolo OAuth 2.0 com a Azure AD B2C, juntamente com a API Management para garantir um backend de Funções Azure usando a EasyAuth.

## <a name="aims"></a>Objetivos
Vamos ver como a API Management pode ser usada num cenário simplificado com As Funções Azure e Azure AD B2C. Irá criar uma aplicação JavaScript (JS) que chama a API, que assina nos utilizadores com Azure AD B2C. Em seguida, utilizará as funcionalidades de política validadas da API Management para proteger a API de backend.

Para a defesa em profundidade, usamos a EasyAuth para validar o token novamente dentro da API de back-end.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir os passos deste artigo, você deve ter:
* Uma conta de armazenamento V2 de propósito geral para acolher a app de página única JS
* Um exemplo de gestão da API Azure 
* Uma aplicação vazia Azure Function (executando o tempo de execução V2 .NET Core, em um Plano de Consumo do Windows) para acolher a chamada API
* Um inquilino Azure AD B2C, ligado a uma subscrição 

Embora, na prática, se utilize recursos na mesma região em cargas de trabalho de produção, para este artigo como-artigo a região de implantação não é importante.

## <a name="overview"></a>Descrição geral
Aqui está uma ilustração dos componentes em uso e o fluxo entre eles uma vez que este processo esteja concluído.
![Componentes em uso e fluxo](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Componentes em uso e fluxo")

Aqui está uma visão geral rápida dos passos:

1. Criar as aplicações Azure AD B2C (Frontend, API Management) e API com âmbitos e conceder acesso à API
1. Crie a inscrição ou inscreva-se em políticas que permitam aos utilizadores iniciar sessão com Azure AD B2C 
1. Configure a API Management com os novos IDs e chaves do cliente Azure AD B2C e chaves para ativar a autorização do utilizador OAuth2 na Consola de Desenvolvimento
1. Construir a Função API
1. Configure a Função API para permitir a EasyAuth com o novo Azure AD B2C Client ID's e Chaves e bloquear para APIM VIP 
1. Construir a Definição de API na Gestão da API
1. Configurar o Oauth2 para a configuração da API Management API
1. Crie a política **CORS** e adicione a política **validada-jwt** para validar o token OAuth para cada pedido de entrada
1. Construir a aplicação de chamada para consumir a API
1. Faça o upload da amostra JS SPA
1. Configure a App do Cliente JS sample com o novo ID do cliente Azure AD B2C E chaves 
1. Testar a Aplicação do Cliente

## <a name="configure-azure-ad-b2c"></a>Configurar Azure Ad B2C 
Abra a lâmina Azure AD B2C no portal e faça os seguintes passos.
1. Selecione o **separador Aplicações** 
1. Clique no botão 'Adicionar' e crie três aplicações para as seguintes finalidades
   * O cliente frontend.
   * A API da Função Backend.
   * [Opcional] O portal de desenvolvimento da API Management (a menos que esteja a gerir a Azure API Management no nível de consumo, mais sobre este cenário mais tarde).
1. Desacei o WebApp / Web API para todas as 3 aplicações e desloque o "Permitir o fluxo implícito" para sim apenas para o Cliente Frontend.
1. Agora, desempenhe a App ID URI, escolha algo único e relevante para o serviço que está a ser criado.
1. Utilize espaços reservados para os urls de resposta por https://localhost enquanto, como, atualizaremos esses urls mais tarde.
1. Clique em 'Criar', em seguida, repita os passos 2-5 para cada uma das três aplicações acima, registando o AppID URI, nome e ID de aplicação para uso posterior para as três aplicações.
1. Abra a aplicação do Portal do Programador de Gestão de API a partir da lista de aplicações e selecione o separador *Chaves* (em Geral) e, em seguida, clique em 'Gerar Tecla' para gerar uma tecla de auth
1. Ao clicar em guardar, grave a chave em algum lugar seguro para posterior utilização - note que este lugar é a única hipótese que você terá para ver e copiar esta chave.
1. Selecione agora o *Separador de Âmbitos Publicados* (No Acesso a API)
1. Crie e nomeie um âmbito para a sua API de função e grave o Scope e o Valor de Âmbito Completo povoado, em seguida, clique em 'Guardar'.
   > [!NOTE]
   > Os âmbitos AZure AD B2C são efetivamente permissões dentro da sua API a que outras aplicações podem solicitar acesso através da lâmina de acesso API a partir das suas aplicações, efetivamente acaba de criar permissões de aplicação para a sua chamada API.
1. Abra as outras duas aplicações e, em seguida, procure no separador *API Access.*
1. Conceda-lhes acesso ao âmbito de API de backend e ao predefinido que já existia ("Aceda ao perfil do utilizador").
1. Gere-lhes uma chave cada uma selecionando o separador *Chaves* em 'General' para gerar uma chave auth e gravar essas teclas em algum lugar seguro para mais tarde.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Crie um fluxo de utilizador "Iniciar ou Iniciar sessão"
1. Voltar à raiz (ou 'visão geral') da Lâmina AD B2C Azure 
1. Em seguida, selecione "Fluxos de Utilizador (Políticas)" e clique em "Novo fluxo de utilizador"
1. Escolha o tipo de fluxo de utilizador 'Iniciar s-se-in e iniciar sins'
1. Dê um nome à apólice e grave-o para mais tarde.
1. Em seguida, em 'Fornecedores de identidade', em seguida, verifique 'User ID sign up' (isto pode dizer 'Email sign up') e clique em OK. 
1. Em 'Atributos e reclamações do utilizador', clique em 'Mostrar Mais...' em seguida, escolha as opções de reclamação que deseja que os seus utilizadores entrem e tenham devolvido no token. Verifique pelo menos 'Display Name' e 'Email Address' para recolher e devolver e clique em 'OK', e clique em 'Criar'.
1. Selecione a política que criou na lista e, em seguida, clique no botão 'Executar o fluxo do utilizador'.
1. Esta ação abrirá a lâmina de fluxo do utilizador executado, selecionará a aplicação frontend e, em seguida, registará o endereço do domínio b2clogin.com que é mostrado no dropdown para 'Select domain'.
1. Clique no link no topo para abrir o 'conhecido ponto final de configuração openid', e registar os valores authorization_endpoint e token_endpoint bem como o valor do próprio link como o conhecido ponto final de configuração openid.

   > [!NOTE]
   > As políticas B2C permitem-lhe expor os pontos finais de login Azure AD B2C para poder capturar diferentes componentes de dados e assinar nos utilizadores de diferentes formas. Neste caso, configuramos uma inscrição ou sinal no ponto final, que expôs um ponto final de configuração bem conhecido, especificamente a nossa política criada foi identificada no URL pelo parâmetro p=.
   > 
   > Uma vez feito isto – tem agora uma plataforma de identidade funcional Business to Consumer que irá inscrever os utilizadores em várias aplicações. 
   > Se quiser, pode clicar no botão 'Executar o fluxo do utilizador' aqui (para passar pela inscrição ou iniciar o teste) e sentir o que irá fazer na prática, mas o passo de redirecionamento no final falhará uma vez que a aplicação ainda não foi implementada.

## <a name="build-the-function-api"></a>Construir a função API
1. Volte para o seu inquilino padrão Azure AD no portal Azure para que possamos configurar itens na sua subscrição novamente 
1. Vá à lâmina de Apps de Função do portal Azure, abra a sua aplicação de função vazia e, em seguida, crie uma nova função de In-Portal 'Webhook + API' através do arranque rápido.
1. Cole o código de amostra de baixo para o Run.csx sobre o código existente que aparece.

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
   > O código de função de script c# que acaba de colar simplesmente regista uma linha para os registos de funções e devolve o texto "Hello World" com alguns dados dinâmicos (a data e hora).

3. Selecione "Integrar" a partir da lâmina esquerda e, em seguida, selecione 'Advanced Editor' no canto superior direito do painel.
4. Cole o código de amostra abaixo sobre o json existente.

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

5. Volte para o separador HttpTrigger1, clique em 'Obter URL', em seguida, copie o URL que aparece.

   > [!NOTE]
   > As encadernações que acabou de criar simplesmente dizem às Funções para responder em pedidos anónimos http GET para o URL que acabou de copiar. ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ) Agora temos uma API https sem servidor escalável, capaz de devolver uma carga útil muito simples.
   > Pode agora testar a chamada desta API a partir de um navegador web utilizando o URL acima, também pode despojar a porção ?code=secret do URL e provar que as Funções Azure devolverão um erro de 401.

## <a name="configure-and-secure-the-function-api"></a>Configurar e assegurar a função API
1. Duas áreas extra na aplicação de função precisam de ser configuradas (Restrições de Auth e Rede).
1. Em primeiro lugar, vamos configurar autenticação / Autorização, por isso clique no nome da aplicação de funções (junto ao ícone das &lt; &gt; funções Z) para mostrar a página geral.
1. Em seguida, selecione o separador 'Funcionalidades da Plataforma' e selecione 'Autenticação/Autorização'.
1. Ligue a função de autenticação do Serviço de Aplicações.
1. Em 'Fornecedores de Autenticação' escolha 'Azure Ative Directory' e escolha 'Avançado' no interruptor modo de gestão.
1. Colar o ID de aplicação da Função Backend (do Azure AD B2C para a caixa 'Client ID')
1. Cole o conhecido ponto final de configuração de id aberto a partir da inscrição ou inscreva-se na política na caixa URL do Emitente (registamos esta configuração anteriormente).
1. Selecione OK.
1. Desacorda a Ação a tomar quando o pedido não for autenticado para "Iniciar sessão com o Diretório Ativo Azure", em seguida, clique em Guardar.

   > [!NOTE]
   > Agora a sua API de função está implantada e deve lançar 401 respostas se a tecla correta não for fornecida, e deve devolver dados quando um pedido válido for apresentado.
   > Adicionou segurança adicional em profundidade na EasyAuth, configurando a opção 'Login with Azure AD' para lidar com pedidos não autenticados. Esteja ciente de que isto irá alterar o comportamento de pedido não autorizado entre a App de Função Backend e o Frontend SPA, uma vez que a EasyAuth emitirá um redirecionamento 302 para a AAD em vez de uma resposta 401 Não Autorizada, corrigiremos isso utilizando a API Management mais tarde.
   > Ainda não temos nenhuma segurança IP aplicada, se você tem uma chave válida e token OAuth2, qualquer um pode chamá-lo de qualquer lugar - idealmente queremos forçar todos os pedidos a vir através da API Management.
   > Se estiver a utilizar o nível de consumo da API Management, não poderá realizar este bloqueio pela VIP, uma vez que não existe um IP estático dedicado para esse nível, terá de confiar no método de bloquear as suas chamadas API através da chave de função secreta partilhada, pelo que os passos 11-13 não serão possíveis.

1. Feche a lâmina 'Autenticação / Autorização' 
1. Selecione 'Networking' e, em seguida, selecione 'Restrições de acesso'
1. Em seguida, bloqueie os IPs da aplicação de função permitida para a instância de Gestão da API VIP. Este VIP é mostrado na gestão da API - secção de visão geral do portal.
1. Se pretender continuar a interagir com o portal de funções e a realizar os passos opcionais abaixo, deve adicionar também aqui o seu próprio endereço IP público ou alcance CIDR.
1. Uma vez que haja uma entrada de permiti na lista, Azure adiciona uma regra implícita de negação para bloquear todos os outros endereços. 

Terá de adicionar blocos de endereços formatados ciDR ao painel de restrições IP. Quando precisa de adicionar um único endereço, como o API Management VIP, tem de o adicionar no formato xx.xx.xx.xx

   > [!NOTE]
   > Agora, a sua API de função não deve ser chamada de outro lugar que não seja através da gestão da API, ou do seu endereço.
   
## <a name="import-the-function-app-definition"></a>Importar a definição de aplicação de função
1. Abra a *lâmina de gestão da API* e, em seguida, abra *a sua instância*.
1. Selecione a Lâmina APIs da secção de Gestão API do seu caso.
1. A partir do painel 'Adicionar uma Nova API', escolha 'App de função', e depois selecione 'Full' a partir do topo do popup.
1. Clique em Procurar, escolha a aplicação de função que está hospedando a API no interior e clique em selecionar.
1. Dê à API um nome e descrição para o uso interno da API Management e adicione-o ao Produto 'ilimitado'.
1. Certifique-se de que grava o URL base para utilização posterior e, em seguida, clique em criar.

## <a name="configure-oauth2-for-api-management"></a>Configure Oauth2 para a Gestão da API

1. Em seguida, selecione a lâmina Oauth 2.0 do separador de segurança e clique em 'Adicionar'
1. Dar valores para o Nome do *Visor* e *Descrição* para o Ponto Final de Oauth adicionado (estes valores aparecerão no passo seguinte como ponto final do Oauth2).
1. Pode introduzir qualquer valor no URL da página de registo do Cliente, uma vez que este valor não será utilizado.
1. Verifique o tipo de Concessão *implícita* e deixe verificado o tipo de concessão de código de autorização.
1. Mude para os campos de ponto final *de Autorização* e *Token* e introduza os valores capturados a partir do conhecido documento xml de configuração anteriormente.
1. Percorra para baixo e povoe um *parâmetro corporal adicional* chamado 'recurso' com o ID do cliente API da Função Backend a partir do registo da App Azure AD B2C
1. Selecione 'Credenciais de cliente', desaprova o ID do Cliente para o ID da aplicação da consola developer - ignore este passo se utilizar o modelo de Gestão de API de consumo.
1. Defina o Segredo do Cliente para a chave que gravou anteriormente - ignore este passo se utilizar o modelo de Gestão de API de consumo.
1. Por último, registem agora a redirect_uri da bolsa de código auth da API Management para posterior utilização.

## <a name="set-up-oauth2-for-your-api"></a>Configurar o Oauth2 para a sua API
1. A sua API aparecerá no lado esquerdo do portal sob a secção 'Todas as APIs', abra a sua API clicando nele.
1. Selecione o separador 'Definições'.
1. Atualize as suas definições selecionando "Oauth 2.0" a partir do botão de rádio de autorização do utilizador.
1. Selecione o servidor Oauth que definiu anteriormente.
1. Verifique a caixa de verificação 'Override Scope' e introduza o âmbito que gravou para a chamada de API de backend mais cedo.

   > [!NOTE]
   > Agora temos uma instância de Gestão da API que sabe como obter tokens de acesso da Azure AD B2C para autorizar pedidos e compreende a nossa configuração de Diretório Ativo Oauth2 Azure B2C.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Configurar as políticas **CORS** e **validar-jwt**

> As seguintes secções devem ser seguidas independentemente da utilização do nível APIM. 

1. Volte para o separador de design e escolha "Todas as APIs", em seguida, clique no botão de visualização de código para mostrar o editor de política.
1. Edite a secção de entrada e cole a xml abaixo para que se leia como se fosse o seguinte.

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
1. Edite o url openid-config para combinar com o seu conhecido ponto final Azure AD B2C para a inscrição ou inscrição na política.
1. Editar o valor de reclamação para corresponder ao ID de aplicação válido, também conhecido como ID do cliente para a aplicação API de backend e guardar.

   > [!NOTE]
   > Agora, a gestão da API é capaz de responder aos pedidos de origem cruzada para aplicações JS SPA, e irá executar estrangulamento, limitação de taxas e pré-validação do token JWT auth sendo passado antes de encaminhar o pedido para a Função API.

   > [!NOTE]
   > A secção seguinte é opcional e não se aplica ao nível **de Consumo,** que não suporta o portal de desenvolvedores.
   > Se não pretender utilizar o portal do programador, ou não puder usá-lo uma vez que está a utilizar o nível de Consumo, por favor, salte este passo e salte diretamente para ["Construir o JavaScript SPA para consumir a API".](#build-the-javascript-spa-to-consume-the-api)

## <a name="optional-configure-the-developer-portal"></a>[Opcional] Configure o portal de desenvolvedores

1. Abra a lâmina Azure AD B2C e navegue para o registo de candidatura para o Portal do Desenvolvedor
1. Defina a entrada 'URL de resposta' à que anotaste quando configuraste a redirect_uri da concessão de código auth na Gestão da API anteriormente.

   Agora que a autorização do utilizador OAuth 2.0 está ativada no `Echo API` , a Consola de Desenvolvimento obtém um token de acesso para o utilizador, antes de ligar para a API.

1. Navegue por qualquer operação sob o `Echo API` portal do desenvolvedor e selecione **Experimente-a** para o levar à Consola do Desenvolvedor.
1. Note um novo item na secção **Autorização,** correspondente ao servidor de autorização que acaba de adicionar.
1. Selecione o código de **autorização** da lista de autorização suspensa, e é solicitado a assinar no inquilino da AD Azure. Se já assinou com a conta, pode não ser solicitado.
1. Após o sucesso do sence-in, é adicionado um `Authorization: Bearer` cabeçalho ao pedido, com um token de acesso do Azure AD B2C codificado na Base64. 
1. Selecione **Enviar** e pode ligar para a API com sucesso.

   > [!NOTE]
   > Agora a gestão da API é capaz de adquirir fichas para o portal de desenvolvedores para testar a sua API e é capaz de entender a sua definição e tornar a página de teste apropriada no portal dev.

1. A partir da lâmina de visão geral do portal de Gestão da API, clique em 'Portal do Desenvolvimento' para iniciar sdecisão como administrador da API.
1. Aqui, você e outros consumidores selecionados da sua API podem testá-los e chamá-los a partir de uma consola.
1. Selecione 'Produtos', em seguida, escolha 'Ilimitado', em seguida, escolha a API que criamos anteriormente e clique em 'TRY IT'
1. Desaprote a chave de subscrição da API e copie-a em algum lugar seguro, juntamente com o url de pedido que você precisará mais tarde.
1. Selecione Também Implicit, a partir do auth auth dropdown e você pode ter que autenticar aqui com um popup.
1. Clique em 'Enviar' e se estiver tudo bem, a sua App de Função deve responder com uma mensagem de olá através da gestão da API com uma mensagem de 200 OK e algum JSON.

   > [!NOTE]
   > Parabéns, tem agora Azure AD B2C, API Management e Azure Functions trabalhando em conjunto para publicar, proteger e consumir uma API. Você deve ter reparado que a API é de facto segura duas vezes usando este método, uma vez com o Cabeçalho OCP-Assinatura-Assinatura de Gestão da API, e uma vez com a Autorização: Portador JWT.
   > Estaria correto, uma vez que este exemplo é uma Aplicação de Página Única JavaScript, utilizamos a Chave de Gestão da API apenas para chamadas de limitação de taxas e faturação.
   > A autorização e autenticação real é tratada pelo Azure AD B2C, e é encapsulada no JWT, que é validado duas vezes, uma pela API Management, e depois por Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Construa o JavaScript SPA para consumir a API
1. Abra a lâmina das contas de armazenamento no portal Azure 
1. Selecione a conta criada e selecione a lâmina 'Static Website' a partir da secção Definições (se não vir uma opção 'Site Estático', verifique se criou uma conta V2).
1. Deslote a função de hospedagem estática na web para 'activado', e deslote o nome do documento de índice para 'index.html', e depois clique em 'guardar'.
1. Note o conteúdo do Ponto Final Primário, uma vez que este local é onde o site frontend será hospedado. 

   > [!NOTE]
   > Você poderia usar ou Azure Blob Storage + CDN reescreve, ou Azure App Service - mas a funcionalidade de hospedagem estática do Site da Blob Storage dá-nos um recipiente predefinido para servir conteúdo estático na Web / html / js / css do Azure Storage e inferirá uma página padrão para nós para zero trabalho.

## <a name="upload-the-js-spa-sample"></a>Faça o upload da amostra JS SPA
1. Ainda na lâmina da conta de armazenamento, selecione a lâmina 'Blobs' da secção Blob Service e clique no recipiente $web que aparece no painel direito.
1. Guarde o código abaixo para um ficheiro localmente na sua máquina, uma vez index.html e, em seguida, faça o upload do ficheiro index.html para o recipiente $web.

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
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},
                    url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Navegue no ponto de final primário do site estático que armazenou anteriormente na última secção.

   > [!NOTE]
   > Parabéns, acabaste de implementar uma App de Página Única JavaScript para o Azure Storage uma vez que ainda não configuramos a aplicação JS com as chaves para a api ou configuraste a aplicação JS com os teus dados Azure AD B2C ainda – a página ainda não funcionará se a abrires.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Configure o JS SPA para Azure AD B2C
1. Agora sabemos onde está tudo: podemos configurar o SPA com o endereço API de Gestão de API adequado e a aplicação/IDs de Azure AD B2C
1. Volte para a lâmina de armazenamento do portal Azure e clique em index.html e, em seguida, escolha 'Editar Blob' 
1. Atualize os detalhes auth para corresponder à sua aplicação frontal que registou anteriormente em B2C, notando que os valores 'b2cScopes' são para o backend da API.
1. A chave webApi e url api podem ser encontradas no painel de teste de Gestão da API para a operação API.
1. Crie uma chave de subscrição APIM dirigindo-se à Gestão da API de volta à lâmina de Gestão da API, selecionando 'Subscrições', e clicando em 'Adicionar Subscrição' e, em seguida, guarde o registo. Clicar na Elipse (...) ao lado da linha criada permitir-lhe-á mostrar as teclas para poder copiar a tecla principal.
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

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Desaprova os URIs de redirecionamento para a app frontal Azure AD B2C
1. Abra a lâmina Azure AD B2C e navegue para o registo de aplicação para a Aplicação Frontal JavaScript
1. Desaponte o URL de redirecionamento para o que anotado quando configurar previamente o ponto final principal do site estático acima

   > [!NOTE] 
   > Esta configuração resultará em que um cliente da aplicação frontend receba um token de acesso com reclamações apropriadas da Azure AD B2C.
   > O SPA poderá adicionar isto como um símbolo portador no cabeçalho https na chamada para a API backend.
   > A API Management irá pré-validar as chamadas simbólicas e limite de taxa para o ponto final pela chave do assinante, antes de passar o pedido à API da Função Azure recetora.
   > O SPA irá dar a resposta no navegador.

   > *Parabéns, configuraste a Azure AD B2C, Azure API Management, Azure Functions, Azure App Service Authorization to work in perfect harmony!*

   > [!NOTE]
   > Agora temos uma aplicação simples com uma API segura simples, vamos testá-la.

## <a name="test-the-client-application"></a>Testar a aplicação do cliente
1. Abra o URL da aplicação de amostra que anotado a partir da conta de armazenamento que criou anteriormente
1. Clique em "Iniciar sessão" no canto superior direito, este clique irá aparecer no seu Azure AD B2C ou iniciar sessão de perfil.
1. O post-in na secção "Iniciar sessão como" do ecrã será povoado a partir do seu JWT.
1. Clique em "Call Web Api", e a página deve atualizar-se com os valores enviados da API segura.

## <a name="and-were-done"></a>E nós terminamos
Os passos acima podem ser adaptados e editados para permitir muitas utilizações diferentes do Azure AD B2C com a API Management.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [o Azure Ative Directory e o OAuth2.0](../active-directory/develop/authentication-vs-authorization.md).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre a API Management.
* Para obter outras formas de garantir o seu serviço back-end, consulte a [autenticação do Certificado Mútuo.](api-management-howto-mutual-certificates.md)
* [Criar uma instância de serviço de Gestão API.](get-started-create-service-instance.md)
* [Gerencie a sua primeira API.](import-and-publish.md)
