---
title: Códigos de erro do Azure Media Player
description: Uma referência de código de erro para o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 89f063ab50cfd880552e80a77ba798e4d06de5b3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709585"
---
# <a name="error-codes"></a>Códigos de erro #

Quando a reprodução não puder ser iniciada ou interrompida, um evento de erro será disparado e a `error()` função devolverá um código e uma mensagem opcional para ajudar o desenvolvedor de aplicações a obter mais detalhes. `error().message` não é a mensagem exibida ao utilizador.  A mensagem apresentada ao utilizador `error().code` baseia-se nos bits 27-20, consulte a tabela abaixo.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Códigos de erro, bits [31-28] (4 bits) ##

Descreva a área do erro.

- 0 – Desconhecido
- 1 - AMP
- 2 - AzureHtml5JS
- 3 - FlashSS
- 4 - Luz de Prata
- 5 - Html5
- 6 - Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Códigos de erro, bits [27-0] (28 bits) ##

Descreva detalhes do erro, os bits 27-20 fornecem um nível alto, bits 19-0 fornecem mais detalhes se disponíveis.


| amp.errorCode. [Nome] | Códigos, Bits [27-0] (28 bits) | Descrição |
|---|---:|---|
| **intervalo de erros MEDIA_ERR_ABORTED (0x0100000 - 0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | Erro genérico de abortar |
| abortadoErrNotImplemented | 0x0100001 | Abortar erro, não implementado |
| abortedErrHttpMixedContentBlocked | 0x0100002 | Erro de abortar, conteúdo misto bloqueado - geralmente ocorre ao carregar um `http://` fluxo a partir de uma `https://` página |
| **MEDIA_ERR_NETWORK erros começam o valor (0x0200000 - 0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | Erro genérico da rede |
| networkErrHttpBadUrlFormat | 0x0200190 | Resposta de erro http 400 |
| networkErrHttpUserAuthRequired | 0x0200191 | Resposta de erro http 401 |
| networkErrHttpUserForbidden | 0x0200193 | Resposta de erro http 403 |
| networkErrHttpUrlNotFound | 0x0200194 | Resposta de erro http 404 |
| networkErrHttpNotAllowed | 0x0200195 | Resposta de erro http 405 |
| networkErrHttpGone | 0x020019A | Resposta de erro http 410 |
| networkErrHttpPreconditionFailed | 0x020019C | Resposta de erro http 412 |
| networkErHttpInternalServerFailure | 0x02001F4 | Resposta de erro http 500
| networkErrHttpBadGateway | 0x02001F6 | Resposta de erro http 502 |
| networkErRHttpServiceUn disponível | 0x02001F7 | Resposta de erro http 503 |
| networkErrHttpGatewayTimeout | 0x02001F8 | Resposta de erro http 504 |
| networkErrTimeout | 0x0200258 | Erro de tempo limite de rede
| networkErerr | 0x0200259 | Resposta de erro de ligação à rede |
| **MEDIA_ERR_DECODE erros (0x0300000 - 0x03FFFFF)** | | |
| descodificerUn | 0x0300000 | Erro genérico de descodificar |
| **MEDIA_ERR_SRC_NOT_SUPPORTED erros (0x0400000 - 0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | Fonte genérica não suporta erro |
| srcErrParsePresentation | 0x0400001 | Erro de análise de apresentação |
| srcErrParseSegment | 0x0400002 | Erro de análise de segmento |
| srcErrUnsupportedPresentation | 0x0400003 | Apresentação não suportada |
| srcErrInvalidSegment | 0x0400004 | Segmento inválido |
| **MEDIA_ERR_ENCRYPTED erros iniciam valor (0x0500000 - 0x05FFFFF)** | | |
| encriptarUnknown | 0x0500000 | Erro encriptado genérico | 
| encriptadorDecrypterNotFound | 0x0500001 | Decrypter não encontrado |
| encriptarDecrypterInit | 0x0500002 | Erro de inicialização de desencripter |
| encriptadorDecrypterNotSupported | 0x0500003 | Decrypter não apoiado |
| encriptarKeyAcquire | 0x0500004 | A aquisição da chave falhou |
| encriptarDesencrição | 0x0500005 | Desencriptação do segmento falhou |
| encriptaçãoErrLicenseAcquire | 0x0500006 | A aquisição de licença falhou |
| **SRC_PLAYER_MISMATCH erros iniciam valor (0x0600000 - 0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | Genérico sem jogador tecnológico correspondente para jogar a fonte |
| srcPlayerMismatchFlashNotIinSinsinado | 0x0600001 |O plugin flash não está instalado, se instalado a fonte pode ser reproduzida. *OU* O Flash 30 está instalado e reproduz o conteúdo AES.  Se for esse o caso, tente um navegador diferente. O Flash 30 não é apoiado hoje a partir de 7 de junho. Veja [as questões conhecidas](azure-media-player-known-issues.md) para mais detalhes. Nota: Se 0x00600003, não estejam instalados flash e Silverlight, se especificado no techOrder.|
| srcPlayerMismatchSilverlightNotIinined | 0x0600002 | O plugin silverlight não está instalado, se instalado a fonte pode ser reproduzida. Nota: Se 0x00600003, não estejam instalados flash e Silverlight, se especificado no techOrder. |
| | 0x00600003 | Tanto o Flash como o Silverlight não estão instalados, se especificado no techOrder. |
| **Erros desconhecidos (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | Erros desconhecidos |

## <a name="user-error-messages"></a>Mensagens de erro do utilizador ##

A mensagem do utilizador apresentada baseia-se nos bits do código de erro 27-20.

- MEDIA_ERR_ABORTED (1) - "Abortaste a reprodução de vídeo"
- MEDIA_ERR_NETWORK (2) - "Um erro de rede fez com que o download de vídeo falhasse a meio caminho."
- MEDIA_ERR_DECODE (3) - "A reprodução de vídeo foi abortada devido a um problema de corrupção ou porque o vídeo usado apresenta funcionalidades que o seu navegador não suportava."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4) - "O vídeo não pôde ser carregado, seja porque o servidor ou a rede falharam ou porque o formato não é suportado."
- MEDIA_ERR_ENCRYPTED (5) - "O vídeo está encriptado e não temos as chaves para o desencriptar."
- SRC_PLAYER_MISMATCH (6) - "Não foi encontrada nenhuma fonte compatível para este vídeo."
- MEDIA_ERR_UNKNOWN (0xFF) - "Ocorreu um erro desconhecido."

## <a name="examples"></a>Exemplos ##

### <a name="0x10600001"></a>0x10600001 ##

"Nenhuma fonte compatível foi encontrada para este vídeo." é apresentado ao utilizador final.

Não existe nenhum jogador tecnológico que possa reproduzir as fontes solicitadas, mas se o plugin Flash estiver instalado, é provável que uma fonte possa ser reproduzida.  

### <a name="0x20200194"></a>0x20200194 ###

"Um erro de rede fez com que o download de vídeo falhasse a meio caminho." é apresentado ao utilizador final.

AzureHtml5JS não conseguiu reproduzir a partir de uma resposta http 404.

### <a name="categorizing-errors"></a>Categorização de erros ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Apanhar um erro específico ###

O seguinte código captura apenas 404 erros:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)