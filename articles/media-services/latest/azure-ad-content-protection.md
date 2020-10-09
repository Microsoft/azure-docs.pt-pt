---
title: Proteção de conteúdo de ponta a ponta utilizando Azure AD
description: Este artigo ensina-lhe como proteger os seus conteúdos com a Azure Media Services e o Azure Ative Directory
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: a6f1a5b532ba3d8d5ce24d6f9856d86719d35c6f
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839542"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Tutorial: Proteção de conteúdo de ponta a ponta utilizando Azure AD

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Com este tutorial e a amostra de jogador fornecida, pode configurar um subsistema de proteção de conteúdos de mídia de ponta a ponta nos serviços Azure Media Services (AMS) e Azure Ative Directy (AAD) para transmitir conteúdo sonoro com todos os formatos DE DRM/AES-128 suportados pela AMS, protocolos de streaming, formatos codec e container. A amostra é genérica o suficiente para acesso seguro a qualquer API REST protegida pela OAuth 2 através do Fluxo de Código de Autorização com Chave de Prova para Troca de Código (PKCE). (O serviço de entrega de licenças Azure Media Services é apenas um deles.) Também funciona para a Microsoft Graph API ou qualquer API de REST desenvolvido sob medida, protegido com OAuth 2 Authorization Code Flow. Este é o documento de companhia do código de [amostra.](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)

Neste tutorial, irá:

> [!div class="checklist"]
>
> * Considere os requisitos de autenticação
> * Entenda como funciona a aplicação
> * Registar uma aplicação de recursos backend
> * Registar uma aplicação de cliente
> * Criar a política-chave de conteúdos de conta de serviços de mídia e políticas de streaming
> * Configurar a aplicação do jogador

