---
title: Proteger backend SPA em Gestão API Azure com Diretório Ativo B2C
description: Proteja uma API com OAuth 2.0 utilizando o Azure Ative Directory B2C, Azure API Management e Easy Auth para ser chamado de um Spa JavaScript utilizando o PKCE habilitado SPA Auth Flow.
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
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: 812b54d10ea3cc3c405f534e36ac66abf3466808
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449293"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Proteja o backend SPA com AAuth 2.0, Azure Ative Directory B2C e Azure API Management

Este cenário mostra-lhe como configurar o seu exemplo de Gestão API Azure para proteger uma API.
Utilizaremos o fluxo Azure AD B2C SPA (Auth Code + PKCE) para adquirir um token, juntamente com a API Management para garantir um backend de funções Azure usando a EasyAuth.

## <a name="aims"></a>Objetivos

Vamos ver como a API Management pode ser usada num cenário simplificado com As Funções Azure e Azure AD B2C. Irá criar uma aplicação JavaScript (JS) que chama a API, que assina nos utilizadores com Azure AD B2C. Em seguida, utilizará as funcionalidades de política validada da API Management, CORS e Rate Limit By Key para proteger a API de backend.

Para a defesa em profundidade, utilizamos a EasyAuth para validar novamente o token dentro da API de back-end e garantir que a gestão da API é o único serviço que pode chamar o backend das Funções Azure.

## <a name="what-will-you-learn"></a>O que vai aprender?

> [!div class="checklist"]
> * Configuração de uma App de página única e API de backend em Azure Ative Directory B2C
> * Criação de uma API de Funções Azure
> * Importação de uma API de Funções Azure para a Gestão da API Azure
> * Assegurar a API na Gestão API da Azure
> * Chamando o Azure Ative Directory B2C Autorizações Pontos finais através das Bibliotecas da Plataforma de Identidade da Microsoft (MSAL.js)
> * Armazenar uma aplicação de página única HTML / Vanilla JS e servi-la a partir de um ponto final de armazenamento de blob Azure Blob

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, você deve ter:

* Uma Conta de Armazenamento V2 de Propósito Geral Azure (StorageV2) para acolher a app de página única JS.
* Uma instância de Gestão da API Azure (Qualquer nível funcionará, incluindo 'Consumo', no entanto, certas características aplicáveis ao cenário completo não estão disponíveis neste nível (taxa-limite-por-chave e IP virtual dedicado), estas restrições são chamadas abaixo no artigo, se for caso disso).
* Uma aplicação vazia Azure Function (executando o tempo de execução V3.1 .NET Core, em um Plano de Consumo) para acolher a chamada API
* Um inquilino Azure AD B2C, ligado a uma subscrição.

Embora, na prática, se utilize recursos na mesma região em cargas de trabalho de produção, para este artigo como-artigo a região de implantação não é importante.

## <a name="overview"></a>Descrição geral

