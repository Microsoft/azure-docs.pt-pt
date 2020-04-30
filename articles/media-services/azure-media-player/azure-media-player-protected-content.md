---
title: Conteúdo protegido do leitor de mídia Azure
description: O Azure Media Player suporta atualmente conteúdo encriptado de envelope AES-128 e conteúdo encriptado comum.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 64414d3ec31e8763b7c576af93374bf514141fd4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726495"
---
# <a name="protected-content"></a>Conteúdo protegido #

O Azure Media Player suporta atualmente conteúdo encriptado de envelope SES-128 e conteúdo encriptado comum (através do PlayReady e Widevine) ou conteúdo encriptado através do FairPlay. Para reproduzir corretamente os conteúdos protegidos, deve dizer `protectionInfo`ao Azure Media Player que . Esta informação existe por fonte e `<source>` pode ser `data-setup`adicionada diretamente na etiqueta através do .  Também pode adicionar `protectionInfo` o diretamente como parâmetro se definir a fonte dinamicamente.

`protectionInfo`aceita um objeto JSON e inclui:

- `type`: `AES` `PlayReady` ou `Widevine` ou ou ou`FairPlay`
- `certificateUrl`: este deve ser um link direto para o seu certificado fairPlay hospedado

- `authenticationToken`: este é um campo de opção para adicionar um símbolo de autenticação não codificado

> [!IMPORTANT]
> O objeto **certificateUrl** só é necessário para FairPlay DRM.***
>[!NOTE]
> A tecnologia padrãoOrder foi alterada para `html5FairPlayHLS` acomodar a nova tecnologia, especificamente para reproduzir conteúdos FairPlay de forma nativa nos navegadores que o suportam (Safari em OSX 8+). Se tiver conteúdo fairPlay para reproduzir **e** mudou a tecnologia padrãoOrder para uma personalizada na sua aplicação, terá de adicionar esta nova tecnologia ao seu objeto techOrder. Recomendamos que o inclua antes do silverlightSS para que o seu conteúdo não reprodução através do PlayReady.

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
> Nem todos os navegadores/plataformas são capazes de reproduzir conteúdo protegido. Consulte a secção [DeReback Technology](azure-media-player-playback-technology.md) para obter mais informações sobre o que é suportado.
> [!IMPORTANT]
> O símbolo passado no leitor destina-se a conteúdo seguro e apenas utilizado para utilizadores autenticados. Presume-se que o pedido está a utilizar sSL ou qualquer outra forma de medida de segurança. Além disso, o utilizador final é assumido para ser de confiança para não usar mal o token; se não for esse o caso, por favor envolva os seus peritos em segurança.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)