Se não tiver uma subscrição do Azure Media Services, crie uma [conta de teste gratuito](https://azure.microsoft.com/free/) Azure e crie uma conta de Serviços de Comunicação Social.

### <a name="duration"></a>Duração
O tutorial deve demorar cerca de duas horas a ser concluído depois de ter a tecnologia pré-requisito pronta para avançar.

## <a name="prerequisites"></a>Pré-requisitos

São utilizadas as versões e conceitos mais recentes da tecnologia. Recomenda-se que se familiarize com eles antes de começar este tutorial.

### <a name="prerequisite-knowledge"></a>Conhecimento dos pré-requisitos

É opcional, mas recomenda-se que esteja familiarizado com os seguintes conceitos antes de iniciar este tutorial:

* Gestão de Direitos Digitais (DRM)
* [Azure Media Services (AMS) v3](./media-services-overview.md)
* [Políticas-chave de conteúdo](content-key-policy-concept.md) AMS utilizando a ferramenta AMS API v3, Azure portal ou a ferramenta [Azure Media Services Explorer (AMSE)](https://github.com/Azure/Azure-Media-Services-Explorer)
* Pontos finais Azure AD v2 na [Plataforma de Identidade](../../active-directory/develop/index.yml) da Microsoft
* Autenticação em nuvem moderna como [OAuth 2.0 e OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [Fluxo de código de autorização em OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md) e por que o PKCE é necessário
  * [Permissão delegada vs permissão de candidatura](../../active-directory/develop/developer-glossary.md#permissions)
* [JWT token](../../active-directory/develop/access-tokens.md), suas alegações, e assinatura de capotamento de chave (incluído na amostra.)

### <a name="prerequisite-code-and-installations"></a>Código e instalações pré-requisitos

* O código de amostra. Descarregue o código de [amostra.](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)
* Uma instalação do Código do Estúdio Visual. Baixe aqui o Código do Estúdio Visual [https://code.visualstudio.com/download](https://code.visualstudio.com/download) .
* Uma instalação de Node.js. Faça o download Node.js [https://nodejs.org](https://nodejs.org) aqui. A NPM vem com a instalação.
* Uma [subscrição do Azure](https://azure.microsoft.com/free/).
* Uma conta Azure Media Services (AMS).
* @azure/msal-browser v2.0, um dos membros da [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) SDK família para diferentes plataformas de clientes
* A versão mais recente do [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)(incluído na amostra.)
* Credenciais FPS da Apple se quiser incluir FairPlay DRM e o certificado de aplicação alojado com CORS que é acessível através do JavaScript do lado do cliente.

> [!IMPORTANT]
> Este tutorial utiliza .NET para criar a restrição de política de chave de conteúdo.  Se não é um desenvolvedor .NET, mas quer tentar Node.js ligar-se aos Serviços de Media Azure, leia [Connect to Media Services v3 API - Node.js](configure-connect-nodejs-howto.md). Existe também um módulo Node.js disponível para lidar automaticamente com a capotamento da chave, ver Node.js [módulo de anúncio de passaporte](https://github.com/AzureAD/passport-azure-ad).

## <a name="consider-the-authentication-and-authorization-requirements"></a>Considere os requisitos de autenticação e autorização

Alguns desafios são apresentados na conceção do subsistema. Tem várias peças móveis, existem constrangimentos na aplicação do cliente, e a chave AD AD Azure que ocorre a cada seis semanas.

A App Single-Page (SPA) utilizada neste tutorial tem em conta os desafios dos requisitos de autenticação e as restrições que se seguem. Utiliza:

* Os pontos finais AZURE AD v2 como plataforma de desenvolvimento AD AZure (v1 pontos finais) estão a mudar para a Microsoft Identity Platform (v2 pontos finais).
* Fluxo de Código de Autorização porque o fluxo de subvenção implícito OAuth 2 foi depreciado.
* Uma aplicação sujeita aos seguintes constrangimentos:
    * Um cliente público não pode esconder o segredo do cliente.  O Fluxo de Código de Autorização por si só requer esconder o segredo do cliente, por isso o Fluxo de Código de Autorização com PKCE é usado.
    * Uma aplicação baseada no navegador que está sujeita a uma caixa de areia de segurança do navegador (CORS/restrição de pré-voo).
    * Uma aplicação baseada no navegador usando o JavaScript moderno que está sujeito a restrições de segurança JavaScript (acessibilidade personalizada do cabeçalho, id de correlação).

## <a name="understand-the-subsystem-design"></a>Compreender o design do subsistema

O desenho do subsistema é mostrado no diagrama seguinte.  Tem três camadas:

* Camada de back-office (em preto) para configurar a política de chave de conteúdo e publicar conteúdo para streaming
* Pontos finais públicos (em azul) que são pontos finais voltados para o jogador/cliente que fornecem autenticação, autorização, licença de DRM e conteúdo encriptado
* App do jogador (em azul claro) que integra todos os componentes e
    * lida com a autenticação do utilizador via Azure AD.
    * lida com access_token aquisição da Azure AD.
    * recebe conteúdo manifesto e encriptado da AMS/CDN.
    * adquire licença de DRM da Azure Media Services.
    * lida com a desencriptação, descodificar e exibir o conteúdo.

![tela para analisar tokens JWT](media/aad-ams-content-protection/subsystem.svg)

Leia [Design de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](./design-multi-drm-system-with-access-control.md) para mais detalhes sobre o subsistema.

## <a name="understand-the-single-page-app"></a>Compreender a aplicação de página única

A aplicação para jogadores é uma aplicação de página única (SPA), desenvolvida no Código do Estúdio Visual utilizando:

* Node.js com ES 6 JavaScript
* @azure/msal-browser 2.0 beta
* Azure Media Player SDK
* Fluxo OAu 2 contra pontos finais Azure AD v2 (Microsoft Identity Platform)

A aplicação do jogador SPA completa as seguintes ações:

* Autenticação do utilizador para utilizadores nativos do inquilino, e utilizadores convidados de outros inquilinos da AAD ou contas MSA. Os utilizadores podem optar por iniciar sinsípis através de um popup ou redirecionamento de navegadores (para navegadores que não permitem popups como o Safari).
* Aquisição através do Código de `access_token` Autorização Flow com PKCE.
* A renovação de `access_token` (fichas emitidas pela AAD são válidas por 1 hora), para a qual `refresh_token` também é adquirida.
* Análise das fichas JWT (ambas `access_token` e `id_token` ) para teste/inspeção.
* Aquisição de licenças de DRM para as três chaves de conteúdo DRMs ou AES-128.
* Streaming de conteúdos sob várias combinações de DRM vs Protocolo de Streaming vs Formato de Contentor. A cadeia de formato correta é gerada para cada combinação.
* Desencriptação, descodificar e exibir.
* A Microsoft Graph API pede para efeitos de resolução de problemas. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

O ecrã para o sign-in, aquisição simbólica, renovação simbólica e ecrã simbólico:

 ![Tela para iniciar sposição, aquisição simbólica, renovação de fichas e exibição simbólica](media/aad-ams-content-protection/token-acquisition.png)

O ecrã para analisar fichas JWT (access_token ou id_token):

![Screenshot que mostra a análise de fichas J W T.](media/aad-ams-content-protection/parsing-jwt-tokens.png)

O ecrã para testar conteúdo protegido com diferentes combinações de DRM/AES vs Protocolos de Streaming vs Formato de Contentor:

![Screenshot que mostra o teste de conteúdo protegido com diferentes combinações de D R M ou A E S versus Protocolos de Streaming versus Formato de Contentor](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Escolha um inquilino AZure AD

> [!NOTE]
> A partir daqui, presume-se que você entrou no portal Azure e tem pelo menos um inquilino AZure AD.

Escolha um inquilino AZure AD para usar para a nossa amostra de ponta a ponta. Tem duas opções:

* Um inquilino existente da Azure AD. Qualquer subscrição da Azure deve ter um inquilino Azure AD, mas um inquilino AZure AD pode ser usado por várias subscrições da Azure.
* Um novo inquilino AZure AD que *não* é usado por qualquer subscrição da Azure. Se escolher a nova opção de inquilino, a conta de serviço de mídia e a app do leitor de amostras devem estar numa subscrição do Azure que utiliza um inquilino Azure AD separado. Isto proporciona alguma flexibilidade. Por exemplo, você poderia usar o seu próprio inquilino Azure AD, mas também a conta de serviço de mídia do cliente na subscrição Azure do cliente.

## <a name="register-the-backend-resource-app"></a>Registar a app de recursos backend

1. Navegue até ao inquilino AZure AD que escolheu ou criou.
1. Selecione **Azure Ative Directory** no menu.
1. Selecione **as inscrições** da App no menu.
1. Clique **+ Novo Registo.**
1. Nomeie a aplicação *LicenseDeliveryResource2* (onde 2 indica pontos finais AAD v2).
1. Selecione **Contas apenas neste diretório organizacional ([o*nome do seu inquilino]* apenas - Inquilino Único)**. Se quiser permitir o acesso a vários inquilinos, selecione uma das outras opções multitenantes.
1. O **Redirect URI** é opcional e pode ser alterado mais tarde.
1. Clique em **Registar**. A vista de registos da App aparecerá.
1. Selecione **Manifesto** no menu. A vista manifesto aparecerá.
1. Alterar o valor do `accessTokenAcceptedVersion` para *2* (sem cotações).
1. Alterar o valor do `groupMembershipClaims` *"SecurityGroup"* (com cotações).
1. Clique em **Guardar**.
1. Selecione **Expor uma API** do menu. Aparecerá a vista De âmbito adicionar. (O Azure fornece um ID URI de aplicação, mas se quiser alterá-lo, pode editar no campo ID URI da aplicação.)
1. Clique **em Guardar e continuar.** A vista vai mudar. Para cada uma das definições na coluna Definição na tabela abaixo, introduza o valor na coluna Valor e, em seguida, clique em **Adicionar âmbito**.

| Definição | Valor | Descrição |
| ------- | ----- | ----------- |
| Nome do âmbito | *DRM. Licença.Entrega* | Como o âmbito será apresentado quando o acesso a esta API for solicitado, e em fichas de acesso quando o âmbito tiver sido concedido a uma aplicação de cliente. Isto deve ser único em toda esta aplicação. Uma boa prática é usar "resource.operation.constraint" como um padrão para gerar o nome. |
| Quem pode consentir? | *Administradores e utilizadores* | Determina se os utilizadores podem consentir com este âmbito em diretórios onde o consentimento do utilizador está ativado. |
| Nome a apresentar do consentimento do administrador | *Entrega de licença DRM* | Qual será o âmbito de aplicação no ecrã de consentimento quando os administradores concordarem com este âmbito. |
| Descrição do consentimento de administração** | *Âmbito de recursos de backend de entrega de licença DRM* | Uma descrição detalhada do âmbito que é exibido quando os administradores do inquilino expandem um âmbito no ecrã de consentimento. |
| Nome a apresentar do consentimento do utilizador | *DRM. Licença.Entrega* | Qual será o âmbito de aplicação no ecrã de consentimento quando os utilizadores concordarem com este âmbito. |
| Descrição do consentimento do utilizador | *Âmbito de recursos de backend de entrega de licença DRM* | Esta é uma descrição detalhada do âmbito que é apresentado quando os utilizadores expandem um âmbito no ecrã de consentimento. |
| Estado | *Ativado* | Determina se este âmbito está disponível para os clientes solicitarem. Desativá-lo para "Desativado" para âmbitos que não queira ser visível para os clientes. Apenas os âmbitos de desativação podem ser eliminados, e recomendamos esperar pelo menos uma semana após o desativação de um âmbito antes de o eliminar para garantir que nenhum cliente ainda o utiliza. |

## <a name="register-the-client-app"></a>Registar a aplicação do cliente

1. Navegue até ao inquilino AZure AD que escolheu ou criou.
1. Selecione **Azure Ative Directory** no menu.
1. Selecione **as inscrições** da App no menu.
1. Clique **+ Novo Registo.**
1. Dê ao cliente um nome, por exemplo, *AMS AAD Content Protection*.
1. Selecione **Contas apenas neste diretório organizacional ([o*nome do seu inquilino]* apenas - Inquilino Único)**. Se quiser permitir o acesso a vários inquilinos, selecione uma das outras opções multitenantes.
1. O **Redirect URI** é opcional e pode ser alterado mais tarde.
1. Clique em **Registar**.
1. Selecione **permissões API** do menu.
1. Clique **+ Adicione uma permissão.** A vista de permissões da API request será aberta.
1. Clique no **separador API** e selecione a aplicação *LicenseDeliveryResource2* criada na última secção.
1. Clique na seta DRM e verifique o *DRM. Licença.Permissão de entrega.*
1. Clique **em Adicionar permissões**. A vista de permissões Add será próxima.
1. Selecione **Manifesto** no menu. A vista manifesto aparecerá.
1. Encontre e adicione os seguintes pares de valor ao `replyUrlsWithType` atributo:

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > Neste momento, ainda não tem o URL para a sua aplicação de jogador.  Se estiver a executar a aplicação a partir do seu webserver local, pode usar apenas o par de valor local. Assim que implementar a aplicação do jogador, pode adicionar a entrada aqui com o URL implantado.  Se se esquecer de o fazer, verá uma mensagem de erro no sinal AZure AD.

1. Clique em **Guardar**.
1. Finalmente para verificar se a sua configuração está correta, selecione **Autenticação**.  Aparecerá a vista de autenticação. A sua aplicação ao cliente será listada como uma App de Página Única (SPA), o URI de redirecionamento será listado, e o tipo de subvenção será O Fluxo de Código de Autorização com PKCE.

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Criar a política-chave de conteúdos de conta dos Media Services e as políticas de streaming

**Esta secção assume que é um desenvolvedor .NET e está familiarizado com a utilização da AMS v3 API.**

> [!NOTE]
> A partir desta escrita, não pode utilizar o portal Azure para a configuração da política chave de serviços de comunicação porque não suporta usar uma chave de assinatura de ficha assimétrica com OpenID-Config.  A configuração deve suportar a capotagem da chave Azure AD porque o token Ad emitido azure é assinado por uma chave assimétrica e a chave rola a cada seis semanas. Portanto, este tutorial utiliza .NET e a AMS v3 API.

Aplicam-se a configuração da [política-chave](content-key-policy-concept.md) de conteúdos e [políticas de streaming](streaming-policy-concept.md) para DRM e AES-128.  Alterar a `ContentKeyPolicyRestriction` política de chave de conteúdo.

Abaixo está o código .NET para criar a restrição de política de chave de conteúdo.

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

Altere o `ida_AADOpenIdDiscoveryDocument` `ida_audience` , e `ida_issuer` valores no código acima. Para encontrar os valores destes itens no portal Azure:

1. Selecione o inquilino AAD que usou anteriormente, clique nas **inscrições** da App no menu e, em seguida, clique no link **Endpoints.**
1. Selecione e copie o valor do campo de **metadados OpenIdConnect** e cole-o no código como `ida_AADOpenIdDiscoveryDocument` valor.
1. O `ida_audience` valor é o ID de Aplicação (cliente) da aplicação registada *LicenseDeliveryResource2*.
1. O `ida_issuer` valor é o `https://login.microsoftonline.com/[tenant_id]/v2.0` URL. Substitua *[tenant_id]* pela identificação do seu inquilino.

## <a name="set-up-the-sample-player-app"></a>Configurar a aplicação do leitor de amostras

Se ainda não o fez, clone ou descarregue a aplicação a partir da origem repo: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad) .

Tem duas opções para configurar a aplicação do jogador:

* Personalização mínima (substituindo apenas alguns valores de cadeia constantes, tais como client_id, tenant_id e URL de streaming), mas deve utilizar o Código do Estúdio Visual e Node.js.
* Se preferir utilizar outro IDE e plataforma web, como ASP.NET Core, pode colocar os ficheiros de página web, ficheiros JavaScript e ficheiro CSS no seu projeto, uma vez que a própria aplicação do leitor não utiliza nenhum código do lado do servidor.

### <a name="option-1"></a>Opção 1

1. Inicie o Visual Studio Code.
1. Para abrir o projeto, clique em Ficheiros -> Open Folder -> navegue e selecione a pasta-mãe do *package.jsno* ficheiro.
1. Abra o ficheiro JavaScript *público/javascript/constants.js*.
1. Substitua `OAUTH2_CONST.CLIENT_ID` pela `client_id` aplicação do seu cliente registado no inquilino da AAD.  Pode encontrar a `client_id` secção Geral da aplicação registada no portal Azure. Nota: é a identificação do cliente, não a identificação do objeto.
1. Substitua `OAUTH2_CONST.TENANT_ID` pelo `tenant_id` seu inquilino AZure AD. Pode encontrar o seu `tenant_id` clicando no menu Azure Ative Directory. O tenant_id aparecerá na secção Geral.
1. Substitua `OAUTH2_CONST.SCOPE` pelo âmbito que adicionou na sua aplicação de cliente registado. Pode encontrar o âmbito navegando para a aplicação de cliente registado a partir do menu de **registos** da App e selecionando a sua aplicação para cliente:
    1. Selecione a aplicação do seu cliente, clique no menu **de permissões API** e, em seguida, selecione o âmbito *DRM. Licença.Entrega* sob a permissão API *LicenseDeliveryResource2*. A permissão deve estar no formato como *api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM. Licença.Entrega*. **Importante**: Mantenha o espaço à frente de `offline_access` `OAUTH2_CONST.SCOPE` .
1. Substitua as duas cordas constantes para `AMS_CONST` o que mostra abaixo. Um é o URL de streaming protegido do seu ativo de teste, e o outro é URL de certificado de aplicação FPS se quiser incluir o caso de teste FairPlay. Caso contrário, pode deixá-lo como `AMS_CONST.APP_CERT_URL` é. Em seguida, clique em **Guardar**.

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

Para testar localmente:

1. No Código do Estúdio Visual (VSC), selecione **Vista** do menu principal e depois **Terminal**.
1. Se ainda não instalou npm, na entrada imediata do comando `npm install` .
1. Entre `npm start` na ordem do comando. (Se a npm não começar, tente mudar o diretório para `npmweb` entrar `cd npmweb` na introdução no comando.)
1. Utilize um navegador para navegar para `http://localhost:3000` .

Dependendo do navegador que utilizar, escolha a combinação correta de DRM/AES vs Streaming Protocol vs Container Format para testar após o início de `access_token` sing (aquisição). Se estiver a testar no Safari no macOS, verifique a opção Redirecionar a API uma vez que o Safari não permite popups. A maioria dos outros navegadores permite popups e opções de redirecionamento.

### <a name="option-2"></a>Opção 2

Se planeia utilizar outro IDE/web platform e/ou um webserver como o IIS em execução na sua máquina de desenvolvimento, copie os seguintes ficheiros para um novo diretório no seu webserver local. Os caminhos abaixo são onde os encontrará no código que descarregou.

* *pontos de vista/index.ejs* (alterar sufixo para .html)
* *pontos de vista/jwt.ejs* (alterar sufixo para .html)
* *pontos de vista/info.ejs* (alterar sufixo para html)
* *público/** (ficheiros JavaScript, CSS, imagens como mostrado abaixo)

1. Copie os ficheiros encontrados na pasta *de visualização* até à raiz do novo diretório.
1. Copie as *pastas encontradas* na pasta *pública* até à raiz do novo diretório.
1. Altere as extensões dos `.ejs` ficheiros para `.html` . (Não é utilizada nenhuma variável do lado do servidor para que possa alterá-la com segurança.)
1. Abra *index.html* em VSC (ou outro editor de código) e altere os e caminhos para que `<script>` `<link>` reflitam onde os ficheiros estão localizados.  Se seguiu os passos anteriores, só tem de apagar o `\` caminho.  Por exemplo, `<script type="text/javascript" src="/javascript/constants.js"></script>` torna-se `<script type="text/javascript" src="javascript/constants.js"></script>` .
1. Personalize as constantes no ficheiro *javascript/constants.js* como na Opção 1.

## <a name="common-customer-scenarios"></a>Cenários comuns do cliente

Agora que completou o tutorial e tem um subsistema de funcionamento, pode tentar modificá-lo para os seguintes cenários do cliente:

### <a name="role-based-access-control-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Role-Based Access Control (RBAC) para entrega de licenças através da adesão ao grupo Azure AD

Até ao momento, o sistema permite que qualquer utilizador que possa iniciar sôms para obter uma licença válida e reproduzir o conteúdo protegido.

É um requisito comum do cliente que um subconjunto de utilizadores autenticados seja autorizado a observar conteúdos enquanto outros não são, por exemplo, um cliente que oferece subscrições básicas e premium para o seu conteúdo de vídeo. Os seus clientes, que pagaram por uma subscrição básica, não devem poder assistir a conteúdos que exijam uma subscrição premium. Seguem-se os passos adicionais necessários para satisfazer este requisito:

#### <a name="set-up-the-azure-ad-tenant"></a>Instale o inquilino AZure AD

1. Crie duas contas no seu inquilino. Podem ser *nomeados premium_user* e *basic_user;*
1. Crie um grupo de utilizadores e chame-lhe *PremiumGroup.*
1. Adicione o *premium_user* ao *PremiumGroup* como membro, mas não adicione o *basic_user* ao grupo.
1. Tome nota do **ID do Objeto** do *Grupo Premium.*

#### <a name="set-up-the-media-services-account"></a>Criar a conta dos Serviços de Comunicação Social

Modifique `ContentKeyPolicyRestriction` (como mostrado na secção acima na Configuração na Conta de Serviço de Mídia), adicionando uma reclamação de *grupos nomeados,* onde `ida_EntitledGroupObjectId` tem o ID do objeto do *PremiumGroup* como seu valor:

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

Os *grupos* afirmam ser membro de um Conjunto de [Reclamações Restritas](../../active-directory/develop/active-directory-claims-mapping.md#claim-sets) em Azure AD.

#### <a name="test"></a>Teste

1. Inscreva-se na conta *premium_user.* Deve ser capaz de reproduzir o conteúdo protegido.
1. Inscreva-se na conta *basic_user.* Deve obter um erro que indique que o vídeo está encriptado, mas não há nenhuma chave para desencriptar o mesmo. Se visualizar os Eventos, erros e downloads com a queda na parte inferior da sobreposição de diagnóstico do jogador, a mensagem de erro deve indicar que a licença adquire falha devido ao valor de reclamação em falta para os grupos reclamam no ponto final do JWT emitido pelo Azure AD token.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Suporte a várias contas de serviços de mídia (em várias subscrições)

Um cliente pode ter várias contas de serviço de mídia em uma única ou múltipla subscrição do Azure. Por exemplo, um cliente pode ter uma conta de serviço de mídia como primária de produção, outra como secundária/backup, e outra para dev/teste.

Tudo o que precisa de fazer é garantir que utiliza o mesmo conjunto de parâmetros que utilizou na secção (Configuração na Conta de Serviço de Mídia) na criação `ContentKeyPolicyRestriction` de todas as contas de serviço de mídia.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Apoiar um cliente, os seus fornecedores e/ou subsidiárias em vários inquilinos da AAD

Como utilizadores da solução, as subsidiárias de um cliente, fornecedores/parceiros podem residir em diferentes inquilinos da AAD, tais `mycustomer.com` `mysubsidiary.com` como, e `myparther.com` . Enquanto esta solução é construída sobre um único inquilino específico da AAD, `mycustomer.com` como, você pode fazê-lo funcionar para utilizadores de outros inquilinos.

Utilizando `mycustomer.com` para esta solução, adicione um utilizador `mypartner.com` de como utilizador convidado ao `mycustomer.com` inquilino. Certifique-se de que o `mypartner.com` utilizador ativa a conta do hóspede. A conta de hóspedes pode ser de outro inquilino da AAD ou de uma `outlook.com` conta.

Note-se que os utilizadores convidados de, depois de `mypartner.com` ativados, ainda são autenticados através do `mycustomer.com` seu próprio/original inquilino da `mypartner.com` AAD, mas o é emitido por `access_token` `mycustomer.com` .

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Apoiar um cliente inquilino/subscrição com uma configuração na sua subscrição/inquilino

Pode utilizar a sua configuração para testar conteúdo protegido na conta/subscrição do serviço de comunicação do seu cliente. Você iria coneca-lo com um inquilino AZure AD e uma conta de serviço de mídia na mesma assinatura. A conta de serviço de mídia do cliente estaria na sua assinatura Azure com o seu próprio inquilino AZure AD.

1. Adicione a conta do seu cliente no seu inquilino como conta de hóspedes.
1. Trabalhe com o seu cliente para preparar conteúdo protegido na conta de serviço de mídia do seu cliente, fornecendo os três parâmetros listados na secção Configuração na Conta de Serviço de Mídia.

O seu cliente pode então navegar na sua configuração, iniciar sôr-se na conta do cliente e testar o seu próprio conteúdo protegido. Também pode iniciar sôms com a sua própria conta e testar o conteúdo do seu cliente.

A sua solução de amostra pode ser configurada num inquilino da Microsoft com subscrição da Microsoft ou inquilino personalizado com subscrição da Microsoft. A instância do Azure Media Service pode ser de outra subscrição com o seu inquilino.

## <a name="clean-up-resources"></a>Limpar os recursos

> [!WARNING]
> Se não vai continuar a utilizar esta aplicação, elimine os recursos que criou enquanto segue este tutorial. Caso contrário, serás cobrado por eles.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Quickstart: Encriptar conteúdo](encrypt-content-quickstart.md)
