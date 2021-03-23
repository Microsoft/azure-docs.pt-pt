---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 8262b0c9abc4911c1e5c039eb02920c18e7de90b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104783616"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

O Grupo de Serviços de Comunicação Azure **Call Hero Sample para Android** demonstra como a biblioteca de clientes Android dos Serviços de Comunicação pode ser usada para construir uma experiência de chamada de grupo que inclui voz e vídeo. Neste arranque rápido da amostra, você aprenderá a configurar e executar a amostra. Para o contexto está prevista uma visão geral da amostra.

## <a name="download-code"></a>Transferir código

Encontre o código finalizado para este arranque rápido no [GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero).

## <a name="overview"></a>Descrição geral

A amostra é uma aplicação nativa do Android que utiliza as bibliotecas de clientes Android dos Serviços de Comunicação Azure para construir uma experiência de chamada que apresenta chamadas de voz e vídeo. A aplicação utiliza um componente do lado do servidor para obter fichas de acesso que são depois usadas para inicializar a biblioteca de clientes Azure Communication Services. Para configurar este componente do lado do servidor, sinta-se livre para seguir o tutorial [do Serviço Fidedigno com Funções Azure.](../../tutorials/trusted-service-tutorial.md)

Aqui está o aspeto da amostra:

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="Screenshot mostrando a página de aterragem da aplicação da amostra.":::

Quando carrega no botão "Iniciar nova chamada", a aplicação Android cria uma nova chamada e junta-se a ela. A aplicação também permite que você se junte a uma chamada de Serviços de Comunicação Azure existente, especificando o ID da chamada existente.

Depois de se juntar a uma chamada, será solicitado que dê permissão à aplicação para aceder à sua câmara e microfone. Também lhe será pedido que forneça um nome de exibição.

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="Screenshot mostrando o ecrã pré-chamada da aplicação da amostra.":::

Assim que configurar o nome e os dispositivos do ecrã, pode juntar-se à chamada. Você verá a tela principal de chamada onde vive a experiência principal de chamada.

:::image type="content" source="../media/calling/main-app-android.png" alt-text="Screenshot mostrando o ecrã principal da aplicação da amostra.":::

Componentes do ecrã principal de chamada:

- **Media Gallery**: O palco principal onde os participantes são mostrados. Se um participante tiver a sua câmara ativada, o seu feed de vídeo é mostrado aqui. Cada participante tem um azulejo individual que mostra o seu nome de exibição e transmissão de vídeo (quando há um). A galeria suporta vários participantes e é atualizada quando os participantes são adicionados ou removidos à chamada.
- **Barra de Ação**: É aqui que estão localizados os controlos de chamada primário. Estes controlos permitem-lhe ligar/desligar o vídeo e o microfone, partilhar o ecrã e deixar a chamada.

Abaixo encontrará mais informações sobre pré-requisitos e passos para configurar a amostra.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Estúdio Android](https://developer.android.com/studio) em execução no seu computador
- Um recurso dos Serviços de Comunicação Azure. Para mais detalhes, consulte [Criar um Recurso de Comunicação Azure](../../quickstarts/create-communication-resource.md).
- Uma Função Azure executando o [Ponto final de autenticação](../../tutorials/trusted-service-tutorial.md) para obter fichas de acesso.

## <a name="running-sample-locally"></a>Amostra de corrida local

A amostra de chamada do grupo pode ser executada localmente usando o Android Studio. Os desenvolvedores podem usar o seu dispositivo físico ou um emulador para testar a aplicação.

### <a name="before-running-the-sample-for-the-first-time"></a>Antes de executar a amostra pela primeira vez

1. Abra o Estúdio Android e selecione `Open an Existing Project`
2. Abra a `AzureCalling` pasta no interior do desbloqueio descarregado para a amostra.
3. Expandir aplicativos/ativos para atualizar `appSettings.properties` . Desaprova o valor para a chave `communicationTokenFetchUrl` ser o URL para o seu Ponto final de autenticação configurado como pré-requisito.

### <a name="run-sample"></a>Amostra de execução

Construa e execute a amostra no Android Studio.

## <a name="optional-securing-an-authentication-endpoint"></a>(Opcional) Assegurar um ponto final de autenticação

Para fins de demonstração, esta amostra utiliza um ponto final acessível ao público por defeito para obter um token dos Serviços de Comunicação Azure. Para cenários de produção, recomendamos usar o seu próprio ponto final seguro para provisões para as suas próprias fichas.

Com uma configuração adicional, esta amostra suporta a ligação a um ponto final protegido do **Azure Ative** (Azure AD) para que o login do utilizador seja necessário para que a aplicação seja recortada por um token dos Serviços de Comunicação Azure. Veja os passos abaixo:

1. Ativar a autenticação do Azure Ative Directory na sua aplicação.  
   - [Registe a sua aplicação no Azure Ative Directory (utilizando as definições da plataforma Android)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [Configure o seu Serviço de Aplicações ou app Azure Functions para usar o login AZure AD](../../../app-service/configure-authentication-provider-aad.md)

2. Aceda à página geral da sua aplicação registada no âmbito dos Registos de Aplicações do Diretório Ativo Azure. Tome nota do `Package name` `Signature hash` , `MSAL Configutaion`

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Configuração do Diretório Ativo Azure no portal Azure.":::

3. Editar `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` e definir para permitir o `isAADAuthEnabled` Azure Ative Directory
4. Editar `AndroidManifest.xml` e definir `android:path` para keystore assinatura hash. (Opcional. O valor atual utiliza haxixe de debug.keystore. Se forem utilizadas teclas diferentes, esta deve ser atualizada.)
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. Copie a configuração do MSAL Android a partir do portal Azure e cole para `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` . Incluir "account_mode" : "SINGLE"
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. Edite `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` e detenva o valor para a chave `communicationTokenFetchUrl` ser o URL para o seu ponto final de autenticação seguro.
7. Editar `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` e definir o valor para a chave a partir de `aadScopes` `Azure Active Directory` `Expose an API` âmbitos

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../../quickstarts/create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

>[!div class="nextstepaction"]
>[Faça o download da amostra do GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero)

Para obter mais informações, veja os seguintes artigos:

- Familiarize-se com [a utilização da biblioteca de clientes de chamadas](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Saiba mais sobre [como funciona a chamada](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Leitura adicional

- [Azure Communication GitHub](https://github.com/Azure/communication) - Encontre mais exemplos e informações na página oficial do GitHub
- [Amostras](./../overview.md) - Encontre mais amostras e exemplos na página geral das nossas amostras.
- [Azure Communication Calling Features](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) - Para saber mais sobre o chamado Android sdk -[Azure Communication Android Calling SDK](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)
