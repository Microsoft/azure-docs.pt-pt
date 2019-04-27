---
title: Terminologia do catálogo de dados do Azure
description: Este artigo fornece uma introdução aos conceitos e termos utilizados na documentação do catálogo de dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: a6f2cf1dcee6a85376c8d767e57c504b6b246e5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580464"
---
# <a name="azure-data-catalog-terminology"></a>Terminologia do catálogo de dados do Azure

Este artigo fornece uma introdução aos conceitos e termos utilizados na documentação do catálogo de dados do Azure.

## <a name="catalog"></a>Catálogo

O catálogo de dados do Azure é um repositório de metadados com base na cloud em que os dados e origens de dados pode ser registado. O catálogo serve como uma localização de armazenamento central para os metadados estruturais extraídos de origens de dados e metadados descritivos que adicionou por utilizadores.

## <a name="data-source"></a>Origem de dados

Uma origem de dados é um sistema ou o contentor que gere recursos de dados. Os exemplos incluem bases de dados do SQL Server, bases de dados Oracle, bases de dados do SQL Server Analysis Services (tabulares ou multidimensionais) e servidores do SQL Server Reporting Services.

## <a name="data-asset"></a>Recurso de dados

Recursos de dados são objetos que origens de dados que podem ser registadas no catálogo. Os exemplos incluem tabelas do SQL Server e vistas, Oracle tabelas e vistas, SQL Server Analysis Services medidas, dimensões e KPIs e relatórios do SQL Server Reporting Services.

## <a name="data-asset-location"></a>Localização do recurso de dados

O catálogo armazena a localização de uma origem de dados ou o recurso de dados, que pode ser utilizado para ligar à origem através de uma aplicação de cliente. O formato e os detalhes da localização variam consoante o tipo de origem de dados. Por exemplo, uma tabela do SQL Server pode ser identificada pelo respetivo nome de quatro parte – nome do servidor, nome de base de dados, o nome do esquema, nome do objeto –, enquanto um relatório de serviços de relatórios do SQL Server podem ser identificado pelo respetivo URL.

## <a name="structural-metadata"></a>Metadados estruturais

Os metadados estruturais são os metadados extraídos da origem de dados que descreve a estrutura de um recurso de dados. Isto inclui a localização de recursos, o nome do objeto e tipo e características de tipo específicas adicionais. Por exemplo, os metadados estruturais para tabelas e vistas incluem os nomes e tipos de dados para colunas do objeto.

## <a name="descriptive-metadata"></a>Metadados descritivos

Metadados descritivos são metadados que descrevem o objetivo ou a intenção de um recurso de dados. Normalmente, os metadados descritivos é adicionado por utilizadores do catálogo através do portal do catálogo de dados do Azure, mas ele também pode ser extraído da origem de dados durante o registo. Por exemplo, a ferramenta de registo do catálogo de dados do Azure irá extrair descrições a propriedade Description no SQL Server Analysis Services e o SQL Server Reporting Services e para o [ms_description propriedade estendida](https://technet.microsoft.com/library/ms190243.aspx) em SQL Bases de dados, se essas propriedades foram preenchidas com os valores.

## <a name="request-access"></a>Pedir acesso

Metadados descritivos de um recurso de dados podem incluir informações sobre como pedir acesso para o recurso de dados ou a origem de dados. Esta informação é apresentada a localização do recurso de dados e pode incluir um ou mais das seguintes opções:

* O endereço de e-mail do utilizador ou equipa responsável por conceder acesso à origem de dados.
* O URL do processo documentado que os utilizadores têm de seguir para obter acesso à origem de dados.
* O URL de uma identidade e acesso à ferramenta de gestão (como o Microsoft Identity Manager) que pode ser utilizado para obter acesso à origem de dados.
* Uma entrada de texto livre que descreve a forma como os utilizadores poderem obter acesso à origem de dados.

## <a name="preview"></a>Pré-visualização

Uma pré-visualização no catálogo de dados do Azure é um instantâneo de até 20 registos que podem ser extraídos da origem de dados durante o registo e armazenados no catálogo com metadados do recurso de dados. A pré-visualização pode ajudar os utilizadores que detetar um recurso de dados compreendam melhor a sua função e objetivo. Em outras palavras, a visualização de dados de exemplo pode ser mais valioso do que ver apenas os nomes das colunas e tipos de dados.
Pré-visualizações só são suportadas para tabelas e vistas e tem de ser selecionadas explicitamente pelo utilizador durante o registo.

## <a name="data-profile"></a>Perfil de Dados

Um perfil de dados no catálogo de dados do Azure é um instantâneo dos metadados de nível de tabela e ao nível da coluna sobre um elemento de dados registados que pode ser extraído da origem de dados durante o registo e armazenado no catálogo com metadados do recurso de dados. O perfil de dados pode ajudar os utilizadores que detetar um recurso de dados compreendam melhor a sua função e objetivo. Semelhante à pré-visualizações, perfis de dados devem ser explicitamente selecionados pelo usuário durante o registo.

> [!NOTE]
> Extrair um perfil de dados pode ser uma operação dispendiosa para tabelas grandes e exibições e pode aumentar significativamente o tempo necessário para se registar uma origem de dados.


## <a name="user-perspective"></a>Ponto de vista do utilizador

No catálogo de dados do Azure, qualquer usuário pode fornecer metadados descritivos para um ativo de dados registados. Cada utilizador tem uma perspectiva diferente sobre os dados e seu uso. Por exemplo, o administrador responsável por um servidor de pode fornecer os detalhes do seu contrato de nível de serviço (SLA) ou a cópia de segurança windows; uma responsável pelos dados pode fornecer ligações para documentação para as empresas e processa o suporte de dados; e um analista pode fornecer uma descrição nos termos que são mais relevantes para outros analistas e que pode ser mais valioso para os usuários que precisam de detetar e compreender os dados.

Cada um desses perspetivas é inerentemente valiosa, e no catálogo de dados do Azure, cada utilizador pode fornecer as informações que faça sentidas a eles, enquanto todos os utilizadores podem utilizar essas informações para compreender os dados e sua finalidade.

## <a name="expert"></a>Especialista

Um especialista é um utilizador que foi identificado como tendo uma perspectiva de "especializada" informada para um recurso de dados. Qualquer utilizador pode adicionar os próprios ou a outro utilizador como um especialista para um recurso. A ser listado como um especialista não transmite qualquer privilégios adicionais no catálogo de dados do Azure; ele permite que os usuários localizar facilmente essas perspectivas que têm mais probabilidades de ser útil ao revisar os metadados descritivos de um recurso.

## <a name="owner"></a>Proprietário

Um proprietário é um utilizador que tem privilégios adicionais para gerir um recurso de dados no catálogo de dados do Azure. Os utilizadores podem assumir a propriedade dos recursos de dados registados e os proprietários podem adicionar outros utilizadores como coproprietários. Para obter mais informações, consulte [como gerir recursos de dados](data-catalog-how-to-manage.md)  

> [!NOTE]
> Propriedade e de gestão só estão disponíveis no Standard Edition do Azure catálogo de dados.

## <a name="registration"></a>Registo

O registo é o ato de extrair metadados do recurso de dados de uma origem de dados e copiá-lo para o serviço de catálogo de dados do Azure. Recursos de dados que foram registados, em seguida, podem ser anotados e detetados.

## <a name="next-steps"></a>Passos Seguintes

[Quickstart: Criar um catálogo de dados do Azure](data-catalog-get-started.md) 
