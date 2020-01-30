---
title: Abordagens de migração dos utilizadores
titleSuffix: Azure AD B2C
description: Discute conceitos fundamentais e avançados sobre migração de utilizadores utilizando a API do Gráfico AD Azure e utilizando opcionalmente políticas personalizadas Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6c70ab718235a67d9f039df638f8ba90b21ab15b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847839"
---
# <a name="migrate-users-to-azure-active-directory-b2c"></a>Utilizadores migrados para o Diretório Ativo Azure B2C

Ao migrar o seu fornecedor de identidade para o Azure Ative Directory B2C (Azure AD B2C), poderá também ter de migrar as contas de utilizador. Este artigo explica como migrar as contas de utilizador existentes de qualquer fornecedor de identidade para O Azure AD B2C. O artigo não pretende ser prescritivo, mas sim, descreve alguns cenários. O desenvolvedor é responsável pela adequação de cada abordagem.

## <a name="user-migration-flows"></a>Fluxos migratórios dos utilizadores

Com o Azure AD B2C, pode migrar os utilizadores através da [API Azure AD Graph][B2C-GraphQuickStart]. O processo de migração do utilizador insere-se em dois fluxos:

- **Pré-migração**: Este fluxo aplica-se quando tem acesso claro às credenciais de um utilizador (nome de utilizador e palavra-passe) ou as credenciais são encriptadas, mas pode desencriptar as mesmas. O processo de pré-migração envolve a leitura dos utilizadores do antigo fornecedor de identidade e a criação de novas contas no diretório Azure AD B2C.

- **Pré-migração e redefinição de palavra-passe**: Este fluxo aplica-se quando a palavra-passe de um utilizador não está acessível. Por exemplo:
  - A palavra-passe é armazenada em formato HASH.
  - A palavra-passe está armazenada num fornecedor de identidade a que não pode aceder. O seu antigo fornecedor de identidade valida a credencial do utilizador, ligando para um serviço web.

Em ambos os fluxos, você primeiro executa o processo de pré-migração, leia os utilizadores do seu antigo fornecedor de identidade, e crie novas contas no diretório Azure AD B2C. Se não tiver a senha, crie a conta utilizando uma palavra-passe gerada aleatoriamente. Em seguida, peça ao utilizador que altere a palavra-passe ou, quando o utilizador faz o sinal de acesso pela primeira vez, o Azure AD B2C pede ao utilizador que a repor.

## <a name="password-policy"></a>Política de senha

A política de senhas Azure AD B2C (para contas locais) baseia-se na política da Azure AD. As políticas de inscrição ou de inscrição ou reset de palavra-passe do Azure AD B2C utilizam a força da palavra-passe "forte" e não expiram quaisquer palavras-passe. Para mais informações, consulte a política de senha seletiva [do Azure.][AD-PasswordPolicies]

Se as contas que pretende migrar utilizarem uma força de senha mais fraca do que a forte força da [palavra-passe aplicada pelo Azure AD B2C,][AD-PasswordPolicies]pode desativar o requisito de senha forte. Para alterar a política de senha padrão, desloque a propriedade `passwordPolicies` para `DisableStrongPassword`. Por exemplo, pode modificar o pedido de criação do utilizador da seguinte forma:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Passo 1: Utilizar a API do Gráfico AD Azure para migrar utilizadores

Cria a conta de utilizador Azure AD B2C via Graph API (com a palavra-passe ou com uma senha aleatória). Esta secção descreve o processo de criação de contas de utilizador no diretório Azure AD B2C utilizando o Graph API.

### <a name="step-11-register-your-application-in-your-tenant"></a>Passo 1.1: Registe a sua candidatura no seu inquilino

Para comunicar com a API graph, primeiro deve ter uma conta de serviço com privilégios administrativos. Em Azure AD, regista uma aplicação e permite o acesso por escrito ao diretório. As credenciais de aplicação são o ID de **aplicação** e **o segredo de aplicação.** A aplicação funciona por si só, não como utilizador, para chamar a API do Gráfico.

Em primeiro lugar, registe uma aplicação que pode utilizar para tarefas de gestão como a migração de utilizadores.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>Passo 1.2: Conceder autorização administrativa ao seu pedido

Em seguida, conceda a aplicação as permissões APi do Gráfico Azure AD necessárias para a escrita ao diretório.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>Passo 1.3: Criar o segredo da aplicação

Crie um segredo de cliente (chave) para utilização pela aplicação de migração do utilizador que configura ruma posteriormente.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Agora tem uma aplicação com permissões para criar, ler e atualizar utilizadores no seu inquilino Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Passo 1.4: (Opcional) Limpeza ambiental

