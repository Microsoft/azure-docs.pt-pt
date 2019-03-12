---
title: Obter uma mensagem do Twitter com as funções do Azure | Documentos da Microsoft
description: Utilizar o sensor de movimento para detetar abanar e utilizar as funções do Azure para encontrar um tweet aleatório com uma hashtag que especificar
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: d684493a3ede55c241c901bb0814630b1d204e0c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542884"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Abane, abane para um Tweet – obter uma mensagem do Twitter com as funções do Azure

Este projeto, irá aprender a utilizar o sensor de movimento para acionar um evento com as funções do Azure. A aplicação obtém um tweet aleatório com um #hashtag que configurar no seu esboço de Arduino. Apresenta o tweet na tela DevKit.

## <a name="what-you-need"></a>Do que precisa

Concluir o [guia de introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Sua DevKit tiver ligado ao Wi-Fi.
* Prepare o ambiente de desenvolvimento.

Uma subscrição ativa do Azure. Se não tiver um, pode registrar-se através de um dos seguintes métodos:

* Ativar um [conta de Microsoft Azure avaliação de 30 dias gratuita](https://azure.microsoft.com/free/)
* Afirmação sua [crédito do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se for um assinante do MSDN ou o Visual Studio

## <a name="open-the-project-folder"></a>Abra a pasta de projeto

Comece por abrir a pasta do projeto. 

### <a name="start-vs-code"></a>Inicie o VS Code

* Certifique-se de que sua DevKit é conectado ao computador.

* Inicie o VS Code.

* Ligar o DevKit ao seu computador.

   > [!NOTE]
   > Ao iniciar o VS Code, poderá receber uma mensagem de erro que o Arduino IDE ou o pacote de quadro relacionados não pode ser encontrado. Se este erro ocorrer, feche o VS Code e volte a iniciar o Arduino IDE. VS Code agora deve localizar o caminho de Arduino IDE corretamente.

### <a name="open-the-arduino-examples-folder"></a>Abra a pasta de exemplos de Arduino

Expanda o lado esquerdo **exemplos de ARDUINO** secção, navegue até **exemplos para MXCHIP AZ3166 > AzureIoT**e selecione **ShakeShake**. É aberta uma nova janela do VS Code, exibindo a pasta do projeto. Se não é possível ver a secção de MXCHIP AZ3166, certifique-se de que o dispositivo está ligado corretamente e reinicie o Visual Studio Code.  
o ![mini solution exemplos](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Também pode abrir o projeto de exemplo da paleta de comandos. Clique em `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, escreva **Arduino**e, em seguida, localize e selecione **Arduino: Exemplos**.

## <a name="provision-azure-services"></a>Aprovisionar serviços do Azure

Na janela da solução, executar a tarefa através de `Ctrl+P` (macOS: `Cmd+P`) ao introduzir `task cloud-provision`.

No terminal VS Code, uma linha de comandos interativa orienta-o através do aprovisionamento os serviços do Azure necessários:

![cloud-provision](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Se a página paradas do Estado de carregamento ao tentar iniciar sessão no Azure, consulte a [passo "página de início de sessão se destaca" em perguntas Freqüentes IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Modificar o #hashtag

Abra `ShakeShake.ino` e procure esta linha de código:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Substitua a cadeia de caracteres `iot` dentro das chavetas por sua hashtag preferencial. O DevKit mais tarde obtém um tweet aleatório, que inclui a hashtag que especificar neste passo.

## <a name="deploy-azure-functions"></a>Implementar as Funções do Azure

Uso `Ctrl+P` (macOS: `Cmd+P`) para executar `task cloud-deploy` para começar a implementar o código de funções do Azure:

![-implementar a cloud](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Ocasionalmente, a função do Azure poderá não funcionar corretamente. Para resolver este problema quando ocorrer, verifique os [secção de "erro de compilação" de IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Criar e carregar o código de dispositivo

Em seguida, criar e carregar o código de dispositivo.

### <a name="windows"></a>Windows

1. Uso `Ctrl+P` para executar `task device-upload`.

2. Terminal pede-lhe para introduzir o modo de configuração. Para tal:

   * Mantenha premida A do botão

   * Push e largue o botão de reposição.

3. A tela exibe a DevKit ID e a 'Configuração'.

### <a name="macos"></a>macOS

1. Coloca o DevKit no modo de configuração:

   Premido o botão A, em seguida, enviar por push e largue o botão de reposição. A tela exibe 'Configuração'.

2. Uso `Cmd+P` para executar `task device-upload` para definir a cadeia de ligação que é obtida a partir do `task cloud-provision` passo.

### <a name="verify-upload-and-run"></a>Certifique-se, carregar e executar

Agora, a cadeia de ligação é definida, verifica e carrega a aplicação, em seguida, executa-o. 

1. Código VS começa a verificar e carregar o esboço de Arduino para sua DevKit:

   ![carregamento de dispositivo](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. O DevKit reinicia e começa a execução do código.

Poderá receber um "erro: AZ3166: Mensagem de erro do pacote desconhecido". Este erro ocorre quando o índice de pacote de quadro não será atualizado corretamente. Para resolver este problema, verifique os [erro de "pacote desconhecido" em perguntas Freqüentes IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

Após a inicialização de aplicação, clique num botão de versão, e moderadamente abane o quadro de DevKit. Esta ação obtém um tweet aleatório, que contém a hashtag que especificou anteriormente. Dentro de alguns segundos, um tweet exibe na tela DevKit:

### <a name="arduino-application-initializing"></a>Arduino inicializar a aplicação...

![A inicializar o Arduino-aplicação](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Prima A suficiente para...

![Prima-um-para-Agite](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Preparado para o suficiente para...

![Pronto para Agite](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>A processar...

![Em processamento](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Prima B para ler...

![Prima B-leitura](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Apresenta um tweet aleatório...

![Apresentar-a-aleatório-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Prima o botão um novamente e, em seguida, abane para um novo tweet.
- Prima o botão B para percorrer o restante do tweet.

## <a name="how-it-works"></a>Como funciona

![Diagrama](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

O esboço de Arduino envia um evento para o IoT Hub do Azure. Este evento é acionado a aplicação de funções do Azure. A aplicação de funções do Azure contém a lógica para ligar à API do Twitter e obter um tweet. Em seguida, encapsula o texto do tweet num C2D mensagem (Cloud-para-dispositivo) e envia-lo de volta ao dispositivo.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Opcional: Utilizar o seu token de portador do Twitter

Para fins de teste, esse projeto de exemplo usa um token de portador do Twitter previamente configurado. No entanto, há uma [limite de taxa](https://dev.twitter.com/rest/reference/get/search/tweets) para cada conta do Twitter. Se pretender considerar o uso de seu próprio token, siga estes passos:

1. Aceda a [portal do programador do Twitter](https://dev.twitter.com/) para registar uma nova aplicação do Twitter.

2. [Obter chave de consumidor e segredos de consumidor](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) da sua aplicação.

3. Uso [alguma utilidade](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) para gerar um token de portador do Twitter a partir dessas duas chaves.

4. Na [portal do Azure](https://portal.azure.com/){: target = blank"}, entrar no **grupo de recursos** e encontrar a função do Azure (tipo: Serviço de aplicações) para o seu projeto de "Abanar, Agite". O nome contém sempre 'abane..."cadeia de caracteres.

   ![azure-function](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Atualizar o código para `run.csx` dentro **funções > shakeshake cs** com o seu próprio token:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
  ```
  
  ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Guarde o ficheiro e clique em **executar**.

## <a name="problems-and-feedback"></a>Problemas e comentários

Como resolver problemas ou forneça comentários. 

### <a name="problems"></a>Problemas

Um problema pode ver se a tela exibe "Sem Tweets", enquanto cada etapa foi executada com êxito. Esta condição ocorre normalmente a primeira vez, implementar e executar o exemplo, uma vez que a aplicação de funções requer que em qualquer lugar de alguns segundos até um minuto para arranque a frio a aplicação. 

Em alternativa, ao executar o código, existem alguns blips que causam um reinício da aplicação. Quando esta condição ocorre, a aplicação de dispositivo pode obter um tempo limite para a obter o tweet. Neste caso, pode experimentar um ou ambos os métodos para resolver o problema:

1. Clique no botão de reposição no DevKit para executar a aplicação de dispositivo novamente.

2. Na [portal do Azure](https://portal.azure.com/), localize a aplicação de funções do Azure que criou e reiniciá-lo:

   ![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Comentários

Se tiver outros problemas, consulte a [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-nos com os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como ligar um dispositivo de DevKit ao seu acelerador de solução de monitorização remota do Azure IoT e obter um tweet, aqui estão os passos sugeridos seguintes:

* [Monitorização remota do IoT solution accelerator descrição geral do Azure](https://docs.microsoft.com/azure/iot-suite/)