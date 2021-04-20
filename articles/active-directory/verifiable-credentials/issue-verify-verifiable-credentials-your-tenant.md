---
title: Tutorial - Emitir e verificar credenciais verificáveis usando o seu inquilino Azure (pré-visualização)
description: Altere a amostra de código credencial verificável para trabalhar com o seu inquilino Azure
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 310c821bf102d267d0b5f77dbf206b896ab2f1c7
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739229"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Tutorial - Emitir e verificar credenciais verificáveis usando o seu inquilino (pré-visualização)

> [!IMPORTANT]
> Azure Ative Directory Verifiable Credentials está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Agora que tem o seu inquilino Azure configurado com o serviço de Credencial Verificável, percorremos os passos necessários para permitir que o seu Azure Ative Directory (Azure AD) emita credenciais verificáveis usando a aplicação da amostra.

Neste artigo:

> [!div class="checklist"]
> * Registe a aplicação de amostra no seu AD Azure
> * Criar um ficheiro de regras e exibição
> * Carregar regras e exibir ficheiros
> * Crie o seu serviço de emitente de credenciais verificáveis para utilizar o Cofre da Chave Azure
> * Atualize o Código da Amostra com a informação do seu inquilino.

O nosso código de amostra requer que os utilizadores autentem a autenticação a um fornecedor de identidade, especificamente a Azure AD B2C, antes de o VC do Perito em Credenciais Verificado poder ser emitido. Nem todos os emitentes de credenciais verificáveis requerem autenticação antes de emitirem credenciais.

Autenticar tokens de ID permite que os utilizadores provem quem são antes de receberem a sua credencial. Quando os utilizadores fazem login com sucesso, o fornecedor de identidade devolve um token de segurança contendo alegações sobre o utilizador. O serviço emitente transforma então estes símbolos de segurança e as suas reivindicações numa credencial verificável. A credencial verificável é assinada com o did do emitente.

Qualquer fornecedor de identidade que suporte o protocolo OpenID Connect é suportado. Exemplos de fornecedores de identidade apoiados incluem [Azure Ative Directory,](../fundamentals/active-directory-whatis.md)e [Azure AD B2C](../../active-directory-b2c/overview.md). Neste tutorial estamos a usar a AAD.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que já completou os passos no [tutorial anterior](enable-your-tenant-verifiable-credentials.md) e tem acesso ao ambiente que usou.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Registe uma aplicação para permitir que as carteiras DID assinem nos utilizadores

Para emitir uma credencial verificável, é necessário registar uma aplicação para que o Autenticador, ou qualquer outra carteira de credenciais verificável, possa inscrever-se nos utilizadores.  

Registe uma aplicação chamada 'VC Wallet App' em Azure AD e obtenha uma identificação do cliente.