Aqui está uma ilustração dos componentes em uso e o fluxo entre eles uma vez que este processo esteja concluído.
![Componentes em uso e fluxo](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Componentes em uso e fluxo")

Aqui está uma visão geral rápida dos passos:

1. Criar as aplicações Azure AD B2C (Frontend, API Management) e API com âmbitos e conceder acesso à API
1. Crie a inscrição e inscreva-se em políticas que permitam aos utilizadores iniciar sessão com Azure AD B2C 
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

   > [!TIP]
   > Vamos capturar algumas peças de informação e chaves, etc. enquanto caminhamos neste documento, pode achar útil ter um editor de texto aberto para armazenar temporariamente os seguintes itens de configuração.  
   >
   > ID DO CLIENTE DE BACKEND B2C:  
   > CHAVE SECRETA DO CLIENTE B2C BACKEND:  
   > B2C BACKEND API SCOPE URI:  
   > ID DO CLIENTE DIANTEIRO B2C:  
   > B2C USER FLOW ENDPOINT URI:  
   > B2C BEM CONHECIDO PONTO FINAL OPENID:   
   > B2C NOME DA POLÍTICA: FRONTENDAPP_SIGNUPANDSIGNIN URL DE FUNÇÃO:  
   > URL BASE APIM API: URL DE PONTO FINAL PRIMÁRIO DE ARMAZENAMENTO:  

## <a name="configure-the-backend-application"></a>Configure a aplicação backend

Abra a lâmina Azure AD B2C no portal e faça os seguintes passos.

1. Selecione o separador **Registos de Aplicações**
1. Clique no botão 'Novo Registo'.
1. Escolha 'Web' na caixa de seleção URI de redirecionamento.
1. Agora desa um nome de exibição, escolha algo único e relevante para o serviço que está a ser criado. Neste exemplo, usaremos o nome "Aplicação de Backend".
1. Use espaços reservados para os urls de resposta, como https://jwt.ms ' (um site de descoding de token da Microsoft), atualizaremos esses urls mais tarde.
1. Certifique-se de que selecionou a opção "Contas em qualquer fornecedor de identidade ou diretório organizacional (para autenticar utilizadores com fluxos de utilizador)"
1. Para esta amostra, desmarque a caixa de "Consentimento administrativo", já que não exigiremos offline_access permissões hoje.
1. Clique em “Registar”.
1. Grave o ID do cliente de aplicação de backend para utilização posterior (apresentado em 'Aplicação (cliente) ID').
1. Selecione o separador *Certificados e Segredos* (em Gestão) e, em seguida, clique em 'Novo Segredo de Cliente' para gerar uma tecla de auth (Aceite as definições predefinidos e clique em 'Adicionar').
1. Ao clicar em 'Add', copie a chave (em 'valor') em algum lugar seguro para posterior utilização como o 'segredo do cliente backend' - note que este diálogo é a única hipótese que terá de copiar esta chave.
1. Selecione agora o *separador Expor um Separador API* (Under Manage).
1. Será solicitado que estabeleça o AppID URI, selecione e grave o valor predefinido.
1. Crie e nomeie o âmbito "Olá" para a sua Função API, pode utilizar a frase "Olá" para todas as opções insutilizáveis, registando o valor de alcance completo povoado URI e, em seguida, clique em 'Add Scope'.
1. Volte à raiz da lâmina Azure AD B2C selecionando a migalha de pão 'Azure AD B2C' na parte superior esquerda do portal.

   > [!NOTE]
   > Os âmbitos AZure AD B2C são efetivamente permissões dentro da sua API a que outras aplicações podem solicitar acesso através da lâmina de acesso API a partir das suas aplicações, efetivamente acaba de criar permissões de aplicação para a sua chamada API.

## <a name="configure-the-frontend-application"></a>Configurar a aplicação frontend

1. Selecione o separador **Registos de Aplicações**
1. Clique no botão 'Novo Registo'.
1. Escolha 'Aplicação de página única (SPA)' na caixa de seleção URI de redirecionamento.
1. Agora desajei o Nome de Exibição e AppID URI, escolha algo único e relevante para a aplicação Frontend que utilizará este registo de aplicações AAD B2C. Neste exemplo, pode utilizar a "Aplicação frontend"
1. De acordo com o registo da primeira aplicação, deixe a seleção de tipos de conta suportada por defeito (autenticando utilizadores com fluxos de utilizador)
1. Use espaços reservados para os urls de resposta, como https://jwt.ms ' (um site de descoding de token da Microsoft), atualizaremos esses urls mais tarde.
1. Deixe a caixa de consentimento de administração de concessão assinalada
1. Clique em “Registar”.
1. Grave o ID do cliente da aplicação frontal para utilização posterior (apresentado em 'ID de aplicação (cliente)").
1. Mude para o separador permissões da *API.*
1. Conceder acesso à aplicação backend clicando em 'Adicionar uma permissão', em seguida'As minhas APIs', selecione a 'Aplicação de backend', selecione 'Permissões', selecione o âmbito criado na secção anterior e clique em 'Adicionar permissões'
1. Clique em 'Grant admin consent for {tenant} e clique em 'Sim' a partir do diálogo popup. Este popup consente a "Aplicação Frontend" para usar a permissão "olá" definida na "Aplicação backend" criada anteriormente.
1. Todas as permissões devem agora mostrar para a app como um tique verde sob a coluna de estado

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Criar um fluxo de utilizador "Iniciar sessão e iniciar sessão"

1. Volte à raiz da lâmina B2C selecionando a migalha de pão Azure AD B2C.
1. Mude para o separador 'Fluxos de Utilizador' (Em Políticas).
1. Clique em "Novo fluxo de utilizador"
1. Escolha o tipo de fluxo de utilizador 'Iniciar e iniciar sessão' e selecione 'Recomendado' e, em seguida, 'Criar'
1. Dê um nome à apólice e grave-o para mais tarde. Para este exemplo, pode utilizar o "Frontendapp_signupandsignin", note que este será prefixado com "B2C_1_" para fazer "B2C_1_Frontendapp_signupandsignin"
1. Em 'Fornecedores de identidade' e "Contas locais", consulte 'Email Sign up' (ou 'User ID sign up' dependendo da config do seu inquilino B2C) e clique em OK. Esta configuração deve-se ao registo de contas B2C locais, não adiando para outro fornecedor de identidade (como um fornecedor de identidade social) para usar a conta de um utilizador nas redes sociais.
1. Deixe o MFA e as definições de acesso condicional ao seu padrão.
1. Em 'Atributos e reclamações do utilizador', clique em 'Mostrar Mais...' em seguida, escolha as opções de reclamação que deseja que os seus utilizadores entrem e tenham devolvido no token. Verifique pelo menos 'Display Name' e 'Email Address' para recolher, com 'Display Name' e 'Email Addresss' para devolver (preste atenção ao facto de estar a recolher emailaddress, singular e pedir para devolver endereços de e-mail, múltiplos) e clicar em 'OK', e clicar em 'Criar'.
1. Clique no fluxo de utilizador que criou na lista e, em seguida, clique no botão 'Executar o fluxo do utilizador'.
1. Esta ação abrirá a lâmina de fluxo do utilizador de execução, selecionará a aplicação frontend, copiará o ponto final do fluxo do utilizador e guardá-la-á para mais tarde.
1. Copie e guarde o link na parte superior, registando como o "conhecido ponto final de configuração openid" para posterior utilização.

   > [!NOTE]
   > As políticas B2C permitem-lhe expor os pontos finais de login Azure AD B2C para poder capturar diferentes componentes de dados e assinar nos utilizadores de diferentes formas.
   > 
   > Neste caso, configuramos um sinal ou assinamos no fluxo (política). Isto também expôs um ponto final de configuração bem conhecido, em ambos os casos a nossa política criada foi identificada no URL pelo parâmetro de cadeia de consulta "p=".  
   >
   > Uma vez feito isto, tem agora uma plataforma de identidade funcional Business to Consumer que irá inscrever os utilizadores em várias aplicações.

## <a name="build-the-function-api"></a>Construir a função API

1. Volte para o seu inquilino padrão Azure AD no portal Azure para que possamos configurar itens na sua subscrição novamente.
1. Vá à lâmina de Apps de Função do portal Azure, abra a sua aplicação de função vazia e, em seguida, clique em 'Funções', clique em 'Adicionar'.
1. No flyout que aparece, escolha 'Desenvolver no portal', em 'selecione um modelo' e, em seguida, escolha 'HTTP trigger', nos detalhes do Modelo nomeá-lo 'olá' com nível de autorização 'Função', em seguida, selecione Adicionar.
1. Mude para a lâmina de teste + e copie o código de amostra a partir de baixo *sobre o código existente* que aparece.
1. Selecione Guardar.

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

   > [!TIP]
   > O código de função de script c# que acaba de colar simplesmente regista uma linha para os registos de funções e devolve o texto "Hello World" com alguns dados dinâmicos (a data e hora).

1. Selecione "Integração" a partir da lâmina esquerda e, em seguida, clique na ligação http (req) dentro da caixa 'Trigger'.
1. A partir do dropdown 'Selecionados métodos HTTP', desmarque o método http POST, deixando apenas OBT selecionado e, em seguida, clique em Guardar.
1. Volte para o separador Código + Teste, clique em 'Obter URL de função', em seguida, copie o URL que aparece e guarde-o para mais tarde.

   > [!NOTE]
   > As encadernações que acabou de criar simplesmente dizem às Funções para responder em pedidos anónimos http GET para o URL que acabou de copiar `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` (). Agora temos uma API https sem servidor escalável, que é capaz de devolver uma carga útil muito simples.
   >
   > Agora pode testar a chamada a este API a partir de um navegador web usando a sua versão do URL acima que acabou de copiar e guardar. Também pode remover os parâmetros de cadeia de consulta "?code=secretkey" do URL , e testar novamente, para provar que as Funções Azure retornam um erro de 401.

## <a name="configure-and-secure-the-function-api"></a>Configurar e assegurar a função API

1. Duas áreas extra na aplicação de função precisam de ser configuradas (Autorização e Restrições de Rede).
1. Em primeiro lugar, vamos configurar a autenticação / Autorização, por isso, volte para a lâmina de raiz da aplicação de função através da migalha de pão.
1. Selecione seguinte 'Autenticação/ Autorização' (em 'Definições').
1. Ligue a função de autenticação do Serviço de Aplicações.
1. Desacorda a Ação a tomar quando o pedido não for autenticado para "Iniciar sessão com o Azure Ative Directory".
1. Em 'Fornecedores de Autenticação', escolha 'Azure Ative Directory'.
1. Escolha 'Avançado' a partir do interruptor modo de gestão.
1. Cole o ID do cliente da aplicação Backend (de Azure AD B2C) na caixa 'Client ID'
1. Cole o conhecido ponto final de configuração de id aberto a partir da inscrição e inscreva-se na política na caixa URL do Emitente (registamos esta configuração anteriormente).
1. Clique em 'Mostrar Segredo' e cole o segredo do cliente da aplicação Backend na caixa apropriada.
1. Selecione OK, que o leva de volta à lâmina/ecrã de seleção do fornecedor de identidade.
1. Deixe [a Token Store](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#token-store) ativada em definições avançadas (predefinição).
1. Clique em 'Guardar' (na parte superior esquerda da lâmina).

   > [!IMPORTANT]
   > Agora a sua Função API é implantada e deve lançar 401 respostas se o JWT correto não for fornecido como Uma Autorização: Cabeçalho ao portador, e deve devolver dados quando um pedido válido for apresentado.  
   > Adicionou segurança adicional em profundidade na EasyAuth, configurando a opção 'Login with Azure AD' para lidar com pedidos não autenticados. Esteja ciente de que isto irá alterar o comportamento de pedido não autorizado entre a App de Função Backend e o Frontend SPA, uma vez que a EasyAuth emitirá um redirecionamento 302 para a AAD em vez de uma resposta 401 Não Autorizada, corrigiremos isso utilizando a API Management mais tarde.  
   >
   > Ainda não temos nenhuma segurança IP aplicada, se você tem uma chave válida e token OAuth2, qualquer um pode chamá-lo de qualquer lugar - idealmente queremos forçar todos os pedidos a vir através da API Management.  
   > 
   > Se estiver a utilizar o nível de consumo de APIM, então [não existe um IP Virtual de Gestão API dedicado](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) para permitir a lista com as restrições de acesso às funções. No Azure API Management Standard SKU e acima [do VIP é inquilino único e para toda a vida do recurso.](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) Para o nível de Consumo de Gestão API Azure, pode bloquear as suas chamadas API através da chave de função secreta partilhada na parte do URI que copiou acima. Além disso, para o nível de consumo - os passos 12-17 abaixo não se aplicam.

1. Feche a lâmina 'Autenticação / Autorização' 
1. Abra a *lâmina de Gestão API do portal* e, em seguida, abra a sua *instância*.
1. Grave o VIP privado apresentado no separador visão geral.
1. Volte à *lâmina Azure Functions do portal* e, em seguida, abra *novamente o seu caso.*
1. Selecione 'Networking' e, em seguida, selecione 'Configurar restrições de acesso'
1. Clique em 'Adicionar Regra', e introduza o VIP copiado no passo 3 acima no formato xx.xx.xx.xx.xx/32.
1. Se pretender continuar a interagir com o portal de funções e a realizar os passos opcionais abaixo, deve adicionar também aqui o seu próprio endereço IP público ou alcance CIDR.
1. Uma vez que haja uma entrada de permiti na lista, Azure adiciona uma regra implícita de negação para bloquear todos os outros endereços.

Terá de adicionar blocos de endereços formatados ciDR ao painel de restrições IP. Quando precisa de adicionar um único endereço, como o API Management VIP, tem de o adicionar no formato xx.xx.xx.xx.xx/32.

   > [!NOTE]
   > Agora, a sua API de função não deve ser chamada de outro lugar que não seja através da gestão da API, ou do seu endereço.

1. Abra a *lâmina de gestão da API* e, em seguida, abra *a sua instância*.
1. Selecione a Lâmina apis (em APIs).
1. A partir do painel 'Adicionar uma Nova API', escolha 'App de função', e depois selecione 'Full' a partir do topo do popup.
1. Clique em Procurar, escolha a aplicação de função que está hospedando a API no interior e clique em selecionar. Em seguida, clique em selecionar novamente.
1. Dê à API um nome e descrição para o uso interno da API Management e adicione-o ao Produto 'ilimitado'.
1. Copiar e gravar o 'URL base' da API e clicar em 'criar'.
1. Clique no separador 'definições' e, em seguida, em subscrição - desligue a caixa de verificação 'Subscrição Necessária', uma vez que usaremos o token Oauth JWT neste caso para limitar a taxa. Note que se estiver a utilizar o nível de consumo, isso continuaria a ser necessário num ambiente de produção. 

   > [!TIP]
   > Se utilizar o nível de consumo de APIM, o produto ilimitado não estará disponível como um fora da caixa. Em vez disso, navegue para "Produtos" em "APIs" e atinja "Add".  
   > Digite "Ilimitado" como nome e descrição do produto e selecione a API que acaba de adicionar a partir da chamada de APIs "+" na parte inferior esquerda do ecrã. Selecione a caixa de verificação "publicada". Deixe o resto como padrão. Finalmente, acerte no botão "criar". Isto criou o produto "ilimitado" e atribuiu-o à sua API. Pode personalizar o seu novo produto mais tarde.

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>Configure e capture as definições corretas do ponto final de armazenamento

1. Abra a lâmina das contas de armazenamento no portal Azure 
1. Selecione a conta criada e selecione a lâmina 'Static Website' a partir da secção Definições (se não vir uma opção 'Site Estático', verifique se criou uma conta V2).
1. Deslote a função de hospedagem estática na web para 'activado', e deslote o nome do documento de índice para 'index.html', e depois clique em 'guardar'.
1. Note o conteúdo do 'Ponto final primário' para mais tarde, uma vez que este local é onde o site frontend será hospedado.

   > [!TIP]
   > Você poderia usar ou Azure Blob Storage + CDN reescrita, ou Azure App Service para hospedar o SPA - mas a funcionalidade de hospedagem estática do Site da Blob Storage dá-nos um recipiente predefinido para servir conteúdo estático na Web / html / js / css da Azure Storage e irá inferir uma página padrão para nós para zero trabalho.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Configurar as políticas **CORS** e **validar-jwt**

> As seguintes secções devem ser seguidas independentemente da utilização do nível APIM. O URL da conta de armazenamento é da conta de armazenamento que terá disponibilizado a partir dos pré-requisitos no topo deste artigo.
1. Mude para a lâmina de gestão API do portal e abra o seu exemplo.
1. Selecione APIs e, em seguida, selecione "Todas as APIs".
1. Em "Processamento de entrada", clique no botão de visualização de código "</>" para mostrar ao editor de política.
1. Edite a secção de entrada e cole a xml abaixo para que se leia como se fosse o seguinte.
1. Substitua os seguintes parâmetros na Política
1. {PrimaryStorageEndpoint} (O 'Ponto final de armazenamento primário' que copiou na secção anterior), {b2cpolicy-bem conhecido-openid} (O 'conhecido ponto final de configuração aberta' que copiou anteriormente) e {backend-api-application-client-id} (Aplicação B2C / ID do cliente para a **API de backend)** com os valores corretos guardados anteriormente.
1. Se estiver a utilizar o nível de consumo da Gestão de API, então deve remover ambas as políticas de limite de taxa por chave, uma vez que esta política não está disponível ao utilizar o nível de consumo da Gestão API Azure.

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > Agora, a gestão da API da Azure é capaz de responder aos pedidos de origem cruzada das suas aplicações JavaScript SPA, e irá executar estrangulamento, limitação de tarifas e pré-validação do token JWT auth sendo passado antes de encaminhar o pedido para a API de função.
   > 
   > Parabéns, tem agora Azure AD B2C, API Management e Azure Functions trabalhando em conjunto para publicar, proteger e consumir uma API!

   > [!TIP]
   > Se estiver a utilizar o nível de consumo de Gestão API, em vez de limitar a taxa pelo assunto JWT ou pelo endereço IP (A taxa de chamada limite por política-chave não é suportada hoje para o nível "Consumo"), pode limitar por quota de taxa de chamada ver [aqui.](./api-management-access-restriction-policies.md#LimitCallRate)  
   > Como este exemplo é uma Aplicação de página única JavaScript, utilizamos a Chave de Gestão da API apenas para chamadas de limitação de taxas e faturação. A autorização e autenticação real é tratada pelo Azure AD B2C, e é encapsulada no JWT, que é validado duas vezes, uma pela API Management, e depois pela Função Azure backend.

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>Faça o upload da amostra JavaScript SPA para armazenamento estático

1. Ainda na lâmina da conta de armazenamento, selecione a lâmina 'Containers' da secção Serviço Blob e clique no recipiente $web que aparece no painel direito.
1. Guarde o código abaixo para um ficheiro localmente na sua máquina, uma vez index.html e, em seguida, faça o upload do ficheiro index.html para o recipiente $web.

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. Navegue no ponto de final primário do site estático que armazenou anteriormente na última secção.

   > [!NOTE]
   > Parabéns, acabaste de implementar uma App de Página Única JavaScript para hospedar conteúdo estático de armazenamento de Azure.  
   > Uma vez que ainda não configuramos a aplicação JS com os seus detalhes Azure AD B2C – a página ainda não funcionará se a abrir.

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>Configure o JavaScript SPA para Azure AD B2C

1. Agora sabemos onde está tudo: podemos configurar o SPA com o endereço API de Gestão de API adequado e a aplicação/IDs de cliente Azure AD B2C.
1. Volte para a lâmina de armazenamento do portal Azure 
1. Selecione 'Contentores' (em 'Definições') 
1. Selecione o recipiente '$web' da lista
1. Selecione index.html blob da lista 
1. Clique em 'Editar' 
1. Atualize os valores de auth na secção msal config para corresponder à sua aplicação *frontal* que registou anteriormente em B2C. Utilize os comentários de código para obter pistas sobre como os valores config devem parecer.
O valor *da autoridade* tem de estar no formato:- https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantname}.onmicrosoft.com}/{signupandsigninpolicyname}, se usou os nossos nomes de amostra e o seu inquilino b2c é chamado de 'contoso', então esperaria que a autoridade fosse ' https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin .
1. Defina os valores api para corresponder ao seu endereço de backend (O Url base da API que gravou anteriormente, e os valores 'b2cScopes' foram registados anteriormente para a *aplicação backend*).
1. Clicar em Guardar

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Desaprova os URIs de redirecionamento para a app frontal Azure AD B2C

1. Abra a lâmina Azure AD B2C e navegue para o registo de aplicação para a Aplicação Frontend JavaScript.
1. Clique em 'Redirecionar URIs' e apagar o espaço reservado ' https://jwt.ms '' que inserimos anteriormente.
1. Adicione um novo URI para o ponto final primário (armazenamento) (menos o corte para a frente).

   > [!NOTE]
   > Esta configuração resultará em que um cliente da aplicação frontend receba um token de acesso com reclamações apropriadas da Azure AD B2C.  
   > O SPA poderá adicionar isto como um símbolo portador no cabeçalho https na chamada para a API backend.  
   > 
   > A API Management irá pré-validar as chamadas simbólicas, limite de taxa para o ponto final, tanto pelo sujeito do JWT emitido pelo Azure ID (o utilizador) como pelo endereço IP do chamador (dependendo do nível de serviço da Gestão da API, ver a nota acima), antes de passar o pedido à API da Função Azure recetora, adicionando a chave de segurança das funções.  
   > O SPA irá dar a resposta no navegador.
   >
   > *Parabéns, configuraste a Azure AD B2C, Azure API Management, Azure Functions, Azure App Service Authorization to work in perfect harmony!*

Agora temos uma aplicação simples com uma API segura simples, vamos testá-la.

## <a name="test-the-client-application"></a>Testar a aplicação do cliente

1. Abra o URL da aplicação de amostra que anotado a partir da conta de armazenamento que criou anteriormente.
1. Clique em "Iniciar sessão" no canto superior direito, este clique irá aparecer no seu Azure AD B2C ou iniciar sessão de perfil.
1. A aplicação deve recebê-lo pelo seu nome de perfil B2C.
1. Clique em "Call API" e a página deverá atualizar-se com os valores enviados da sua API protegida.
1. Se clicar *repetidamente* no botão Call API e estiver a funcionar no nível de desenvolvimento ou acima da API Management, deve ter em atenção que a sua solução começará a limitar a API e esta funcionalidade deve ser reportada na app com uma mensagem apropriada.

## <a name="and-were-done"></a>E nós terminamos

Os passos acima podem ser adaptados e editados para permitir muitas utilizações diferentes do Azure AD B2C com a API Management.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Azure Ative Directory e o OAuth2.0](../active-directory/develop/authentication-vs-authorization.md).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre a API Management.
* Para obter outras formas de garantir o seu serviço back-end, consulte a [autenticação do Certificado Mútuo.](api-management-howto-mutual-certificates.md)
* [Criar uma instância de serviço de Gestão API.](get-started-create-service-instance.md)
* [Gerencie a sua primeira API.](import-and-publish.md)
