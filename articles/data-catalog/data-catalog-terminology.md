---
title: Terminologia do Catálogo de Dados Azure
description: Este artigo fornece uma introdução aos conceitos e termos utilizados na documentação do Catálogo de Dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: d6c813dec3922334f7462b1226ea22371fd5f43b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68736280"
---
# <a name="azure-data-catalog-terminology"></a>Terminologia do Catálogo de Dados Azure

Este artigo fornece uma introdução aos conceitos e termos utilizados na documentação do Catálogo de Dados do Azure.

## <a name="catalog"></a>Catálogo

O Azure Data Catalog é um repositório de metadados baseado na nuvem no qual fontes de dados e ativos de dados podem ser registados. O catálogo serve como um local de armazenamento central para metadados estruturais extraídos de fontes de dados e para metadados descritivos adicionados pelos utilizadores.

## <a name="data-source"></a>Origem de dados

Uma fonte de dados é um sistema ou contentor que gere os ativos de dados. Exemplos incluem bases de dados de servidores SQL, bases de dados oracle, bases de dados de serviços de análise de servidores SQL (tabular ou multidimensional) e servidores de Serviços de Relatório de Servidores SQL.

## <a name="data-asset"></a>Ativo de dados

Os ativos de dados são objetos contidos em fontes de dados que podem ser registados no catálogo. Exemplos incluem tabelas e vistas do SQL Server, tabelas e vistas da Oracle, medidas de serviços de análise de servidores SQL, dimensões e KPI's e relatórios dos Serviços de Relatórios de Servidores do SQL.

## <a name="data-asset-location"></a>Localização do ativo de dados

O catálogo armazena a localização de uma fonte de dados ou um ativo de dados, que pode ser usado para se conectar à fonte usando uma aplicação do cliente. O formato e os detalhes da localização variam em função do tipo de fonte de dados. Por exemplo, uma tabela SQL Server pode ser identificada pelo seu nome de quatro partes - nome do servidor, nome da base de dados, nome do esquema, nome do objeto - enquanto um Relatório de Serviços de Relatório de Relatório de Relatório de Relatório de Relatório de Servidor sql pode ser identificado pelo seu URL.

## <a name="structural-metadata"></a>Metadados estruturais

Metadados estruturais são os metadados extraídos de uma fonte de dados que descreve a estrutura de um ativo de dados. Isto inclui a localização do ativo, o seu nome e tipo de objeto, e características específicas do tipo. Por exemplo, os metadados estruturais para tabelas e vistas incluem os nomes e tipos de dados para as colunas do objeto.

## <a name="descriptive-metadata"></a>Metadados descritivos

