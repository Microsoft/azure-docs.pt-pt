---
title: Gerir bibliotecas para Apache Spark em Azure Synapse Analytics
description: Saiba como adicionar e gerir bibliotecas usadas pela Apache Spark em Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 8d478b35b702e02f303358972526c091ceb3657e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95917130"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Gerir bibliotecas para Apache Spark em Azure Synapse Analytics

As bibliotecas fornecem código reutilizável que poderá querer incluir nos seus programas ou projetos. Para disponibilizar códigos de terceiros ou construídos localmente para as suas aplicações, pode instalar uma biblioteca numa das suas Piscinas Apache Spark sem servidor (pré-visualização). Uma vez instalada uma biblioteca para uma piscina Spark, está disponível para todas as sessões usando a mesma piscina. 

## <a name="before-you-begin"></a>Before you begin
- Para instalar e atualizar bibliotecas, tem de ter as permissões **do Storage Blob Data Contributor** ou do Proprietário de **Dados blob de armazenamento** na conta primária de Armazenamento Gen2 que está ligada ao espaço de trabalho Azure Synapse Analytics.
  
## <a name="default-installation"></a>Instalação predefinida
Apache Spark in Azure Synapse Analytics tem uma instalação completa de Anacondas mais bibliotecas adicionais. A lista completa de bibliotecas pode ser encontrada no [suporte da versão Apache Spark.](apache-spark-version-support.md) 

Quando uma instância Spark começa, estas bibliotecas serão automaticamente incluídas. Os pacotes adicionais python e personalizados podem ser adicionados ao nível da piscina Spark (pré-visualização).


## <a name="manage-python-packages"></a>Gerir pacotes Python
Uma vez identificadas as bibliotecas que gostaria de utilizar para a sua aplicação Spark, pode instalá-las numa piscina Spark (pré-visualização). 

 Um ficheiro *requirements.txt* (saída do `pip freeze` comando) pode ser usado para atualizar o ambiente virtual. Os pacotes listados neste ficheiro para instalação ou upgrade são descarregados a partir de PyPi no momento do arranque da piscina. Este ficheiro de requisitos é usado sempre que uma instância Spark é criada a partir dessa piscina Spark.

> [!IMPORTANT]
> - Se a embalagem que está a instalar for grande ou demorar muito tempo a ser instalada, isto afeta o tempo de arranque da instância Spark.
> - Os pacotes que requerem suporte do compilador no momento da instalação, como o CCG, não são suportados.
> - Os pacotes não podem ser desclassificados, apenas adicionados ou atualizados.
> - Para instalar bibliotecas, tem de ter permissões do Fornecedor de dados blob de armazenamento ou do proprietário de dados da blob de armazenamento na conta primária de Armazenamento Gen2 ligada ao espaço de trabalho synapse.

### <a name="requirements-format"></a>Formato de requisitos

