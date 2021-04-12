---
title: Tutorial - Configurar o seu Diretório Ativo Azure para emitir credenciais verificáveis (Pré-visualização)
description: Neste tutorial, você constrói o ambiente necessário para implementar credenciais verificáveis no seu inquilino
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: cd39f6c484ebe116918611bb1d543c1919a3cb0a
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222950"
---
# <a name="tutorial---configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Tutorial - Configurar o seu Diretório Ativo Azure para emitir credenciais verificáveis (Pré-visualização)

Neste tutorial, baseamos-se no trabalho feito como parte do artigo get [start](get-started-verifiable-credentials.md) e configuramos o seu Azure Ative Directory (AZure AD) com o seu próprio [identificador descentralizado](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (DID). Utilizamos o identificador descentralizado para emitir uma credencial verificável utilizando a app da amostra e o seu emitente; no entanto, neste tutorial, ainda usamos a amostra Azure B2C inquilino para a autenticação.  No nosso próximo tutorial, tomaremos medidas adicionais para que a app seja configurada para trabalhar com o seu AZure AD.

> [!IMPORTANT]
> Azure Ative Directory Verifiable Credentials está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Neste artigo:

> [!div class="checklist"]
> * Você cria os serviços necessários para embarcar no seu AD Azure para credenciais verificáveis 
> * Estamos a criar o seu DID
> * Estamos a personalizar os ficheiros Regras e Exibição
> * Configure credenciais verificáveis em Azure AD.


## <a name="prerequisites"></a>Pré-requisitos

Antes de completar com sucesso este tutorial, tem de ser primeiro:

- Complete o [Get start](get-started-verifiable-credentials.md).
- Tenha uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure AD com [licença](https://azure.microsoft.com/pricing/details/active-directory/)P2 . Siga o [Como criar uma conta de programador gratuita](how-to-create-a-free-developer-account.md) se não tiver uma.
- Um caso de [Azure Key Vault](../../key-vault/general/overview.md) onde tem direito a criar chaves e segredos.

## <a name="azure-active-directory"></a>Azure Active Directory

Antes de começarmos, precisamos de um inquilino da AD Azure. Quando o seu inquilino está habilitado para credenciais verificáveis, é-lhe atribuído um identificador descentralizado (DID) e está habilitado com um serviço emitente para a emissão de credenciais verificáveis. Qualquer credencial verificável que emite é emitida pelo seu inquilino e pelo seu DID. O DID também é utilizado para verificar credenciais verificáveis.
Se acabou de criar uma subscrição de teste Azure, o seu inquilino não precisa de ser preenchido com contas de utilizador, mas terá de ter pelo menos uma conta de teste de utilizador para completar tutoriais posteriores.

## <a name="create-a-key-vault"></a>Criar um Key Vault

Ao trabalhar com credenciais verificáveis, você tem controlo completo e gestão das chaves criptográficas que o seu inquilino usa para assinar digitalmente credenciais verificáveis. Para emitir e verificar credenciais, você deve fornecer a Azure AD com acesso ao seu próprio exemplo de Azure Key Vault.

1. A partir do menu do portal Azure, ou na página **Inicial,** selecione **Criar um recurso**.
2. Na caixa de busca, introduza o **cofre da chave**.
3. Na lista de resultados, selecione **Key Vault**.
4. Na secção Key Vault, selecione **Criar**.
5. Na secção **Criar cofre de chaves**, forneça as seguintes informações:
    - **Subscrição**: selecione uma subscrição.
    - No **Grupo de Recursos,** escolha **Criar novo** e insira um nome de grupo de recursos como o **grupo vc-resource- group**. Estamos a usar o mesmo nome de grupo de recursos em vários artigos.
    - **Nome**: é necessário um nome exclusivo. Utilizamos **woodgroup-vc-kv** para substituir este valor pelo seu próprio nome único.
    - No menu pendente **Localização**, selecione uma localização.
    - Deixe as outras opções com os valores predefinidos.
6. Depois de fornecer as informações acima, selecione **A Política de Acesso**

    ![criar uma página de cofre chave](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. No ecrã da Política de Acesso, escolha **Adicionar Política de Acesso**

    >[!NOTE]
    > Por predefinição, a conta que cria o cofre da chave é a única com acesso. O serviço de credencial verificável precisa de acesso ao cofre da chave. O cofre-chave deve ter uma política de acesso que permite ao Administrador **criar teclas,** ter a capacidade de **eliminar as teclas** se optar por não optar e **assinar** para criar a ligação de domínio para a credencial verificável. Se estiver a utilizar a mesma conta durante os testes, certifique-se de modificar a política de incumprimento para conceder o **sinal** de conta, além das permissões por defeito concedidas aos criadores de cofres.

8. Para o Administrador do Utilizador, certifique-se de que a secção de permissões chave tem **Criar,** **Eliminar** e **Assinar** ativado. Por predefinição, criar e eliminar já estão ativados e o Signo deve ser a única Chave Permissão que precisa de ser atualizada. 

    ![Permissões do Cofre chave](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. Selecione **Rever + criar**.
10. Selecione **Criar**.
11. Vá ao cofre e tome nota do nome do cofre e URI

Tome nota das duas propriedades listadas abaixo:

- **Nome do cofre**: No exemplo, o nome de valor é **woodgrove-vc-kv**. Usa este nome para outros passos.
- **Cofre URI**: No exemplo, este valor é https://woodgrove-vc-kv.vault.azure.net/ . As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

>[!NOTE]
> Cada transação de cofre-chave resulta em custos adicionais de subscrição do Azure. Reveja a [página de preços do Cofre chave](https://azure.microsoft.com/pricing/details/key-vault/) para mais detalhes.

>[!IMPORTANT]
> Durante a pré-visualização do Azure Ative Directory Verifiable Credentials, as chaves e segredos criados no seu cofre não devem ser modificados uma vez criados. Apagar, desativar ou atualizar as suas chaves e segredos invalida quaisquer credenciais emitidas. Não modifique as suas chaves ou segredos durante a pré-visualização.

## <a name="create-a-modified-rules-and-display-file"></a>Criar regras modificadas e apresentar ficheiro

Nesta secção, usamos as regras e exibimos ficheiros da app do emitente sample e modificamo-los ligeiramente para criar a primeira credencial verificável do seu inquilino.

1. Copie ambas as regras e exiba ficheiros json para uma pasta temporária e rebatize-as **MyFirstVC-display.jse** **MyFirstVC-rules.js** respectivamente. Pode encontrar ambos os ficheiros em **emitente\issuer_config**

   ![exibir e regras ficheiros como parte do diretório de aplicações de amostra](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![exibir e regras ficheiros numa pasta temporária](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. Abra a MyFirstVC-rules.jsno arquivo do seu editor de código. 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

Agora vamos mudar o campo tipo para "MyFirstVC". 

  ```json
   "type": ["MyFirstVC"]
  
  ```

Poupe esta mudança.

 >[!NOTE]
   > Não estamos a mudar a **"configuração"** ou a **"client_id"** neste momento no tutorial. Ainda usamos o inquilino Microsoft B2C que usamos no [Get start](get-started-verifiable-credentials.md). Usaremos o seu AD Azure no próximo tutorial.

3. Abra a MyFirstVC-display.jsno arquivo do seu editor de código.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

Vamos fazer algumas modificações para que esta credencial verificável pareça visivelmente diferente da versão do código de amostra. 
    
```json
     "card": {
        "title": "My First VC",
        "issuedBy": "Your Issuer Name",
        "backgroundColor": "#ffffff",
        "textColor": "#000000",
```

Guarde estas mudanças.
## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Antes de criarmos a nossa primeira credencial verificável, precisamos de criar um recipiente de Armazenamento Blob que possa conter a nossa configuração e regras.

1. Crie uma conta de armazenamento utilizando as opções apresentadas abaixo. Para etapas detalhadas, reveja o artigo [da conta de armazenamento Criar.](../../storage/common/storage-account-create.md?tabs=azure-portal)

   - **Assinatura:** Escolha a subscrição que estamos usando para estes tutoriais.
   - **Grupo de recursos:** Escolha o mesmo grupo de recursos que usamos em tutoriais anteriores **(vc-resource-group).**
   - **Nome:**  Um nome único.
   - **Localização:** (EUA) LESTE US.
   - **Desempenho:** O padrão.
   - **Tipo de conta:** Armazenamento V2.
   - **Replicação:** Localmente redundante.
 
   ![Criar uma nova conta de armazenamento](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. Depois de criar a conta de armazenamento, precisamos criar um recipiente. Selecione **recipientes** em **armazenamento blob** e crie um recipiente utilizando os valores abaixo fornecidos:

    - **Nome:** vc-container
    - **Nível de acesso público:** Privado (sem acesso anónimo)

   ![Criar um contentor](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. Agora selecione o seu novo recipiente e carre deixe de carregar as novas regras e exibir ficheiros **MyFirstVC-display.js** e **MyFirstVC-rules.jsque** criamos anteriormente.

   ![carregar ficheiro de regras](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>Atribuir papel de blob

Antes de criar a credencial, precisamos primeiro dar ao utilizador assinado a correta atribuição de função para que possam aceder aos ficheiros em Storage Blob.

1. Navegue para o recipiente **de**  >  **armazenamento.**
2. Escolha **o Controlo de Acesso (IAM)** do menu à esquerda.
3. Escolha **atribuições de funções.**
4. Selecione **Adicionar**.
5. Na secção **Função,** escolha **Storage Blob Data Reader**.
6. Ao **abrigo do acesso de atribuir para** escolher o **utilizador, o grupo ou o princípio do serviço.**
7. Em **Seleção**: Escolha a conta que está a utilizar para executar estes passos.
8. **Selecione Guardar** para completar a atribuição de funções.


   ![Adicionar uma atribuição de função](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >Por predefinição, os criadores de contentores obtêm a função **proprietário** atribuída. O papel **de Proprietário** não é suficiente por si só. A sua conta precisa da função **de Leitor de Dados blob de armazenamento.** Para mais análise de informação [Utilize o portal Azure para atribuir uma função Azure para acesso a dados de blob e fila](../../storage/common/storage-auth-aad-rbac-portal.md)

## <a name="set-up-verifiable-credentials-preview"></a>Configurar credenciais verificáveis (Pré-visualização)

Agora temos de dar o último passo para preparar o seu inquilino para credenciais verificáveis.

1. A partir do portal Azure, procure **credenciais verificáveis.**
2. Escolha **credenciais verificáveis (pré-visualização)**.
3. Escolha **Começar**
4. Precisamos de configurar a sua organização e fornecer o nome da organização, domínio e cofre chave. Vamos olhar cada um dos valores.

      - **nome da organização**: Este nome é como você referencia o seu negócio dentro do serviço credencial verificável. Este valor não é virado para o cliente.
      - **Domínio:** O domínio introduzido é adicionado a um ponto final de serviço no seu documento DID. [O Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) e outras carteiras utilizam estas informações para validar que o seu DID está [ligado ao seu domínio](how-to-dnsbind.md). Se a carteira puder verificar o DID, apresenta um símbolo verificado. Se a carteira não puder verificar o DID, informa o utilizador que a credencial foi emitida por uma organização que não pôde validar. O domínio é o que liga o seu DID a algo tangível que o utilizador possa saber sobre o seu negócio.
      - **Cofre chave:** Fornece o nome do Cofre chave que criamos anteriormente.
 
   >[!IMPORTANT]
   > O domínio não pode ser um redirecionamento, caso contrário o DID e o domínio não podem ser ligados. Certifique-se de que utiliza https://www.domain.com o formato.
    
5. Escolha **Salvar e criar credencial**

    ![configurar a sua identidade organizacional](media/enable-your-tenant-verifiable-credentials/save-create.png)

Parabéns, o seu inquilino está agora habilitado para a pré-visualização da Credencial Verificável!

## <a name="create-your-vc-in-the-portal"></a>Crie o seu VC no portal

O passo anterior deixa-o na página Criar uma nova página **de credenciais.** 

   ![credenciais verificáveis começar](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. Em Nome Credencial, adicione o nome **MyFirstVC**. Este nome é usado no portal para identificar as suas credenciais verificáveis e está incluído como parte do contrato de credenciais verificáveis.
2. Na secção de ficheiros 'Visualizar', escolha **o ficheiro de exibição Configure**
3. Na secção **contas de Armazenamento,** selecione **woodgrovevcstorage**.
4. Na lista dos recipientes disponíveis, escolha **o vc-container**.
5. Escolha o **MyFirstVC-display.jsno** ficheiro que criamos anteriormente.
6. A partir da **Criação de uma nova credencial** na secção **de ficheiros Regras** escolha **ficheiro de regras de configuração**
7. Na secção **de contas de Armazenamento,** selecione **woodgrovecstorage**
8. Escolha **o vc-container**.
9. Selecione **MyFirstVC-rules.jsem**
10. A partir do Create um novo ecrã **credencial** escolher **Criar.**

   ![criar uma nova credencial](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>URL credencial

Agora que tem uma nova credencial, copie a URL credencial.

   ![A emissão de URL credencial](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>O URL credencial é a combinação das regras e dos ficheiros de exibição. É o URL que o Autenticador avalia antes de apresentar aos requisitos de emissão de credenciais verificáveis do utilizador.  

## <a name="update-the-sample-app"></a>Atualizar a aplicação da amostra

Agora fazemos modificações no código do emitente da aplicação da amostra para atualizá-lo com o seu URL credencial verificável. Isto permite-lhe emitir credenciais verificáveis usando o seu próprio inquilino.

1. Vá à pasta onde colocou os ficheiros da aplicação da amostra.
2. Abra a pasta emitente e, em seguida, abra app.js no Código do Estúdio Visual.
3. Atualize a constante 'credencial' com o seu novo URL credencial e desloque a constante credencialType para 'MyFirstVC' e guarde o ficheiro.

    ![imagem de código de estúdio visual mostrando as áreas relevantes destacadas](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. Abra um pedido de comando e abra a pasta emitente.
5. Execute a aplicação de nó atualizado.

    ```terminal
    node app.js
    ```

6. Usando um comando diferente, executar ngrok para configurar um URL em 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Também pode notar um aviso de que esta aplicação ou site pode ser arriscado. A mensagem é esperada neste momento porque ainda não ligamos o seu DID ao seu domínio. Siga as instruções [de ligação AO DNS](how-to-dnsbind.md) para configurar isto.

    
7. Abra o URL HTTPS gerado pelo ngrok.

    ![Pontos finais de encaminhamento NGROK](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. Escolha **GET CREDENCIAL**
9. No Autenticador digitalizar o código QR.
10. **Nesta aplicação ou site pode ser uma** mensagem de aviso arriscada escolher **Advanced**.

  ![Aviso inicial](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. No aviso do site de risco escolha **Proceder de qualquer maneira (inseguro)**

  ![Segundo aviso sobre o emitente](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. No Add um ecrã **credencial** notar algumas coisas: 
    1. Na parte superior do ecrã pode ver uma mensagem vermelha **não verificada**
    1. A credencial é personalizada com base nas alterações que fizemos ao ficheiro de exibição.
    1. O **Sôr-in na sua** conta aponta para **didplayground.b2clogin.com**.
    
   ![adicionar tela de credencial com aviso](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. Escolha **Iniciar sessão na sua conta** e autenticar utilizando as informações credenciais que forneceu no tutorial de [início.](get-started-verifiable-credentials.md)
14. Depois de autenticar com sucesso o botão **Adicionar** já não está acinzentado. Escolha **Adicionar**.

  ![adicionar ecrã credencial após autenticação](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Emitimos agora uma credencial verificável usando o nosso inquilino para gerar o nosso vc enquanto ainda utiliza o nosso inquilino B2C para autenticação.

  ![vc emitido pelo seu AD azul e autenticado pela nossa instância Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Teste verificando o VC usando a aplicação da amostra

Agora que emitimos a credencial verificável do nosso próprio inquilino, vamos verificar usando a nossa aplicação de amostra.

>[!IMPORTANT]
> Ao testar, utilize o mesmo e-mail e senha que usou durante o artigo ['Iniciar'.](get-started-verifiable-credentials.md) Enquanto o seu inquilino está emitindo o vc, a entrada vem de um símbolo de identificação emitido pelo inquilino Microsoft B2C. No tutorial dois, estamos mudando a emissão de ficha para o seu inquilino

1. Abra **as definições** na lâmina de credenciais verificáveis no portal Azure. Copiar o identificador descentralizado (DID).

   ![copiar o DID](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. Agora abra parte da pasta de verificação dos ficheiros da aplicação da amostra. Precisamos atualizar o ficheiro app.js no código de amostra do verificador e fazer as seguintes alterações:

    - **credencial:** mude para o seu URL credencial
    - **credencialType**: 'MyFirstVC'
    - **emitenteDid**: Copiar este valor do portal Azure>credenciais verificáveis (Pré-visualização)>Configurações>identificador descentralizado (DID)
    
   ![atualizar o emitente constanteDid para combinar com o seu identificador de emitente](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. Pare de executar o seu serviço de ngrok emitente.

    ```cmd
    control-c
    ```

4. Agora corra ngrok com a porta de verificação 8082.

    ```cmd
    ngrok http 8082
    ```

5. Em outra janela terminal, navegue para a aplicação do verificador e execute-a da mesma forma que executamos a app emitente.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. Abra o url ngrok no seu navegador e digitalize o código QR utilizando o Autenticador no seu dispositivo móvel.
7. No seu dispositivo móvel, escolha **Permitir** o novo ecrã **de pedido de permissão.**

    >[!NOTE]
    > A assinatura did deste VC ainda é o Microsoft B2C. O Verificador DID ainda é do inquilino da Microsoft Sample App. Uma vez que o DID da Microsoft está ligado a um domínio que possuímos, não vê o aviso como experimentámos durante o fluxo de emissão. Isto será atualizado na próxima secção.
    
   ![novo pedido de permissão](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Não verificou com sucesso a sua credencial.

## <a name="next-steps"></a>Passos seguintes

Agora que tem o código de amostra que emite um VC do seu emitente, permite continuar até à secção seguinte onde utiliza o seu próprio fornecedor de identidade para autenticar utilizadores que tentam obter credenciais verificáveis e usar o seu DID para assinar pedidos de apresentação.

> [!div class="nextstepaction"]
> [Tutorial - Emitir e verificar credenciais verificáveis usando o seu inquilino](issue-verify-verifiable-credentials-your-tenant.md)


