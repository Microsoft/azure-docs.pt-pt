---
title: Opções de jogador de mídia azure
description: O código de incorporação do Azure Media Player é simplesmente uma etiqueta de vídeo HTML5, pelo que para muitas das opções pode utilizar os atributos de etiqueta padrão para definir as opções.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e26215115b4c4484e5e05a2fd94a4d2c6680a4d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727167"
---
# <a name="options"></a>Opções #

## <a name="setting-options"></a>Opções da definição ##

O código de incorporação do Azure Media Player é simplesmente uma etiqueta de vídeo HTML5, pelo que para muitas das opções pode utilizar os atributos de etiqueta padrão para definir as opções.

`<video controls autoplay ...>`

Em alternativa, pode utilizar o atributo de configuração de dados para fornecer opções no formato [JSON.](http://json.org/example.html) É também assim que definiria opções que não são padrão para a etiqueta de vídeo.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Finalmente, se não estiver a utilizar o atributo de configuração de dados para desencadear a configuração do leitor, pode passar num objeto com as opções do jogador como segundo argumento na função de configuração JavaScript.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> As opções no construtor só estão definidas na primeira inicialização antes da definição da fonte.  Se pretender modificar as opções no mesmo elemento Azure Media Player inicializado, tem de atualizar as opções antes de alterar a fonte. Pode atualizar as opções no `myPlayer.options({/*updated options*/});`JavaScript utilizando . Note que apenas as opções alteradas serão afetadas, todas as outras opções previamente definidas persistirão.

## <a name="individual-options"></a>Opções individuais ##

> [!NOTE]
>Os Atributos de Etiquetas de Vídeo só podem ser verdadeiros ou falsos (booleanos), basta incluir o atributo (nenhum sinal igual) para ligá-lo, ou excluí-lo para desligá-lo. Por exemplo, para ligar `<video controls="true" ...>` os `<video controls ...>` controlos: WRONG RIGHT A maior questão que as pessoas enfrentam é tentar definir estes valores como falso sem uso falso como valor (por exemplo, controlos="falsos") que realmente faz o contrário e define o valor para o verdadeiro porque o atributo ainda está incluído.

### <a name="controls"></a>controlos ###

A opção de controlo define se o leitor tem ou não controlos com os quais o utilizador pode interagir. Sem controlos, a única forma de iniciar o vídeo é com o atributo de reprodução automática ou através da API.

`<video controls ...>` ou `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Se a reprodução automática for verdadeira, o vídeo começará a ser reproduzido assim que a página for carregada (sem qualquer interação do utilizador).

> [!NOTE]
> Esta opção não é suportada por dispositivos móveis como Windows Phone, Apple iOS e Android. Os dispositivos móveis bloqueiam a funcionalidade de reprodução automática para evitar o uso dos planos de dados mensais do consumidor (muitas vezes caros). É necessário um toque/clique do utilizador para iniciar o vídeo neste caso.

`<video autoplay ...>`ou`{ "autoplay": true }`

### <a name="poster"></a>cartaz ###
O atributo do cartaz define a imagem que exibe antes do vídeo começar a ser reproduzido. Esta é muitas vezes uma moldura do vídeo ou um ecrã de título personalizado. Assim que o utilizador clicar, a imagem desaparecerá.

`<video poster="myPoster.jpg" ...>` ou `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>largura ###

O atributo de largura define a largura do visor do vídeo.

`<video width="640" ...>` ou `{ "width": 640 }`

### <a name="height"></a>altura ###

O atributo de altura define a altura do visor do vídeo.

`<video height="480" ...>` ou `{ "height": 480 }`

### <a name="plugins"></a>plugins ###

Os plugins JSON determinam quais os plugins que são carregados com essa instância de AMP permite configurar quaisquer opções que o plugin possa ter.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Para obter mais informações sobre o desenvolvimento e utilização do plugin, consulte [plugins de escrita](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>outras opções ###

Outras opções podem `<video>` ser definidas na etiqueta utilizando o `data-setup` parâmetro que leva um JSON.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Isto está explicitamente definido para falso. Ao definir-se como falso, permitirá que a pele do Azure Media Player seja renderizada da mesma forma em todas as plataformas.  Além disso, ao contrário do nome, o toque continuará a ser ativado.

### <a name="fluid"></a>fluido ###

Ao definir esta opção para um verdadeiro elemento de vídeo, a largura total do recipiente-mãe e a altura serão ajustadas para encaixar num vídeo com uma relação padrão de 16:9.

`<video ... data-setup='{"fluid": true}'>`

`fluid`opção substitui `width` `height` as definições explícitas e configurações. Esta opção só está disponível `2.0.0` na versão Azure Media Player e posteriormente.

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed`a opção controla a reproduçãoSpeed e o conjunto de definições de velocidade de reprodução disponíveis para o utilizador. `playbackSpeed`leva um objeto. Para permitir o controlo da velocidade de `enabled` reprodução na barra de controlo, a propriedade do objeto tem de ser definida como verdadeira. Um exemplo de permitir a velocidade de reprodução na marcação:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Outras propriedades `playbackSpeed` da definição são dadas pelo objeto [PlaybackSpeedOptions.](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions)

Exemplo de definição de opções de velocidade de reprodução no JavaScript:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Esta opção só está disponível na versão 2.0.0 do Azure Media Player.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

A `staleDataTimeLimitInSec` opção é uma otimização que permite configurar quantos segundos de dados antigos gostaria de manter nos amortecedores mediaSource. Opção desativada por predefinição.

### <a name="cea708captionssettings"></a>cea708CaptionsDefinições ###

A definição ativada permite-lhe exibir legendas CEA ao vivo nos seus streams ao vivo e arquivos ao vivo. O atributo da etiqueta não é necessário, se não estiver incluído, o leitor voltará a ser um rótulo predefinido.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Esta opção só está disponível na versão 2.1.1 do Azure Media Player.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)