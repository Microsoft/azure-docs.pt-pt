---
title: Gerir bibliotecas Python para Apache Spark
description: Aprenda a adicionar e gerir bibliotecas Python usadas pela Apache Spark em Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 2d6ac02402414f096a46fec0340c3074d8e1784a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586646"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Gerir bibliotecas Python para Apache Spark em Azure Synapse Analytics

As bibliotecas fornecem código reutilizável que poderá querer incluir nos seus programas ou projetos. 

Você pode precisar atualizar o seu ambiente de piscina Apache Spark sem servidor por várias razões. Por exemplo, pode descobrir que:
- uma das suas dependências principais acabou de lançar uma nova versão.
- precisa de um pacote extra para treinar o seu modelo de aprendizagem automática ou preparar os seus dados.
- encontrou um pacote melhor e já não precisa do pacote mais antigo.

Para disponibilizar código de terceiros ou construído localmente para as suas aplicações, pode instalar uma biblioteca numa das suas piscinas Apache Spark sem servidor ou sessão de portátil. Neste artigo, vamos cobrir como você pode gerir bibliotecas Python através da sua piscina Apache Spark sem servidor.

## <a name="default-installation"></a>Instalação predefinida
Apache Spark in Azure Synapse Analytics tem um conjunto completo de bibliotecas para engenharia de dados comuns, preparação de dados, machine learning e tarefas de visualização de dados. A lista completa de bibliotecas pode ser encontrada no [suporte da versão Apache Spark.](apache-spark-version-support.md) 

Quando uma instância Spark começa, estas bibliotecas serão automaticamente incluídas. Píton extra e pacotes personalizados podem ser adicionados ao nível da piscina spark e da sessão.

## <a name="pool-libraries"></a>Bibliotecas de piscinas
Uma vez identificadas as bibliotecas Python que gostaria de usar para a sua aplicação Spark, pode instalá-las numa piscina spark. Bibliotecas ao nível da piscina estão disponíveis para todos os cadernos e empregos que estão funcionando na piscina.

Há duas maneiras primárias de instalar uma biblioteca num cluster:
-  Instale uma biblioteca de espaço de trabalho que tenha sido carregada como um pacote de espaço de trabalho.
-  Forneça uma especificação ambiental *requirements.txt* ou *Conda ambiente.yml* para instalar pacotes de repositórios como PyPI, Conda-Forge, entre outros.

> [!IMPORTANT]
> - Se a embalagem que está a instalar for grande ou demorar muito tempo a ser instalada, isto afeta o tempo de arranque da instância Spark.
> - Alterar a versão PySpark, Python, Scala/Java, .NET ou Spark não é suportado.
> - A instalação de pacotes de repositórios externos como pyPI, Conda-Forge ou os canais Conda predefinidos não é suportado em espaços de trabalho habilitados a DEP.

### <a name="install-python-packages"></a>Instalar pacotes Python
Os pacotes Python podem ser instalados a partir de repositórios como PyPI e Conda-Forge fornecendo um ficheiro de especificação ambiental. 

#### <a name="environment-specification-formats"></a>Formatos de especificação ambiental

##### <a name="pip-requirementstxt"></a>PIP requirements.txt
Um ficheiro *requirements.txt* (saída do `pip freeze` comando) pode ser usado para melhorar o ambiente. Quando uma piscina é atualizada, os pacotes listados neste ficheiro são descarregados a partir de PyPI. As dependências completas são então em cache e guardadas para posterior reutilização da piscina. 

