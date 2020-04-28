---
title: 'FAQ: SRE e DevOps Microsoft Docs'
titleSuffix: Azure
description: 'FAQ: Compreender a relação entre SRE e DevOps'
author: dnblankedelman
manager: efreeman
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 04/22/2020
ms.author: dnb
ms.openlocfilehash: e917c609b484b1a58377fea2f6cdd75dde30ca6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196416"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>Perguntas frequentes: Qual é a relação entre sre e DevOps?

Há um conjunto de questões comuns que surgem em torno da relação entre a engenharia de fiabilidade do site e os DevOps, incluindo "Como são eles mesmos? Em que diferem? Podemos ter os dois na nossa organização?". Este artigo tenta partilhar algumas das respostas que têm sido oferecidas pelas comunidades SRE e DevOps que nos aproximam de uma compreensão desta relação.

## <a name="how-are-they-the-same"></a>Como são os mesmos?

SRE e DevOps são práticas de operações modernas que foram criadas e desenvolvidas em resposta aos desafios que incluíam:

- uma complexidade crescente dos nossos ambientes de produção e processos de desenvolvimento
- aumento da dependência das empresas sobre o funcionamento contínuo desses ambientes
- a incapacidade de escalar a mão de obra linearmente com o tamanho destes ambientes
- a necessidade de mover-se mais rápido, mantendo ainda a estabilidade operacional

Ambas as práticas de operações valorizam a atenção a assuntos que são cruciais para lidar com estes desafios como monitorização/observabilidade, automação, documentação e ferramentas de desenvolvimento de software colaborativo.

Existe uma sobreposição considerável na ferramenta e nas áreas de trabalho entre a SRE e a DevOps. Como diz o Livro de Fiabilidade do _Site,_ "a SRE acredita nas mesmas coisas que os DevOps, mas por razões ligeiramente diferentes."

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>Três formas diferentes de comparar as duas práticas de operações

As semelhanças entre sre e DevOps são claras. Onde fica realmente interessante é como os dois diferem ou divergem. Aqui oferecemos três maneiras de pensar sobre a sua relação como uma forma de trazer alguma nuance a esta questão. Podem não concordar com estas respostas, mas cada uma delas proporciona um bom ponto de partida para a discussão.

### <a name="class-sre-implements-interface-devops"></a>"Classe SRE implementa interface DevOps"

_O Livro de Fiabilidade do Site_ (mencionado na nossa lista de livros de [recursos)](../resources/books.md)discute sRE e DevOps no seu primeiro capítulo. Este capítulo usa a frase "classe SRE implementa interface DevOps" como legenda. Isto pretende sugerir (usando uma frase dirigida aos desenvolvedores) que o SRE poderia ser considerado uma implementação específica da filosofia DevOps. Como o capítulo salienta, "Os DevOps são relativamente silenciosos sobre como executar operações a um nível detalhado" enquanto o SRE é consideravelmente mais proscrito nas suas práticas. Assim, uma resposta possível à questão de como os dois se relacionam é que a SRE poderia ser considerada uma das muitas possíveis implementações de DevOps.

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>SRE é para a fiabilidade como DevOps é para a entrega

Esta comparação é um pouco enlameada porque existem múltiplas definições tanto para SRE como DevOps, mas ainda é potencialmente útil. Começa com a pergunta "Se tivesse saqueado cada prática de operações em uma ou duas palavras que refletem a sua principal preocupação, o que seria?"

Se utilizarmos esta definição de SRE a partir do centro de engenharia de fiabilidade do [local:](../index.yml)

> A Engenharia de Fiabilidade do Site é uma disciplina de engenharia dedicada a ajudar uma organização a alcançar, de forma sustentável, o nível de fiabilidade adequado nos seus sistemas, serviços e produtos.

então seria fácil dizer que a palavra para SRE é "fiabilidade". Tê-lo bem no meio do nome também oferece algumas excelentes provas para esta afirmação.

Se utilizarmos esta definição de DevOps do Centro de [Recursos Azure DevOps:](https://docs.microsoft.com/azure/devops/learn/)

> O DevOps é a união das pessoas, do processo e dos produtos que nos permite proporcionar valor aos nossos utilizadores finais.

em seguida, uma destilação semelhante para DevOps poderia ser "entrega".

Daí "SRE é para a fiabilidade como DevOps é para a entrega".

### <a name="direction-of-attention"></a>Direção de atenção

Esta resposta é citada ou ligeiramente parafraseada de uma contribuição de Thomas Limoncelli para o livro _Seekking SRE_ mencionado na nossa lista de livros de [recursos.](../resources/books.md) Ele nota que os engenheiros da DevOps estão em grande parte focados no gasoduto de desenvolvimento de software com responsabilidades ocasionais de operações de produção, enquanto as SREs se concentram em operações de produção com responsabilidades ocasionais de gasoduto SDLC.

Mas, mais importante ainda, ele também desenha um diagrama que começa com o processo de desenvolvimento de software de um lado e as operações de produção funcionam do outro. Os dois estão ligados pelo oleoduto habitual que é construído para tirar o código de um desenvolvedor, pastoreá-lo através do número desejado de testes e estágios e, em seguida, mover esse código para a produção.

Limoncelli nota que os engenheiros da DevOps começam no ambiente de desenvolvimento e automatizam passos para a produção. Uma vez concluídos, voltam a otimizar estrangulamentos.

As SREs, por outro lado, concentram-se nas operações de produção e alcançam profundamente o gasoduto como forma de melhorar o resultado final (basicamente trabalhando na direção oposta).

É esta diferença na direção do foco SRE e DevOps que pode ajudar a diferenciá-los.

## <a name="coexistence-in-the-same-organization"></a>Coexistência na mesma organização

A pergunta final que gostaríamos de abordar é: "Podes ter sre e DevOps na mesma organização?"

A resposta a esta pergunta é um "sim" enfático.

Esperamos que as respostas anteriores ofereçam alguma ideia de como as duas práticas de operações se sobrepõem e, quando não se sobrepõem, como podem ser complementares em foco. Organizações com uma prática estabelecida de DevOps podem experimentar práticas SRE em pequena escala (por exemplo, experimentar SLIs e SLOs) sem ter que se comprometer a criar posições ou equipas SRE. Este é um padrão de adoção SRE bastante comum.

## <a name="next-steps"></a>Passos Seguintes

Interessado em aprender mais sobre engenharia de fiabilidade do site ou DevOps? Confira o nosso centro de engenharia de [fiabilidade](../index.yml) do site e o centro de [recursos Azure DevOps](https://docs.microsoft.com/azure/devops/learn/).
