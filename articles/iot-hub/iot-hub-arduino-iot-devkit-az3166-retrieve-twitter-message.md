---
title: Recupere uma mensagem no Twitter com as funções Azure | Microsoft Docs
description: Use o sensor de movimento para detetar agitação e use funções Azure para encontrar um tweet aleatório com uma hashtag que especifique
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: af1685f6455c0642800cba7dd604fcc836bcd7a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147905"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Shake, Shake for a Tweet -- Recupere uma mensagem no Twitter com funções Azure

Neste projeto, aprende-se a usar o sensor de movimento para desencadear um evento utilizando funções Azure. A aplicação recupera um tweet aleatório com um #hashtag configurar no seu esboço Arduino. O tweet aparece no ecrã DevKit.

## <a name="what-you-need"></a>O que precisa

Termine o [Guia de Início](./iot-hub-arduino-iot-devkit-az3166-get-started.md) para:

* Tenha o seu DevKit ligado ao Wi-Fi.
* Prepare o ambiente de desenvolvimento.

Uma subscrição ativa do Azure. Se não tiver um, pode registar-se através de um destes métodos:

* Ativar uma [conta gratuita de 30 dias da Microsoft Azure](https://azure.microsoft.com/free/)
* Reclame o seu [crédito Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se for assinante da MSDN ou do Visual Studio

## <a name="open-the-project-folder"></a>Abra a pasta do projeto

Comece por abrir a pasta do projeto. 

### <a name="start-vs-code"></a>Iniciar código VS

* Certifique-se de que o seu DevKit está ligado ao seu computador.

* Inicie o VS Code.

* Ligue o DevKit ao seu computador.

   > [!NOTE]
   > Ao lançar o Código VS, poderá receber uma mensagem de erro de que o Arduino IDE ou o pacote de placas relacionados não possam ser encontrados. Se ocorrer este erro, feche o Código VS e volte a lançar o Arduino IDE. O Código VS deve agora localizar corretamente o caminho Arduino IDE.

### <a name="open-the-arduino-examples-folder"></a>Abra a pasta Arduino Examples

Expanda a secção **ARDUINO EXEMPLOS** do lado esquerdo, navegue **para Exemplos para MXCHIP AZ3166 > AzureIoT,** e selecione **ShakeShake**. Abre-se uma nova janela VS Code, exibindo a pasta do projeto. Se não conseguir ver a secção MXCHIP AZ3166, certifique-se de que o seu dispositivo está corretamente ligado e reinicie o Código do Estúdio Visual.  
os ![ mini-exemplos de solução](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Também pode abrir o projeto de amostra a partir da paleta de comando. Clique `Ctrl+Shift+P` (macOS: `Cmd+Shift+P` ) para abrir a paleta de comando, **digite Arduino,** e depois encontre e selecione **Arduino: Exemplos**.

## <a name="provision-azure-services"></a>Prestação de serviços Azure

Na janela da solução, passe a sua tarefa através `Ctrl+P` (macOS: `Cmd+P` ) entrando `task cloud-provision` .

No terminal de Código VS, uma linha de comando interativa guia-o através do fornecimento dos serviços Azure necessários:

![A screenshot mostra a linha de comando interativa do terminal visual Studio Code.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Se a página estiver pendurada no estado de carregamento ao tentar entrar no Azure, consulte o [passo "iniciar sessão" no IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Modifique o #hashtag

Abra `ShakeShake.ino` e procure esta linha de código:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Substitua a corda `iot` dentro dos aparelhos encaracolados pela hashtag preferida. O DevKit mais tarde recupera um tweet aleatório que inclui a hashtag que especifica neste passo.

## <a name="deploy-azure-functions"></a>Implementar as Funções do Azure

Utilizar `Ctrl+P` (macOS: `Cmd+P` ) para executar para começar a `task cloud-deploy` implantar o código Azure Functions:

![O Screenshot mostra o Código do Estúdio Visual onde pode executar a implementação de cloud-deploy de tarefas para implementar o código Azure Functions.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Ocasionalmente, a Função Azure pode não funcionar corretamente. Para resolver este problema quando ocorrer, verifique a [secção "erro de compilação" da FAQ IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Construa e carreie o código do dispositivo

Em seguida, construa e carreie o código do dispositivo.

### <a name="windows"></a>Windows

1. Use `Ctrl+P` para correr `task device-upload` .

2. O terminal solicita-lhe que entre no modo de configuração. Para tal:

   * Mantenha o botão A

   * Empurre e solte o botão de reset.

3. O ecrã apresenta o ID de DevKit e 'Configuração'.

### <a name="macos"></a>macOS

1. Coloque o DevKit no modo de configuração:

   Mantenha premir o botão A, em seguida, pressione e liberte o botão de reset. O ecrã apresenta 'Configuração'.

2. Utilize `Cmd+P` para executar para definir a cadeia de `task device-upload` ligação que é recuperada do `task cloud-provision` degrau.

### <a name="verify-upload-and-run"></a>Verifique, carrede e corra

Agora que a cadeia de ligação está definida, verifica e carrega a aplicação e, em seguida, executa-a. 

1. O Código VS começa a verificar e a enviar o esboço Arduino para o seu DevKit:

   ![A screenshot mostra o Código do Estúdio Visual a verificar e a carregar o esboço do Arduino.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. O DevKit reinicia e começa a executar o código.

Pode obter uma mensagem de erro "Error: AZ3166: Unknown package". Este erro ocorre quando o índice do pacote de placa não é atualizado corretamente. Para resolver este problema, verifique o [erro do "pacote desconhecido" nas FAQ do IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

Após a inicialização da aplicação, clique e liberte o botão A e, em seguida, agite suavemente a placa DevKit. Esta ação recupera um tweet aleatório, que contém a hashtag especificada anteriormente. Em poucos segundos, um tweet aparece no ecrã do DevKit:

### <a name="arduino-application-initializing"></a>Arduino aplicação inicializando...

![Inicialização de aplicações Arduino](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Pressione A para agitar...

![Pressione-A-para-agitar](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Pronto para tremer...

![Pronto a agitar](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>A processar...

![Em processamento](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Pressione B para ler...

![Press-B-to-read](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Exibimos um tweet aleatório...

![Display-a-random-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Pressione novamente o botão A e, em seguida, agite para um novo tweet.
- Prima o botão B para percorrer o resto do tweet.

## <a name="how-it-works"></a>Como funciona

![O diagrama mostra um dispositivo móvel a enviar um evento para o Azure I o T Hub, que aciona uma aplicação Azure Function para solicitar um tweet, que é enviado de volta para a app e reencaminhado para o centro e para o dispositivo móvel.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

O esboço de Arduino envia um evento para o Azure IoT Hub. Este evento aciona a aplicação Azure Functions. A aplicação Azure Functions contém a lógica de ligar à API do Twitter e recuperar um tweet. Em seguida, embrulha o texto do tweet numa mensagem C2D (Cloud-to-device) e envia-o de volta para o dispositivo.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Opcional: Use o seu próprio símbolo do porta-twitter

Para efeitos de teste, este projeto de amostra utiliza um símbolo pré-configurado do Twitter. No entanto, há um [limite de taxas](https://dev.twitter.com/rest/reference/get/search/tweets) para cada conta de Twitter. Se quiser considerar usar o seu próprio símbolo, siga estes passos:

1. Vá ao [portal Do Desenvolvimento do Twitter](https://dev.twitter.com/) para registar uma nova aplicação do Twitter.

2. [Obtenha a Chave do Consumidor e os Segredos](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) do Consumidor da sua aplicação.

3. Use [alguma utilidade](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) para gerar um sinal do portador do Twitter a partir destas duas teclas.

4. No [portal Azure](https://portal.azure.com/){:target="_blank"}, entre no **Grupo de Recursos** e encontre a Função Azure (Tipo: Serviço de Aplicações) para o seu projeto "Shake, Shake". O nome contém sempre "shake...". corda.

   ![A screenshot do portal Azure mostra um serviço de aplicações para o projeto.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Atualize o código para `run.csx` dentro **de Funções > shakeshake-cs** com o seu próprio token:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![A screenshot mostra o código C# para a função onde pode introduzir o seu token.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Guarde o ficheiro e clique em **Executar**.

## <a name="problems-and-feedback"></a>Problemas e feedback

Como resolver problemas ou fornecer feedback. 

### <a name="problems"></a>Problemas

Um problema que pode ver se o ecrã apresenta 'No Tweets' enquanto cada passo foi executado com sucesso. Esta condição normalmente acontece na primeira vez que implementa e executa a amostra porque a aplicação de função requer em qualquer lugar de um par de segundos até um minuto para iniciar a app a frio. 

Ou, ao executar o código, existem alguns blips que causam o reinício da aplicação. Quando esta condição acontece, a aplicação do dispositivo pode obter um tempo limite para ir buscar o tweet. Neste caso, pode tentar um ou ambos estes métodos para resolver o problema:

1. Clique no botão reset no DevKit para executar novamente a aplicação do dispositivo.

2. No [portal Azure,](https://portal.azure.com/)encontre a app Azure Functions que criou e reinicie-a:

   ![O Screenshot mostra o portal Azure com uma aplicação Azure Functions e o botão Reiniciar.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

Se tiver outros problemas, consulte o [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-nos através dos seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a ligar um dispositivo DevKit ao seu acelerador de solução de monitorização remota Azure IoT e recuperar um tweet, aqui estão os próximos passos sugeridos:

* [Visão geral do acelerador de solução de monitorização remota Azure IoT](/azure/iot-suite/)