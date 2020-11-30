---
title: Serviços de Aprendizagem automática em Azure SQL Gestded Instance (pré-visualização)
description: Este artigo fornece uma visão geral ou serviços de aprendizagem automática em Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: c805bacbd4a2219fb79168ad6426efd8b0a390df
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324521"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Serviços de Aprendizagem automática em Azure SQL Gestded Instance (pré-visualização)

Machine Learning Services é uma característica do Azure SQL Managed Instance (pré-visualização) que fornece aprendizagem automática na base de dados, suportando scripts Python e R. A funcionalidade inclui pacotes Microsoft Python e R para análise preditiva de alto desempenho e machine learning. Os dados relacionais podem ser utilizados em scripts através de procedimentos armazenados, script T-SQL contendo declarações python ou R, ou código Python ou R contendo T-SQL.

> [!IMPORTANT]
> O Machine Learning Services é uma funcionalidade do SQL Managed Instance que está atualmente em pré-visualização pública.
> Esta funcionalidade de pré-visualização está inicialmente disponível num número limitado de regiões dos EUA, Ásia Europa e Austrália, com regiões adicionais a serem adicionadas mais tarde.
>
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Inscreva-se para a pré-visualização](#signup) abaixo.

## <a name="what-is-machine-learning-services"></a>O que é o Serviço de Aprendizagem automática?

Serviços de Machine Learning em Azure SQL Managed Instance permite executar scripts Python e R na base de dados. Pode usá-lo para preparar e limpar dados, fazer engenharia de recursos e treinar, avaliar e implementar modelos de machine learning dentro de uma base de dados. A funcionalidade executa os seus scripts onde os dados residem e elimina a transferência dos dados através da rede para outro servidor.

Utilize serviços de aprendizagem automática com suporte R/Python em Azure SQL Gestded Instance para:

- **Executar scripts R e Python para fazer a preparação de dados e processamento de dados para fins gerais** - Pode agora levar as suas scripts R/Python para Azure SQL Managed Instance onde os seus dados vivem, em vez de ter que mover dados para outro servidor para executar scripts R e Python. Pode eliminar a necessidade de movimento de dados e problemas associados relacionados com a latência, segurança e conformidade.

- **Modelos de aprendizagem de máquinas de comboio na base de dados** - Você pode treinar modelos usando quaisquer algoritmos de código aberto. Pode facilmente escalar o seu treino para todo o conjunto de dados em vez de depender de conjuntos de dados de amostras retirados da base de dados.

- **Implemente os seus modelos e scripts na produção em procedimentos armazenados** - Os scripts e modelos treinados podem ser operacionalizados simplesmente incorporando-os em procedimentos armazenados T-SQL. As aplicações que se ligam ao Azure SQL Managed Instance podem beneficiar de previsões e inteligência nestes modelos, chamando apenas um procedimento armazenado. Também pode utilizar a função T-SQL PREDICT nativa para operacionalizar modelos para pontuação rápida em cenários de pontuação em tempo real altamente simultâneos.

As distribuições base de Python e R estão incluídas nos Serviços de Aprendizagem Automática. Pode instalar e utilizar pacotes e estruturas de código aberto, tais como PyTorch, TensorFlow e scikit-learn, além dos pacotes microsoft [revoscalepy](/sql/advanced-analytics/python/ref-py-revoscalepy) e [microsoftml](/sql/advanced-analytics/python/ref-py-microsoftml) para Python, e [RevoScaleR,](/sql/advanced-analytics/r/ref-r-revoscaler) [MicrosoftML,](/sql/advanced-analytics/r/ref-r-microsoftml) [olapR](/sql/advanced-analytics/r/ref-r-olapr)e [sqlrutils](/sql/advanced-analytics/r/ref-r-sqlrutils) para R.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Inscrever-se na pré-visualização

Esta pré-visualização pública limitada está sujeita aos [termos de pré-visualização do Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Se você está interessado em aderir ao programa de pré-visualização e aceitar estes termos, então você pode solicitar a inscrição criando um bilhete de apoio Azure em [**https://azure.microsoft.com/support/create-ticket/**](https://azure.microsoft.com/support/create-ticket/) . 

1. Na página **'Criar um bilhete de apoio',** clique em **Criar um Incidente.**

1. Na página **de suporte Help +,** clique em **Novo pedido de suporte** para criar um novo bilhete.

1. Selecione as seguintes opções:
   - Tipo de emissão - **Técnico**
   - Subscrição - *selecione a sua subscrição*
   - Serviço - **SQL Gestdibilly Instance**
   - Recurso - *selecione a sua instância gerida*
   - Resumo - *insira uma breve descrição do seu pedido*
   - Tipo de problema - **Serviços de aprendizagem automática para exemplo gerido do SQL (Pré-visualização)**
   - Subtipo de problema - **Outras questões ou "Como Fazer"**

1. Clique **em seguida: Soluções**.

1. Leia as informações sobre a pré-visualização e, em seguida, clique em **Seguinte: Detalhes**.

1. Nesta página:
   - Para a pergunta **Está a tentar inscrever-se para a Pré-visualização?** **Yes** 
   - Para **descrição,** insira as especificidades do seu pedido, incluindo o nome do servidor lógico, região e ID de subscrição que pretende inscrever na pré-visualização. Insira outros detalhes conforme apropriado.
   - Selecione o seu método de contacto preferido. 

1. Quando terminar, clique em **Seguinte: Review + create** e, em seguida, clique em **Criar**.

Assim que estiver inscrito no programa, a Microsoft vai incluí-lo na pré-visualização pública e ativar o Machine Learning Services para a base de dados existente ou nova.

Durante a pré-visualização pública, o Machine Learning Services no SQL Managed Instance não são recomendados para cargas de trabalho em produção.

## <a name="next-steps"></a>Passos seguintes

- Consulte as [principais diferenças dos serviços de aprendizagem automática de servidores SQL.](machine-learning-services-differences.md)
- Para aprender a usar Python em serviços de machine learning, consulte [os scripts Run Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context&view=sql-server-ver15).
- Para aprender a usar R em Serviços de Aprendizagem Automática, consulte [scripts Run R](/sql/machine-learning/tutorials/quickstart-r-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context&view=sql-server-ver15).
- Para obter mais informações sobre machine learning em outras plataformas SQL, consulte a [documentação de aprendizagem automática SQL.](/sql/machine-learning/)