O seguinte corte mostra o formato do ficheiro de requisitos. O nome do pacote PyPi está listado juntamente com uma versão exata. Este ficheiro segue o formato descrito na documentação de referência de congelamento de [pip.](https://pip.pypa.io/en/stable/reference/pip_freeze/) Este exemplo fixa uma versão específica. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Instalar pacotes Python
À medida que desenvolve a sua aplicação Spark, poderá descobrir que precisa de atualizar ou instalar novas bibliotecas. As bibliotecas podem ser atualizadas durante ou após a criação da piscina.

#### <a name="install-packages-during-pool-creation"></a>Instalar pacotes durante a criação da piscina
Para instalar bibliotecas numa piscina spark (pré-visualização) durante a criação da piscina:
   
1. Navegue para o seu espaço de trabalho Azure Synapse Analytics a partir do portal Azure.
   
2. Selecione **Criar a piscina Apache Spark** e, em seguida, selecione o **separador Definições Adicionais.** 
   
3. Faça o upload do ficheiro de configuração do ambiente utilizando o seletor de ficheiros na secção **Pacotes** da página. 
   
    ![Adicione bibliotecas Python durante a criação de piscinas](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Adicionar bibliotecas Python")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Instalar pacotes a partir do Espaço de Trabalho Synapse
Para atualizar ou adicionar bibliotecas adicionais a uma piscina Spark (pré-visualização) do portal Azure Synapse Analytics:

1.  Navegue para o seu espaço de trabalho Azure Synapse Analytics a partir do portal Azure.
   
2.  Lance o seu espaço de trabalho Azure Synapse Analytics a partir do portal Azure.

3.  **Selecione Gerir** a partir do painel de navegação principal e, em seguida, selecione **as piscinas Apache Spark**.
   
4. Selecione uma única piscina Spark e carreque o ficheiro de configuração do ambiente utilizando o seletor de ficheiros na secção  **Pacotes** da página.

    ![Adicione bibliotecas Python em sinapse](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png)
   
#### <a name="install-packages-from-the-azure-portal"></a>Instalar pacotes a partir do portal Azure
Para instalar uma biblioteca numa piscina spark (pré-visualização) diretamente do portal Azure:
   
 1. Navegue para o seu espaço de trabalho Azure Synapse Analytics a partir do portal Azure.
   
 2. Na secção **de recursos synapse,** selecione o separador **piscinas Apache Spark** e selecione uma piscina Spark da lista.
   
 3. Selecione **Pacotes** da secção **Definições** da piscina Spark. 

 4. Faça o upload do ficheiro de configuração do ambiente utilizando o seletor de ficheiros.

    ![Screenshot que realça o botão de ficheiro configurar ambiente upload.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Adicionar bibliotecas Python")

### <a name="verify-installed-libraries"></a>Verificar bibliotecas instaladas

Para verificar se as versões corretas das bibliotecas corretas são instaladas, executar o seguinte código

```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
### <a name="update-python-packages"></a>Atualizar pacotes Python
Os pacotes podem ser adicionados ou modificados a qualquer momento entre as sessões. Quando um novo ficheiro de configuração de pacote é carregado, isto substituirá os pacotes e versões existentes.  

Para atualizar ou desinstalar uma biblioteca:
1. Navegue para o seu espaço de trabalho Azure Synapse Analytics. 

2. Utilizando o portal Azure ou o espaço de trabalho Azure Synapse, selecione a **piscina Apache Spark** que pretende atualizar.

3. Navegue na secção **Pacotes** e carregue um novo ficheiro de configuração ambiental
   
4. Assim que guardar as alterações, terá de terminar as sessões ativas e deixar a piscina reiniciar. Opcionalmente, pode forçar as sessões ativas a terminar selecionando a caixa de verificação para **forçar novas definições**.

    ![Adicionar bibliotecas Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Adicionar bibliotecas Python")
   

> [!IMPORTANT]
> Ao selecionar a opção para **forçar novas definições,** terminará todas as sessões atuais para o pool Spark selecionado. Uma vez terminadas as sessões, terá de esperar pelo recomeço da piscina. 
>
> Se esta definição não for verificada, terá de esperar que a sessão atual de Faísca termine ou pare manualmente. Uma vez terminada a sessão, terá de deixar a piscina recomeçar. 


## <a name="manage-a-python-wheel"></a>Gerir uma Roda Python

### <a name="install-a-custom-wheel-file"></a>Instale um ficheiro de roda personalizado
Pacotes de rodas construídos personalizados podem ser instalados na piscina Apache Spark, enviando todos os ficheiros das rodas para a conta Azure Data Lake Storage (Gen2) que está ligada ao espaço de trabalho da Synapse. 

Os ficheiros devem ser enviados para o seguinte caminho no recipiente padrão da conta de armazenamento: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!IMPORTANT]
>Os pacotes personalizados podem ser adicionados ou modificados entre as sessões. No entanto, terá de esperar pela piscina e pela sessão para reiniciar para ver o pacote atualizado.

## <a name="next-steps"></a>Passos seguintes
- Ver as bibliotecas padrão: [Suporte à versão Apache Spark](apache-spark-version-support.md)
