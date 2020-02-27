---
title: Tipos urls suportados para importação - QnA Maker
description: Compreenda como os tipos de URLs são usados para importar e criar conjuntos qnA.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: cf2a02f55d7c95ff9b3f0231ea07a783d18b73cf
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651830"
---
# <a name="urls-supported-for-importing-documents"></a>URLs suportados para importar documentos

Compreenda como os tipos de URLs são usados para importar e criar conjuntos qnA.

## <a name="faq-urls"></a>URLs de FAQ

O QnA Maker pode suportar páginas web de FAQ em 3 formas diferentes:

* Páginas de FAQ simples
* Páginas de FAQ com links
* Páginas de FAQ com uma página inicial de tópicos

### <a name="plain-faq-pages"></a>Páginas de FAQ simples

Este é o tipo mais comum da página de FAQ, em que as respostas siga imediatamente as perguntas na mesma página.

Segue-se um exemplo de uma página de FAQ simples:

![Exemplo de página de FAQ simples para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Páginas de FAQ com links

Este tipo de página de perguntas frequentes, as perguntas são agregadas em conjunto e são associadas a respostas que estão em diferentes seções da mesma página ou em diferentes páginas.

Segue-se um exemplo de uma página de FAQ com ligações nas secções que se encontram na mesma página:

 ![Exemplo de página de FAQ de ligação de seção para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Página de Tópicos dos Pais liga para páginas de respostas de crianças

Este tipo de FAQ tem uma página de Tópicos onde cada tópico está ligado a um conjunto correspondente de perguntas e respostas numa página diferente. O Fabricante QnA rasteja todas as páginas ligadas para extrair as perguntas e respostas correspondentes.

Abaixo está um exemplo de uma página de Tópicos com links para secções de FAQ em páginas diferentes.

 ![Exemplo de página de perguntas frequentes sobre a ligação avançada para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>URLs de suporte

O QnA Maker pode processar páginas web de suporte semi-estruturadas, tais como artigos web que descrevem como executar uma determinada tarefa, como diagnosticar e resolver um determinado problema, e quais são as melhores práticas para um determinado processo. A extração funciona melhor em conteúdo que tem uma estrutura clara com rubricas hierárquicas.

> [!NOTE]
> A extração de artigos de apoio é uma nova funcionalidade e está em fase inicial. Funciona melhor para páginas simples, bem estruturadas, e não contêm cabeçalhos/rodapés complexos.

![QnA Maker suporta a extração de páginas web semi-estruturadas onde uma estrutura clara é apresentada com títulos hierárquicos](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)