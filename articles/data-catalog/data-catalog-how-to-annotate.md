---
title: Como anotar fontes de dados no Catálogo de Dados do Azure
description: Como-a-artigo destacando como anotar ativos de dados no Catálogo de Dados Azure, incluindo nomes amigáveis, tags, descrições e especialistas.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 89fa1b542c54fc962f8c12a2142884c677afb259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674653"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Como anotar fontes de dados no Catálogo de Dados do Azure

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introdução

**O Microsoft Azure Data Catalog** é um serviço de cloud totalmente gerido que serve como um sistema de registo e sistema de descoberta para fontes de dados empresariais. Por outras palavras, o Data Catalog tem tudo a ver com ajudar as pessoas a descobrir, compreender e usar fontes de dados, e ajudar as organizações a obter mais valor a partir dos seus dados existentes. Quando uma fonte de dados é registada no Catálogo de Dados, os seus metadados são copiados e indexados pelo serviço, mas a história não termina aí. O Data Catalog permite que os utilizadores forneçam os seus próprios metadados descritivos – como descrições e tags – para complementar os metadados extraídos da fonte de dados e para tornar a fonte de dados mais compreensível para mais pessoas.

## <a name="annotation-and-crowdsourcing"></a>Anotação e crowdsourcing
Todos têm uma opinião. E isto é uma coisa boa.
O Data Catalog reconhece que diferentes utilizadores têm diferentes perspetivas sobre as fontes de dados da empresa, e que cada uma destas perspetivas pode ser valiosa. Considere o seguinte cenário:

* O administrador do sistema conhece o contrato de nível de serviço para os servidores ou serviços que acolhem a fonte de dados.
* O administrador da base de dados conhece o horário de backup de cada base de dados, e as janelas de processamento etl permitidas.
* O proprietário do sistema conhece o processo para que os utilizadores solicitem acesso à fonte de dados.
* O administrador de dados sabe como os ativos e atributos no mapa de origem de dados ao modelo de dados da empresa.
* O analista sabe como os dados são usados no contexto dos processos de negócio que suportam.

Cada uma destas perspetivas é valiosa, e o Data Catalog utiliza uma abordagem de crowdsourcing para metadados que permite que cada um seja capturado e usado para fornecer uma imagem completa de fontes de dados registadas. Utilizando o portal Data Catalog, cada utilizador pode adicionar e editar as suas próprias anotações, ao mesmo tempo que pode visualizar anotações fornecidas por outros utilizadores.

## <a name="different-types-of-annotations"></a>Diferentes tipos de anotações
O Catálogo de Dados suporta os seguintes tipos de anotações:

| Anotação | Notas |
| --- | --- |
| Nome amigável |Nomes amigáveis podem ser fornecidos ao nível do ativo de dados, para tornar os ativos de dados mais facilmente compreendidos. Os nomes amigáveis são mais úteis quando o nome do objeto subjacente é enigmático, abreviado ou não significativo para os utilizadores. |
| Description |As descrições podem ser fornecidas nos níveis de atributos/colunas. As descrições são anotações de texto curto de forma livre que descrevem a perspetiva do utilizador sobre o ativo dos dados ou a sua utilização. |
| Tags (etiquetas de utilizador) |As etiquetas podem ser fornecidas nos níveis de atributos/colunas. As etiquetas de utilizador são etiquetas definidas pelo utilizador que podem ser usadas para categorizar ativos ou atributos de dados. |
| Etiquetas (etiquetas glossárias) |As etiquetas podem ser fornecidas nos níveis de atributos/colunas. As etiquetas glossárias são termos glossários definidos centralmente que podem ser usados para categorizar ativos de dados ou atributos usando uma taxonomia de negócio comum. Para mais informações  [consulte Como Configurar o Glossário de Negócios para Marcação Governada](data-catalog-how-to-business-glossary.md) |
| Especialistas |Os especialistas podem ser fornecidos ao nível do ativo de dados. Os especialistas identificam utilizadores ou grupos com perspetivas especializadas sobre os dados e podem servir como pontos de contacto para os utilizadores que descobrem as fontes de dados registadas e têm dúvidas que não são respondidas pelas anotações existentes. |
| Pedir acesso |As informações de acesso ao pedido podem ser fornecidas ao nível do ativo dos dados. Esta informação é para os utilizadores que descobrem uma fonte de dados que ainda não têm permissões de acesso. Os utilizadores podem introduzir o endereço de e-mail do utilizador ou grupo que concede acesso, o URL do processo ou ferramenta a que os utilizadores precisam de ter acesso, ou podem introduzir o processo em si como texto. |
| Documentação |A documentação pode ser fornecida ao nível do ativo dos dados. A documentação do ativo é uma rica informação de texto que pode incluir links e imagens, e que pode fornecer qualquer informação não transmitida através de descrições e tags. |

## <a name="annotating-multiple-assets"></a>Anotar múltiplos ativos
Ao selecionar vários ativos de dados no portal data Catalog, os utilizadores podem anotar todos os ativos selecionados numa única operação. As anotações aplicar-se-ão a todos os ativos selecionados, facilitando a seleção e fornecendo uma descrição consistente e conjuntos de tags e especialistas para ativos de dados relacionados.

> [!NOTE]
> As etiquetas e especialistas também podem ser fornecidos ao registar ativos de dados utilizando a ferramenta de registo de fontes de dados do Catálogo de Dados.
>
>

Ao selecionar várias tabelas e vistas, apenas as colunas que todos os ativos de dados selecionados têm em comum serão exibidas no portal do Catálogo de Dados. Isto permite que os utilizadores forneçam tags e descrições para todas as colunas com o mesmo nome para todos os ativos selecionados.

## <a name="annotations-and-discovery"></a>Anotações e descobertas
Assim como os metadados extraídos da fonte de dados durante o registo são adicionados ao índice de pesquisa do Catálogo de Dados, os metadados fornecidos pelo utilizador também estão indexados. Isto significa que não só as anotações facilitam a compreensão dos dados que descobrem, como as anotações também facilitam a descoberta dos ativos de dados anotados, pesquisando os termos que lhes fazem sentido.

## <a name="summary"></a>Resumo
Registar uma fonte de dados com o Data Catalog torna esses dados detetáveis copiando metadados estruturais e descritivos da fonte de dados para o serviço Catálogo. Uma vez registada uma fonte de dados, os utilizadores podem fornecer anotações para facilitar a descoberta e compreensão dentro do portal do Catálogo de Dados.

## <a name="see-also"></a>Ver também
* Começar com o tutorial do Catálogo de [Dados Azure](data-catalog-get-started.md) para detalhes passo a passo sobre como anotar fontes de dados.