O seguinte corte mostra o formato do ficheiro de requisitos. O nome do pacote PyPI está listado juntamente com uma versão exata. Este ficheiro segue o formato descrito na documentação de referência de congelamento de [pip.](https://pip.pypa.io/en/stable/reference/pip_freeze/) 

Este exemplo fixa uma versão específica. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>Formato YML (pré-visualização)
Além disso, você também pode fornecer um arquivo *environment.yml* para atualizar o ambiente da piscina. Os pacotes listados neste ficheiro são descarregados dos canais Conda padrão, Conda-Forge e PyPI. Pode especificar outros canais ou remover os canais predefinidos utilizando as opções de configuração.

Este exemplo especifica os canais e as dependências Conda/PyPI. 

```
name: stats2
channels:
- defaults
dependencies:
- bokeh
- numpy
- pip:
  - matplotlib
  - koalas==1.7.0
```
Para obter detalhes sobre a criação de um ambiente a partir deste ficheiro environment.yml, consulte [criar um ambiente a partir de um ficheiro environment.yml](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually).

#### <a name="update-python-packages"></a>Atualizar pacotes Python
Uma vez identificado o ficheiro de especificações ambientais ou o conjunto de bibliotecas que pretende instalar na piscina Spark, pode atualizar as bibliotecas da piscina Spark navegando para o Azure Synapse Studio ou portal Azure. Aqui, pode fornecer a especificação ambiental e selecionar as bibliotecas do espaço de trabalho para instalar. 

Uma vez guardadas as alterações, uma função Spark executará a instalação e cache o ambiente resultante para posterior reutilização. Uma vez concluído o trabalho, novos trabalhos spark ou sessões de portátil usarão as bibliotecas de piscinas atualizadas. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Gerir pacotes do Azure Synapse Studio ou do portal Azure
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
   
2. Faça o upload do ficheiro de configuração do ambiente utilizando o seletor de ficheiros na secção  **Pacotes** da página.
3. Também pode selecionar **pacotes** adicionais de espaço de trabalho para adicionar à sua piscina. 
4. Assim que guardar as alterações, será ativado um trabalho do sistema para instalar e cache nas bibliotecas especificadas. Este processo ajuda a reduzir o tempo geral de arranque da sessão. 
5. Uma vez concluído o trabalho com sucesso, todas as novas sessões irão recolher as bibliotecas de piscinas atualizadas.

> [!IMPORTANT]
> Ao selecionar a opção para **forçar novas definições,** terminará todas as sessões atuais para o pool Spark selecionado. Uma vez terminadas as sessões, terá de esperar pelo recomeço da piscina. 
>
> Se esta definição não for verificada, terá de esperar que a sessão atual de Faísca termine ou pare manualmente. Uma vez terminada a sessão, terá de deixar a piscina recomeçar.


##### <a name="track-installation-progress-preview"></a>Progresso da instalação da pista (pré-visualização)
Um trabalho de Spark reservado é iniciado cada vez que uma piscina é atualizada com um novo conjunto de bibliotecas. Este trabalho de faísca ajuda a monitorizar o estado da instalação da biblioteca. Se a instalação falhar devido a conflitos de bibliotecas ou outros problemas, a piscina Spark reverterá para o seu estado anterior ou padrão. 

Além disso, os utilizadores também podem inspecionar os registos de instalação para identificar conflitos de dependência ou ver quais as bibliotecas instaladas durante a atualização da piscina.

Para ver estes registos:
1. Navegue para a lista de aplicações Spark no **separador Monitor.** 
2. Selecione o trabalho de aplicação spark do sistema que corresponde à sua atualização de piscina. Estes trabalhos de sistema funcionam sob o título *de SystemReservedJob-LibraryManagement.*
   ![Screenshot que destaca o trabalho reservado ao sistema na biblioteca.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Ver trabalho de biblioteca de sistema")
3. Mude para ver o **controlador** e os registos **de stdout.** 
4. Dentro dos resultados, você verá os registos relacionados com a instalação das suas dependências.
    ![Screenshot que destaca o sistema reservado resultados de trabalho na biblioteca.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Ver o progresso do trabalho na biblioteca do sistema")

## <a name="install-wheel-files"></a>Instalar ficheiros de rodas
Os ficheiros das rodas python são uma forma comum de embalar bibliotecas Python. Dentro do Azure Synapse Analytics, os utilizadores podem enviar os seus ficheiros de rodas para uma localização bem conhecida, a conta de armazenamento de datas Azure ou carregar usando a interface de pacotes de espaço de trabalho Azure Synapse.

### <a name="workspace-packages-preview"></a>Pacotes de espaço de trabalho (pré-visualização)
Os pacotes de espaço de trabalho podem ser ficheiros de rodas personalizados ou privados. Você pode enviar estes pacotes para o seu espaço de trabalho e mais tarde atribuí-los para uma piscina de faíscas específica.

Para adicionar pacotes de espaço de trabalho:
1. Navegue para o **separador Pacotes Manage**  >  **Workspace.**
2. Faça o upload dos ficheiros das rodas utilizando o seletor de ficheiros.
3. Uma vez que os ficheiros tenham sido enviados para o espaço de trabalho Azure Synapse, pode adicionar estes pacotes a uma determinada piscina Apache Spark.

![Screenshot que destaca pacotes de espaço de trabalho.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Ver pacotes de espaço de trabalho")

>[!WARNING]
>- Dentro do Azure Synapse, uma piscina Apache Spark pode aproveitar bibliotecas personalizadas que são carregadas como Pacotes Workspace ou carregadas dentro de um conhecido caminho de armazenamento do Lago de Dados Azure. No entanto, ambas as opções não podem ser utilizadas simultaneamente dentro da mesma piscina Apache Spark. Se as embalagens forem fornecidas utilizando ambos os métodos, apenas serão instalados os ficheiros das rodas especificados na lista de pacotes workspace. 
>
>- Uma vez que os Pacotes Workspace (pré-visualização) são usados para instalar pacotes numa determinada piscina Apache Spark, existe uma limitação de que já não pode especificar pacotes utilizando o caminho da conta de armazenamento na mesma piscina.  

### <a name="storage-account"></a>Conta de armazenamento
Os pacotes de rodas personalizados podem ser instalados na piscina Apache Spark, enviando todos os ficheiros das rodas para a conta Azure Data Lake Storage (Gen2) que está ligada ao espaço de trabalho da Synapse. 

Os ficheiros devem ser enviados para o seguinte caminho no recipiente padrão da conta de armazenamento: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> Em alguns casos, poderá ser necessário criar o caminho do ficheiro com base na estrutura acima se já não existir. Por exemplo, pode ser necessário adicionar a ```python``` pasta dentro da pasta se ela já não ```libraries``` existir.

> [!IMPORTANT]
> Para instalar bibliotecas personalizadas utilizando o método de armazenamento de dados Azure DataLake, tem de ter as permissões **do Fornecedor de Dados blob de armazenamento** ou do proprietário do armazenamento **na** conta primária de Armazenamento Gen2 que está ligada ao espaço de trabalho Azure Synapse Analytics.


## <a name="session-scoped-packages-preview"></a>Pacotes com âmbito de sessão (pré-visualização)
Além dos pacotes ao nível da piscina, também pode especificar bibliotecas com âmbito de sessão no início de uma sessão de cadernos.  As bibliotecas com âmbito de sessão permitem especificar e utilizar ambientes piton personalizados dentro de uma sessão de portátil. 

Ao utilizar bibliotecas com âmbito de sessão, é importante ter em mente os seguintes pontos:
   - Quando instala bibliotecas com âmbito de sessão, apenas o caderno atual tem acesso às bibliotecas especificadas. 
   - Estas bibliotecas não terão impacto em outras sessões ou empregos utilizando a mesma piscina Spark. 
   - Estas bibliotecas estão instaladas em cima das bibliotecas de funcionação e de nível de piscina. 
   - As bibliotecas de cadernos terão a maior precedência.

Para especificar pacotes com âmbito de sessão:
1.  Navegue até à piscina spark selecionada e certifique-se de que tem bibliotecas ao nível da sessão ativadas.  Pode ativar esta definição navegando no **separador Pacotes de** Conjuntos de  >  **Conjuntos De Faíscas Do Apache.**  >   ![](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Ativar pacotes de sessão")
2.  Uma vez aplicada a definição, pode abrir um caderno e selecionar Pacotes **de Sessão** >  **de Configuração**.
  ![Especifique pacotes de sessão.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Configuração da sessão de atualização")
3.  Aqui, pode carregar um ficheiro Conda *environment.yml* para instalar ou atualizar pacotes dentro de uma sessão. Assim que iniciar a sessão, as bibliotecas especificadas serão instaladas. Uma vez terminada a sessão, estas bibliotecas deixarão de estar disponíveis, uma vez que são específicas da sua sessão.

## <a name="verify-installed-libraries"></a>Verificar bibliotecas instaladas
Para verificar se as versões corretas das bibliotecas corretas são instaladas a partir de PyPI, execute o seguinte código:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
Em alguns casos, para ver as versões do pacote da Conda, poderá ter de inspecionar a versão do pacote individualmente.

## <a name="next-steps"></a>Passos seguintes
- Ver as bibliotecas padrão: [Suporte à versão Apache Spark](apache-spark-version-support.md)
- Erros de instalação da biblioteca de resolução de [problemas: Erros na biblioteca de resolução de problemas](apache-spark-troubleshoot-library-errors.md)
- Crie um canal Conda privado utilizando a sua Conta de Armazenamento do Lago de Dados Azure: [canais privados Conda](./spark/../apache-spark-custom-conda-channel.md)
