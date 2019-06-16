---
title: Como anotar origens de dados no catálogo de dados do Azure
description: Artigo que mostra como destacando como anotar recursos de dados no catálogo de dados do Azure, incluindo nomes amigáveis, etiquetas, descrições e especialistas.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 5a7e6bb2-863c-4eca-b614-1c814920d9ed
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 18dfd9eb66c1dc71ecb14bb748d9cdd6afeb00a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65953182"
---
# <a name="how-to-annotate-data-sources"></a>Como anotar origens de dados
## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço cloud totalmente gerido que funciona como um sistema de registo e sistema de deteção de origens de dados empresariais. Em outras palavras, o catálogo de dados é tudo para ajudar as pessoas a detetar, compreender e utilizar origens de dados e as organizações de ajuda para obter mais valor dos seus dados existentes. Quando uma origem de dados está registrada no catálogo de dados, seus metadados é copiado e indexados pelo serviço, mas a história não acaba nisso. Catálogo de dados permite aos utilizadores fornecer seus próprios metadados descritivos – como descrições e marcas – para complementar os metadados extraídos da origem de dados e fazer com que a origem de dados mais compreensível para mais pessoas.

## <a name="annotation-and-crowdsourcing"></a>Anotação e crowdsourcing
Todos têm uma opinião. E isso é algo bom.
Catálogo de dados reconhece que diferentes usuários têm diferentes perspectivas sobre origens de dados empresariais, e cada um desses perspectivas que pode ser valiosa. Considere o seguinte cenário:

* O administrador de sistema sabe o contrato de nível de serviço para servidores ou serviços que alojam a origem de dados.
* O administrador de banco de dados sabe a agenda de cópia de segurança para cada base de dados e os windows de processamento do ETL permitidos.
* O proprietário do sistema sabe o processo para que os utilizadores peçam acesso à origem de dados.
* O responsável pelos dados sabe como mapeiam os ativos e os atributos da origem de dados para o modelo de dados da empresa.
* O analista sabe como os dados são utilizados no contexto dos processos de negócios, que eles oferecem suporte.

Cada um desses perspetivas é valiosa, e o catálogo de dados utiliza uma abordagem de crowdsourcing aos metadados permite que cada um deles ser capturado e usado para oferecer uma visão completa das origens de dados registados. Com o portal do catálogo de dados, cada utilizador pode adicionar e editar a suas própria anotações, enquanto a capacidade de ver anotações fornecidas por outros utilizadores.

## <a name="different-types-of-annotations"></a>Diferentes tipos de anotações
Catálogo de dados suporta os seguintes tipos de anotações:

| Anotação | Notas |
| --- | --- |
| Nome amigável |Nomes amigáveis podem ser fornecidos no nível de elemento de dados, para que os recursos de dados mais facilmente compreendidos. Nomes amigáveis são mais úteis quando o nome do objeto subjacente é confuso, abreviado ou caso contrário, não significativo para os utilizadores. |
| Descrição |As descrições que podem ser fornecidas no recurso de dados e no atributo / níveis de coluna. As descrições são anotações de texto breve de forma livre que descrevem o usuário perspetivas no recurso de dados ou do seu uso. |
| Etiquetas (etiquetas de utilizador) |As etiquetas podem ser fornecidas no recurso de dados e no atributo / níveis de coluna. Etiquetas de utilizador são etiquetas definido pelo utilizador que podem ser utilizadas para categorizar os recursos de dados ou atributos. |
| Tags (marcas de glossário) |As etiquetas podem ser fornecidas no recurso de dados e no atributo / níveis de coluna. Etiquetas de glossário são termos do glossário definidas centralmente que podem ser utilizados para categorizar os recursos de dados ou atributos usando uma taxonomia comercial comum. Para mais informações, veja [Como configurar o Glossário Comercial para Etiquetagem Regida](data-catalog-how-to-business-glossary.md) |
| Especialistas |Especialistas podem ser fornecidos no nível de elemento de dados. Especialistas identificar utilizadores ou grupos com perspetivas especialistas nos dados e podem servir como pontos de contato para os utilizadores que detetar as origens de dados registados e dúvidas que não são abordadas as anotações existentes. |
| Pedir acesso |Informação do pedido de acesso pode ser fornecida no nível de elemento de dados. Estas informações são para os utilizadores detetam uma origem de dados que ainda não têm permissões para aceder. Os utilizadores podem introduzir o endereço de e-mail do utilizador ou grupo que concede o acesso, o URL do processo ou a ferramenta que os utilizadores precisam para obter acesso, ou podem introduzir o próprio processo como texto. |
| Documentação |Documentação pode ser fornecida no nível de elemento de dados. Documentação do recurso é informações de texto avançado que pode incluir ligações e imagens e que pode fornecer quaisquer informações não são transmitidas através de etiquetas e descrições. |

## <a name="annotating-multiple-assets"></a>Anotar vários recursos
Ao selecionar vários recursos de dados no portal do catálogo de dados, os utilizadores podem anotar todos os recursos selecionados numa única operação. Anotações serão aplicada a todos os recursos selecionados, facilitando a selecionar e fornecer uma descrição consistente e conjuntos de etiquetas e especialistas para recursos de dados relacionados.

> [!NOTE]
> As etiquetas e especialistas também podem ser fornecidos quando o registo de recursos de dados usando os dados do catálogo de dados da ferramenta de registo de origem.
>
>

Quando selecionar várias tabelas e vistas, apenas as colunas que todos os dados ativos têm em comum de selecionados será apresentado no portal do catálogo de dados. Isso permite que os utilizadores forneçam etiquetas e descrições para todas as colunas com o mesmo nome para todos os recursos selecionados.

## <a name="annotations-and-discovery"></a>Anotações e de deteção
Assim como os metadados extraídos da origem de dados durante o registo é adicionado para o índice de pesquisa do catálogo de dados, os metadados fornecidos pelo usuário também são indexados. Isso significa que não só anotações tornam mais fácil para os utilizadores a compreender os dados, que eles descobrem, anotações também o tornam mais fácil para os utilizadores detetar os recursos de dados anotados ao pesquisar com os termos que façam sentido a eles.

## <a name="summary"></a>Resumo
Registar uma origem de dados no catálogo de dados torna esses dados detetável ao copiar os metadados estruturais e descritivo da origem de dados para o serviço de catálogo. Depois de uma origem de dados ser registada, os usuários podem fornecer anotações para tornar mais fácil detetar e compreender, de, no portal do catálogo de dados.

## <a name="see-also"></a>Consulte também
* [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) tutorial para obter detalhes passo a passo sobre como anotar origens de dados.