A permissão de dados de leitura e escrita de dados de *diretório* *não* inclui o direito de excluir os utilizadores. Para dar à sua aplicação a capacidade de eliminar utilizadores (para limpar o seu ambiente), deve realizar um passo extra, que envolve executar o PowerShell para definir as permissões do Administrador de Conta utilizador. Caso contrário, pode passar para a secção seguinte.

> [!IMPORTANT]
> Você deve usar uma conta de administrador de inquilino B2C que é *local* para o inquilino B2C. A sintaxe do nome da conta é *administradora\@contosob2c.onmicrosoft.com*.

Neste script PowerShell, que requer o [módulo Azure AD PowerShell V2,][AD-Powershell]faça o seguinte:

1. Ligue-se ao seu serviço online. Para isso, execute o `Connect-AzureAD` cmdlet no pedido de comando do Windows PowerShell e forneça as suas credenciais.

1. Utilize o ID da **aplicação** para atribuir a aplicação a função de administrador da conta de utilizador. Estas funções têm identificadores bem conhecidos, por isso tudo o que precisa de fazer é introduzir o seu ID de **aplicação** no script.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

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

Altere o valor `$AppId` com o id de **aplicação**Azure AD .

## <a name="step-2-pre-migration-application-sample"></a>Passo 2: Amostra de aplicação pré-migração

Você pode encontrar a amostra de código de pré-migração no repositório `azure-ad-b2c/user-migration` GitHub mantido pela comunidade:

[azure-ad-b2c/user-migration/pré-migração][UserMigrationSample-code] (GitHub)

### <a name="step-21-edit-the-migration-data-file"></a>Passo 2.1: Editar o ficheiro de dados da migração

A aplicação da amostra utiliza um ficheiro JSON que contém dados de utilizadores idiotas. Depois de executar com sucesso a amostra, pode alterar o código para consumir os dados da sua própria base de dados. Ou pode exportar o perfil do utilizador para um ficheiro JSON e, em seguida, definir a aplicação para utilizar esse ficheiro.

Para editar o ficheiro JSON, abra a solução `AADB2C.UserMigration.sln` Visual Studio. No projeto `AADB2C.UserMigration`, abra o ficheiro `UsersData.json`.

![Porção do ficheiro UsersData.json mostrando blocos JSON de dois utilizadores](./media/user-migration/pre-migration-data-file.png)

Como pode ver, o ficheiro contém uma lista de entidades utilizadoras. Cada entidade utilizadora tem as seguintes propriedades:

- e-mail
- displayName
- firstName
- lastName
- palavra-passe (pode estar vazio)

> [!NOTE]
> Na hora da compilação, o Visual Studio copia o ficheiro para o diretório `bin`.

### <a name="step-22-configure-the-application-settings"></a>Passo 2.2: Configure as definições de aplicação

No âmbito do projeto `AADB2C.UserMigration`, abra o ficheiro *App.config.* Substitua as seguintes definições de aplicação com os seus próprios valores:

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
> - A utilização de uma corda de ligação à mesa Azure é descrita nas secções seguintes.
> - O seu nome de inquilino B2C é o domínio que inseriu durante a criação de inquilinos, e está exposto no portal Azure. O nome do inquilino geralmente termina com o sufixo *.onmicrosoft.com* (por exemplo, *contosob2c.onmicrosoft.com).*

### <a name="step-23-run-the-pre-migration-process"></a>Passo 2.3: Executar o processo de pré-migração

Clique na solução `AADB2C.UserMigration` e, em seguida, reconstrua a amostra. Se tiver sucesso, deverá agora ter um ficheiro `UserMigration.exe` executável localizado em `AADB2C.UserMigration\bin\Debug\net461`. Para executar o processo de migração, utilize um dos seguintes parâmetros da linha de comando:

- Para **migrar os utilizadores com senha,** utilize o comando `UserMigration.exe 1`.

- Para **migrar os utilizadores com senha aleatória,** utilize o comando `UserMigration.exe 2`. Esta operação também cria uma entidade de mesa Azure. Mais tarde, configura a política de chamar o serviço REST API. O serviço utiliza uma tabela Azure para acompanhar e gerir o processo de migração.

