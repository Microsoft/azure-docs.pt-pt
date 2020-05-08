---
title: Plugins de escrita para Azure Media Player
description: Saiba escrever um plugin com Azure Media Player com JavaScript
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: 7902dfdf81d8e44921a5218d56effc90f433f02d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857401"
---
# <a name="writing-plugins-for-azure-media-player"></a>Plugins de escrita para Azure Media Player #

Um plugin é JavaScript escrito para estender ou melhorar o leitor. Pode escrever plugins que alteram a aparência do Azure Media Player, a sua funcionalidade ou até mesmo ter interface com outros serviços. Pode fazê-lo em dois passos fáceis:

## <a name="step-1"></a>Passo 1 ##

Escreva o seu JavaScript numa função como esta:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

Pode escrever o seu código diretamente `<script>` na sua página HTML dentro de tags ou num ficheiro JavaScript externo. Se fizer este último, certifique-se de incluir `<head>` o ficheiro JavaScript na página HTML *após* o script AMP.

Exemplo:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>Passo 2 ##
Inicialize o plugin com JavaScript de uma de duas formas:

Método 1:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

Método 2:

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

As opções de plugin não são necessárias, incluindo as que apenas permitem que os desenvolvedores que usam o plugin configurem o seu comportamento sem terem de alterar o código fonte.

Para inspiração e mais exemplos sobre a criação de um plugin, dê uma olhada na nossa [galeria](azure-media-player-plugin-gallery.md)

>[!NOTE]
> O código plugin altera dinamicamente itens no DOM durante a vida útil da experiência do jogador do espectador, nunca faz alterações permanentes no código fonte do jogador. É aqui que uma compreensão das ferramentas de desenvolvimento do seu navegador vem a calhar. Por exemplo, se quiser alterar a aparência de um elemento no leitor, pode encontrar o seu elemento HTML pelo seu nome de classe e, em seguida, adicionar ou alterar atributos a partir daí. Aqui está um grande recurso para mudar os [atributos HTML.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Plugins Integrados ###

 Existem atualmente dois plugins cozidos em AMP: a [ponta do tempo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) e as [teclas quentes](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Estes plugins foram originalmente desenvolvidos para serem plugins modulares para o leitor, mas estão agora incluídos no código fonte do jogador.

### <a name="plugin-gallery"></a>Galeria Plugin ###

A [galeria plugin](https://aka.ms/ampplugins) tem vários plugins que a comunidade já contribuiu para funcionalidades como marcadores de linha temporal, zoom, análise e muito mais. A página fornece acessos aos plugins e instruções sobre como configurar, bem como uma demonstração que mostra o plugin em ação. Se criar um plugin legal que acha que deve ser incluído na nossa galeria, sinta-se à vontade para o submeter para que possamos verificar.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)
