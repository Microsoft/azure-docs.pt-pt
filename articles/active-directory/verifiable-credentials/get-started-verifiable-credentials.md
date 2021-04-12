---
title: Tutorial - Começar com Azure Ative Directory Verifiable Credentials usando uma aplicação de amostra (pré-visualização)
description: Neste tutorial, você aprende a emitir credenciais verificáveis usando a nossa app de amostra e inquilino de teste
ms.service: identity
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 04/01/2021
ms.openlocfilehash: deebaf31197d8b7335f887ae447f05add45278b2
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222890"
---
#  <a name="tutorial---get-started-with-azure-active-directory-verifiable-credentials-using-a-sample-app-preview"></a>Tutorial - Começar com Azure Ative Directory Verifiable Credentials usando uma aplicação de amostra (pré-visualização)

Neste tutorial, passamos os passos necessários para emitir a sua primeira credencial verificável: um cartão de peritos de credencial verificada. Em seguida, pode usar este cartão para provar a um verificador que é um perito em credenciais verificado, dominado na arte da credenciação digital. Começa com as credenciais verificáveis do Azure Ative Directory, utilizando a aplicação de amostra de Credenciais Verificáveis para emitir a sua primeira credencial verificável.

![Esta é uma imagem de um cartão de exemplo](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Azure Ative Directory Verifiable Credentials está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

- [NodeJS](https://nodejs.org/en/download/) versão 10.14 ou superior instalada no nosso sistema de teste.
- Você precisa de [GIT](https://git-scm.com/downloads) instalado Se você quiser clonar o repositório que acolhe a aplicação da amostra,
- [Visual Studio Code](https://code.visualstudio.com/Download)
- Um sistema para hospedar o nosso site de amostras.
- Um dispositivo móvel com a versão Microsoft Authenticator 6.2005.3599 ou superior instalada.
- [NGROK](https://ngrok.com/) livre.

## <a name="download-the-sample-code"></a>Descarregue o código de amostra

Para emitir um Cartão de Perito credencial verificado, precisa de executar um website na sua máquina local. O site é usado para iniciar um processo de emissão de credencial verificável. Fornecemos um site simples, escrito no NodeJS, que usamos ao longo deste tutorial.

Primeiro, baixe o nosso código de amostra do GitHub [aqui,](https://github.com/Azure-Samples/active-directory-verifiable-credentials)ou clone o repositório para a sua máquina local:

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

Pode querer familiarizar-se com o código nos websites da amostra. A `issuer` pasta contém todo o código utilizado para emitir uma credencial verificável. Mais detalhes estão disponíveis na leitura da [amostra.](https://github.com/Azure-Samples/active-directory-verifiable-credentials)

## <a name="run-the-issuer-website"></a>Executar o site do emitente

Pode executar os passos a partir do Código do Estúdio Visual ou de qualquer linha de comando disponível no seu sistema operativo. 

1. Navegue para a pasta `issuer`. 

    ```terminal
    cd issuer
    ```

2. Uma vez lá, precisamos instalar todas as embalagens necessárias e iniciar o site.

   ```terminal
    npm install
    node app.js
    ```

3. No terminal, verá agora que a sua app emitente está a ouvir na porta 8081. Agora vamos configurar um proxy reverso com o Ngrok para que o Autenticador possa comunicar com a sua aplicação. 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Criar um proxy invertido com ngrok

Quando executa o website da amostra, o seu dispositivo precisa de comunicar com o servidor Nó em execução na sua máquina local. Recomendamos a utilização [do ngrok](https://ngrok.com/) como uma forma fácil de disponibilizar o seu servidor de desenvolvimento local através da internet.

1.  Depois de descarregar e extrair **ngrok,** temos de correr:

    ```terminal
     ngrok http 8081
    ```

Por predefinição, o site da amostra funciona na `8081` porta. **A Ngrok** produz dois URLs de encaminhamento para o seu servidor. Copie o URL com o `https://` prefixo.

![ngrok ajuda-o a disponibilizar os pontos finais da sua aplicação através da internet](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> Se estiver a utilizar o PowerShell, poderá ter de escrever `./ngrok` para que o comando seja reconhecido.

Agora que a sua porta local está exposta à internet usando o ngrok, o site da amostra utiliza automaticamente o nome de anfitrião gerado pelo ngrok. Abra o seu navegador e navegue para o URL de encaminhamento de https ngrok. Você deve ser capaz de visitar com sucesso a página inicial do site da amostra. Se a página abrir, o seu dispositivo pode comunicar com a aplicação de amostra em execução no servidor local. Está pronto para emitir um cartão de perito credencial verificado.

## <a name="issue-a-credential"></a>Emita uma credencial

1. Instale o Autenticador no seu dispositivo móvel. O Microsoft Authenticator é utilizado para receber, armazenar e apresentar as suas credenciais verificáveis às partes interessadas.

2. Em seguida, emita-se uma credencial verificável. **Clique** no botão **Obter Credencial.** Quando clica no botão **Obter Credencial,** o site da amostra apresenta um código QR, que pode ser digitalizado usando o Autenticador. Se visualizar o site a partir do navegador no seu dispositivo móvel, clicar no botão **Get Credential** aciona um link profundo que abre a aplicação autenticadora e não requer a digitalização de um código QR.

   ![Obtenha botão de credencial](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Digitalize o código QR do site usando o Authenticator, ou se estiver a aceder ao website através de um clique móvel no botão de credencial Get para ativar o link profundo. 

   ![Código QR ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. Note que o botão **Adicionar** está acinzentado neste momento. Escolha **Iniciar sôm nas suas contas** abaixo da imagem do cartão.

   ![Iniciar sessão ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. Antes de obter o seu cartão de peritos em Credencial, o inquilino que estamos usando requer que forneça informações de autenticação. Se esta é a sua primeira vez passando por tutorial não tem uma conta de peritos credenciais, crie uma nova conta de utilizador no nosso inquilino B2C.

   ![autenticar antes de proceder](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. Depois de ter assinado, o botão **Adicionar** já não está acinzentado. Escolha **Adicionar** para aceitar o seu novo VC.

   ![Escolha adicionar após autenticação](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. Parabéns! Tem agora um perito em credenciais verificado, VC.

   ![VC especialista em credencial adicionado](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
Em seguida, é hora de verificar a sua credencial.

## <a name="validate-credentials"></a>Validar credenciais

Agora que completou a parte de emissão do tutorial e tem uma credencial verificável em Autenticador, é hora de validá-la na sua própria aplicação de verificação.

1.  Pare de executar o seu serviço de ngrok emitente.

    ```terminal
     control+c
    ```


2. Noutra janela terminal, abra a pasta de aplicações Verifier e execute-a da mesma forma que executámos a aplicação emitente.

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. Agora corra ngrok com a porta de verificação 8082.

    ```terminal
    ngrok http 8082
    ```

4. Abra o url de reencaminhamento de https ngrok no seu navegador e toque no botão **VERIFICAR CREDENCIAL.**  

   ![verificar botão de perito credencial](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Abra o Autenticador e digitalize o código QR.

   ![digitalizar código qr para verificar credencial](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > No iOS, é o canto superior direito e no Android é o inferior direito. Digitalize o código QR.

6. Escolha **Permitir** o novo ecrã de pedido de permissão no Autenticador. Ao pressionar, está a assinar uma apresentação verificável com o seu DID (Identificador Descentralizado) para provar que controla de facto esta Credencial Verificável.

   ![novo pedido de permissão](media/get-started-verifiable-credentials/new-permission-request.png)

    Após uma apresentação bem sucedida, três coisas deveriam ter sido atualizadas:

   1. A página web deve agora exibir "Parabéns, O Seu Nome" + é um Perito credencial Verificado!".
   
    ![parabéns, verificar novamente](media/get-started-verifiable-credentials/congratulations.png)


   2. O terminal de aplicações do verificador também deve exibir a mesma mensagem a partir dos registos.
   
    ![atividade de aplicação no comando](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. No Autenticador, deverá haver uma entrada para a atividade recente desta apresentação.

    ![Atividade no Autenticador](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> Durante a execução da aplicação verificador, o Ngrok pode deixar de funcionar e apresentar um erro de que existem demasiadas ligações. Descobrimos que isto pode ser evitado registando a sua conta com o Ngrok. 

## <a name="next-steps"></a>Passos seguintes

Agora que completou com sucesso o guia de arranque rápido, é hora de criar o seu próprio identificador descentralizado no serviço de credenciais verificáveis Azure AD e emitir a sua própria credencial verificável.

>[!div class="nextstepaction"] 
>[Configure o seu próprio emitente usando a aplicação de amostra de credenciais verificáveis](./enable-your-tenant-verifiable-credentials.md)
