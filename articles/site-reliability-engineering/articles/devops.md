---
title: 'FAQ: SRE e DevOps / Microsoft Docs'
titleSuffix: Azure
description: 'FAQ: Compreender a relação entre SRE e DevOps'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: 3d0698f2780a4ccc41eedbde70fddea1766c5f21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090334"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>Perguntas frequentes: Qual é a relação entre SRE e DevOps?

Há um conjunto de questões comuns que surgem em torno da relação entre a engenharia de fiabilidade do site e devOps, incluindo "Como são as mesmas? Em que diferem? Podemos ter os dois na nossa organização?". Este artigo tenta partilhar algumas das respostas que têm sido oferecidas pelas comunidades SRE e DevOps que nos aproximam de uma compreensão desta relação.

## <a name="how-are-they-the-same"></a>Como são os mesmos?

SRE e DevOps são ambas práticas de operações modernas que foram criadas e desenvolvidas em resposta a desafios que incluíam:

- uma complexidade crescente dos nossos ambientes produtivos e processos de desenvolvimento
- o aumento da dependência do negócio no funcionamento contínuo desses ambientes
- a incapacidade de dimensionar a força de trabalho de forma linear com o tamanho destes ambientes
- a necessidade de mover-se mais rapidamente, mantendo a estabilidade operacional

Ambas as práticas de operações valorizam a atenção a assuntos que são cruciais para lidar com estes desafios como monitorização/observabilidade, automação, documentação e ferramentas colaborativas de desenvolvimento de software.

Existe uma sobreposição considerável na ferramenta e nas áreas de trabalho entre SRE e DevOps. Como diz o Livro de _Fiabilidade do Site,_ "a SRE acredita nas mesmas coisas que os DevOps, mas por razões ligeiramente diferentes."

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>Três formas diferentes de comparar as duas práticas de operações

As semelhanças entre SRE e DevOps são claras. Onde fica realmente interessante é como os dois diferem ou divergem. Aqui oferecemos três maneiras de pensar sobre a sua relação como uma forma de trazer alguma nuance a esta questão. Pode não concordar com estas respostas, mas cada uma delas fornece um bom ponto de partida para discussão.

### <a name="class-sre-implements-interface-devops"></a>"Classe SRE implementa interface DevOps"

_O Livro de Fiabilidade do Site_ (mencionado na nossa [lista de livros de recursos)](../resources/books.md)discute SRE e DevOps no seu primeiro capítulo. Este capítulo usa a frase "classe SRE implementa interface DevOps" como legenda. Isto pretende sugerir (usando uma frase dirigida aos desenvolvedores) que a SRE poderia ser considerada uma implementação específica da filosofia DevOps. Como o capítulo salienta, "DevOps é relativamente silencioso sobre como executar operações a um nível detalhado" enquanto o SRE é consideravelmente mais proscrito nas suas práticas. Assim, uma resposta possível à questão de saber como os dois se relacionam é que a SRE poderia ser considerada uma das muitas implementações possíveis de DevOps.

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>SRE é para a fiabilidade como DevOps é para entrega

Esta comparação é um pouco enlameada porque existem múltiplas definições tanto para SRE como para DevOps, mas ainda é potencialmente útil. Começa com a pergunta "Se tivesse de destilar cada prática de operações em uma ou duas palavras que reflitam a sua preocupação principal, qual seria?"

Se usarmos esta definição de SRE do [centro de engenharia de fiabilidade](../index.yml)do local:

> A Engenharia de Fiabilidade do Site é uma disciplina de engenharia dedicada a ajudar uma organização a alcançar, de forma sustentável, o nível de fiabilidade adequado nos seus sistemas, serviços e produtos.

então seria fácil dizer que a palavra para SRE é "fiabilidade". Tê-lo bem no meio do nome também oferece excelentes provas para esta alegação.

Se usarmos esta definição de DevOps do [centro de recursos Azure DevOps:](https://docs.microsoft.com/azure/devops/learn/)

> O DevOps é a união das pessoas, do processo e dos produtos que nos permite proporcionar valor aos nossos utilizadores finais.

então uma destilação semelhante para DevOps poderia ser "entrega".

Daí que "SRE seja a fiabilidade como DevOps é para entrega".

### <a name="direction-of-attention"></a>Direção de atenção

Esta resposta é citada ou ligeiramente parafraseada de uma contribuição de Thomas Limoncelli para o livro _Seekking SRE_ mencionado na nossa [lista de livros de recursos.](../resources/books.md) Ele observa que os engenheiros da DevOps estão em grande parte focados no oleoduto de desenvolvimento de software com responsabilidades ocasionais de operações de produção, enquanto as SREs se concentram em operações de produção com responsabilidades ocasionais de gasoduto SDLC.

Mas, mais importante ainda, ele também desenha um diagrama que começa com o processo de desenvolvimento de software de um lado e as operações de produção funcionam do outro. Os dois estão ligados pelo oleoduto habitual que é construído para tirar o código de um desenvolvedor, encaminhá-lo através do número desejado de testes e estágio e, em seguida, mover esse código para a produção.

Limoncelli nota que os engenheiros da DevOps começam no ambiente de desenvolvimento e automatizam passos para a produção. Uma vez concluídos, voltam a otimizar os estrangulamentos.

As SREs, por outro lado, concentram-se nas operações de produção e chegam ao interior do oleoduto como forma de melhorar o resultado final (basicamente trabalhando na direção oposta).

É esta diferença na direção do foco SRE e DevOps que pode ajudar a diferenciá-los.

## <a name="coexistence-in-the-same-organization"></a>Coexistência na mesma organização

A última pergunta que gostaríamos de abordar é: "Pode ter SRE e DevOps na mesma organização?"

A resposta a esta pergunta é um "sim" enfático.

Esperamos que as respostas anteriores ofereçam alguma ideia de como as duas práticas de operações se sobrepõem e, quando não se sobrepõem, como podem ser complementares em foco. As organizações com uma prática de DevOps estabelecida podem experimentar práticas SRE em pequena escala (por exemplo, experimentar SLIs e SLOs) sem ter que se comprometer a criar posições ou equipas SRE. Este é um padrão de adoção SRE bastante comum.

## <a name="next-steps"></a>Passos Seguintes

Interessado em aprender mais sobre engenharia de fiabilidade do site ou DevOps? Consulte o nosso [centro de engenharia de fiabilidade do site](../index.yml) e o centro de recursos [Azure DevOps.](https://docs.microsoft.com/azure/devops/learn/)
