---
title: Abordagens de migração de utilizador no Azure Active Directory B2C | Documentos da Microsoft
description: Discuta core e conceitos avançados sobre a migração de utilizador com o Graph API e, opcionalmente, utilizar as políticas personalizadas do Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e19e21ab31fcee87aac6e7e8a0e1d0fbd0a7452c
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409912"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Migração de utilizador
Ao migrar o fornecedor de identidade para o Azure Active Directory B2C (Azure AD B2C), também poderá ter de migrar a conta de utilizador. Este artigo explica como migrar as contas de utilizador existentes de qualquer fornecedor de identidade para o Azure AD B2C. O artigo não se destina a ser prescritivas, mas, em vez disso, ele descreve alguns cenários. O desenvolvedor é responsável pela adequação de cada abordagem.

## <a name="user-migration-flows"></a>Fluxos de migração de utilizador
Com o Azure AD B2C, pode migrar os usuários por meio [Azure AD Graph API][B2C-GraphQuickStart]. O processo de migração de utilizadores enquadra-se a dois fluxos:

- **Pré-migração**: Este fluxo aplica-se quando ter claro acesso às credenciais de um utilizador (nome de utilizador e palavra-passe) ou as credenciais são encriptadas, mas pode descriptografá-los. O processo de pré-migração envolve a leitura os utilizadores do fornecedor de identidade antigo e criar novas contas no diretório do Azure AD B2C.

- **Pré-migração e a palavra-passe da reposição do**: Este fluxo aplica-se quando a palavra-passe de um utilizador não está acessível. Por exemplo:
   - A palavra-passe é armazenada em formato HASH.
   - A palavra-passe é armazenada num fornecedor de identidade que não pode aceder. O fornecedor de identidade antiga valida a credencial do usuário ao chamar um serviço web.

Em ambos os fluxos, primeiro execute o processo de pré-migração, leia os utilizadores através do seu fornecedor de identidade antiga e criar novas contas no diretório do Azure AD B2C. Se não tiver a palavra-passe, vai criar a conta com uma palavra-passe gerada aleatoriamente. , Em seguida, pede ao utilizador para alterar a palavra-passe ou, quando o utilizador inicia sessão pela primeira vez, o Azure AD B2C pede ao utilizador para repô-lo.

## <a name="password-policy"></a>Política de palavra-passe
A política de palavra-passe do Azure AD B2C (para contas locais) baseia-se a política do Azure AD. O Azure AD B2C inscrição ou início de sessão e palavra-passe de reposição de utilização de políticas a força da palavra-passe "segura" e não expirarem as palavras-passe. Para obter mais informações, consulte [política de palavra-passe do Azure AD][AD-PasswordPolicies].

Se as contas que pretende migrar, utilize uma força da senha mais fraca do que o [força da senha forte imposta pelo Azure AD B2C][AD-PasswordPolicies], pode desabilitar o requisito de palavra-passe segura. Para alterar a política de palavra-passe predefinida, defina o `passwordPolicies` propriedade `DisableStrongPassword`. Por exemplo, pode modificar o pedido de utilizador de criar da seguinte forma:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Passo 1: Utilizar o Azure AD Graph API para migrar utilizadores
Criar a conta de utilizador do Azure AD B2C através do Graph API (com a palavra-passe ou com uma palavra-passe aleatória). Esta secção descreve o processo de criação de contas de utilizador no diretório do Azure AD B2C ao utilizar a Graph API.

### <a name="step-11-register-your-application-in-your-tenant"></a>Passo 1.1: Registar a sua aplicação no seu inquilino
Para comunicar com a Graph API, primeiro tem de ter uma conta de serviço com privilégios administrativos. No Azure AD, pode registar uma aplicação e a autenticação para o Azure AD. As credenciais são **ID da aplicação** e **segredo de aplicação**. O aplicativo age em nome próprio, não como um utilizador, para chamar a API de gráfico.

