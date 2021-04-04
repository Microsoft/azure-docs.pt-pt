---
title: Tipos de URLs suportados para importação - QnA Maker
description: Entenda como os tipos de URLs são usados para importar e criar pares QnA.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 8bf50c1ea81cdf5246c47646d1a55926fe7d58d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91776702"
---
# <a name="urls-supported-for-importing-documents"></a>URLs apoiados para importação de documentos

Entenda como os tipos de URLs são usados para importar e criar pares QnA.

## <a name="faq-urls"></a>FAQ URLs

O QnA Maker pode suportar páginas web de FAQ em 3 formas diferentes:

* Páginas simples de FAQ
* Páginas de FAQ com links
* Páginas de FAQ com uma página inicial de tópicos

### <a name="plain-faq-pages"></a>Páginas simples de FAQ

Este é o tipo mais comum de página de PERGUNTAS Frequentes, em que as respostas seguem imediatamente as perguntas na mesma página.

Abaixo está um exemplo de uma página simples de FAQ:

![Exemplo simples da página de FAQ para uma base de conhecimento](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Páginas de FAQ com links

Neste tipo de página de PERGUNTAS Frequentes, as perguntas são agregadas em conjunto e estão ligadas a respostas que estão em diferentes secções da mesma página, ou em páginas diferentes.

Abaixo está um exemplo de uma página de FAQ com links em secções que estão na mesma página:

 ![Secção Link FAQ exemplo página para uma base de conhecimento](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Página de tópicos dos pais ligações para páginas de respostas de crianças

Este tipo de FAQ tem uma página de Tópicos onde cada tópico está ligado a um conjunto correspondente de perguntas e respostas numa página diferente. O QnA Maker rasteja todas as páginas ligadas para extrair as perguntas correspondentes & respostas.

Abaixo está um exemplo de uma página de Tópicos com links para secções faq em diferentes páginas.

 ![Exemplo de página de FAQ de ligação profunda para uma base de conhecimento](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>UrLs de apoio

O QnA Maker pode processar páginas web de suporte semi-estruturados, tais como artigos web que descrevem como executar uma determinada tarefa, como diagnosticar e resolver um determinado problema, e quais são as melhores práticas para um determinado processo. A extração funciona melhor em conteúdos que têm uma estrutura clara com títulos hierárquicos.

> [!NOTE]
> A extração para artigos de suporte é uma nova característica e está em fase inicial. Funciona melhor para páginas simples, que estão bem estruturadas, e não contêm cabeçalhos/rodapés complexos.

![O QnA Maker suporta a extração de páginas web semi-estruturadas onde uma estrutura clara é apresentada com títulos hierárquicos](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)