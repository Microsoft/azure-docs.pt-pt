---
title: Gerir bibliotecas Scala & Java para Apache Spark
description: Saiba como adicionar e gerir bibliotecas Scala e Java em Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: c70ecc4fc5469d728bc12d47024585ccf00ff98e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098711"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>Gerir pacotes Scala e Java para Apache Spark em Azure Synapse Analytics

As bibliotecas fornecem código reutilizável que poderá querer incluir nos seus programas ou projetos. 

Pode precisar de atualizar o ambiente de piscina Apache Spark sem servidor por várias razões. Por exemplo, pode descobrir que:
- uma das suas dependências principais lançou uma nova versão.
- precisa de um pacote extra para treinar o seu modelo de aprendizagem automática ou preparar os seus dados.
- encontrou um pacote melhor e já não precisa do pacote mais antigo.

Para disponibilizar código de terceiros ou construído localmente para as suas aplicações, pode instalar uma biblioteca numa das suas piscinas Apache Spark sem servidor ou sessão de portátil. Neste artigo, vamos cobrir como você pode gerir pacotes Scala e Java.

## <a name="default-installation"></a>Instalação predefinida
Apache Spark in Azure Synapse Analytics tem um conjunto completo de bibliotecas para engenharia de dados comuns, preparação de dados, machine learning e tarefas de visualização de dados. A lista completa de bibliotecas pode ser encontrada no [suporte da versão Apache Spark.](apache-spark-version-support.md) 

Quando uma instância Spark começa, estas bibliotecas serão automaticamente incluídas. Pacotes Extra Scala/Java podem ser adicionados no pool spark e nível de sessão.

## <a name="workspace-packages"></a>Pacotes de espaço de trabalho
Os pacotes do espaço de trabalho podem ser ficheiros personalizados ou privados de frascos. Você pode enviar estes pacotes para o seu espaço de trabalho e mais tarde atribuí-los para uma piscina de faíscas específica.

Para adicionar pacotes de espaço de trabalho:
1. Navegue para o **separador Pacotes Manage**  >  **Workspace.**
2. Faça o upload dos ficheiros do frasco utilizando o seletor de ficheiros.
3. Uma vez que os ficheiros tenham sido enviados para o espaço de trabalho Azure Synapse, pode adicionar estes ficheiros de frascos a uma determinada piscina Apache Spark.