1. Siga as instruções para registar um pedido com [Azure AD](../develop/quickstart-register-app.md) Ao registar-se, utilize os valores abaixo.

   - Nome: "VC Wallet App"
   - Tipos de conta suportados: Contas neste diretório organizacional apenas
   - Redirecionamento URI: vcclient://openid/

   ![registar uma aplicação](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Depois de registar o pedido, escreva o ID de Aplicação (cliente). Vai precisar deste valor mais à frente.

   ![ID do cliente de aplicação](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Selecione o botão **Endpoints** e copie o documento de metadados OpenID Connect URI. Precisa desta informação para a próxima secção. 

   ![pontos finais emitentes](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>Crie a sua app nó com acesso ao Cofre da Chave Azure

Para autenticar o pedido de emissão de credencial de um utilizador, o site emitente utiliza as suas chaves criptográficas no Cofre da Chave Azure. Para aceder ao Azure Key Vault, o seu website precisa de um ID de cliente e segredo de cliente que possa ser usado para autenticar o Azure Key Vault.

1. Ao visualizar a página geral da aplicação da carteira VC selecione **Certificados & segredos**.
    ![certificados e segredos](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. Na secção **de segredos** do Cliente escolha **novo segredo do cliente**
    1. Adicione uma descrição como "Node VC cliente secreto"
    1. Expira: num ano.
  ![Segredo de aplicação com um prazo de um ano](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Copie o SEGREDO. Precisa desta informação para atualizar a sua aplicação de nó de amostra.

>[!WARNING]
> Tens uma hipótese de copiar o segredo. O segredo é uma maneira de se ausso depois disto. Não copie a identificação. 

Depois de criar a sua aplicação e o segredo do cliente em Azure AD, precisa de conceder ao pedido as permissões necessárias para realizar operações no seu Cofre-Chave. A realização destas alterações de permissão é necessária para permitir que o website aceda e utilize as chaves privadas aí armazenadas.

1. Vai para o Cofre de Chaves.
2. Selecione o cofre chave que estamos usando para estes tutoriais.
3. Escolha **políticas de acesso** no navegador esquerdo
4. Escolha **+Adicionar Política de Acesso.**
5. Na secção **de permissões chave** escolha **Obter** e **Assinar**.
6. Selecione **Principal** e use o ID da aplicação para pesquisar a aplicação que registramos anteriormente. Selecione-a.
7. Selecione **Adicionar**.
8. Escolha **SAVE**.

Para mais informações sobre permissões do Key Vault e controlo de acesso, leia o guia RBAC do [cofre-chave](../../key-vault/general/rbac-guide.md)

![atribuir permissões de cofre chave](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>Faça alterações que correspondam ao seu ambiente

Até agora, temos trabalhado com a nossa aplicação de amostras. A aplicação utiliza [o Azure Ative Directory B2C](../../active-directory-b2c/overview.md) e estamos agora a mudar para usar o Azure AD, pelo que precisamos de fazer algumas alterações não só para corresponder ao seu ambiente, mas também para suportar alegações adicionais que não foram usadas antes.

1. Copie o ficheiro de regras abaixo e guarde-o para **modified-expertRules.jsem**. 

    > [!NOTE]
    > **"âmbito": "perfil aberto"** está incluído neste ficheiro Regras e não foi incluído no ficheiro Regras da App da Amostra. A próxima secção explicará como ativar as reclamações opcionais no seu inquilino Azure Ative Directory. 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. Abra o ficheiro e substitua os **valores de client_id** e **configuração** pelos dois valores que copiámos na secção anterior.

    ![destacando os dois valores que precisam de ser modificados como parte deste passo](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  A **Configuração de** valor é o documento de metadados OpenID Connect URI.

  Uma vez que o Código da Amostra está a utilizar o Azure Ative Directory B2C e estamos a utilizar o Azure Ative Directory, precisamos de adicionar reclamações opcionais através de âmbitos para que estas alegações sejam incluídas no ID Token para serem escritas na Credencial Verificável. 

3. De volta ao portal Azure, abra o Diretório Ativo Azure.
4. Escolha **as inscrições da App.**
5. Abra a App VC Wallet que criamos anteriormente.
6. Escolha **a configuração token**.
7. Escolha **+ Adicionar reclamação opcional**

     ![sob configuração simbólica adicionar uma reivindicação opcional](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. A partir do **tipo Token** escolha **iD** e da lista de reclamações disponíveis escolha **given_name** e **family_name**

     ![adicionar reclamações opcionais](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Prima **Adicionar**.
10. Se receber um aviso de permissões como mostrado abaixo, verifique a caixa e selecione **Adicionar**.

     ![adicionar permissões para reclamações opcionais](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

Agora, quando um utilizador é apresentado com o "sign in" para obter a sua credencial verificável, a App VC Wallet sabe incluir as alegações específicas através do parâmetro de âmbito a ser escrito na Credencial Verificável.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Crie um novo VC com este ficheiro de regras e o antigo ficheiro de exibição

1. Faça o upload do novo ficheiro de regras para o nosso contentor
1. A partir da página de credenciais verificáveis criar uma nova credencial chamada **ModificadoCredentialExpert** usando o antigo ficheiro de exibição e o novo ficheiro de regras **(modified-credentialExpert.jsligado).**
1. Após o processo de criação de credenciais concluído a partir da página **de visão geral** copiar o **URL de credencial de emissão** e guardá-lo porque precisamos dele na secção seguinte.

## <a name="before-we-continue"></a>Antes de continuarmos

Temos de juntar alguns valores antes de podermos fazer as alterações de código necessárias. Utilizamos estes valores na secção seguinte para fazer com que o código de amostra utilize as suas próprias chaves armazenadas no seu cofre. Até agora devemos ter os seguintes valores prontos.

- **Contrato URI** a partir da credencial que acabamos de criar (URL credencial de emissão)
- **ID do cliente de aplicação** Recebemos isto quando registámos a aplicação Node.
- **Segredo do cliente** Criámo-lo mais cedo quando concedemos acesso à sua aplicação ao cofre de chaves.

Há alguns outros valores que precisamos obter antes de podermos fazer as alterações uma vez na nossa aplicação de amostra. Vamos buscá-los agora!

### <a name="verifiable-credentials-settings"></a>Definições de credenciais verificáveis

1. Navegue na página Credenciais Verificáveis e escolha **Definições**.  
1. Copie os seguintes valores:

    - Identificador de inquilino 
    - Identificador emitente (o seu DID)
    - Cofre chave (uri)

1. Sob o identificador da chave de assinatura, há um URI, mas só precisamos de uma parte. Copie da parte que diz **emitenteSigningKeyION** como realçado pelo retângulo vermelho na imagem abaixo.

   ![sinal no identificador chave](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>Documento DID

1. Abra o Explorador de [Rede DIF ION](https://identity.foundation/ion/explorer/)

2. Cole o seu DID na barra de pesquisa.

4. A partir da resposta formatada encontrar a secção chamada **VerificationMethod**
5. Em "verificationMethod" copie o id e rotule-o como kvSigningKeyId
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Agora temos tudo o que precisamos para fazer as alterações no nosso código de amostra.

- **Emitente:** app.js atualizar credencial const com o seu novo contrato uri
- **Verificador:** app.js atualizar o emitenteDid com o seu identificador de emitentes
- **Emitente e Verificador** atualizam o didconfig.jscom os seguintes valores:


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>Esta é uma aplicação de demonstração e normalmente nunca deve dar o segredo à sua aplicação diretamente.


Agora tem tudo em que pensar e verificar a sua própria Credencial Verificável do seu inquilino do Azure Ative Directory com as suas próprias chaves. 

## <a name="issue-and-verify-the-vc"></a>Emita e verifique o VC

Siga os mesmos passos que seguimos no tutorial anterior para emitir a credencial verificável e validá-la com a sua app. Uma vez concluída com sucesso o processo de verificação, está agora pronta para continuar a aprender sobre credenciais verificáveis.

1. Abra um pedido de comando e abra a pasta emitente.
2. Execute a aplicação de nó atualizado.

    ```terminal
    node app.js
    ```

3. Usando um comando diferente, executar ngrok para configurar um URL em 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Também pode notar um aviso de que esta aplicação ou site pode ser arriscado. A mensagem é esperada neste momento porque ainda não ligamos o seu DID ao seu domínio. Siga as instruções [de ligação AO DNS](how-to-dnsbind.md) para configurar isto.

    
4. Abra o URL HTTPS gerado pelo ngrok.

    ![Pontos finais de encaminhamento NGROK](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Escolha **GET CREDENCIAL**
6. No Autenticador digitalizar o código QR.
7. **Nesta aplicação ou site pode ser uma** mensagem de aviso arriscada escolher **Advanced**.

  ![Aviso inicial](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. No aviso do site de risco escolha **Proceder de qualquer maneira (inseguro)**

  ![Segundo aviso sobre o emitente](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. No Add um ecrã **credencial** notar algumas coisas: 
    1. Na parte superior do ecrã pode ver uma mensagem vermelha **não verificada**
    1. A credencial é personalizada com base nas alterações que fizemos ao ficheiro de exibição.
    1. O **SÔr-S.A. na sua** opção de conta aponta para o seu sinal AD Azure na página.
    
   ![adicionar tela de credencial com aviso](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Escolha **Iniciar sôm nas suas contas** e autenticar usando um Utilizador no seu inquilino AZure AD.
11. Depois de autenticar com sucesso o botão **Adicionar** já não está acinzentado. Escolha **Adicionar**.

  ![adicionar ecrã credencial após autenticação](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Emitimos agora uma credencial verificável usando o seu inquilino para gerar o seu vc enquanto ainda utiliza o inquilino B2C original para autenticação.

  ![vc emitido pelo seu AD azul e autenticado pela nossa instância Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Teste verificando o VC usando a aplicação da amostra

Agora que emitimos a credencial verificável do seu próprio inquilino com reclamações do seu AD Azure, vamos verificar usando a aplicação da amostra.

1. Pare de executar o seu serviço de ngrok emitente.

    ```cmd
    control-c
    ```

2. Agora corra ngrok com a porta de verificação 8082.

    ```cmd
    ngrok http 8082
    ```

3. Em outra janela terminal, navegue para a aplicação do verificador e execute-a da mesma forma que executamos a app emitente.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Abra o url ngrok no seu navegador e digitalize o código QR utilizando o Autenticador no seu dispositivo móvel.
5. No seu dispositivo móvel, escolha **Permitir** o novo ecrã **de pedido de permissão.**

   >[!IMPORTANT]
    > Uma vez que a App sample também está a usar o seu DID para assinar o pedido de apresentação, irá notar um aviso de que esta aplicação ou website pode ser arriscado. A mensagem é esperada neste momento porque ainda não ligamos o seu DID ao seu domínio. Siga as instruções [de ligação AO DNS](how-to-dnsbind.md) para configurar isto.
    
   ![novo pedido de permissão](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Verificou agora com sucesso a sua credencial e o website deve apresentar o seu primeiro e último nome a partir da conta de utilizador do Azure AD. 

Já completou o tutorial e é oficialmente um Perito em Credenciais Verificada! A sua aplicação de amostra está a usar o seu DID para emitir e verificar, enquanto escreve alegações numa credencial verificável a partir do seu AD Azure. 

## <a name="next-steps"></a>Passos seguintes

- Saiba como criar [credenciais personalizadas](credential-design.md)
- [Exemplos de](issuer-openid.md) comunicação de serviço de emitentes
