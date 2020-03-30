---
title: Como usar hudson com armazenamento Blob / Microsoft Docs
description: Descreve como usar Hudson com armazenamento Azure Blob como um repositório para construir artefactos.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: a89439f49dd53f09d5cd40be0bf2e4981e9235d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201390"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Utilização do Armazenamento Azure com uma solução de Integração Contínua Hudson
## <a name="overview"></a>Descrição geral
As seguintes informações mostram como usar o armazenamento Blob como um repositório de artefactos de construção criados por uma solução de Integração Contínua (CI) de Hudson, ou como fonte de ficheiros descarregados para serem usados num processo de construção. Um dos cenários em que você acha isto útil é quando você está codificando em um ambiente de desenvolvimento ágil (usando Java ou outras línguas), as construções estão funcionando com base na integração contínua, e você precisa de um repositório para seus artefactos de construção, para que você possa, por exemplo, partilhe-os com outros membros da organização, seus clientes, ou mantenha um arquivo.  Outro cenário é quando o seu próprio trabalho de construção requer outros ficheiros, por exemplo, dependências para descarregar como parte da entrada de construção.

Neste tutorial, estará a utilizar o plugin de Armazenamento Azure para o CI Hudson disponibilizado pela Microsoft.

## <a name="introduction-to-hudson"></a>Introdução a Hudson
A Hudson permite a integração contínua de um projeto de software, permitindo que os desenvolvedores integrem facilmente as suas alterações de código e tenham construído si de forma automática e frequente, aumentando assim a produtividade dos desenvolvedores. As construções são versonizadas e os artefactos de construção podem ser enviados para vários repositórios. Este artigo mostrará como usar o armazenamento Azure Blob como o repositório dos artefactos de construção. Também mostrará como descarregar dependências do armazenamento do Azure Blob.

Mais informações sobre Hudson podem ser encontradas no [Meet Hudson.](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)

## <a name="benefits-of-using-the-blob-service"></a>Benefícios da utilização do serviço Blob
Os benefícios de usar o serviço Blob para acolher os seus artefactos ágeis de construção de desenvolvimento incluem:

* Alta disponibilidade dos seus artefactos de construção e/ou dependências transferíveis.
* Desempenho quando a sua solução Hudson CI envia os seus artefactos de construção.
* Desempenho quando os seus clientes e parceiros descarregam os seus artefactos de construção.
* Controlo sobre as políticas de acesso ao utilizador, com uma escolha entre acesso anónimo, acesso partilhado baseado em expiração, acesso privado, etc.

## <a name="prerequisites"></a>Pré-requisitos
Necessitará do seguinte para utilizar o serviço Blob com a sua solução Hudson CI:

