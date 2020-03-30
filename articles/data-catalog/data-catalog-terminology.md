---
title: Terminologia do Catálogo de Dados Azure
description: Este artigo fornece uma introdução aos conceitos e termos utilizados na documentação do Catálogo de Dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: d6c813dec3922334f7462b1226ea22371fd5f43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736280"
---
# <a name="azure-data-catalog-terminology"></a>Terminologia do Catálogo de Dados Azure

Este artigo fornece uma introdução aos conceitos e termos utilizados na documentação do Catálogo de Dados do Azure.

## <a name="catalog"></a>Catálogo

O Catálogo de Dados Azure é um repositório de metadados baseado na nuvem no qual fontes de dados e ativos de dados podem ser registados. O catálogo serve como um local central de armazenamento para metadados estruturais extraídos de fontes de dados e para metadados descritivos adicionados pelos utilizadores.

## <a name="data-source"></a>Origem de dados

Uma fonte de dados é um sistema ou contentor que gere os ativos de dados. Exemplos incluem bases de dados do Servidor SQL, bases de dados oracle, bases de dados de Serviços de Análise de Servidores SQL (tabular ou multidimensional) e servidores de Serviços de Reporte de Servidores SQL.

## <a name="data-asset"></a>Ativo de dados

Os ativos de dados são objetos contidos em fontes de dados que podem ser registados no catálogo. Exemplos incluem tabelas e vistas do Servidor SQL, tabelas e vistas oracle, medidas de Serviços de Análise de Servidores SQL, dimensões e KPIs, e relatórios de Serviços de Reporte de Servidores SQL.

## <a name="data-asset-location"></a>Localização do ativo de dados

O catálogo armazena a localização de uma fonte de dados ou um ativo de dados, que pode ser usado para se ligar à fonte usando uma aplicação do cliente. O formato e os detalhes da localização variam em função do tipo de fonte de dados. Por exemplo, uma tabela SQL Server pode ser identificada pelo seu nome de quatro partes – nome do servidor, nome da base de dados, nome do esquema, nome do objeto – enquanto um Relatório de Serviços de Reporte de Servidores SQL pode ser identificado pelo seu URL.

## <a name="structural-metadata"></a>Metadados estruturais

Metadados estruturais são os metadados extraídos de uma fonte de dados que descreve a estrutura de um ativo de dados. Isto inclui a localização do ativo, o seu nome e tipo de objeto, e características adicionais específicas do tipo. Por exemplo, os metadados estruturais para tabelas e vistas incluem os nomes e tipos de dados para as colunas do objeto.

## <a name="descriptive-metadata"></a>Metadados descritivos

