---
title: Serviços de Machine Learning com R (visualização)
description: Este artigo descreve o Serviços de Machine Learning do banco de dados SQL do Azure (com R) e explica como ele funciona.
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
ms.openlocfilehash: a2af1fdd1ee461e3b3db613ff4a575649da2dfdc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827442"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Banco de dados SQL do Azure Serviços de Machine Learning com R (visualização)

Serviços de Machine Learning é um recurso do banco de dados SQL do Azure, usado para executar scripts R no banco de dados. O recurso inclui pacotes do Microsoft R para análise preditiva de alto desempenho e aprendizado de máquina. Os dados relacionais podem ser usados em scripts R por meio de procedimentos armazenados, o script T-SQL contendo instruções R ou o código R que contém o T-SQL.

> [!IMPORTANT]
> O banco de dados SQL do Azure Serviços de Machine Learning (com R) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> A visualização pública está disponível para bancos de dados individuais e pools elásticos usando o modelo de compra baseado em vCore nas camadas de serviço de **uso geral** e crítico para os **negócios** . Nesta visualização pública inicial, não há suporte para a camada de serviço de **hiperescala** e para a opção de implantação de **instância gerenciada** . Atualmente, a única linguagem suportada é R. Ainda não há suporte para Python neste momento.
>
> A versão prévia está disponível atualmente nas seguintes regiões: Europa Ocidental, Europa Setentrional, oeste dos EUA 2, leste dos EUA, Sul EUA Central, norte EUA Central, Canadá central, Sudeste Asiático, sul da Índia e sudeste da Austrália.
>
> [Inscreva-se para a versão prévia](#signup) abaixo.

## <a name="what-you-can-do-with-r"></a>O que você pode fazer com o R

Use o poder da linguagem R para fornecer análise avançada e aprendizado de máquina no banco de dados. Essa capacidade traz cálculos e processamento para onde residem os dados, eliminando a necessidade de efetuar pull de dados pela rede. Além disso, você pode aproveitar o poder dos pacotes de R corporativos para fornecer análises avançadas em escala.

A Serviços de Machine Learning inclui uma distribuição base de R, sobrepostas com pacotes de R Enterprise da Microsoft. As funções e os algoritmos do R da Microsoft são projetados para escala e utilitário, fornecendo análise preditiva, modelagem estatística, visualizações de dados e algoritmos de aprendizado de máquina de ponta.

### <a name="r-packages"></a>Pacotes de R

Os pacotes de R de software livre mais comuns são pré-instalados em Serviços de Machine Learning. Os seguintes pacotes do R da Microsoft também estão incluídos:

| Pacote de R | Descrição|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | O Microsoft R Open é a distribuição aprimorada do R da Microsoft. É uma plataforma de software livre completa para análise estatística e ciência de dados. Ele é baseado em e 100% compatível com o R e inclui recursos adicionais para melhorar o desempenho e o reprodução. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR é a biblioteca principal para R escalonáveis. as funções nessa biblioteca estão entre as mais amplamente usadas. Transformações e manipulação de dados, Resumo Estatístico, visualização e muitas formas de modelagem e análise são encontrados nessas bibliotecas. Além disso, as funções nessas bibliotecas distribuem automaticamente cargas de trabalho entre núcleos disponíveis para processamento paralelo, com a capacidade de trabalhar em partes de dados que são coordenadas e gerenciadas pelo mecanismo de cálculo. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | O MicrosoftML adiciona algoritmos de aprendizado de máquina para criar modelos personalizados para análise de texto, análise de imagem e análise de sentimentos. |

Além dos pacotes pré-instalados, você pode [instalar pacotes adicionais](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Inscrever-se na pré-visualização

Para se inscrever para a visualização pública, siga estas etapas:

1. Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

2. Envie um email para a Microsoft em [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) para se inscrever na visualização pública. A pré-visualização pública do Machine Learning Services (com R) na Base de Dados SQL do Azure não está ativada por predefinição.

Quando você estiver inscrito no programa, a Microsoft integrará você à visualização pública e habilitará o R para seu banco de dados novo ou existente.

Serviços de Machine Learning com R não é recomendável para a carga de trabalho de produção durante a visualização pública.

## <a name="next-steps"></a>Passos seguintes

- Consulte as [principais diferenças de SQL Server serviços de Machine Learning](sql-database-machine-learning-services-differences.md).
- Para saber como usar o R para consultar o Serviços de Machine Learning do banco de dados SQL do Azure (versão prévia), consulte o [Guia de início rápido](sql-database-connect-query-r.md).
- Para começar com alguns scripts R simples, consulte [criar e executar scripts r simples no banco de dados SQL do Azure serviços de Machine Learning (versão prévia)](sql-database-quickstart-r-create-script.md).
