---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 1318c47bcded47159006977db09604bb53674973
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487948"
---
[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

O Grupo de Serviços de Comunicação Azure **Chamando a amostra de herói para iOS** demonstra como a biblioteca de clientes iOS dos Serviços de Comunicação pode ser usada para construir uma experiência de chamada de grupo que inclui voz e vídeo. Neste arranque rápido da amostra, você aprenderá a configurar e executar a amostra. Para o contexto está prevista uma visão geral da amostra.

## <a name="overview"></a>Descrição Geral

A amostra é uma aplicação nativa do iOS que utiliza as bibliotecas de clientes iOS dos Serviços de Comunicação Azure para construir uma experiência de chamada que apresenta chamadas de voz e vídeo. A aplicação utiliza um componente do lado do servidor para obter fichas de acesso que são depois usadas para inicializar a biblioteca de clientes Azure Communication Services. Para configurar este componente do lado do servidor, sinta-se livre para seguir o tutorial [do Serviço Fidedigno com Funções Azure.](../../tutorials/trusted-service-tutorial.md)

Aqui está o aspeto da amostra:

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="Screenshot mostrando a página de aterragem da aplicação da amostra.":::

Quando carrega no botão "Iniciar nova chamada", a aplicação iOS cria uma nova chamada e junta-se a ela. A aplicação também permite que você se junte a uma chamada de Serviços de Comunicação Azure existente, especificando o ID da chamada existente.

Depois de se juntar a uma chamada, será solicitado que dê permissão à aplicação para aceder à sua câmara e microfone. Também lhe será pedido que forneça um nome de exibição.

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="Screenshot mostrando o ecrã pré-chamada da aplicação da amostra.":::

Assim que configurar o nome e os dispositivos do ecrã, pode juntar-se à chamada. Você verá a tela principal de chamada onde vive a experiência principal de chamada.

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="Screenshot mostrando o ecrã principal da aplicação da amostra.":::

Componentes do ecrã principal de chamada:

- **Media Gallery**: O palco principal onde os participantes são mostrados. Se um participante tiver a sua câmara ativada, o seu feed de vídeo é mostrado aqui. Cada participante tem um azulejo individual que mostra o seu nome de exibição e transmissão de vídeo (quando há um). A galeria suporta vários participantes e é atualizada quando os participantes são adicionados ou removidos à chamada.
- **Barra de Ação**: É aqui que estão localizados os controlos de chamada primário. Estes controlos permitem-lhe ligar/desligar o vídeo e o microfone, partilhar o ecrã e deixar a chamada.

Abaixo encontrará mais informações sobre pré-requisitos e passos para configurar a amostra.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Um Mac que executa [o Xcode,](https://go.microsoft.com/fwLink/p/?LinkID=266532)juntamente com um certificado de desenvolvimento válido instalado no seu Keychain.
- Um recurso dos Serviços de Comunicação Azure. Para mais detalhes, consulte [Criar um Recurso de Comunicação Azure](../../quickstarts/create-communication-resource.md).
- Uma função Azure executando [a lógica de Serviço Fidedigno](../../tutorials/trusted-service-tutorial.md) para obter tokens de acesso.

## <a name="running-sample-locally"></a>Amostra de corrida local

A amostra de chamada de grupo pode ser executada localmente usando o XCode. Os desenvolvedores podem usar o seu dispositivo físico ou um emulador para testar a aplicação.

### <a name="before-running-the-sample-for-the-first-time"></a>Antes de executar a amostra pela primeira vez

1. Instale as dependências funcionando `pod install` .
2. Abra `ACSCall.xcworkspace` em XCode.
3. Atualização `AppSettings.plist` . Desaponte o valor para `acsTokenFetchUrl` a chave ser o URL para o seu Ponto final de autenticação.

### <a name="run-sample"></a>Amostra de execução

Construa e passe a amostra no XCode.

## <a name="optional-securing-an-authentication-endpoint"></a>(Opcional) Assegurar um ponto final de autenticação

Para fins de demonstração, esta amostra utiliza um ponto final acessível ao público por defeito para obter um token dos Serviços de Comunicação Azure. Para cenários de produção, recomendamos usar o seu próprio ponto final seguro para provisões para as suas próprias fichas.

Com uma configuração adicional, esta amostra suporta a ligação a um ponto final protegido do **Azure Ative** (Azure AD) para que o login do utilizador seja necessário para que a aplicação seja recortada por um token dos Serviços de Comunicação Azure. Veja os passos abaixo:

1. Ativar a autenticação do Azure Ative Directory na sua aplicação.  
   - [Registe a sua aplicação no Diretório Ativo Azure (utilizando as definições da plataforma iOS/macOS)](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [Configure o seu Serviço de Aplicações ou app Azure Functions para usar o login AZure AD](../../../app-service/configure-authentication-provider-aad.md)
2. Aceda à página geral da sua aplicação registada no âmbito dos Registos de Aplicações do Diretório Ativo Azure. Tome nota do `Application (client) ID` `Directory (tenant) ID` , `Application ID URI`

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Configuração do Diretório Ativo Azure no portal Azure.":::

3. Abra `AppSettings.plist` em Xcode, adicione os seguintes valores-chave:
   - `acsTokenFetchUrl`: A URL para solicitar o token dos Serviços de Comunicação da Azure 
   - `isAADAuthEnabled`: Um valor booleano para indicar se a autenticação simbólica dos Serviços de Comunicação Azure é necessária ou não
   - `aadClientId`: ID da sua aplicação (cliente)
   - `aadTenantId`: O seu Diretório (inquilino) ID
   - `aadRedirectURI`: O URI de redirecionamento deve estar neste formato: `msauth.<app_bundle_id>://auth`
   - `aadScopes`: Um conjunto de âmbitos de permissão solicitados aos utilizadores para autorização. Adicione `<Application ID URI>/user_impersonation` à matriz para conceder acesso ao ponto final de autenticação

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../../quickstarts/create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos:

- Familiarize-se com [a utilização da biblioteca de clientes de chamadas](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Saiba mais sobre [como funciona a chamada](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Leitura adicional

- [Amostras](./../overview.md) - Encontre mais amostras e exemplos na página geral das nossas amostras.