Em primeiro lugar, registe a sua aplicação de migração no Azure AD. Em seguida, crie uma chave de aplicação (segredo de aplicação) e defina o aplicativo com privilégios de escrita.

1. Inicie sessão no [Portal do Azure][Portal].
   
1. Escolha o Azure AD **B2C** inquilino ao selecionar a sua conta na parte superior direita da janela.
   
1. No painel esquerdo, selecione **do Azure Active Directory** (não do Azure AD B2C). Para encontrá-la, poderá ter de selecionar **mais serviços**.
   
1. Selecione **Registos das aplicações**.
   
1. Selecione **Novo registo de aplicação**.
   
   ![Novo registo de aplicação](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
   
1. Crie uma nova aplicação ao fazer o seguinte:
   - Para **Name**, utilize **B2CUserMigration** ou qualquer outro nome que desejar.
   - Para **tipo de aplicação**, utilize **aplicação/API Web**.
   - Para **URL de início de sessão**, utilize **https://localhost** (como não é relevante para esta aplicação).
   - Selecione **Criar**.
   
1. Depois do aplicativo é criado, no **aplicativos** , selecione o recém-criado **B2CUserMigration** aplicação.
   
1. Selecione **propriedades**, copie a **ID da aplicação**e guarde-o para utilizar mais tarde.

### <a name="step-12-create-the-application-secret"></a>Passo 1.2: Criar o segredo de aplicação
1. No portal do Azure **aplicação registada** janela, selecione **chaves**.
   
1. Adicione uma nova chave (também conhecido como um segredo do cliente) e, em seguida, copie a chave para utilização posterior.
   
   ![ID da aplicação e as chaves](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)
   
### <a name="step-13-grant-administrative-permission-to-your-application"></a>Passo 1.3: Conceda permissões administrativas para a sua aplicação
1. No portal do Azure **aplicação registada** janela, selecione **permissões obrigatórias**.

1. Selecione **Windows do Azure Active Directory**.
   
1. Na **ativar o acesso ao** painel, em **permissões de aplicação**, selecione **dados de diretório de leitura e escrita**e, em seguida, selecione **guardar**.
   
1. Na **permissões obrigatórias** painel, selecione **conceder permissões**.
   
   ![Permissões de aplicações](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)
   
Agora tem uma aplicação com permissões para criar, ler e atualizar utilizadores do seu inquilino do Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Passo 1.4: (Opcional) Limpeza de ambiente
Ler e escrever as permissões de dados do diretório fazer *não* incluem o direito de eliminar os utilizadores. Para dar a sua aplicação a capacidade de eliminar utilizadores (para limpar o ambiente), tem de efetuar um passo extra, o que envolve a execução do PowerShell para definir permissões de administrador de conta de utilizador. Caso contrário, pode avançar para a secção seguinte.

> [!IMPORTANT]
> Tem de utilizar uma conta de administrador de inquilino de B2C que seja *local* para o inquilino do B2C. A sintaxe do nome de conta é *admin@contosob2c.onmicrosoft.com*.

>[!NOTE]
> O seguinte script do PowerShell requer [do Azure Active Directory PowerShell versão 2][AD-Powershell].

Neste script de PowerShell, faça o seguinte:
1. Ligar ao seu serviço online. Para tal, execute o `Connect-AzureAD` cmdlet no Windows PowerShell, linha de comandos e forneça as suas credenciais.
   
1. Utilize o **ID da aplicação** para atribuir a aplicação a função de administrador de conta de utilizador. Estas funções têm identificadores bem conhecidos, portanto, tudo o que precisa fazer é digitar seus **ID da aplicação** no script.
   
```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Alteração da `$AppId` valor com o Azure AD **ID da aplicação**.

## <a name="step-2-pre-migration-application-sample"></a>Passo 2: Exemplo de aplicativo pré-migração
[Transferir e executar o código de exemplo][UserMigrationSample]. Pode baixá-lo como um ficheiro. zip.

### <a name="step-21-edit-the-migration-data-file"></a>Passo 2.1: Edite o ficheiro de dados de migração
A aplicação de exemplo utiliza um ficheiro JSON que contém dados de utilizador fictício. Depois de executar com êxito o exemplo, pode alterar o código para consumir os dados da sua própria base de dados. Ou, pode exportar o perfil de utilizador para um ficheiro JSON e, em seguida, defina a aplicação para utilizar esse ficheiro.

Para editar o ficheiro JSON, abra o `AADB2C.UserMigration.sln` solução do Visual Studio. Na `AADB2C.UserMigration` projeto, abra o `UsersData.json` ficheiro.

![Ficheiro de dados do utilizador](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Como pode ver, o ficheiro contém uma lista de entidades de utilizador. Cada entidade de utilizador tem as seguintes propriedades:
- e-mail
- displayName
- firstName
- lastName
- palavra-passe (pode estar vazio)

> [!NOTE]
> No momento da compilação, o Visual Studio copia o ficheiro para o `bin` diretório.

### <a name="step-22-configure-the-application-settings"></a>Passo 2.2: Configurar as definições de aplicação
Sob o `AADB2C.UserMigration` projeto, abra a *App. config* ficheiro. Substitua as seguintes definições de aplicação com seus próprios valores:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - A utilização de uma cadeia de ligação de tabelas do Azure é descrita nas secções seguintes.
> - Nome do seu inquilino B2C é o domínio que introduziu durante a criação do inquilino, e é apresentado no portal do Azure. O nome do inquilino, normalmente, termina com o sufixo *. onmicrosoft.com* (por exemplo, *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Passo 2.3: Executar o processo de pré-migração
Com o botão direito do `AADB2C.UserMigration` solução e, em seguida, a recriação de exemplo. Se for bem-sucedida, já deve ter uma `UserMigration.exe` ficheiro executável, localizado na `AADB2C.UserMigration\bin\Debug\net461`. Para executar o processo de migração, utilize um dos seguintes parâmetros da linha de comandos:

- Para **migrar os utilizadores com palavra-passe**, utilize o `UserMigration.exe 1` comando.

- Para **migrar os utilizadores com palavra-passe aleatória**, utilize o `UserMigration.exe 2` comando. Esta operação também cria uma entidade de tabelas do Azure. Posteriormente, vai configurar a política para chamar o serviço de REST API. O serviço utiliza uma tabela do Azure para controlar e gerenciar o processo de migração.

![Demonstração do processo de migração](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Passo 2.4: Verifique o processo de pré-migração
Para validar a migração, utilize um dos seguintes dois métodos:

- Para procurar um utilizador ao nome a apresentar, utilize o portal do Azure:
   
   1. Open **do Azure AD B2C**e, em seguida, selecione **utilizadores e grupos**.
   
   1. Na caixa de pesquisa, escreva o nome a apresentar do utilizador e, em seguida, veja o perfil do usuário.
   
- Para obter um utilizador por endereço de e-mail de início de sessão, utilize este aplicativo de exemplo:
   
   1. Execute o seguinte comando:
   
      ```Console
          UserMigration.exe 3 {email address}
      ```
      
      > [!TIP]
      > Também pode obter um utilizador por nome a apresentar, utilizando o seguinte comando: `UserMigration.exe 4 "<Display name>"`.
      
   1. Abra o ficheiro de UserProfile.json num editor de JSON para ver informações do utilizador.
   
      ![O ficheiro de UserProfile.json](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)
      
### <a name="step-25-optional-environment-cleanup"></a>Etapa 2.5: (Opcional) Limpeza de ambiente
Se quiser limpar o inquilino do Azure AD a cópia de segurança e remover utilizadores do diretório do Azure AD, execute o `UserMigration.exe 5` comando.

> [!NOTE]
> * Para limpar o seu inquilino, configure permissões de administrador de conta de utilizador para a sua aplicação.
> * A aplicação de migração de exemplo limpa todos os utilizadores que estão listados no ficheiro JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Passo 2.6: Inicie sessão com utilizadores migrados (com a palavra-passe)
Depois de executar o processo de pré-migração com palavras-passe do utilizador, as contas estão prontas a utilizar e os utilizadores poderem iniciar a sua aplicação utilizando o Azure AD B2C. Se não tiver acesso às senhas de usuários, avance para a secção seguinte.

## <a name="step-3-help-users-reset-their-password"></a>Passo 3: Ajudar os utilizadores a repor a palavra-passe
Se migrar os utilizadores com uma palavra-passe aleatória, terá de repor a palavra-passe. Para ajudá-los a repor a palavra-passe, envie um e-mail de boas-vindos com uma ligação para repor a palavra-passe.

Para obter a ligação à sua política de reposição de palavra-passe, faça o seguinte:

1. Selecione **definições do Azure AD B2C**e, em seguida, selecione **Repor palavra-passe** propriedades da política.

1. Selecione a aplicação.

    > [!NOTE]
    > Executar agora requer, pelo menos, um aplicativo para ser foi pré-registado no inquilino. Para saber como registar aplicações, veja o Azure AD B2C [começar] [ B2C-GetStarted] artigo ou o [registo de aplicação] [ B2C-AppRegister] artigo.

1. Selecione **executar agora**e, em seguida, verifique a política.

1. Na **executar ponto final agora** caixa, copie o URL e, em seguida, enviá-la aos seus utilizadores.

    ![Conjunto de registos de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Passo 4: (Opcional) Alterar a política para verificar e definir o estado de migração de utilizador

> [!NOTE]
> Verificar e alterar o estado de migração de utilizador, tem de utilizar uma política personalizada. As instruções de configuração do [introdução às políticas personalizadas] [ B2C-GetStartedCustom] deve ser concluído.
>

Quando os utilizadores tentam iniciar sessão sem primeiro a repor a palavra-passe, a política deverá devolver uma mensagem de erro amigável. Por exemplo:
>*A palavra-passe expirou. Para repô-lo, selecione a ligação de reposição de palavra-passe.*

Este passo opcional requer a utilização de políticas personalizadas do Azure AD B2C, conforme descrito no [introdução às políticas personalizadas] [ B2C-GetStartedCustom] artigo.

Nesta secção, pode alterar a política para verificar o estado de migração de utilizador no início de sessão. Se o utilizador não alterou a palavra-passe, devolver uma mensagem de erro de HTTP 409 que pede ao utilizador para selecionar o **Esqueceu-se a sua palavra-passe?** ligação.

Para controlar a alteração de palavra-passe, utilize uma tabela do Azure. Quando executa o processo de pré-migração com o parâmetro da linha de comandos `2`, criar uma entidade de utilizador uma tabela do Azure. O serviço faz o seguinte:

- No início de sessão, a política do Azure AD B2C invoca a migração do serviço RESTful, enviar uma mensagem de e-mail como uma afirmação de entrada. O serviço de pesquisa para o endereço de e-mail na tabela do Azure. Se o endereço existir, o serviço gera uma mensagem de erro: *Tem de alterar palavra-passe*.

- Depois do utilizador altera com êxito a palavra-passe, remova a entidade da tabela do Azure.

>[!NOTE]
>Utilizamos uma tabela do Azure para simplificar o exemplo. Pode armazenar o estado de migração em qualquer base de dados ou como uma propriedade personalizada na conta do Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1: Atualizar a definição de aplicação
1. Para testar a demonstração da RESTful API, abra `AADB2C.UserMigration.sln` no Visual Studio.

1. Na `AADB2C.UserMigration.API` projeto, abra a *appSettings* ficheiro. Substitua a definição configurada no [passo 2.2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Passo 4.2: Implementar a sua aplicação web no serviço de aplicações do Azure
No Explorador de soluções, faça duplo clique no `AADB2C.UserMigration.API`, selecione "Publicar...". Siga as instruções para publicar no serviço de aplicações do Azure. Para obter mais informações, consulte [implementar a aplicação no App Service do Azure][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Passo 4.3: Adicionar um perfil técnico e a validação do perfil técnico na sua política
1. No Solution Explorer, expanda "Solução de itens" e abra o *TrustFrameworkExtensions.xml* ficheiro de política.
1. Alteração `TenantId`, `PublicPolicyUri` e `<TenantId>` campos do `yourtenant.onmicrosoft.com` ao nome do seu inquilino.
1. Sob o `<TechnicalProfile Id="login-NonInteractive">` elemento, substitua todas as instâncias de `ProxyIdentityExperienceFrameworkAppId` e `IdentityExperienceFrameworkAppId` com os IDs de aplicação configurado no [introdução às políticas personalizadas][B2C-GetStartedCustom].
1. Sob o `<ClaimsProviders>` nó, encontrar o seguinte fragmento XML. Alterar o valor de `ServiceUrl` para apontar para o URL de serviço de aplicações do Azure.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

O perfil técnico anterior define uma afirmação de entrada: `signInName` (enviar como e-mail). No início de sessão, a afirmação é enviada para o ponto de final RESTful.

Depois de definir o perfil técnico para a sua API RESTful, diga a sua política do Azure AD B2C para chamar o perfil técnico. O fragmento XML substitui `SelfAsserted-LocalAccountSignin-Email`, que é definida na política de base. O fragmento XML também adiciona `ValidationTechnicalProfile`, com ReferenceId apontando para o perfil técnico `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Passo 4.4: Carregar a política para o seu inquilino
1. Na [portal do Azure][Portal], mude para o [contexto do inquilino do Azure AD B2C][B2C-NavContext]e, em seguida, selecione **do Azure AD B2C**.

1. Selecione **arquitetura de experiências de identidade**.

1. Selecione **todas as políticas**.

1. Selecione **carregar política**.

1. Selecione o **substituir a política, se existir** caixa de verificação.

1. Carregar o *TrustFrameworkExtensions.xml* de ficheiros e certifique-se de que ele passa a validação.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Passo 4.5: Testar a política personalizada com executar agora
1. Selecione **definições do Azure AD B2C**e, em seguida, aceda à **Framework de experiência de identidade**.

1. Open **B2C_1A_signup_signin**, a política personalizada de terceiros (RP) da entidade confiadora que carregou e, em seguida, selecione **executar agora**.

1. Tente iniciar sessão com uma das credenciais dos utilizadores migrados e, em seguida, selecione **sessão**. A API REST deve lançar a seguinte mensagem de erro:

    ![Conjunto de registos de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Passo 4.6: (Opcional) Resolver problemas relacionados com a API REST
Pode ver e monitorizar as informações de registo em tempo quase real.

1. No menu de definições do seu aplicativo RESTful, sob **monitorização**, selecione **registos de diagnóstico**.

1. Definir **registo de aplicação (sistema de ficheiros)** ao **no**.

1. Definir o **nível** ao **verboso**.

1. Selecione **guardar**

    ![Conjunto de registos de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. Sobre o **configurações** menu, selecione **fluxo de registo**.

1. Verificar a saída da RESTful API.

> [!IMPORTANT]
> Utilize os registos de diagnóstico apenas durante o desenvolvimento e teste. O resultado de RESTful API pode conter informações confidenciais que não devem ser expostas na produção.
>

## <a name="optional-download-the-complete-policy-files"></a>(Opcional) Transferir os ficheiros de política concluída
Depois de concluir o [introdução às políticas personalizadas] [ B2C-GetStartedCustom] passo a passo, é recomendável que criar seu cenário com seus próprios arquivos de política personalizada. Para referência, nós fornecemos [arquivos de diretiva de exemplo][UserMigrationSample].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample]: https://github.com/yoelhor/Azure-AD-B2C-UserMigration
