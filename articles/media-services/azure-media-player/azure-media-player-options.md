---
title: Opções de jogador de media azure
description: O código incorporado Azure Media Player é simplesmente uma tag de vídeo HTML5, por isso para muitas das opções pode utilizar os atributos de marca padrão para definir as opções.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 261d4710e1c88a89c6dcef06dad430cd996b2869
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448784"
---
# <a name="options"></a>Opções #

## <a name="setting-options"></a>Opções da definição ##

O código incorporado Azure Media Player é simplesmente uma tag de vídeo HTML5, por isso para muitas das opções pode utilizar os atributos de marca padrão para definir as opções.

`<video controls autoplay ...>`

Em alternativa, pode utilizar o atributo de configuração de dados para fornecer opções no formato [JSON.](http://json.org/example.html) É também assim que definiria opções que não são padrão para a etiqueta de vídeo.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Finalmente, se não estiver a utilizar o atributo de configuração de dados para ativar a configuração do jogador, pode passar num objeto com as opções do jogador como segundo argumento na função de configuração JavaScript.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> As opções no construtor são definidas apenas na primeira inicialização antes da origem ser definida.  Se desejar modificar as opções no mesmo elemento Azure Media Player inicializado, tem de atualizar as opções antes de alterar a fonte. Pode atualizar as opções no JavaScript utilizando `myPlayer.options({/*updated options*/});` . Note que apenas as opções alteradas serão afetadas, todas as outras opções previamente definidas irão persistir.

## <a name="individual-options"></a>Opções individuais ##

> [!NOTE]
>Os Atributos de Etiqueta de Vídeo só podem ser verdadeiros ou falsos (boolean), basta incluir o atributo (nenhum sinal igual) para ligá-lo ou excluí-lo para desligá-lo. Por exemplo, para ligar os controlos: DIREITO ERRADO `<video controls="true" ...>` O maior problema que as `<video controls ...>` pessoas se despem é tentar definir estes valores em falso usando falso como o valor (por exemplo, controlos="falso") que realmente faz o contrário e define o valor verdadeiro porque o atributo ainda está incluído.

### <a name="controls"></a>controlos ###

A opção de controlo define se o leitor tem ou não controlos com os quais o utilizador pode interagir. Sem controlos, a única forma de iniciar a reprodução de vídeo é com o atributo de reprodução automática ou através da API.

`<video controls ...>` ou `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Se a reprodução automática for verdadeira, o vídeo começará a ser reproduzido assim que a página estiver carregada (sem qualquer interação do utilizador).

> [!NOTE]
> Esta opção não é suportada por dispositivos móveis como Windows Phone, Apple iOS e Android. Os dispositivos móveis bloqueiam a funcionalidade de reprodução automática para evitar o uso excessivo dos planos de dados mensais dos consumidores (muitas vezes caros). É necessário um toque/clique do utilizador para iniciar o vídeo neste caso.

`<video autoplay ...>`ou `{ "autoplay": true }`

### <a name="poster"></a>cartaz ###
O atributo do cartaz define a imagem que exibe antes do vídeo começar a ser reproduzível. Esta é frequentemente uma moldura do vídeo ou um ecrã de título personalizado. Assim que o utilizador clicar em reproduzir a imagem desaparecerá.

`<video poster="myPoster.jpg" ...>` ou `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>largura ###

O atributo de largura define a largura do ecrã do vídeo.

`<video width="640" ...>` ou `{ "width": 640 }`

### <a name="height"></a>altura ###

O atributo de altura define a altura do visor do vídeo.

`<video height="480" ...>` ou `{ "height": 480 }`

### <a name="plugins"></a>plugins ###

Os plugins JSON determinam quais os plugins que são carregados com este tipo de AMP permite configurar quaisquer opções que o plugin possa ter.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Para obter mais informações sobre o desenvolvimento e utilização de [plugins, consulte os plugins de escrita](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>outras opções ###

Outras opções podem ser definidas na `<video>` etiqueta utilizando o parâmetro que leva um `data-setup` JSON.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Isto é explicitamente definido como falso. Ao configurar o falso, permitirá que a pele do Azure Media Player seja tornada a mesma através das plataformas.  Além disso, ao contrário do nome, o toque continuará a ser ativado.

### <a name="fluid"></a>fluido ###

Ao definir esta opção para um verdadeiro elemento de vídeo, a largura total do recipiente dos pais e a altura será ajustada para encaixar um vídeo com uma relação de aspeto padrão de 16:9.

`<video ... data-setup='{"fluid": true}'>`

`fluid` opção sobrepõe-se explícita `width` e `height` configurações. Esta opção só está disponível na versão Azure Media Player `2.0.0` e posteriormente.

### <a name="playbackspeed"></a>reproduçãoSpeed ###

`playbackSpeed` opção controla reprodução Controlo de velocidade e conjunto de definições de velocidade de reprodução disponíveis para o utilizador. `playbackSpeed` leva um objeto. Para permitir o controlo de velocidade de reprodução na barra de controlo, a propriedade `enabled` do objeto tem de ser definida como verdadeira. Um exemplo de permitir a velocidade de reprodução na marcação:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`

Outras propriedades da `playbackSpeed` configuração são dadas por `PlaybackSpeedOptions` objeto.

Exemplo de definição de opções de velocidade de reprodução em JavaScript:

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

Esta opção só está disponível na versão 2.0.0 do Azure Media Player e posteriormente.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

A `staleDataTimeLimitInSec` opção é uma otimização que permite configurar quantos segundos de dados velhos gostaria de manter nos amortecedores mediaSource. Opção desativada por predefinição.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

A definição ativa para ser verdadeira permite-lhe exibir legendas CEA ao vivo nas suas transmissões ao vivo e arquivos ao vivo. O atributo da etiqueta não é necessário, se não for incluído, o leitor recorrerá a uma etiqueta predefinida.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Esta opção só está disponível na versão 2.1.1 do Azure Media Player e posteriormente.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)
