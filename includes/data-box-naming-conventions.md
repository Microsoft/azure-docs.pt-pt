---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244651"
---
| Entidade                                       | Convenções   |
|----------------------------------------------|-----------------------------------------------------|
| Os nomes dos contentores de blob de blocos e BLOBs de páginas | Tem de ser um nome DNS válido é entre 3 e 63 carateres de comprimento. <br>  Tem de começar com uma letra ou um número. <br> Pode conter apenas letras minúsculas, números e hífen (-). <br> Cada hífen (-) tem de ser imediatamente precedido e seguido por uma letra ou um número. <br> Hífenes consecutivos não são permitidos em nomes. |
| Os nomes das partilhas de ficheiros do Azure                  | Mesmo que acima                                          |
| Nomes de diretórios e arquivos para os ficheiros do Azure     |<li> Preservação de caso, maiúsculas e minúsculas e não pode exceder os 255 carateres de comprimento. </li><li> Não pode terminar com a barra (/). </li><li>Se for fornecido, será automaticamente removido. </li><li> Não são permitidos os seguintes carateres: <code>" \\ / : \| < > * ?</code></li><li> Os carateres de URL reservados devem ser escritos corretamente. </li><li> Não são permitidos carateres ilegais de caminho de URL. Código pontos como \\uE000 não são carateres válidos do Unicode. Alguns caracteres ASCII ou Unicode, como carateres de controlo (0x00 para 0x1F \\u0081, etc.), não também são permitidas. Para regras para Unicode cadeias de caracteres no HTTP/1.1, consulte RFC 2616, secção 2.2: Regras básicas e RFC 3987. </li><li> Não são permitidos os seguintes nomes de ficheiros: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, caráter de ponto (.) e dois pontos carateres (.).</li>|
| Nomes de blobs para blob de blocos e blob de páginas      | </li><li>Os nomes de blobs são sensíveis a maiúsculas e minúsculas e podem conter qualquer combinação de carateres. </li><li>Um nome de blob tem de ter entre 1 e 1024 carateres de comprimento. </li><li>Os carateres de URL reservados devem ser escritos corretamente. </li><li>O número de segmentos de linha que inclui o nome do blob não pode exceder 254. Um segmento de linha é a cadeia de carateres entre os carateres delimitadores consecutivos (por exemplo, uma barra "/") que corresponde ao nome de um diretório virtual.</li> |
