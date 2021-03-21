---
title: Azure Media Player Conteúdo Protegido
description: A Azure Media Player suporta atualmente conteúdo encriptado de envelope AES-128 e conteúdo encriptado comum.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 83f144c06c23f3ab5507e3561be4a12350e20a42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91329691"
---
# <a name="protected-content"></a>Conteúdo protegido #

A Azure Media Player suporta atualmente conteúdo encriptado de envelope AES-128 e conteúdo encriptado comum (através do PlayReady e Widevine) ou conteúdo encriptado via FairPlay. Para reproduzir corretamente os conteúdos protegidos, tem de dizer ao Azure Media Player o `protectionInfo` . Esta informação existe por fonte e pode ser adicionada diretamente na `<source>` etiqueta através do `data-setup` .  Também pode adicionar o `protectionInfo` diretamente como parâmetro se definir a fonte de forma dinâmica.

`protectionInfo` aceita um objeto JSON e inclui:

- `type`: `AES` ou `PlayReady` ou `Widevine` ou `FairPlay`
- `certificateUrl`: este deve ser um link direto para o seu certificado FairPlay hospedado

- `authenticationToken`: este é um campo de opção para adicionar um token de autenticação não codificado

> [!IMPORTANT]
> O **objeto certificadoUrl** só é necessário para FairPlay DRM.***
>[!NOTE]
> O techOrder padrão foi alterado para acomodar a nova tecnologia, `html5FairPlayHLS` especificamente para reproduzir os conteúdos FairPlay de forma nativa nos navegadores que o suportam (Safari no OSX 8+). Se tiver conteúdo FairPlay para reproduzir **E** alterou o techOrder padrão para um personalizado na sua aplicação, terá de adicionar esta nova tecnologia ao seu objeto techOrder. Recomendamos que o inclua antes da luz de prataSSS para que o seu conteúdo não reproduza através do PlayReady.

## <a name="code-sample"></a>Exemplo de código ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

ou, com vários DRM

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> Nem todos os navegadores/plataformas são capazes de reproduzir conteúdo protegido. Consulte a secção ['Tecnologia de Reprodução'](azure-media-player-playback-technology.md) para obter mais informações sobre o que é suportado.
> [!IMPORTANT]
> O token passado para o leitor destina-se a conteúdos seguros e apenas utilizado para utilizadores autenticados. Presume-se que o pedido está a utilizar sSL ou qualquer outra forma de medida de segurança. Além disso, o utilizador final é assummed para ser confiável para não usar indevidamente o token; Se não for esse o caso, por favor envolva os seus peritos em segurança.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)