Metadados descritivos são metadados que descrevem a finalidade ou intenção de um ativo de dados. Os metadados tipicamente descritivos são adicionados pelos utilizadores do catálogo que utilizam o portal Do Catálogo de Dados Do Azure, mas também podem ser extraídos da fonte de dados durante o registo. Por exemplo, a ferramenta de registo do Catálogo de Dados Azure extrairá descrições da propriedade Descrição nos Serviços de Análise de Servidores SQL e serviços de reporte de servidores SQL, e a partir da [propriedade ms_description estendida](https://technet.microsoft.com/library/ms190243.aspx) nas bases de dados do SQL Server, se estas propriedades tiverem sido povoadas com valores.

## <a name="request-access"></a>Pedir acesso

Os metadados descritivos de um ativo de dados podem incluir informações sobre como solicitar o acesso ao ativo de dados ou fonte de dados. Esta informação é apresentada com a localização do ativo de dados, e pode incluir uma ou mais das seguintes opções:

* O endereço de e-mail do utilizador ou equipa responsável pela concessão de acesso à fonte de dados.
* O URL do processo documentado que os utilizadores devem seguir para ter acesso à fonte de dados.
* O URL de uma ferramenta de gestão de identidade e acesso (como o Microsoft Identity Manager) que pode ser usado para ter acesso à fonte de dados.
* Uma entrada de texto livre que descreve como os utilizadores podem ter acesso à fonte de dados.

## <a name="preview"></a>Pré-visualização

Uma pré-visualização no Catálogo de Dados do Azure é uma imagem instantânea de até 20 registos que podem ser extraídos da fonte de dados durante o registo, e armazenados no catálogo com os metadados dos dados. A pré-visualização pode ajudar os utilizadores que descobrem um ativo de dados a entender melhor a sua função e propósito. Por outras palavras, ver dados de amostra supor pode ser mais valioso do que ver apenas os nomes das colunas e os tipos de dados.
As pré-visualizações são suportadas apenas para tabelas e vistas, e devem ser explicitamente selecionadas pelo utilizador durante o registo.

## <a name="data-profile"></a>Perfil de Dados

Um perfil de dados no Catálogo de Dados do Azure é uma imagem instantânea de metadados ao nível da tabela e de colunas sobre um ativo de dados registado que pode ser extraído da fonte de dados durante o registo, e armazenado no catálogo com os metadados dos dados. O perfil de dados pode ajudar os utilizadores que descobrem um ativo de dados a entender melhor a sua função e propósito. À semelhança das pré-visualizações, os perfis de dados devem ser explicitamente selecionados pelo utilizador durante o registo.

> [!NOTE]
> A extração de um perfil de dados pode ser uma operação dispendiosa para grandes tabelas e vistas, podendo aumentar significativamente o tempo necessário para registar uma fonte de dados.


## <a name="user-perspective"></a>Perspetiva do utilizador

No Catálogo de Dados Do Azure, qualquer utilizador pode fornecer metadados descritivos para um ativo de dados registado. Cada utilizador tem uma perspetiva distinta sobre os dados e a sua utilização. Por exemplo, o administrador responsável por um servidor pode fornecer os detalhes do seu contrato de nível de serviço (SLA) ou janelas de backup; Um administrador de dados pode fornecer links para a documentação para os processos empresariais que os suportes de dados; e um analista pode fornecer uma descrição nos termos mais relevantes para outros analistas, e que pode ser mais valioso para aqueles utilizadores que precisam descobrir e entender os dados.

Cada uma destas perspetivas é inerentemente valiosa, e com o Catálogo de Dados Azure cada utilizador pode fornecer as informações que lhes são significativas, enquanto todos os utilizadores podem utilizar essa informação para compreender os dados e a sua finalidade.

## <a name="expert"></a>Especialista

Um especialista é um utilizador que foi identificado como tendo uma perspetiva "especialista" informada para um ativo de dados. Qualquer utilizador pode adicionar-se a si próprio ou a outro utilizador como um especialista para um ativo. Ser listado como especialista não transmite quaisquer privilégios adicionais no Catálogo de Dados do Azure; permite que os utilizadores localizem facilmente as perspetivas que são mais prováveis de serem úteis na revisão dos metadados descritivos de um ativo.

## <a name="owner"></a>Proprietário

Um proprietário é um utilizador que tem privilégios adicionais para gerir um ativo de dados no Catálogo de Dados Azure. Os utilizadores podem tomar posse de ativos de dados registados, e os proprietários podem adicionar outros utilizadores como coproprietários. Para mais informações, consulte [Como gerir os ativos de dados](data-catalog-how-to-manage.md)  

> [!NOTE]
> A propriedade e a gestão estão disponíveis apenas na Edição Padrão do Catálogo de Dados Azure.

## <a name="registration"></a>Registo

O registo é o ato de extrair metadados de dados de uma fonte de dados e copiá-los para o serviço De Catálogo de Dados Do Azure. Os ativos de dados registados podem então ser anotados e descobertos.

## <a name="next-steps"></a>Passos seguintes

[Quickstart: Criar um Catálogo de Dados Azure](data-catalog-get-started.md) 
