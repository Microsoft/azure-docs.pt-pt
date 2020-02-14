---
title: Como usar o Hudson com o armazenamento de BLOBs | Microsoft Docs
description: Descreve como usar o Hudson com o armazenamento de BLOBs do Azure como um repositório para artefatos de compilação.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: a89439f49dd53f09d5cd40be0bf2e4981e9235d4
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201390"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Utilização do Armazenamento Azure com uma solução de Integração Contínua Hudson
## <a name="overview"></a>Descrição geral
As informações a seguir mostram como usar o armazenamento de BLOBs como um repositório de artefatos de compilação criados por uma solução de CI (integração contínua) Hudson ou como uma fonte de arquivos que podem ser baixados para serem usados em um processo de compilação. Um dos cenários onde você acharia isso útil é quando você está codificando em um ambiente de desenvolvimento ágil (usando Java ou outras linguagens), as compilações são executadas com base na integração contínua e você precisa de um repositório para seus artefatos de compilação, para que você possa, por exemplo, compartilhe-os com outros membros da organização, seus clientes ou mantenha um arquivo morto.  Outro cenário é quando o trabalho de compilação em si requer outros arquivos, por exemplo, dependências para baixar como parte da entrada da compilação.

Neste tutorial, você usará o plug-in do armazenamento do Azure para Hudson CI disponibilizado pela Microsoft.

## <a name="introduction-to-hudson"></a>Introdução ao Hudson
A Hudson permite a integração contínua de um projeto de software, permitindo que os desenvolvedores integrem facilmente suas alterações de código e tenham compilações geradas de forma automática e frequente, aumentando assim a produtividade dos desenvolvedores. As compilações têm controle de versão e os artefatos de compilação podem ser carregados em vários repositórios. Este artigo mostrará como usar o armazenamento de BLOBs do Azure como o repositório dos artefatos de compilação. Ele também mostrará como baixar dependências do armazenamento de BLOBs do Azure.

Mais informações sobre Hudson podem ser encontradas no [Meet Hudson.](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)

## <a name="benefits-of-using-the-blob-service"></a>Benefícios do uso do serviço blob
Os benefícios de usar o serviço blob para hospedar seus artefatos de compilação de desenvolvimento Agile incluem:

* Alta disponibilidade de seus artefatos de compilação e/ou dependências baixáveis.
* Desempenho quando sua solução Hudson CI carrega seus artefatos de compilação.
* Desempenho quando seus clientes e parceiros baixam seus artefatos de compilação.
* Controle sobre as políticas de acesso do usuário, com uma opção entre acesso anônimo, acesso de assinatura de acesso compartilhado baseado em expiração, acesso privado etc.

## <a name="prerequisites"></a>Pré-requisitos
Você precisará do seguinte para usar o serviço blob com sua solução Hudson CI:

