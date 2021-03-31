---
title: Perguntas frequentes - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para perguntas frequentes sobre a API de Visão De Computador em Serviços Cognitivos Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "68564607"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>API de visão de computador frequentemente fez perguntas

> [!TIP]
> Se não conseguir encontrar respostas para as suas perguntas neste FAQ, tente perguntar à comunidade API da Visão de Computador no [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou contacte [ajuda e suporte no UserVoice](https://cognitive.uservoice.com/)

---

**Pergunta:** *Posso treinar a API da Visão De Computador para utilizar etiquetas personalizadas?  Por exemplo, gostaria de me alimentar em imagens de raças de gatos para "treinar" a IA e, em seguida, receber o valor da raça num pedido de IA.*

**Resposta**: Esta função não está disponível. No entanto, os nossos engenheiros estão a trabalhar para trazer esta funcionalidade para a Visão Computacional.

---

**Pergunta**: *A Visão Computacional pode ser utilizada localmente sem ligação à Internet?*

**Resposta**: Atualmente, não oferecemos uma solução local ou local.

---

**Pergunta**: *A Visão Computacional pode ser utilizada para ler matrículas?*

**Resposta**: A API vision oferece uma boa deteção de texto com OCR, mas não está atualmente otimizada para matrículas. Estamos constantemente a tentar melhorar os nossos serviços e adicionámos OCR para reconhecimento de matrículas automáticas à nossa lista de pedidos de funcionalidades.

---

**Pergunta**: *Que tipos de superfícies de escrita são suportadas para reconhecimento de caligrafia?*

**Resposta**: A tecnologia funciona com diferentes tipos de superfícies, incluindo quadros brancos, papel branco e notas adesivas amarelas.

---

**Pergunta:** *Quanto tempo demora a operação de reconhecimento da caligrafia?*

**Resposta**: O tempo que demora depende do comprimento do texto. Para textos mais longos, pode demorar até alguns segundos. Por isso, depois de concluída a operação "Reconhecer texto manuscrito", poderá ter de esperar antes de recuperar os resultados utilizando a operação "Obter resultados de operação de texto manuscrito".

---

**Pergunta**: *Como é que a tecnologia de reconhecimento de caligrafia lida com texto que foi inserido com um cuidado no meio de uma linha?*

**Resposta**: Este texto é devolvido como uma linha separada pela operação de reconhecimento da caligrafia.

---

**Pergunta**: *Como é que a tecnologia de reconhecimento de caligrafia lida com palavras ou linhas riscadas?*

**Resposta**: Se as palavras forem riscadas com várias linhas para as tornar irreconhecíveis, a operação de reconhecimento da caligrafia não as apanha. No entanto, se as palavras forem riscadas usando uma única linha, essa travessia é tratada como ruído, e as palavras ainda são captadas pela operação de reconhecimento da caligrafia.

---

**Pergunta**: *Que orientações de texto são apoiadas para a tecnologia de reconhecimento da caligrafia?*

**Resposta**: O texto orientado para ângulos de até 30 graus a 40 graus pode ser captado pela operação de reconhecimento da caligrafia.

---