![Screenshot que destaca pacotes de espaço de trabalho.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Ver pacotes de espaço de trabalho")

## <a name="pool-libraries"></a>Bibliotecas de piscinas
Uma vez identificados os pacotes Scala e Java que gostaria de utilizar para a sua aplicação Spark, pode instalá-los numa piscina Spark. Bibliotecas ao nível da piscina estão disponíveis para todos os cadernos e empregos que estão funcionando na piscina.

Pode atualizar as bibliotecas da piscina Spark navegando para o Azure Synapse Studio ou portal Azure. Aqui, pode selecionar as bibliotecas do espaço de trabalho para instalar. 

Após a salvação das alterações, uma função Spark executará a instalação e cache o ambiente resultante para posterior reutilização. Uma vez concluído o trabalho, novos trabalhos spark ou sessões de portátil usarão as bibliotecas de piscinas atualizadas. 

> [!IMPORTANT]
> - Se a embalagem que está a instalar for grande ou demorar muito tempo a ser instalada, isto afeta o tempo de arranque da instância Spark.
> - Alterar a versão PySpark, Python, Scala/Java, .NET ou Spark não é suportado.

#### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Gerir pacotes do Azure Synapse Studio ou do portal Azure
As bibliotecas de piscinas spark podem ser geridas a partir do Azure Synapse Studio ou do portal Azure. 

Para atualizar ou adicionar bibliotecas a uma piscina Spark:
1. Navegue para o seu espaço de trabalho Azure Synapse Analytics a partir do portal Azure.

    Se estiver a atualizar a partir do **portal Azure:**

    - Na secção **de recursos synapse,** selecione o separador **piscinas Apache Spark** e selecione uma piscina Spark da lista.
     
    - Selecione as **Embalagens** da secção **Definições** da piscina Spark.
  
    ![Screenshot que realça o botão de ficheiro de configuração do ambiente de carregamento.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Adicionar bibliotecas Python")
   
    Se estiver a atualizar-se a partir do **Estúdio Synapse:**
    - **Selecione Gerir** a partir do painel de navegação principal e, em seguida, selecione **as piscinas Apache Spark**.

    - Selecione a secção **Pacotes** para uma piscina de faíscas específica.
    ![Screenshot que realça a opção de configuração do ambiente de upload a partir do estúdio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Adicionar bibliotecas Python do Studio")
   
2. Para adicionar ficheiros Jar, navegue na secção **de pacotes workspace** para adicionar à sua piscina. 
3. Assim que guardar as alterações, será ativado um trabalho do sistema para instalar e cache nas bibliotecas especificadas. Este processo ajuda a reduzir o tempo geral de arranque da sessão. 
4. Uma vez concluído o trabalho com sucesso, todas as novas sessões irão recolher as bibliotecas de piscinas atualizadas.

> [!IMPORTANT]
> Ao selecionar a opção para **forçar novas definições,** terminará todas as sessões atuais para o pool Spark selecionado. Uma vez terminadas as sessões, terá de esperar pelo recomeço da piscina. 
>
> Se esta definição não for verificada, terá de esperar que a sessão atual de Faísca termine ou pare manualmente. Uma vez terminada a sessão, terá de deixar a piscina recomeçar.

#### <a name="track-installation-progress-preview"></a>Progresso da instalação da pista (pré-visualização)
Um trabalho de Spark reservado é iniciado cada vez que uma piscina é atualizada com um novo conjunto de bibliotecas. Este trabalho de faísca ajuda a monitorizar o estado da instalação da biblioteca. Se a instalação falhar devido a conflitos de bibliotecas ou outros problemas, a piscina Spark reverterá para o seu estado anterior ou padrão. 

Além disso, os utilizadores também podem inspecionar os registos de instalação para identificar conflitos de dependência ou ver quais as bibliotecas instaladas durante a atualização da piscina.

Para ver estes registos:
1. Navegue para a lista de aplicações Spark no **separador Monitor.** 
2. Selecione o trabalho de aplicação spark do sistema que corresponde à sua atualização de piscina. Estes trabalhos de sistema funcionam sob o título *de SystemReservedJob-LibraryManagement.*
   ![Screenshot que destaca o trabalho reservado ao sistema na biblioteca.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Ver trabalho de biblioteca de sistema")
3. Mude para ver o **controlador** e os registos **de stdout.** 
4. Dentro dos resultados, verá os registos relacionados com a instalação dos seus pacotes.
    ![Screenshot que destaca o sistema reservado resultados de trabalho na biblioteca.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Ver o progresso do trabalho na biblioteca do sistema")

## <a name="session-scoped-libraries"></a>Bibliotecas com âmbito de sessão 
Além das bibliotecas ao nível da piscina, também pode especificar bibliotecas com âmbito de sessão no início de uma sessão de cadernos.  As bibliotecas com âmbito de sessão permitem especificar e utilizar pacotes de frascos exclusivamente dentro de uma sessão de portátil. 

Ao utilizar bibliotecas com âmbito de sessão, é importante ter em mente os seguintes pontos:
   - Quando instala bibliotecas com âmbito de sessão, apenas o caderno atual tem acesso às bibliotecas especificadas. 
   - Estas bibliotecas não terão impacto em outras sessões ou empregos utilizando a mesma piscina Spark. 
   - Estas bibliotecas estão instaladas em cima das bibliotecas de funcionação e de nível de piscina. 
   - As bibliotecas de cadernos terão a maior precedência.

Para especificar pacotes Java ou Scala com sessão, pode utilizar a ```%%configure``` opção:

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

Recomendamos que faça a configuração %% no início do seu caderno. Pode consultar este [documento](https://github.com/cloudera/livy#request-body) para obter a lista completa de parâmetros válidos.

## <a name="next-steps"></a>Passos seguintes
- Ver as bibliotecas padrão: [Suporte à versão Apache Spark](apache-spark-version-support.md)
- Erros de instalação da biblioteca de resolução de [problemas: Erros na biblioteca de resolução de problemas](apache-spark-troubleshoot-library-errors.md)
