---
title: Perguntas frequentes - Visão Computorizada
titleSuffix: Azure Cognitive Services
description: Obtenha respostas a perguntas frequentes sobre a API de Visão Computacional em Serviços Cognitivos Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68564607"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Visão computorizada API frequentemente perguntas

> [!TIP]
> Se não encontrar respostas às suas perguntas neste FAQ, tente perguntar à comunidade API da Visão de Computador no [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou contacte [ajuda e suporte no UserVoice](https://cognitive.uservoice.com/)

---

**Pergunta**: *Posso treinar a API da Visão Computacional para utilizar etiquetas personalizadas?  Por exemplo, gostaria de alimentar-me de imagens de raças de gatos para "treinar" a IA e, em seguida, receber o valor da raça a pedido de IA.*

**Resposta**: Esta função não está atualmente disponível. No entanto, os nossos engenheiros estão a trabalhar para levar esta funcionalidade à Visão Computacional.

---

**Pergunta**: *A Visão Computacional pode ser utilizada localmente sem ligação à Internet?*

**Resposta**: Atualmente não oferecemos uma solução no local ou local.

---

**Pergunta**: *A Visão Computacional pode ser usada para ler matrículas?*

**Resposta**: A Vision API oferece uma boa deteção de texto com OCR, mas não está atualmente otimizada para matrículas. Estamos constantemente a tentar melhorar os nossos serviços e adicionámos OCR para reconhecimento de placas de automóveis à nossa lista de pedidos de funcionalidades.

---

**Pergunta**: *Que tipos de superfícies de escrita são suportadas para o reconhecimento* da caligrafia?

**Resposta**: A tecnologia funciona com diferentes tipos de superfícies, incluindo quadros brancos, papel branco e notas amarelas pegajosas.

---

**Pergunta**: Quanto tempo demora a operação de *reconhecimento de caligrafia?*

**Resposta**: O tempo que demora depende do comprimento do texto. Para textos mais longos, pode levar até vários segundos. Portanto, após a operação de texto manuscrito reconhecer, poderá ter de esperar antes de recuperar os resultados utilizando a operação Resultado da Operação De texto manuscrita Get Handwritten.

---

**Pergunta**: Como é que a tecnologia de reconhecimento de *caligrafia lida com o texto que foi inserido utilizando um cuidado no meio de uma linha?*

**Resposta**: Este texto é devolvido como uma linha separada pela operação de reconhecimento de caligrafia.

---

**Pergunta**: *Como é que a tecnologia de reconhecimento de caligrafia lida com palavras ou linhas riscadas?*

**Resposta**: Se as palavras forem riscadas com múltiplas linhas para torná-las irreconhecíveis, a operação de reconhecimento de caligrafia não as apanha. No entanto, se as palavras forem riscadas usando uma única linha, essa travessia é tratada como ruído, e as palavras ainda são captadas pela operação de reconhecimento de caligrafia.

---

**Pergunta**: Que orientações de *texto são apoiadas para a tecnologia* de reconhecimento de caligrafia?

**Resposta**: O texto orientado para ângulos de até 30 graus a 40 graus pode ser captado pela operação de reconhecimento de caligrafia.

---
