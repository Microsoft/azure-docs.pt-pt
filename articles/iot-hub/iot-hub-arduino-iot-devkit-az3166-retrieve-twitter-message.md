---
title: Recupere uma mensagem no Twitter com funções azure / Microsoft Docs
description: Use o sensor de movimento para detetar agitação e utilização de Funções Azure para encontrar um tweet aleatório com uma hashtag que especifique
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: dc4ff35ff04680e8635d54c25212c8ae639ae472
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60779905"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Shake, Shake for a Tweet -- Recupere uma mensagem no Twitter com funções azure

Neste projeto, aprende-se a usar o sensor de movimento para desencadear um evento utilizando funções Azure. A aplicação recupera um tweet aleatório com um #hashtag configurano seu esboço arduino. O tweet aparece no ecrã DevKit.

## <a name="what-you-need"></a>Do que precisa

Termine o [Guia de Início de Partida](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Tenha o seu DevKit ligado ao Wi-Fi.
* Prepare o ambiente de desenvolvimento.

Uma subscrição ativa do Azure. Se não tiver um, pode registar-se através de um destes métodos:

* Ativar uma [conta gratuita de 30 dias do Microsoft Azure](https://azure.microsoft.com/free/)
* Reclame o seu [crédito Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se for assinante da MSDN ou do Visual Studio

## <a name="open-the-project-folder"></a>Abra a pasta do projeto

Comece por abrir a pasta do projeto. 

### <a name="start-vs-code"></a>Iniciar código VS

* Certifique-se de que o seu DevKit está ligado ao computador.

* Inicie o VS Code.

* Ligue o DevKit ao seu computador.

   > [!NOTE]
   > Ao lançar o Código VS, pode receber uma mensagem de erro segundo a qual o Pacote Arduino IDE ou o pacote de placas relacionados não podem ser encontrados. Se este erro ocorrer, feche o Código VS e volte a lançar o Arduino IDE. O Código VS deve agora localizar corretamente o caminho Arduino IDE.

### <a name="open-the-arduino-examples-folder"></a>Abra a pasta Arduino Exemplos

Expanda a secção **DE EXEMPLOS ARDUINO** do lado esquerdo, navegue para **Exemplos para MXCHIP AZ3166 > AzureIoT,** e selecione **ShakeShake**. Abre-se uma nova janela vs Código, exibindo a pasta do projeto. Se não conseguir ver a secção MXCHIP AZ3166, certifique-se de que o seu dispositivo está devidamente ligado e reinicia o Código do Estúdio Visual.  
os ![mini-solução-exemplos](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Também pode abrir o projeto de amostra a partir da paleta de comando. Clique `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comando, digite **Arduino,** e depois encontre e selecione **Arduino: Exemplos**.

## <a name="provision-azure-services"></a>Serviços De prestação de serviços Azure

Na janela de solução, `Ctrl+P` passe a `Cmd+P`sua tarefa `task cloud-provision`através (macOS: ) entrando .

No terminal vs Código, uma linha de comando interativa guia-o através do fornecimento dos serviços Azure necessários:

![nuvem-provision](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Se a página estiver pendurada no estado de carregamento ao tentar iniciar o contrato com o Azure, consulte o [passo "login" no IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Modificar a #hashtag

Abra `ShakeShake.ino` e procure esta linha de código:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Substitua `iot` a corda dentro do aparelho encaracolado com a sua hashtag preferida. O DevKit mais tarde recupera um tweet aleatório que inclui a hashtag que especifica neste passo.

## <a name="deploy-azure-functions"></a>Implementar as Funções do Azure

Utilização `Ctrl+P` (macOS: `Cmd+P` `task cloud-deploy` ) para funcionar para começar a implementar o código funções Azure:

![nuvem-implantação](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Ocasionalmente, a Função Azure pode não funcionar corretamente. Para resolver este problema quando ocorrer, verifique a [secção "erro de compilação" do IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Construir e carregar o código do dispositivo

Em seguida, construa e carregue o código do dispositivo.

### <a name="windows"></a>Windows

1. Use `Ctrl+P` para `task device-upload`correr.

2. O terminal pede-lhe para entrar no modo de configuração. Para tal:

   * Mantenha para baixo o botão A

   * Pressione e liberte o botão de reset.

3. O ecrã apresenta o ID de DevKit e 'Configuração'.

### <a name="macos"></a>macOS

1. Coloque o DevKit no modo de configuração:

   Segure o botão A, em seguida, pressione e liberte o botão de reset. O ecrã apresenta 'Configuração'.

2. Utilize `Cmd+P` para `task device-upload` executar para definir a corda `task cloud-provision` de ligação que é recuperada do degrau.

### <a name="verify-upload-and-run"></a>Verificar, carregar e executar

Agora a cadeia de ligação está definida, verifica e carrega a aplicação e executa-a. 

1. O Código VS começa a verificar e a carregar o esboço arduino para o seu DevKit:

   ![dispositivo-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. O DevKit reinicia e começa a executar o código.

Pode obter uma mensagem de erro "Error: AZ3166: Unknown package". Este erro ocorre quando o índice do pacote de placa não é atualizado corretamente. Para resolver este problema, verifique o [erro do "pacote desconhecido" no IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

Após a inicialização da aplicação, clique e solte o botão A e, em seguida, agite suavemente a placa DevKit. Esta ação recupera um tweet aleatório, que contém a hashtag que especificou anteriormente. Dentro de poucos segundos, um tweet exibe no ecrã de DevKit:

### <a name="arduino-application-initializing"></a>Aplicação Arduino inicializando...

![Arduino-application-initializing](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Pressione A para tremer...

![Imprensa-A-a-shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Pronto para tremer...

![Pronto-a-agitar](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>A processar...

![Em processamento](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Pressione B para ler...

![Imprensa-B-a-ler](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Exibir um tweet aleatório...

![Display-a-random-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Prima novamente o botão A e, em seguida, agite para um novo tweet.
- Pressione o botão B para percorrer o resto do tweet.

## <a name="how-it-works"></a>Como funciona

![diagrama](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

O esboço de Arduino envia um evento para o Hub Azure IoT. Este evento despoleta a aplicação Funções Azure. A aplicação Azure Functions contém a lógica de ligar à API do Twitter e recuperar um tweet. Em seguida, embrulha o texto do tweet numa mensagem C2D (Cloud-to-device) e envia-o de volta para o dispositivo.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Opcional: Use o seu próprio token do portador do Twitter

Para efeitos de teste, este projeto de amostra utiliza um token de porta-twitter pré-configurado. No entanto, há um [limite de taxas](https://dev.twitter.com/rest/reference/get/search/tweets) para cada conta no Twitter. Se quiser considerar usar o seu próprio símbolo, siga estes passos:

1. Vá ao [portal Twitter Developer](https://dev.twitter.com/) para registar uma nova aplicação do Twitter.

2. [Obtenha a Chave do Consumidor e os Segredos](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) do Consumidor da sua aplicação.

3. Use [algum utilitário](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) para gerar um token do portador do Twitter a partir destas duas chaves.

4. No [portal Azure](https://portal.azure.com/){:target="_blank"}, entre no **Grupo de Recursos** e encontre a Função Azure (Tipo: Serviço de Aplicações) para o seu projeto "Shake, Shake". O nome sempre contém "shake..." corda.

   ![azure-função](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Atualize o `run.csx` código para dentro de **Funções > shakeshake-cs** com o seu próprio token:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Guarde o ficheiro e clique em **Executar**.

## <a name="problems-and-feedback"></a>Problemas e feedback

Como resolver problemas ou fornecer feedback. 

### <a name="problems"></a>Problemas

Um problema que pode ver se o ecrã apresenta 'No Tweets' enquanto cada passo corre com sucesso. Esta condição normalmente acontece na primeira vez que você implementa e executa a amostra porque a aplicação de função requer qualquer coisa de alguns segundos até um minuto para começar a aplicação fria. 

Ou, ao executar o código, existem alguns blips que causam o reinício da aplicação. Quando esta condição acontece, a aplicação do dispositivo pode obter um tempo para ir buscar o tweet. Neste caso, pode tentar um ou ambos os métodos para resolver o problema:

1. Clique no botão de reset no DevKit para executar novamente a aplicação do dispositivo.

2. No [portal Azure,](https://portal.azure.com/)encontre a app Funções Azure que criou e reinicie-a:

   ![azure-função-reiniciar](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Comentários

Se tiver outros problemas, consulte o [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-nos utilizando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a ligar um dispositivo DevKit ao seu acelerador de solução de monitorização remota Azure IoT e recuperar um tweet, aqui estão os próximos passos sugeridos:

* [Visão geral do acelerador de solução de monitorização remota Azure IoT](https://docs.microsoft.com/azure/iot-suite/)