* Uma solução de Integração Contínua Hudson.
  
    Se não tiver atualmente uma solução CI Hudson, pode executar uma solução CI Hudson utilizando a seguinte técnica:
  
  1. Numa máquina ativada por Java, [descarregue o ficheiro Hudson WAR](https://www.eclipse.org/hudson/download.php).
  2. Num pedido de comando que é aberto à pasta que contém a Guerra de Hudson, executar a Guerra de Hudson. Por exemplo, se descarregou a versão 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. No seu navegador, abra. `http://localhost:8080/` Isto vai abrir o tablier hudson.
  4. Após a primeira utilização de Hudson, complete a configuração inicial em `http://localhost:8080/`.
  5. Depois de completar a configuração inicial, cancele a instância de execução da Guerra `http://localhost:8080/`de Hudson, reinicie a Guerra de Hudson e reabra o painel de instrumentos hudson, que utilizará para instalar e configurar o plugin de Armazenamento Azure.
     
      Enquanto uma solução típica de Hudson CI seria criada para funcionar como um serviço, executar a guerra de Hudson na linha de comando será suficiente para este tutorial.
* Uma conta do Azure. Pode inscrever-se numa conta <https://www.azure.com>Azure em .
* Uma conta de armazenamento do Azure. Se ainda não tiver uma conta de armazenamento, pode criar uma utilizando os passos da [Conta Criar uma Conta](../common/storage-account-create.md)de Armazenamento .
* A familiaridade com a solução CI hudson é recomendada, mas não é necessária, uma vez que o seguinte conteúdo usará um exemplo básico para mostrar-lhe os passos necessários ao usar o serviço Blob como um repositório para artefactos de construção de CI Hudson.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Como usar o serviço Blob com hudson CI
Para utilizar o serviço Blob com a Hudson, terá de instalar o plugin de Armazenamento Azure, configurar o plugin para utilizar a sua conta de armazenamento e, em seguida, criar uma ação pós-construção que carregue os seus artefactos de construção para a sua conta de armazenamento. Estes passos são descritos nas seguintes secções.

## <a name="how-to-install-the-azure-storage-plugin"></a>Como instalar o plugin de armazenamento Azure
1. Dentro do painel de hudson, clique em **Manage Hudson**.
2. Na página **Manage Hudson,** clique em **Gerir Plugins**.
3. Clique no separador **Disponível.**
4. Clique em **Outros**.
5. Na secção **Descarregadores** de Artefactos, selecione **plugin de armazenamento Microsoft Azure**.
6. Clique em **Instalar**.
7. Depois de a instalação estar concluída, reinicie o Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Como configurar o plugin de Armazenamento Azure para usar a sua conta de armazenamento
1. Dentro do painel de hudson, clique em **Manage Hudson**.
2. Na página **Manage Hudson,** clique em **Configurar sistema**.
3. Na secção de configuração da conta de **armazenamento do Microsoft Azure:**
   
    a. Insira o nome da sua conta de armazenamento, que pode obter no [portal Azure](https://portal.azure.com).
   
    b. Introduza a chave da sua conta de armazenamento, também obtida a partir do [portal Azure.](https://portal.azure.com)
   
    c. Utilize o valor padrão para **o URL de ponto final do serviço Blob** se estiver a utilizar a nuvem azure global. Se estiver a utilizar uma nuvem Azure diferente, utilize o ponto final conforme especificado no [portal Azure](https://portal.azure.com) para a sua conta de armazenamento.
   
    d. Clique em **validar credenciais** de armazenamento para validar a sua conta de armazenamento.
   
    e. [Opcional] Se tiver contas de armazenamento adicionais que deseja disponibilizar ao seu CI Hudson, clique em **Adicionar mais contas**de armazenamento .
   
    f. Clique em **Guardar** para guardar as suas definições.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Como criar uma ação pós-construção que carregue os seus artefactos de construção para a sua conta de armazenamento
Para efeitos de instrução, primeiro teremos de criar um trabalho que crie vários ficheiros e, em seguida, adicione a ação pós-construção para fazer o upload dos ficheiros para a sua conta de armazenamento.

1. Dentro do painel de Hudson, clique em **New Job**.
2. Nomeie o trabalho **MyJob,** clique em **Construir um trabalho de software de estilo livre,** e depois clique em **OK**.
3. Na secção **Construção** da configuração de trabalho, clique em Adicionar passo de **construção** e escolha **executar comando de lote windows**.
4. No **Comando,** utilize os seguintes comandos:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. Na secção **de Ações Pós-Construção** da configuração do trabalho, clique em **carregar artefactos para armazenamento Microsoft Azure Blob**.
6. Para obter o nome da conta de **armazenamento,** selecione a conta de armazenamento a utilizar.
7. Para **designação de contentores,** especifique o nome do recipiente. (O recipiente será criado se ainda não existir quando os artefactos de construção forem carregados.) Pode utilizar variáveis ambientais, por isso, para este **exemplo, introduza ${JOB_NAME}** como o nome do recipiente.
   
    **Dica**
   
    Abaixo da secção **Comando** onde introduziu um script para executar o comando do **lote do Windows** é uma ligação às variáveis ambientais reconhecidas por Hudson. Clique nesse link para saber os nomes e descrições variáveis do ambiente. Variáveis ambientais que contenham caracteres especiais, como a **variável ambiente BUILD_URL,** não são permitidas como um nome de contentor ou um caminho virtual comum.
8. Clique em **Tornar o novo contentor público por padrão** para este exemplo. (Se quiser utilizar um recipiente privado, terá de criar uma assinatura de acesso partilhado para permitir o acesso. Isto está fora do âmbito deste artigo. Pode saber mais sobre assinaturas de acesso partilhado na Utilização de Assinaturas de [Acesso Partilhado (SAS)](storage-sas-overview.md).)
9. [Opcional] Clique em **limpar o recipiente antes** de carregar se quiser que o recipiente seja limpo do conteúdo antes de serem carregados artefactos de construção (deixe-o descontrolado se não quiser limpar o conteúdo do recipiente).
10. Para **que a Lista de Artefactos carregue,** introduza **texto/*.txt**.
11. Para o **caminho virtual comum para artefactos carregados,** insira **${BUILD\_ID}/${BUILD\_NUMBER}**.
12. Clique em **Guardar** para guardar as suas definições.
13. No painel hudson, clique em **Build Now** para executar **MyJob**. Examine a saída da consola para obter o estado. As mensagens de estado para o Armazenamento Azure serão incluídas na saída da consola quando a ação pós-construção começar a carregar artefactos de construção.
14. Após a conclusão bem sucedida do trabalho, você pode examinar os artefactos de construção abrindo a bolha pública.
    
    a. Inicie sessão no [Portal do Azure](https://portal.azure.com).
    
    b. Clique em **Armazenamento**.
    
    c. Clique no nome da conta de armazenamento que usou para Hudson.
    
    d. Clique em **Contentores**.
    
    e. Clique no recipiente chamado **myjob**, que é a versão minúscula do nome de trabalho que atribuiu quando criou o emprego de Hudson. Os nomes dos contentores e os nomes de bolhas são minúsculos (e sensíveis a casos) no Armazenamento Azure. Dentro da lista de bolhas para o contentor chamado **myjob** você deve ver **hello.txt** e **date.txt**. Copie o URL para qualquer um destes itens e abra-o no seu navegador. Verá o ficheiro de texto que foi enviado como um artefacto de construção.

Apenas uma ação pós-construção que envia artefactos para o armazenamento do Azure Blob pode ser criada por trabalho. A única ação pós-construção para enviar artefactos para o armazenamento do Azure Blob pode especificar diferentes ficheiros (incluindo wildcards) e caminhos para ficheiros dentro **da Lista de Artefactos para carregar** usando um semi-cólon como separador. Por exemplo, se a sua construção hudson produzir ficheiros JAR e ficheiros TXT na pasta de **construção** do seu espaço de trabalho, e quiser carregar ambos para armazenamento Azure Blob, use o seguinte para a **Lista de Artefactos para carregar** valor: **construir/\*.jar;construir/\*.txt**. Também pode usar sintaxe de dois cólons para especificar um caminho a utilizar dentro do nome blob. Por exemplo, se pretender que os JARs sejam carregados usando **binários** no caminho da bolha e os ficheiros TXT sejam carregados usando **avisos** no caminho da bolha, utilize o seguinte para que a **Lista de Artefactos carregue** valor: **\*construa/ .jar:binários;construção/\*.txt:avisos**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Como criar um passo de construção que descarrega do armazenamento do Azure Blob
Os seguintes passos mostram como configurar um passo de construção para descarregar itens do armazenamento do Azure Blob. Isto seria útil se quiser incluir itens na sua construção, por exemplo, JARs que você mantém no armazenamento Azure Blob.

1. Na secção **Construção** da configuração de trabalho, clique em Adicionar passo de **construção** e escolha **Baixar do armazenamento Do Blob Azure**.
2. Para obter o nome da **conta de armazenamento,** selecione a conta de armazenamento a utilizar.
3. Para **o nome do recipiente,** especifique o nome do recipiente que tem as bolhas que pretende descarregar. Pode usar variáveis ambientais.
4. Para **o nome Blob,** especifique o nome blob. Pode usar variáveis ambientais. Além disso, pode utilizar um asterisco, como um wildcard depois de especificar a letra ou s inicial do nome blob. Por exemplo, **o\\projeto*** especificaria todas as bolhas cujos nomes começam com o **projeto**.
5. [Opcional] Para descarregar o **caminho,** especifique o caminho na máquina Hudson onde pretende descarregar ficheiros a partir do armazenamento do Azure Blob. As variáveis ambientais também podem ser usadas. (Se não fornecer um valor para o **caminho de descarregamento,** os ficheiros do armazenamento Do Blob Azure serão descarregados para o espaço de trabalho do trabalho.)

Se tiver itens adicionais que pretende descarregar no armazenamento do Azure Blob, pode criar passos adicionais de construção.

Depois de executar uma construção, pode verificar a saída da consola de histórico de construção, ou olhar para a sua localização de descarregamento, para ver se as bolhas que esperava foram descarregadas com sucesso.

## <a name="components-used-by-the-blob-service"></a>Componentes utilizados pelo serviço Blob
O seguinte fornece uma visão geral dos componentes do serviço Blob.

* **Conta de armazenamento**: Todo o acesso ao Armazenamento Azure é feito através de uma conta de armazenamento. Este é o nível mais alto do espaço de nome para aceder a bolhas. Uma conta pode conter um número ilimitado de contentores, desde que o seu tamanho total seja inferior a 100 TB.
* **Recipiente**: Um recipiente proporciona um agrupamento de um conjunto de bolhas. Todos os blobs tem de estar num contentor. Uma conta pode conter um número ilimitado de contentores. Um contentor pode armazenar um número ilimitado de blobs.
* **Blob**: Um ficheiro de qualquer tipo e tamanho. Existem dois tipos de bolhas que podem ser armazenadas no Armazenamento Azure: blocos e bolhas de página. A maioria dos ficheiros são blocos. Uma única bolha de bloco pode ter até 200 GB de tamanho. Este tutorial usa bolhas de bloco. As bolhas de página, outro tipo de bolha, podem ter até 1 TB de tamanho, e são mais eficientes quando as gamas de bytes num ficheiro são modificadas frequentemente. Para mais informações sobre bolhas, consulte [Understanding Block Blobs, Append Blobs e Page Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formato URL**: As bolhas são endereçadas utilizando o seguinte formato URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (O formato acima aplica-se à nuvem azure global. Se estiver a utilizar uma nuvem Azure diferente, utilize o ponto final dentro do [portal Azure](https://portal.azure.com) para determinar o seu ponto final URL.)
  
    No formato `storageaccount` acima, representa o nome `container_name` da sua conta de armazenamento, representa o nome do seu recipiente, e `blob_name` representa o nome da sua bolha, respectivamente. Dentro do nome do recipiente, pode ter múltiplos caminhos, separados por um corte dianteiro, **/**. O nome do recipiente de exemplo neste tutorial foi **MyJob**, e **${BUILD\_\_ID}/${BUILD NUMBER}** foi usado para o caminho virtual comum, resultando na bolha ter um URL do seguinte formulário:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Passos seguintes
* [Conheça Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Azure Storage SDK for Java](https://github.com/azure/azure-storage-java) (SDK do Armazenamento do Azure para Java)
* [Azure Storage Client SDK Reference](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html) (Referência do SDK do Cliente do Armazenamento do Azure)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog da equipe de armazenamento azure](https://blogs.msdn.com/b/windowsazurestorage/)

Para obter mais informações, visite [Azure para programadores Java](/java/azure).
