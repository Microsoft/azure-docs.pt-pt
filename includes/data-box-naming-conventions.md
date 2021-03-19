---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "66244651"
---
| Entidade                                       | Convenções   |
|----------------------------------------------|-----------------------------------------------------|
| Nomes de recipientes para bloco blob e bolha de página | Deve ser um nome DNS válido com 3 a 63 caracteres de comprimento. <br>  Tem de começar com uma letra ou um número. <br> Pode conter apenas letras minúsculas, números e o hífen (-). <br> Cada hífen (-) tem de ser imediatamente precedido e seguido por uma letra ou um número. <br> Hífenes consecutivos não são permitidos em nomes. |
| Partilhar nomes para ficheiros Azure                  | Mesmo que acima                                          |
| Diretório e nomes de ficheiros para ficheiros Azure     |<li> Conservação de casos, caso-insensível e não deve exceder 255 caracteres de comprimento. </li><li> Não pode terminar com o corte dianteiro (/). </li><li>Se fornecido, será automaticamente removido. </li><li> Não são permitidos caracteres seguintes: <code>" \\ / : \| < > * ?</code></li><li> Os carateres de URL reservados devem ser escritos corretamente. </li><li> Não são permitidos caracteres de caminhos de URL ilegais. Pontos de código como \\ uE000 não são caracteres Unicode válidos. Alguns caracteres ASCII ou Unicode, como caracteres de controlo (0x00 a 0x1F, \\ u0081, etc.), também não são permitidos. Para as regras que regem as cadeias Unicode em HTTP/1.1 ver RFC 2616, Secção 2.2: Regras Básicas e RFC 3987. </li><li> Não são permitidos os seguintes nomes de ficheiros: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, dot character (.) e dois caracteres dot (.).</li>|
| Nomes de blobs para blob de blocos e blob de páginas      | </li><li>Os nomes de blobs são sensíveis a maiúsculas e minúsculas e podem conter qualquer combinação de carateres. </li><li>Um nome de blob tem de ter entre 1 e 1024 carateres de comprimento. </li><li>Os carateres de URL reservados devem ser escritos corretamente. </li><li>O número de segmentos de linha que inclui o nome do blob não pode exceder 254. Um segmento de linha é a cadeia de carateres entre os carateres delimitadores consecutivos (por exemplo, uma barra "/") que corresponde ao nome de um diretório virtual.</li> |
