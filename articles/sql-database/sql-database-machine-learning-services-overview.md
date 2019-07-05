---
title: Serviços do Azure SQL da base de dados Machine Learning com visão geral do R (pré-visualização)
description: Este artigo descreve os serviços do Azure SQL Database Machine Learning (com R) e explica como funciona.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 186b986fe1931365ee34efab2e04e58908402cc0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427947"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Serviços do Azure SQL da base de dados Machine Learning com R (pré-visualização)

Serviços de Machine Learning é um recurso do Azure SQL Database, utilizado para a execução de scripts do R na base de dados. A funcionalidade inclui pacotes de Microsoft R para Análise Preditiva de alto desempenho e o machine learning. Os dados relacionais podem ser usados nos scripts de R por meio de procedimentos armazenados, instruções de R que contém script T-SQL ou código de R que contém o T-SQL.

> [!IMPORTANT]
> Serviços do Azure SQL Database Machine Learning (com R) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> A pré-visualização pública está disponível para bases de dados individuais e conjuntos elásticos com o modelo de compra baseado em vCore na **fins gerais** e **crítico para a empresa** escalões de serviço. Esta pré-visualização pública inicial, o **hiperescala** escalão de serviço e o **instância gerida** opção de implementação não são suportadas. Atualmente, a única linguagem suportada é R. Ainda não há suporte para Python neste momento.
>
> A pré-visualização está atualmente disponível nas seguintes regiões: Europa Ocidental, Europa do Norte, E.U.A. oeste 2, E.U.A. leste, Centro-Sul dos E.U.A., Centro-Norte, Canadá Central, Sudeste asiático, Sul da Índia e Sudeste da Austrália.
>
> [Inscreva-se na pré-visualização](#signup) abaixo.

## <a name="what-you-can-do-with-r"></a>O que pode fazer com R

Utilize o poder da linguagem R para fornecer análises avançadas e aprendizagem automática na base de dados. Esta capacidade traz cálculos e processamento para onde os dados residem, eliminando a necessidade de extrair os dados através da rede. Além disso, pode aproveitar o poder dos pacotes de R enterprise para fornecer análise avançada à escala.

Machine Learning Services inclui uma distribuição base da linguagem R, recoberto com pacotes de R enterprise da Microsoft. Funções de R e algoritmos da Microsoft estão concebidos para o dimensionamento e o utilitário, entrega de Análise Preditiva, modelação estatística, visualizações de dados e algoritmos de aprendizagem de ponta.

### <a name="r-packages"></a>Pacotes de R

Pacotes de R de código-fonte aberto mais comuns são previamente instaladas no Machine Learning Services. Os seguintes pacotes de R da Microsoft também estão incluídos:

| Pacote de R | Descrição|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open é a distribuição aprimorada do R da Microsoft. É uma plataforma completa de código-fonte aberto para estatística análise e ciência de dados. É com base em e 100% compatível com R e inclui capacidades adicionais para maior desempenho e capacidade de reprodução. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | Pacotes RevoScaleR é a biblioteca principal para a dimensionável R. as funções nesta biblioteca estão entre o mais amplamente utilizadas. Transformações de dados e manipulação, resumo de estatístico, visualização e muitas formas de modelagem e análises são encontradas dessas bibliotecas. Além disso, as funções nessas bibliotecas automaticamente distribuam as cargas de trabalho em núcleos disponíveis para processar, com a capacidade de trabalhar em segmentos de dados coordenados e geridos pelo mecanismo de cálculo de paralela. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML adiciona algoritmos de machine learning para criar modelos personalizados para análise de texto, imagens de análises e análise de sentimentos. |

Além de pacotes de previamente instalados, pode [instalar pacotes adicionais](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Inscrever-se na pré-visualização

Para se inscrever na pré-visualização pública, siga estes passos:

1. Se não tiver uma subscrição do Azure, [criar uma conta](https://azure.microsoft.com/free/) antes de começar.

2. Enviar um e-mail à Microsoft em [ sqldbml@microsoft.com ](mailto:sqldbml@microsoft.com) para se inscrever na pré-visualização pública. A pré-visualização pública do Machine Learning Services (com R) na Base de Dados SQL do Azure não está ativada por predefinição.

Assim que estiver inscrito no programa, Microsoft irá carregar para a pré-visualização pública e ativar R para sua existente ou novo banco de dados.

Serviços de Machine Learning com R não é recomendado para a carga de trabalho de produção durante a pré-visualização pública.

## <a name="next-steps"></a>Passos Seguintes

- Consulte a [diferenças do SQL Server Machine Learning Services de chave](sql-database-machine-learning-services-differences.md).
- Para saber como utilizar o R para consulta SQL da base de dados serviços Azure Machine Learning (pré-visualização), veja a [guia de início rápido](sql-database-connect-query-r.md).
- Para começar com alguns scripts simples do R, veja [criar e executar scripts R simples no Machine Learning Services (pré-visualização) do banco de dados do Azure SQL](sql-database-quickstart-r-create-script.md).
