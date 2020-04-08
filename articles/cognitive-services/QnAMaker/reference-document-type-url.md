---
title: Tipos urls suportados para importação - QnA Maker
description: Compreenda como os tipos de URLs são usados para importar e criar pares QnA.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: c6c6c7ba19fe7be54733f45997134fddb56a6ba0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804321"
---
# <a name="urls-supported-for-importing-documents"></a>URLs suportados para importar documentos

Compreenda como os tipos de URLs são usados para importar e criar pares QnA.

## <a name="faq-urls"></a>FAQ URLs

O QnA Maker pode suportar páginas web de FAQ em 3 formas diferentes:

* Páginas simples de FAQ
* Páginas de FAQ com links
* Páginas de FAQ com uma página inicial de tópicos

### <a name="plain-faq-pages"></a>Páginas simples de FAQ

Este é o tipo mais comum de página de FAQ, em que as respostas seguem imediatamente as perguntas na mesma página.

Abaixo está um exemplo de uma página simples de FAQ:

![Exemplo de página de FAQ simples para uma base de conhecimento](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Páginas de FAQ com links

Neste tipo de página de PERGUNTAS, as perguntas são agregadas em conjunto e estão ligadas a respostas que estão em diferentes secções da mesma página, ou em páginas diferentes.

Abaixo está um exemplo de uma página de FAQ com links em secções que estão na mesma página:

 ![Exemplo da página faQ do Link da Secção para uma base de conhecimento](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Página de Tópicos dos Pais liga para páginas de respostas de crianças

Este tipo de FAQ tem uma página de Tópicos onde cada tópico está ligado a um conjunto correspondente de perguntas e respostas numa página diferente. O QnA Maker rasteja todas as páginas ligadas para extrair as perguntas correspondentes & respostas.

Abaixo está um exemplo de uma página de Tópicos com links para secções de FAQ em páginas diferentes.

 ![Exemplo de página de FAQ de ligação profunda para uma base de conhecimento](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>URLs de suporte

O QnA Maker pode processar páginas web de suporte semi-estruturadas, tais como artigos web que descrevem como executar uma determinada tarefa, como diagnosticar e resolver um determinado problema, e quais são as melhores práticas para um determinado processo. A extração funciona melhor em conteúdo que tem uma estrutura clara com rubricas hierárquicas.

> [!NOTE]
> A extração de artigos de apoio é uma nova funcionalidade e está em fase inicial. Funciona melhor para páginas simples, bem estruturadas, e não contêm cabeçalhos/rodapés complexos.

![QnA Maker suporta a extração de páginas web semi-estruturadas onde uma estrutura clara é apresentada com títulos hierárquicos](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)