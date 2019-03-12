---
title: Perguntas mais frequentes sobre - de imagem digitalizada
titlesuffix: Azure Cognitive Services
description: Obtenha respostas às perguntas mais frequentes sobre a API de imagem digitalizada nos serviços cognitivos do Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 0155a601a0b1e1ed0a89773540d48ff79c38be73
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726299"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>API de imagem digitalizada perguntas mais frequentes

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Se não conseguir encontrar respostas para suas perguntas neste FAQ, experimente perguntar à Comunidade de API de imagem digitalizada no [Stack Overflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou contacte [ajuda e suporte no UserVoice](https://cognitive.uservoice.com/)

-----

**Pergunta**: *Pode preparar API de imagem digitalizada para utilizar etiquetas personalizadas?  Por exemplo, eu gostaria de feed em fotografias de breeds gato "Preparar" a IA, em seguida, receber o valor de linhagem numa solicitação de IA.*

**resposta**: Esta função não está atualmente disponível. No entanto, os nossos engenheiros a trabalhar para colocar essa funcionalidade para imagem digitalizada.

-----

**Pergunta**: *Imagem digitalizada podem ser usada localmente sem uma ligação à internet?*

**resposta**: Atualmente não oferecemos no local ou solução local.

-----

**Pergunta**: *Que idiomas são suportados com imagem digitalizada?*

**resposta**: As linguagens suportadas incluem:

| | | Idiomas Suportados | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Dinamarquês (da-DK)  | Holandês (nl-NL)     | Português           | Finlandês (fi-FI)            |Francês (fr-FR)
| Alemão (Alemanha-DE)  | Grego (el-GR)     | Hungarian (hu-HU) | Italiano (it-IT)            | Japonês (ja-JP)
| Coreano (ko-KR)  | Norueguês (não-nb) | Polonês (pl-PL)    | Português (pt-BR) (pt-PT) | Russo (ru-RU)
| Espanhol (es-ES)   | Sueco (sv-SV)     | Turco (tr-TR)   |                            |

-----

**Pergunta**: *Imagem digitalizada pode ser usada para ler pratos de licença?*

**resposta**: A API de imagem digitalizada oferece deteção de texto boa com o OCR, mas não está atualmente otimizado para pratos de licença. Estamos constantemente a tentar melhorar os nossos serviços e adicionou OCR para reconhecimento de placa de automática para nossa lista de pedidos de funcionalidades.

-----

**Pergunta**: *Que idiomas são suportados para reconhecimento de manuscrito?*

**resposta**: Atualmente, é suportada apenas em inglês.

-----

**Pergunta**: *Que tipos de superfícies de escrita são suportados para reconhecimento de manuscrito?*

**resposta**: A tecnologia funciona com diferentes tipos de superfícies, incluindo quadros de comunicações, documentos técnicos e notas.

-----

**Pergunta**: *Quanto tempo demora a operação de reconhecimento de manuscrito?*

**resposta**: A quantidade de tempo que demora depende o comprimento do texto. Para textos de mais tempo, ele pode demorar vários segundos. Portanto, depois de concluída a operação de reconhecer texto manuscrito, poderá ter de aguardar antes de pode recuperar os resultados usando a operação obter Resultado da operação texto manuscrito.

-----

**Pergunta**: *Como a tecnologia de reconhecimento de manuscrito com texto que foi inserido usando um acento circunflexo no meio de uma linha?*

**resposta**: Esse texto é devolvido como uma linha separada pela operação de reconhecimento de manuscrito.

-----

**Pergunta**: *Como a tecnologia de reconhecimento de manuscrito com linhas ou Escalamento ultrapassados palavras?*

**resposta**: Se as palavras são riscamos com várias linhas para capturá-los irreconhecível, a operação de reconhecimento de manuscrito não escolhê-los. No entanto, se as palavras são riscamos usando uma única linha, essa cruzamento é tratado como ruído e as palavras ainda obterem pegou pela operação de reconhecimento de manuscrito.

-----

**Pergunta**: *As orientações de texto são suportadas para a tecnologia de reconhecimento de manuscrito?*

**resposta**: Texto orientado em ângulos de até cerca de 30 graus para 40 graus pode obter capturado pela operação de reconhecimento de manuscrito.

-----