Metadados descritivos são metadados que descrevem a finalidade ou intenção de um ativo de dados. Os metadados descritivos normalmente são adicionados pelos utilizadores do catálogo utilizando o portal Azure Data Catalog, mas também podem ser extraídos da fonte de dados durante o registo. Por exemplo, a ferramenta de registo do Catálogo de Dados Azure extrairá descrições da propriedade Description nos Serviços de Análise de Servidores SQL e serviços de reporte de servidores SQL, e a partir do [ms_description propriedade estendida](https://technet.microsoft.com/library/ms190243.aspx) nas bases de dados do SQL Server, se estas propriedades tiverem sido povoadas com valores.

## <a name="request-access"></a>Pedir acesso

Os metadados descritivos de um ativo de dados podem incluir informações sobre como solicitar o acesso ao ativo de dados ou fonte de dados. Esta informação é apresentada com a localização do ativo dos dados, e pode incluir uma ou mais das seguintes opções:

* O endereço de e-mail do utilizador ou equipa responsável pela concessão de acesso à fonte de dados.
* O URL do processo documentado que os utilizadores devem seguir para ter acesso à fonte de dados.
* O URL de uma ferramenta de gestão de identidade e acesso (como o Microsoft Identity Manager) que pode ser usado para ter acesso à fonte de dados.
* Uma entrada de texto livre que descreve como os utilizadores podem ter acesso à fonte de dados.

## <a name="preview"></a>Pré-visualizar

Uma pré-visualização no Catálogo de Dados Azure é uma imagem instantânea de até 20 registos que podem ser extraídos da fonte de dados durante o registo, e armazenados no catálogo com os metadados do ativo de dados. A pré-visualização pode ajudar os utilizadores que descobrem um ativo de dados a compreender melhor a sua função e finalidade. Por outras palavras, ver dados de amostras pode ser mais valioso do que ver apenas os nomes das colunas e os tipos de dados.
As pré-visualizações são suportadas apenas para tabelas e vistas, e devem ser explicitamente selecionadas pelo utilizador durante o registo.

## <a name="data-profile"></a>Perfil de Dados

Um perfil de dados no Catálogo de Dados Azure é uma imagem instantânea de metadados de nível de tabela e coluna sobre um ativo de dados registado que pode ser extraído da fonte de dados durante o registo, e armazenado no catálogo com os metadados do ativo de dados. O perfil de dados pode ajudar os utilizadores que descobrem um ativo de dados a compreender melhor a sua função e finalidade. À semelhança das pré-visualizações, os perfis de dados devem ser explicitamente selecionados pelo utilizador durante o registo.

> [!NOTE]
> Extrair um perfil de dados pode ser uma operação dispendiosa para grandes tabelas e vistas, e pode aumentar significativamente o tempo necessário para registar uma fonte de dados.


## <a name="user-perspective"></a>Perspetiva do utilizador

No Catálogo de Dados Azure, qualquer utilizador pode fornecer metadados descritivos para um ativo de dados registado. Cada utilizador tem uma perspetiva distinta sobre os dados e a sua utilização. Por exemplo, o administrador responsável por um servidor pode fornecer os detalhes do seu contrato de nível de serviço (SLA) ou janelas de backup; um administrador de dados pode fornecer ligações à documentação para os processos empresariais que os suportes de dados suportam; e um analista pode fornecer uma descrição nos termos mais relevantes para outros analistas, e que pode ser mais valioso para aqueles utilizadores que precisam descobrir e entender os dados.

Cada uma destas perspetivas é inerentemente valiosa, e com o Azure Data Catalog cada utilizador pode fornecer a informação que lhes é significativa, enquanto todos os utilizadores podem utilizar essa informação para compreender os dados e a sua finalidade.

## <a name="expert"></a>Especialista

Um perito é um utilizador que foi identificado como tendo uma perspetiva "expert" informada para um ativo de dados. Qualquer utilizador pode adicionar-se a si próprio ou a outro utilizador como um especialista para um ativo. Ser listado como perito não transmite quaisquer privilégios adicionais no Catálogo de Dados do Azure; permite que os utilizadores localizem facilmente as perspetivas que são mais prováveis de serem úteis ao rever os metadados descritivos de um ativo.

## <a name="owner"></a>Proprietário

Um proprietário é um utilizador que tem privilégios adicionais para gerir um ativo de dados no Azure Data Catalog. Os utilizadores podem apropriar-se de ativos de dados registados, e os proprietários podem adicionar outros utilizadores como coproprietários. Para mais informações, consulte [Como gerir os ativos de dados](data-catalog-how-to-manage.md)  

> [!NOTE]
> A propriedade e a gestão só estão disponíveis na Edição Padrão do Catálogo de Dados Azure.

## <a name="registration"></a>Registo

O registo é o ato de extrair metadados de ativos de dados de uma fonte de dados e copiá-lo para o serviço Azure Data Catalog. Os ativos de dados registados podem então ser anotados e descobertos.

## <a name="next-steps"></a>Passos seguintes

[Quickstart: Criar um Catálogo de Dados Azure](data-catalog-get-started.md) 
