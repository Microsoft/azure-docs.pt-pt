---
title: Melhores práticas de código seguro
titleSuffix: Azure Machine Learning
description: Saiba mais sobre potenciais ameaças à segurança que possam existir quando se desenvolve para a Azure Machine Learning. Conheça as mitigações que a Azure ML proporciona e as melhores práticas para garantir que os seus ambientes de desenvolvimento se mantenham seguros.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 4bc9a982f6ce77b803a3ba91e050bcda9ec74fed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91728527"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Melhores práticas de código seguro com Azure Machine Learning

No Azure Machine Learning, pode enviar ficheiros e conteúdos de qualquer fonte para o Azure. Os conteúdos dentro de cadernos ou scripts da Jupyter que carrega podem potencialmente ler dados das suas sessões, aceder a dados dentro da sua organização em Azure ou executar processos maliciosos em seu nome.

> [!IMPORTANT]
> Apenas execute cadernos ou scripts de fontes fidedignas. Por exemplo, onde você ou a sua equipa de segurança reviram o caderno ou o script.

## <a name="potential-threats"></a>Ameaças potenciais

O desenvolvimento com a Azure Machine Learning envolve frequentemente ambientes de desenvolvimento baseados na web (Cadernos & estúdio Azure ML). Ao utilizar ambientes de desenvolvimento baseados na Web, as ameaças potenciais são:

* [Scripting cross site (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __Injeção DOM__: Este tipo de ataque pode modificar o UI exibido no navegador. Por exemplo, alterando o comportamento do botão de funcionação num Bloco de Notas Jupyter.
    * __Acesso a token/cookies__: Os ataques XSS também podem aceder a armazenamento local e cookies de navegador. O seu token de autenticação Azure Ative (AAD) está armazenado no armazenamento local. Um ataque XSS poderia usar este token para fazer chamadas de API em seu nome e, em seguida, enviar os dados para um sistema externo ou API.

* [Falsificação de pedido de cross site (CSRF)](https://owasp.org/www-community/attacks/csrf): Este ataque pode substituir o URL de uma imagem ou ligação com o URL de um script malicioso ou API. Quando a imagem é carregada, ou o link clicado, uma chamada é feita para o URL.

## <a name="azure-ml-studio-notebooks"></a>Cadernos de estúdio Azure ML

O estúdio Azure Machine Learning oferece uma experiência de caderno hospedada no seu navegador. As células de um bloco de notas podem obter documentos html ou fragmentos que contenham código malicioso.  Quando a saída é prestada, o código pode ser executado.

__Possíveis ameaças:__
* Scripting cross site (XSS)
* Falsificação de pedido de cross site (CSRF)

__Mitigações fornecidas pela Azure Machine Learning:__
* __A saída de células de__ código é escoada num iframe. O iframe impede que o script aceda ao DOM do pai, cookies ou armazenamento de sessão.
* O conteúdo __das células markdown__ é limpo usando a biblioteca dompurify. Isto bloqueia scripts maliciosos de execução com células de marcação são renderizados.
* __Os__ links URL de imagem e __Markdown__ são enviados para um ponto final da Microsoft, que verifica valores maliciosos. Se for detetado um valor malicioso, o ponto final rejeita o pedido.

__Ações recomendadas:__
* Verifique se confia no conteúdo dos ficheiros antes de ser enviado para o estúdio. Ao fazer o upload, tem de reconhecer que está a enviar ficheiros fidedignos.
* Ao selecionar um link para abrir uma aplicação externa, será solicitado a confiar na aplicação.

## <a name="azure-ml-compute-instance"></a>Exemplo de computação Azure ML

A azure machine learning compute instance acolhe __Jupyter__ e __Jupyter Lab__. Ao utilizar um dos dois, as células de um bloco de notas ou código podem obter documentos html ou fragmentos que contenham código malicioso. Quando a saída é prestada, o código pode ser executado. As mesmas ameaças também se aplicam quando se utiliza __o RStudio__ alojado numa instância computacional.

__Possíveis ameaças:__
* Scripting cross site (XSS)
* Falsificação de pedido de cross site (CSRF)

__Mitigações fornecidas pela Azure Machine Learning:__
* Nenhum. Jupyter e Jupyter Lab são aplicações de código aberto hospedadas no caso do cálculo Azure Machine Learning.

__Ações recomendadas:__
* Verifique se confia no conteúdo dos ficheiros antes de ser enviado para o estúdio. Ao fazer o upload, tem de reconhecer que está a enviar ficheiros fidedignos.

## <a name="report-security-issues-or-concerns"></a>Reportar questões ou preocupações de segurança 

O Azure Machine Learning é elegível ao abrigo do Programa Microsoft Azure Bounty. Para mais informações,  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) visite.

## <a name="next-steps"></a>Passos seguintes

* [Segurança empresarial para Azure Machine Learning](concept-enterprise-security.md)