![Janela de comando pronta mostrando saída do comando UserMigration.exe](./media/user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Passo 2.4: Verifique o processo de pré-migração

Para validar a migração, utilize um dos dois seguintes métodos:

- Para procurar um utilizador pelo nome do visor, utilize o portal Azure:

   1. Abra **o Azure AD B2C**e, em seguida, selecione **Utilizadores**.
   1. Na caixa de pesquisa, digite o nome de exibição do utilizador e, em seguida, veja o perfil do utilizador.

- Para recuperar um utilizador através do endereço de e-mail de sessão, utilize a aplicação da amostra:

   1. Execute o seguinte comando:

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > Também pode recuperar um utilizador através do nome do visor utilizando o seguinte comando: `UserMigration.exe 4 "<Display name>"`.

   1. Abra o ficheiro UserProfile.json num editor da JSON para ver as informações do utilizador.

      ![Ficheiro UserProfile.json aberto no editor do Código do Estúdio Visual](./media/user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Passo 2.5: (Opcional) Limpeza ambiental

Se quiser limpar o seu inquilino DaAzure AD e remover os utilizadores do diretório Azure AD, execute o comando `UserMigration.exe 5`.

> [!NOTE]
> * Para limpar o seu inquilino, configure as permissões do Administrador de Conta de Utilizador para a sua aplicação.
> * A aplicação de migração da amostra limpa todos os utilizadores que estão listados no ficheiro JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Passo 2.6: Inscreva-se com utilizadores migrados (com senha)

Depois de executar o processo de pré-migração com senhas de utilizador, as contas estão prontas a ser utilizadas e os utilizadores podem iniciar sessão na sua aplicação utilizando o Azure AD B2C. Se não tiver acesso às palavras-passe do utilizador, continue na secção seguinte.

## <a name="step-3-help-users-reset-their-password"></a>Passo 3: Ajudar os utilizadores a redefinir a sua palavra-passe

Se migrar os utilizadores com uma senha aleatória, devem redefinir a sua palavra-passe. Para ajudá-los a repor a palavra-passe, envie um e-mail de boas-vindas com um link para redefinir a palavra-passe.

Para obter o link para a sua política de redefinição de palavra-passe, siga estes passos. Este procedimento pressupõe que criou anteriormente uma [política personalizada](custom-policy-get-started.md)de redefinição de palavra-passe.

1. Selecione o diretório que contém o seu inquilino Azure AD B2C utilizando o filtro de **assinatura Diretório +** Na secção superior direita do [portal Azure](https://portal.azure.com).
1. Selecione **Azure AD B2C** no menu à esquerda (ou de dentro de **todos os serviços).**
1. Em **políticas**, selecione **estrutura de experiência de identidade**.
1. Selecione a sua política de redefinição de palavra-passe. Por exemplo, *B2C_1A_PasswordReset.*
1. Selecione a sua aplicação no drop-down da **aplicação Select.**

    > [!NOTE]
    > **A corrida agora** requer pelo menos um pedido para ser registado no seu inquilino. Para saber registar candidaturas, consulte [Tutorial: Registe uma candidatura no Azure Ative Directory B2C][B2C-AppRegister].

1. Copie o URL mostrado na caixa de texto de **ponto final run** agora e, em seguida, envie-o para os seus utilizadores.

    ![Página de política de redefinição de password com Run agora ponto final destacado](./media/user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Passo 4: (Opcional) Altere a sua política para verificar e definir o estado de migração do utilizador

> [!NOTE]
> Para verificar e alterar o estado de migração do utilizador, deve utilizar uma política personalizada. As instruções de configuração do [Get start with custom policies][B2C-GetStartedCustom] devem ser completadas.

Quando os utilizadores tentam iniciar sessão sem reajustar a palavra-passe primeiro, a sua política deve devolver uma mensagem de erro amigável. Por exemplo:

> *A sua senha expirou. Para redefinir, selecione a ligação de palavra-passe redefinida.*

Este passo opcional requer a utilização de políticas personalizadas Azure AD B2C, como descrito no Getting started com artigo de [políticas personalizadas.][B2C-GetStartedCustom]

Nesta secção, altera a política de verificação do estado de migração do utilizador no início do registo. Se o utilizador não alterar a palavra-passe, devolva uma mensagem de erro HTTP 409 que pede ao utilizador para selecionar o link **Esqueci-me da palavra-passe?**

Para acompanhar a mudança de senha, utilize uma tabela Azure. Quando executa o processo de pré-migração com o parâmetro de linha de comando `2`, cria-se uma entidade utilizadora numa tabela Azure. O seu serviço faz o seguinte:

- No início do início, a política Azure AD B2C invoca o seu serviço RESTful migratório, enviando uma mensagem de correio eletrónico como uma reclamação de entrada. O serviço procura o endereço de e-mail na tabela Azure. Se o endereço existir, o serviço lança uma mensagem de erro: *Tem de alterar a palavra-passe*.

- Depois de o utilizador alterar com sucesso a palavra-passe, retire a entidade da tabela Azure.

> [!NOTE]
> Usamos uma tabela Azure para simplificar a amostra. Pode armazenar o estado de migração em qualquer base de dados ou como propriedade personalizada na conta Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1: Atualizar a definição da sua aplicação

1. Para testar a demonstração da API RESTful, abra `AADB2C.UserMigration.sln` no Estúdio Visual.
1. No projeto `AADB2C.UserMigration.API`, abra o ficheiro *Web.config.* Substitua a regulação por uma configurada no [passo 2.2:](#step-22-configure-the-application-settings)

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Passo 4.2: Implemente a sua aplicação web para o Serviço de Aplicações Azure

No Solution Explorer, clique à direita na `AADB2C.UserMigration.API`, selecione "Publicar...". Siga as instruções para publicar no Serviço de Aplicações Azure. Para mais informações, consulte [A sua aplicação para o Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Passo 4.3: Adicione um perfil técnico e validação de perfil técnico à sua política

1. No Solution Explorer, expanda "Solution Items", e abra o ficheiro de política *TrustFrameworkExtensions.xml.*
1. Mude `TenantId`, `PublicPolicyUri` e `<TenantId>` campos de `yourtenant.onmicrosoft.com` para o nome do seu inquilino.
1. Sob o elemento `<TechnicalProfile Id="login-NonInteractive">`, substitua todas as instâncias de `ProxyIdentityExperienceFrameworkAppId` e `IdentityExperienceFrameworkAppId` com os IDs de aplicação configurados em [Iniciar-se com políticas personalizadas][B2C-GetStartedCustom].
1. Sob o nó `<ClaimsProviders>`, encontre o seguinte corte XML. Altere o valor do `ServiceUrl` para apontar para o seu URL de Serviço de Aplicações Azure.

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

O perfil técnico anterior define uma reclamação de entrada: `signInName` (envio como e-mail). No momento do inessão, a reclamação é enviada para o seu ponto final RESTful.

Depois de definir o perfil técnico para a sua API RESTful, configure o perfil técnico `SelfAsserted-LocalAccountSignin-Email` existente para, adicionalmente, ligar para o seu perfil técnico REST API, substituindo-o no seu ficheiro *TrustFrameworkExtensions.xml:*

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="LocalAccountUserMigration" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

Em seguida, mude a `Id` do perfil técnico `LocalAccountSignIn` para `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Passo 4.4: Faça upload da apólice para o seu inquilino

1. No [portal Azure,][Portal]mude para o [contexto do seu inquilino Azure AD B2C][B2C-NavContext]e, em seguida, selecione **Azure AD B2C**.
1. Selecione **Identity Experience Framework**.
1. Selecione **todas as políticas**.
1. Selecione **carregar política**.
1. Marque a caixa de seleção **substituir a política se ela existir** .
1. Carregue o arquivo *TrustFrameworkExtensions. xml* e certifique-se de que ele passe na validação.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Passo 4.5: Teste a política personalizada usando run now

1. Selecione **Azure AD B2C**e, em seguida, selecione Quadro de Experiência de **Identidade**.
1. Abra *B2C_1A_signup_signin,* a política personalizada do partido que você carregou, e depois selecione **Run agora**.
1. Introduza as credenciais de um dos utilizadores migrados e, em seguida, selecione **Sign In**. A Sua API REST deve lançar a seguinte mensagem de erro:

    ![Página de inscrição mostrando a mensagem de erro da palavra-passe de alteração](./media/user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Passo 4.6: (Opcional) Problemas saem da sua API REST

Pode visualizar e monitorizar a informação de registo em tempo quase real.

1. No menu de definições da sua aplicação RESTful, sob **monitorização,** selecione **registos**de diagnóstico .
1. Definir **registo de aplicações (sistema de ficheiros)** para **on**.
1. Desloque o **nível** para **Verbose**.
1. Selecione **Guardar**

    ![Diagnóstico slogs página de configuração no portal Azure](./media/user-migration/pre-migration-diagnostic-logs.png)

1. No menu **Definições,** selecione **log stream**.
1. Verifique a saída da API RESTful.

> [!IMPORTANT]
> Utilize os registos de diagnóstico apenas durante o desenvolvimento e teste. A produção restful API pode conter informações confidenciais que não devem ser expostas na produção.

## <a name="optional-download-the-complete-policy-files"></a>(Opcional) Descarregue os ficheiros de política completos

Depois de completar o [Get iniciado com políticas personalizadas,][B2C-GetStartedCustom] recomendamos que construa o seu cenário utilizando os seus próprios ficheiros de política personalizadas. Para sua referência, fornecemos ficheiros de política de [amostras][UserMigrationSample-policy].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
