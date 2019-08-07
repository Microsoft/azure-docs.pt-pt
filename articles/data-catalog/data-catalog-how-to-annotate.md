---
title: Como anotar fontes de dados no catálogo de dados do Azure
description: Artigo de instruções destacando como anotar ativos de dados no catálogo de dados do Azure, incluindo nomes amigáveis, marcas, descrições e especialistas.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 708c62971a20a7071accf7591a4e2914f7dbd9f3
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736361"
---
# <a name="how-to-annotate-data-sources"></a>Como anotar origens de dados
## <a name="introduction"></a>Introdução
**Microsoft Azure catálogo de dados** é um serviço de nuvem totalmente gerenciado que serve como um sistema de registro e sistema de descoberta para fontes de dados empresariais. Em outras palavras, o catálogo de dados é responsável por ajudar as pessoas a descobrir, compreender e usar fontes de dados e ajudar as organizações a obter mais valor de seus dados existentes. Quando uma fonte de dados é registrada no catálogo de dados, seus metadados são copiados e indexados pelo serviço, mas a história não termina lá. O catálogo de dados permite que os usuários forneçam seus próprios metadados descritivos – como descrições e marcas – para complementar os metadados extraídos da fonte de dados e tornar a fonte de dados mais compreensível para mais pessoas.

## <a name="annotation-and-crowdsourcing"></a>Anotação e crowdsourcing
Todos têm uma opinião. E isso é algo bom.
O catálogo de dados reconhece que diferentes usuários têm diferentes perspectivas em fontes de dados empresariais e que cada uma dessas perspectivas pode ser valiosa. Considere o seguinte cenário:

* O administrador do sistema conhece o contrato de nível de serviço para os servidores ou serviços que hospedam a fonte de dados.
* O administrador de banco de dados conhece o agendamento de backup para cada banco de dados e as janelas de processamento de ETL permitidas.
* O proprietário do sistema conhece o processo para que os usuários solicitem acesso à fonte de dados.
* O administrador de dados sabe como os ativos e atributos na fonte de dados são mapeados para o modelo de dados corporativo.
* O analista sabe como os dados são usados no contexto dos processos de negócios aos quais eles dão suporte.

Cada uma dessas perspectivas é valiosa e o catálogo de dados usa uma abordagem crowdsourcing para metadados que permite que cada um seja capturado e usado para fornecer uma imagem completa das fontes de dados registradas. Usando o portal do catálogo de dados, cada usuário pode adicionar e editar suas próprias anotações, enquanto pode exibir anotações fornecidas por outros usuários.

## <a name="different-types-of-annotations"></a>Diferentes tipos de anotações
O catálogo de dados dá suporte aos seguintes tipos de anotações:

| Anotação | Notas |
| --- | --- |
| Nome amigável |Nomes amigáveis podem ser fornecidos no nível de ativo de dados, para tornar os ativos de dados mais facilmente compreendidos. Nomes amigáveis são mais úteis quando o nome do objeto subjacente é confuso, abreviado ou não é significativo para os usuários. |
| Descrição |As descrições podem ser fornecidas nos níveis de ativo de dados e atributo/coluna. As descrições são anotações de texto curto de forma livre que descrevem a perspectiva do usuário sobre o ativo de dados ou seu uso. |
| Marcas (marcas de usuário) |As marcas podem ser fornecidas nos níveis de ativo de dados e atributo/coluna. Marcas de usuário são rótulos definidos pelo usuário que podem ser usados para categorizar os ativos de dados ou atributos. |
| Marcas (marcas de Glossário) |As marcas podem ser fornecidas nos níveis de ativo de dados e atributo/coluna. As marcas de glossário são termos de Glossário definidos centralmente que podem ser usados para categorizar os ativos de dados ou atributos usando uma taxonomia de negócios comum. Para mais informações, veja [Como configurar o Glossário Comercial para Etiquetagem Regida](data-catalog-how-to-business-glossary.md) |
| Em |Os especialistas podem ser fornecidos no nível de ativo de dados. Os especialistas identificam usuários ou grupos com perspectivas de especialistas sobre os dados e podem servir como pontos de contato para usuários que descobrem as fontes de dados registradas e têm perguntas que não são respondidas pelas anotações existentes. |
| Pedir acesso |As informações de solicitação de acesso podem ser fornecidas no nível de ativo de dados. Essas informações são para os usuários que descobrem uma fonte de dados que ainda não têm permissões para acessar. Os usuários podem inserir o endereço de email do usuário ou grupo que concede acesso, a URL do processo ou da ferramenta que os usuários precisam para obter acesso ou podem inserir o próprio processo como texto. |
| Documentação |A documentação pode ser fornecida no nível de ativo de dados. A documentação do ativo é uma informação de Rich Text que pode incluir links e imagens, e que pode fornecer todas as informações não transmitidas por meio de descrições e marcas. |

## <a name="annotating-multiple-assets"></a>Anotando vários ativos
Ao selecionar vários ativos de dados no portal do catálogo de dados, os usuários podem anotar todos os ativos selecionados em uma única operação. As anotações serão aplicadas a todos os ativos selecionados, facilitando a seleção e a fornecer uma descrição consistente e conjuntos de marcas e especialistas para ativos de dados relacionados.

> [!NOTE]
> Marcas e especialistas também podem ser fornecidos ao registrar ativos de dados usando a ferramenta de registro de fonte de dados do catálogo de dados.
>
>

Ao selecionar várias tabelas e exibições, somente as colunas que todos os ativos de dados selecionados têm em comum serão exibidas no portal do catálogo de dados. Isso permite que os usuários forneçam marcas e descrições para todas as colunas com o mesmo nome para todos os ativos selecionados.

## <a name="annotations-and-discovery"></a>Anotações e descoberta
Assim como os metadados extraídos da fonte de dados durante o registro são adicionados ao índice de pesquisa do catálogo de dados, os metadados fornecidos pelo usuário também são indexados. Isso significa que não apenas as anotações tornam mais fácil para os usuários entender os dados que eles descobrem, as anotações também facilitam a descoberta dos ativos de dados anotados por meio da pesquisa usando os termos que fazem sentido para eles.

## <a name="summary"></a>Resumo
O registro de uma fonte de dados com o catálogo de dados torna esses dados detectáveis por meio da cópia de metadados estruturais e descritivos da fonte de dados para o serviço de catálogo. Depois que uma fonte de dados é registrada, os usuários podem fornecer anotações para facilitar a descoberta e a compreensão de dentro do portal do catálogo de dados.

## <a name="see-also"></a>Consulte também
* [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) para obter detalhes passo a passo sobre como anotar fontes de dados.
