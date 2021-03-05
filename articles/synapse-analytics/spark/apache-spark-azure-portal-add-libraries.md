---
title: Gestão de pacotes
description: Saiba como adicionar e gerir bibliotecas usadas pela Apache Spark em Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 38826451bbb8ec9303f61db53544cfe8ca2fcd41
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123093"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Gerir bibliotecas para Apache Spark em Azure Synapse Analytics
As bibliotecas fornecem código reutilizável que poderá querer incluir nos seus programas ou projetos. 

Você pode precisar atualizar o seu ambiente de piscina Apache Spark sem servidor por várias razões. Por exemplo, pode descobrir que:
- uma das suas dependências principais lançou uma nova versão.
- precisa de um pacote extra para treinar o seu modelo de aprendizagem automática ou preparar os seus dados.
- encontrou um pacote melhor e já não precisa do pacote mais antigo.
- a sua equipa construiu um pacote personalizado que precisa disponível na sua piscina Apache Spark.

Para disponibilizar código de terceiros ou construído localmente para as suas aplicações, pode instalar uma biblioteca numa das suas piscinas Apache Spark sem servidor ou sessão de portátil.
  
## <a name="default-installation"></a>Instalação predefinida
Apache Spark in Azure Synapse Analytics tem uma instalação completa de Anacondas mais bibliotecas extra. A lista completa de bibliotecas pode ser encontrada no [suporte da versão Apache Spark.](apache-spark-version-support.md) 

Quando uma instância Spark começa, estas bibliotecas serão automaticamente incluídas. Pacotes adicionais podem ser adicionados ao nível da piscina Spark ou ao nível da sessão.

## <a name="workspace-packages"></a>Pacotes de espaço de trabalho
Ao desenvolver aplicações ou modelos personalizados, a sua equipa pode desenvolver vários artefactos de código, como ficheiros de rodas ou frascos para embalar o seu código. 

No Synapse, os pacotes de espaço de trabalho podem ser personalizados ou ficheiros de rodas ou frascos privados. Você pode enviar estes pacotes para o seu espaço de trabalho e mais tarde atribuí-los para uma piscina de faíscas específica. Uma vez atribuídos, estes pacotes de espaço de trabalho são automaticamente instalados em todas as sessões de piscina Spark.

Para saber mais sobre como gerir bibliotecas de espaço de trabalho, visite os seguintes guias:

- [Pacotes de espaço de trabalho python (pré-visualização): ](./apache-spark-manage-python-packages.md#install-wheel-files) Faça upload dos ficheiros da roda Python como um pacote de espaço de trabalho e, mais tarde, adicione estes pacotes a piscinas apache spark sem servidor específicos.
- [Pacotes de espaço de trabalho Scala/Java (pré-visualização): ](./apache-spark-manage-scala-packages.md#workspace-packages) Carreque os ficheiros de frascos de Scala e Java como um pacote de espaço de trabalho e, mais tarde, adicione estes pacotes a piscinas apache spark sem servidor específicos.

## <a name="pool-packages"></a>Pacotes de piscina
Em alguns casos, você pode querer normalizar o conjunto de pacotes que são usados em uma determinada piscina Apache Spark. Esta normalização pode ser útil se os mesmos pacotes forem normalmente instalados por várias pessoas na sua equipa. 

Utilizando as capacidades de gestão da piscina Azure Synapse Analytics, pode configurar o conjunto padrão de bibliotecas que gostaria de instalar num determinado conjunto Apache Spark sem servidor. Estas bibliotecas estão instaladas em cima do tempo de funcionação da [base.](./apache-spark-version-support.md) 

Atualmente, a gestão da piscina é apenas suportada para Python. Para python, as piscinas Synapse Spark usam a Conda para instalar e gerir as dependências de pacotes Python. Ao especificar as suas bibliotecas ao nível da piscina, pode agora fornecer um requirements.txt ou um ambiente.yml. Este ficheiro de configuração de ambiente é usado sempre que uma instância Spark é criada a partir dessa piscina Spark. 

Para saber mais sobre estas capacidades, visite a documentação sobre a [gestão da piscina Python.](./apache-spark-manage-python-packages.md#pool-libraries)

> [!IMPORTANT]
> - Se a embalagem que está a instalar for grande ou demorar muito tempo a ser instalada, isto afeta o tempo de arranque da instância Spark.
> - Alterar a versão PySpark, Python, Scala/Java, .NET ou Spark não é suportado.
> - A instalação de pacotes de PyPI não é suportada em espaços de trabalho habilitados a DEP.

## <a name="session-scoped-packages"></a>Pacotes com âmbito de sessão
Muitas vezes, ao fazer análises interativas de dados ou machine learning, poderá descobrir que pretende experimentar pacotes mais recentes ou pode precisar de pacotes que ainda não estão disponíveis na sua piscina Apache Spark. Em vez de atualizar a configuração da piscina, os utilizadores podem agora usar pacotes com âmbito de sessão para adicionar, gerir e atualizar as dependências da sessão.

Os pacotes com âmbito de sessão permitem aos utilizadores definir as dependências dos pacotes no início da sessão. Quando instala um pacote com âmbito de sessão, apenas a sessão atual tem acesso às embalagens especificadas. Como resultado, estes pacotes de sessão não terão impacto em outras sessões ou empregos utilizando a mesma piscina Apache Spark. Além disso, estas bibliotecas são instaladas em cima dos pacotes de tempo de funcionaamento e de nível de piscina. 

Para saber mais sobre como gerir pacotes com âmbito de sessão, visite os seguintes guias de como fazer:

- [Pacotes de sessão python (pré-visualização):](./apache-spark-manage-python-packages.md) No início de uma sessão, forneça um *ambiente Conda.yml* para instalar pacotes Python adicionais de repositórios populares. 
- [Pacotes de sessão Scala/Java: ](./apache-spark-manage-scala-packages.md) No início da sessão, forneça uma lista de ficheiros de frascos para instalar ```%%configure``` utilizando .

## <a name="next-steps"></a>Passos seguintes
- Ver as bibliotecas padrão: [Suporte à versão Apache Spark](apache-spark-version-support.md)
