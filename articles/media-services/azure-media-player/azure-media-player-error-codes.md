---
title: Códigos de erro do Jogador de Mídia Azure
description: Uma referência de código de erro para O Leitor de Mídia Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727258"
---
# <a name="error-codes"></a>Códigos de erro #

Quando a reprodução não puder iniciar ou tiver parado, um evento de erro será disparado e a `error()` função devolverá um código e uma mensagem opcional para ajudar o desenvolvedor da aplicação a obter mais detalhes. `error().message`não é a mensagem exibida ao utilizador.  A mensagem exibida ao utilizador `error().code` baseia-se em bits 27-20, ver tabela abaixo.

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
- 4 - SilverlightSS
- 5 - Html5
- 6 - Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Códigos de erro, bits [27-0] (28 bits) ##

Descreva detalhes do erro, os bits 27-20 fornecem um alto nível, os bits 19-0 fornecem mais detalhes se disponíveis.


| código de erro. [nome] | Códigos, Bits [27-0] (28 bits) | Descrição |
|---|---:|---|
| **MEDIA_ERR_ABORTED intervalo de erros (0x0100000 - 0x01FFFFF)** | | |
| abortadoErrUnknown | 0x01000000 | Erro genérico de abortar |
| abortadoErrNotImplemented | 0x01000001 | Abortar erro, não implementado |
| abortadoErrHttpMixedContentBlocked | 0x01000002 | Abortar erro, conteúdo misto bloqueado - `http://` geralmente `https://` ocorre ao carregar um fluxo de uma página |
| **erros MEDIA_ERR_NETWORK começam o valor (0x0200000 - 0x02FFFFF)** | | |
| redeErrUnknown | 0x0200000 | Erro genérico da rede |
| redeErrHttpBadUrlFormat | 0x0200190 | Resposta de erro http 400 |
| redeErrHttpUserAuthRequired | 0x0200191 | Resposta de erro http 401 |
| redeErrHttpUserForbidden | 0x0200193 | Resposta de erro http 403 |
| redeErrHttpUrlNotFound | 0x0200194 | Resposta de erro http 404 |
| redeErrHttpNotAllowed | 0x0200195 | Resposta de erro http 405 |
| redeErrHttpGone | 0x020019A | Resposta de erro http 410 |
| redeErrHttpPreconditionFailed | 0x020019C | Resposta de erro http 412 |
| redeErrHttpInternalServerFalha | 0x02001F4 | Resposta de erro http 500
| redeErrHttpBadGateway | 0x02001F6 | Resposta de erro http 502 |
| redeErrHttpServiceIndisponível | 0x02001F7 | Resposta de erro http 503 |
| redeErrHttpGatewayTimeout | 0x02001F8 | Resposta de erro http 504 |
| networkErrTimeout | 0x0200258 | Erro de tempo limite de rede
| redeErrerr | 0x0200259 | Resposta de erro de ligação de rede |
| **MEDIA_ERR_DECODE erros (0x03000000 - 0x03FFFFF)** | | |
| descodificarDesconhecido | 0x03000000 | Erro genérico de descodificação |
| **erros MEDIA_ERR_SRC_NOT_SUPPORTED (0x04000000 - 0x04FFFFF)** | | |
| srcErrUnknown | 0x04000000 | Fonte genérica não suportada erro |
| srcErrParsePresentation | 0x0400001 | Erro de apresentação de parse |
| srcErrParseSegment | 0x0400002 | Erro de análise de segmento |
| apresentação srcErrUnsupported | 0x0400003 | Apresentação não suportada |
| srcErrInvalidSegment | 0x0400004 | Segmento inválido |
| **MEDIA_ERR_ENCRYPTED erros começam o valor (0x05000000 - 0x05FFFFF)** | | |
| encriptarErrUnknown | 0x05000000 | Erro genérico encriptado | 
| encriptadorErDecrypterNotFound | 0x0500001 | Desencriptador não encontrado |
| encriptarErrDecrypterInit | 0x05000002 | Erro de inicialização de desencriptador |
| encriptadorErDecrypterNotSupported | 0x0500003 | Desencriptador não suportado |
| encriptadorKeyAdquirir | 0x0500004 | A aquisição da chave falhou |
| encriptarErrDecryption | 0x05000005 | Desencriptação do segmento falhou |
| encriptadorLicenseAdquirir | 0x05000006 | Licença adquirida falhou |
| **erros SRC_PLAYER_MISMATCH começam o valor (0x06000000 - 0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x06000000 | Genérico sem jogador de tecnologia correspondente para jogar a fonte |
| srcPlayerMismatchFlashNotInstalado | 0x0600001 |O plugin flash não está instalado, se instalado a fonte pode ser reproduzido. *OU* O Flash 30 está instalado e reproduzindo o conteúdo DaES.  Se for esse o caso, por favor tente um navegador diferente. Flash 30 não é apoiado hoje a partir de 7 de junho. Consulte [questões conhecidas](azure-media-player-known-issues.md) para mais detalhes. Nota: Se 0x00600003, tanto flash como Silverlight não estão instalados, se especificado na tecnologiaOrder.|
| srcPlayerMismatchSilverlightNãoInstalado | 0x0600002 | O plugin silverlight não está instalado, se instalado a fonte pode ser reproduzido. Nota: Se 0x00600003, tanto flash como Silverlight não estão instalados, se especificado na tecnologiaOrder. |
| | 0x00600003 | Tanto o Flash como o Silverlight não estão instalados, se especificados na techOrder. |
| **Erros desconhecidos (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | Erros desconhecidos |

## <a name="user-error-messages"></a>Mensagens de erro do utilizador ##

A mensagem de utilizador apresentada baseia-se nas bits 27-20 do código de erro.

- MEDIA_ERR_ABORTED (1) -"Abortou a reprodução de vídeo"
- MEDIA_ERR_NETWORK (2) - "Um erro de rede fez com que o download de vídeo falhasse a parcial.".
- MEDIA_ERR_DECODE (3) - "A reprodução de vídeo foi abortada devido a um problema de corrupção ou porque o vídeo usado funcionalidades que o seu navegador não suportava."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"O vídeo não podia ser carregado, quer porque o servidor ou a rede falharam ou porque o formato não é suportado."
- MEDIA_ERR_ENCRYPTED (5) - "O vídeo está encriptado e não temos as chaves para desencriptar."
- SRC_PLAYER_MISMATCH (6) - "Não foi encontrada nenhuma fonte compatível para este vídeo."
- MEDIA_ERR_UNKNOWN (0xFF) - "Ocorreu um erro desconhecido".

## <a name="examples"></a>Exemplos ##

### <a name="0x10600001"></a>0x10600001 ##

"Nenhuma fonte compatível foi encontrada para este vídeo." é apresentado ao utilizador final.

Não existe nenhum leitor de tecnologia que possa reproduzir as fontes solicitadas, mas se o plugin Flash for instalado, é provável que uma fonte possa ser reproduzida.  

### <a name="0x20200194"></a>0x20200194 ###

"Um erro de rede fez com que o download do vídeo falhasse a parcial-way." é apresentado ao utilizador final.

O AzureHtml5JS não reprodução a partir de uma resposta http 404.

### <a name="categorizing-errors"></a>Erros de categorização ###

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

O código seguinte captura apenas 404 erros:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)