* Uma solução de integração contínua Hudson.
  
    Se atualmente você não tiver uma solução Hudson CI, poderá executar uma solução Hudson CI usando a seguinte técnica:
  
  1. Numa máquina ativada por Java, [descarregue o ficheiro Hudson WAR](https://www.eclipse.org/hudson/download.php).
  2. Em um prompt de comando que é aberto na pasta que contém a guerra da Hudson, execute o Hudson WAR. Por exemplo, se você tiver baixado a versão 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. No seu navegador, abra `http://localhost:8080/`. Isso abrirá o painel do Hudson.
  4. Após a primeira utilização de Hudson, complete a configuração inicial em `http://localhost:8080/`.
  5. Depois de completar a configuração inicial, cancele a instância de execução da Guerra de Hudson, reinicie a Guerra de Hudson e reabra o tablier Hudson, `http://localhost:8080/`, que utilizará para instalar e configurar o plugin de Armazenamento Azure.
     
      Embora uma solução típica do Hudson CI seja configurada para ser executada como um serviço, a execução da guerra de Hudson na linha de comando será suficiente para este tutorial.
* Uma conta do Azure. Pode inscrever-se numa conta Azure no <https://www.azure.com>.
* Uma conta de armazenamento do Azure. Se ainda não tiver uma conta de armazenamento, pode criar uma utilizando os passos da [Conta Criar uma Conta](../common/storage-account-create.md)de Armazenamento .
* A familiaridade com a solução Hudson CI é recomendada, mas não é necessária, pois o conteúdo a seguir usará um exemplo básico para mostrar as etapas necessárias ao usar o serviço blob como um repositório para artefatos de compilação do Hudson CI.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Como usar o serviço blob com o Hudson CI
Para usar o serviço blob com o Hudson, você precisará instalar o plug-in do armazenamento do Azure, configurar o plug-in para usar sua conta de armazenamento e, em seguida, criar uma ação de pós-compilação que carregue seus artefatos de compilação em sua conta de armazenamento. Essas etapas são descritas nas seções a seguir.

## <a name="how-to-install-the-azure-storage-plugin"></a>Como instalar o plug-in do armazenamento do Azure
1. Dentro do painel de hudson, clique em **Manage Hudson**.
2. Na página **Manage Hudson,** clique em **Gerir Plugins**.
3. Clique no separador **Disponível.**
4. Clique em **Outros**.
5. Na secção **Descarregadores** de Artefactos, selecione **plugin de armazenamento Microsoft Azure**.
6. Clique em **Instalar**.
7. Após a conclusão da instalação, reinicie o Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Como configurar o plug-in do armazenamento do Azure para usar sua conta de armazenamento
1. Dentro do painel de hudson, clique em **Manage Hudson**.
2. Na página **Manage Hudson,** clique em **Configurar sistema**.
3. Na secção de configuração da conta de **armazenamento do Microsoft Azure:**
   
    a. Insira o nome da sua conta de armazenamento, que pode obter no [portal Azure](https://portal.azure.com).
   
    b. Introduza a chave da sua conta de armazenamento, também obtida a partir do [portal Azure.](https://portal.azure.com)
   
    c. Utilize o valor padrão para **o URL de ponto final do serviço Blob** se estiver a utilizar a nuvem azure global. Se estiver a utilizar uma nuvem Azure diferente, utilize o ponto final conforme especificado no [portal Azure](https://portal.azure.com) para a sua conta de armazenamento.
   
    d. Clique em **validar credenciais** de armazenamento para validar a sua conta de armazenamento.
   
    e. [Opcional] Se tiver contas de armazenamento adicionais que deseja disponibilizar ao seu CI Hudson, clique em **Adicionar mais contas**de armazenamento .
   
    f. Clique em **Guardar** para guardar as suas definições.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Como criar uma ação de pós-compilação que carrega seus artefatos de compilação em sua conta de armazenamento
Para fins de instrução, primeiro precisaremos criar um trabalho que criará vários arquivos e, em seguida, adicionar a ação de pós-compilação para carregar os arquivos em sua conta de armazenamento.

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
   
    **Sugestão**
   
    Abaixo da secção **Comando** onde introduziu um script para executar o comando do **lote do Windows** é uma ligação às variáveis ambientais reconhecidas por Hudson. Clique nesse link para aprender os nomes e descrições de variáveis de ambiente. Variáveis ambientais que contenham caracteres especiais, como a **variável ambiente BUILD_URL,** não são permitidas como um nome de contentor ou um caminho virtual comum.
8. Clique em **Tornar o novo contentor público por padrão** para este exemplo. (Se desejar usar um contêiner privado, você precisará criar uma assinatura de acesso compartilhado para permitir o acesso. Isso está além do escopo deste artigo. Pode saber mais sobre assinaturas de acesso partilhado na Utilização de Assinaturas de [Acesso Partilhado (SAS)](storage-sas-overview.md).)
9. [Opcional] Clique em **limpar o recipiente antes** de carregar se quiser que o recipiente seja limpo do conteúdo antes de serem carregados artefactos de construção (deixe-o descontrolado se não quiser limpar o conteúdo do recipiente).
10. Para **que a Lista de Artefactos carregue,** introduza **texto/*.txt**.
11. Para o **caminho virtual comum para artefactos carregados,** insira **${BUILD\_ID}/${BUILD\_NUMBER}** .
12. Clique em **Guardar** para guardar as suas definições.
13. No painel hudson, clique em **Build Now** para executar **MyJob**. Examine a saída do console para obter o status. As mensagens de status para o armazenamento do Azure serão incluídas na saída do console quando a ação de pós-compilação começar a carregar artefatos de compilação.
14. Após a conclusão bem-sucedida do trabalho, você pode examinar os artefatos de compilação abrindo o blob público.
    
    a. Inicie sessão no [Portal do Azure](https://portal.azure.com).
    
    b. Clique em **Armazenamento**.
    
    c. Clique no nome da conta de armazenamento que você usou para Hudson.
    
    d. Clique em **Recipientes**.
    
    e. Clique no recipiente chamado **myjob**, que é a versão minúscula do nome de trabalho que atribuiu quando criou o emprego de Hudson. Nomes de contêiner e nomes de BLOBs são minúsculos (e diferenciam maiúsculas de minúsculas) no armazenamento do Azure. Dentro da lista de bolhas para o contentor chamado **myjob** você deve ver **hello.txt** e **date.txt**. Copie a URL para qualquer um desses itens e abra-a no navegador. Você verá o arquivo de texto que foi carregado como um artefato de compilação.

Somente uma ação de pós-compilação que carrega artefatos no armazenamento de BLOBs do Azure pode ser criada por trabalho. A única ação pós-construção para enviar artefactos para o armazenamento do Azure Blob pode especificar diferentes ficheiros (incluindo wildcards) e caminhos para ficheiros dentro **da Lista de Artefactos para carregar** usando um semi-cólon como separador. Por exemplo, se a sua construção hudson produzir ficheiros JAR e ficheiros TXT na pasta de **construção** do seu espaço de trabalho, e quiser carregar ambos para armazenamento Azure Blob, use o seguinte para a **Lista de Artefactos para carregar** valor: **construir/\*.jar;construir/\*.txt**. Você também pode usar a sintaxe de dois-pontos duplos para especificar um caminho a ser usado no nome do blob. Por exemplo, se pretender que os JARs sejam carregados usando **binários** no caminho da bolha e os ficheiros TXT sejam carregados usando **avisos** no caminho da bolha, utilize o seguinte para que a **Lista de Artefactos carregue** valor: **construa/\*.jar:binários;construção/\*.txt::avisos**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Como criar uma etapa de compilação que é baixada do armazenamento de BLOBs do Azure
As etapas a seguir mostram como configurar uma etapa de compilação para baixar itens do armazenamento de BLOBs do Azure. Isso seria útil se você quiser incluir itens em sua compilação, por exemplo, JARs que você mantém no armazenamento de BLOBs do Azure.

1. Na secção **Construção** da configuração de trabalho, clique em Adicionar passo de **construção** e escolha **Baixar do armazenamento Do Blob Azure**.
2. Para obter o nome da **conta de armazenamento,** selecione a conta de armazenamento a utilizar.
3. Para **o nome do recipiente,** especifique o nome do recipiente que tem as bolhas que pretende descarregar. Você pode usar variáveis de ambiente.
4. Para **o nome Blob,** especifique o nome blob. Você pode usar variáveis de ambiente. Além disso, você pode usar um asterisco, como um caractere curinga depois de especificar as letras iniciais do nome do blob. Por exemplo, **o projeto\\** * especificaria todas as bolhas cujos nomes começam com o **projeto.**
5. [Opcional] Para descarregar o **caminho,** especifique o caminho na máquina Hudson onde pretende descarregar ficheiros a partir do armazenamento do Azure Blob. As variáveis de ambiente também podem ser usadas. (Se não fornecer um valor para o **caminho de descarregamento,** os ficheiros do armazenamento Do Blob Azure serão descarregados para o espaço de trabalho do trabalho.)

Se você tiver itens adicionais que deseja baixar do armazenamento de BLOBs do Azure, poderá criar etapas de compilação adicionais.

Depois de executar uma compilação, você pode verificar a saída do console de histórico de compilação ou examinar seu local de download para ver se os BLOBs esperados foram baixados com êxito.

## <a name="components-used-by-the-blob-service"></a>Componentes usados pelo serviço blob
O seguinte fornece uma visão geral dos componentes do serviço BLOB.

* **Conta de armazenamento**: Todo o acesso ao Armazenamento Azure é feito através de uma conta de armazenamento. Este é o nível mais alto do namespace para acessar BLOBs. Uma conta pode conter um número ilimitado de contêineres, desde que seu tamanho total esteja abaixo de 100 TB.
* **Recipiente**: Um recipiente proporciona um agrupamento de um conjunto de bolhas. Todos os blobs tem de estar num contentor. Uma conta pode conter um número ilimitado de contentores. Um contentor pode armazenar um número ilimitado de blobs.
* **Blob**: Um ficheiro de qualquer tipo e tamanho. Há dois tipos de BLOBs que podem ser armazenados no armazenamento do Azure: blobs de blocos e de páginas. A maioria dos arquivos são blobs de blocos. Um único blob de blocos pode ter até 200 GB de tamanho. Este tutorial usa blobs de bloco. Blobs de páginas, outro tipo de BLOB, podem ter até 1 TB de tamanho e são mais eficientes quando intervalos de bytes em um arquivo são modificados com frequência. Para mais informações sobre bolhas, consulte [Understanding Block Blobs, Append Blobs e Page Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formato URL**: As bolhas são endereçadas utilizando o seguinte formato URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (O formato acima se aplica à nuvem global do Azure. Se estiver a utilizar uma nuvem Azure diferente, utilize o ponto final dentro do [portal Azure](https://portal.azure.com) para determinar o seu ponto final URL.)
  
    No formato acima, `storageaccount` representa o nome da sua conta de armazenamento, `container_name` representa o nome do seu contentor, e `blob_name` representa o nome da sua bolha, respectivamente. Dentro do nome do recipiente, pode ter múltiplos caminhos, separados por um corte dianteiro, **/** . O nome do recipiente de exemplo neste tutorial foi **MyJob**, e **${BUILD\_ID}/${BUILD\_NUMBER}** foi usado para o caminho virtual comum, resultando na bolha ter um URL do seguinte formulário:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Passos seguintes
* [Conheça Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [SDK de armazenamento azure para Java](https://github.com/azure/azure-storage-java)
* [Azure Storage Client SDK Reference](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html) (Referência do SDK do Cliente do Armazenamento do Azure)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogue da Equipa de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/)

Para obter mais informações, visite [Azure para programadores Java](/java/azure).
