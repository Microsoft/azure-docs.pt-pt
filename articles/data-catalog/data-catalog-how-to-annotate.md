---
title: Como anotar fontes de dados no Catálogo de Dados do Azure
description: Como fazer o artigo destacando como anotar ativos de dados no Catálogo de Dados Do Azure, incluindo nomes amigáveis, tags, descrições e especialistas.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: c1e022591ce1aee073330055744fbd78d97c0b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950250"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Como anotar fontes de dados no Catálogo de Dados do Azure

## <a name="introduction"></a>Introdução

O **Microsoft Azure Data Catalog** é um serviço de cloud totalmente gerido que serve como um sistema de registo e sistema de descoberta para fontes de dados da empresa. Por outras palavras, o Data Catalog tem tudo a ver com ajudar as pessoas a descobrir, compreender e usar fontes de dados, e ajudar as organizações a obter mais valor a partir dos seus dados existentes. Quando uma fonte de dados é registada no Data Catalog, os seus metadados são copiados e indexados pelo serviço, mas a história não termina aí. O Data Catalog permite que os utilizadores forneçam os seus próprios metadados descritivos – como descrições e tags – para complementar os metadados extraídos da fonte de dados e tornar a fonte de dados mais compreensível para mais pessoas.

## <a name="annotation-and-crowdsourcing"></a>Anotação e crowdsourcing
Todos têm uma opinião. E isto é uma coisa boa.
O Data Catalog reconhece que diferentes utilizadores têm diferentes perspetivas sobre as fontes de dados da empresa, e que cada uma destas perspetivas pode ser valiosa. Considere o seguinte cenário:

* O administrador do sistema conhece o acordo de nível de serviço para os servidores ou serviços que acolhem a fonte de dados.
* O administrador da base de dados conhece o calendário de cópias de segurança de cada base de dados e as janelas de processamento etl permitidas.
* O proprietário do sistema conhece o processo para que os utilizadores solicitem o acesso à fonte de dados.
* O responsável pelos dados sabe como os ativos e atributos no mapa de origem de dados ao modelo de dados da empresa.
* O analista sabe como os dados são usados no contexto dos processos de negócio que apoiam.

Cada uma destas perspetivas é valiosa, e o Data Catalog usa uma abordagem de crowdsourcing para metadados que permite que cada um seja capturado e usado para fornecer uma imagem completa de fontes de dados registadas. Utilizando o portal Data Catalog, cada utilizador pode adicionar e editar as suas próprias anotações, ao mesmo tempo que consegue ver anotações fornecidas por outros utilizadores.

## <a name="different-types-of-annotations"></a>Diferentes tipos de anotações
O Data Catalog suporta os seguintes tipos de anotações:

| Anotação | Notas |
| --- | --- |
| Nome amigável |Nomes amigáveis podem ser fornecidos ao nível do ativo de dados, para tornar os ativos de dados mais facilmente compreendidos. Os nomes amigáveis são mais úteis quando o nome do objeto subjacente é enigmático, abreviado ou não significativo para os utilizadores. |
| Descrição |As descrições podem ser fornecidas ao ativo de dados e aos níveis de atributo/coluna. As descrições são anotações de texto curto sem forma que descrevem a perspetiva do utilizador sobre o ativo de dados ou a sua utilização. |
| Etiquetas (etiquetas de utilizador) |As etiquetas podem ser fornecidas ao ativo de dados e atributos/níveis de coluna. As etiquetas de utilizador são etiquetas definidas pelo utilizador que podem ser usadas para categorizar ativos ou atributos de dados. |
| Tags (etiquetas glossárias) |As etiquetas podem ser fornecidas ao ativo de dados e atributos/níveis de coluna. As etiquetas glossárias são termos glossários definidos centralmente que podem ser usados para categorizar ativos de dados ou atributos usando uma taxonomia comercial comum. Para mais informações consulte [Como configurar o Glossário de Negócios para Marcação Governada](data-catalog-how-to-business-glossary.md) |
| Especialistas |Os especialistas podem ser fornecidos ao nível dos ativos de dados. Os especialistas identificam utilizadores ou grupos com perspetivas de especialistas sobre os dados e podem servir como pontos de contacto para os utilizadores que descobrem as fontes de dados registadas e têm questões que não são respondidas pelas anotações existentes. |
| Pedir acesso |A informação de acesso ao pedido pode ser fornecida ao nível do ativo de dados. Esta informação destina-se a utilizadores que descubram uma fonte de dados que ainda não têm permissão de acesso. Os utilizadores podem introduzir o endereço de e-mail do utilizador ou grupo que concede acesso, o URL do processo ou ferramenta que os utilizadores precisam para ter acesso, ou podem entrar no processo por si mesmocomo texto. |
| Documentação |A documentação pode ser fornecida ao nível do ativo de dados. A documentação do ativo é rica em informações de texto que podem incluir links e imagens, e que podem fornecer qualquer informação não transmitida através de descrições e etiquetas. |

## <a name="annotating-multiple-assets"></a>Anotando vários ativos
Ao selecionar vários ativos de dados no portal Data Catalog, os utilizadores podem anotar todos os ativos selecionados numa única operação. As anotações aplicar-se-ão a todos os ativos selecionados, facilitando a seleção e fornecendo uma descrição consistente e conjuntos de tags e especialistas para ativos de dados relacionados.

> [!NOTE]
> Tags e especialistas também podem ser fornecidos no registo de ativos de dados usando a ferramenta de registo de fonte de dados do Catálogo de Dados.
>
>

Ao selecionar várias tabelas e vistas, apenas as colunas que todos os ativos de dados selecionados têm em comum serão exibidas no portal Data Catalog. Isto permite que os utilizadores forneçam etiquetas e descrições para todas as colunas com o mesmo nome para todos os ativos selecionados.

## <a name="annotations-and-discovery"></a>Anotações e descobertas
Assim como os metadados extraídos da fonte de dados durante o registo são adicionados ao índice de pesquisa do Catálogo de Dados, os metadados fornecidos pelo utilizador também estão indexados. Isto significa que não só as anotações facilitam a compreensão dos dados que descobrem, como as anotações também facilitam a descoberta dos meios de dados anotados, procurando os termos que lhes fazem sentido.

## <a name="summary"></a>Resumo
Registar uma fonte de dados com data Catalog torna esses dados detetáveis através da cópia de metadados estruturais e descritivos da fonte de dados para o serviço 'Catálogo'. Uma vez registada uma fonte de dados, os utilizadores podem fornecer anotações para facilitar a descoberta e a compreensão a partir do portal Data Catalog.

## <a name="see-also"></a>Consulte também
* Inicie com o tutorial do Catálogo de [Dados Azure](data-catalog-get-started.md) para detalhes passo a passo sobre como anotar fontes de dados.
