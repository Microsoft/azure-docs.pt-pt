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
ms.openlocfilehash: 10bfc3ce4666ee1653110099a3c8d22a58d80f35
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985306"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Utilizar o Armazenamento do Azure com uma solução Hudson de Integração Contínua
## <a name="overview"></a>Descrição geral
As informações a seguir mostram como usar o armazenamento de BLOBs como um repositório de artefatos de compilação criados por uma solução de CI (integração contínua) Hudson ou como uma fonte de arquivos que podem ser baixados para serem usados em um processo de compilação. Um dos cenários onde você acharia isso útil é quando você está codificando em um ambiente de desenvolvimento ágil (usando Java ou outras linguagens), as compilações são executadas com base na integração contínua e você precisa de um repositório para seus artefatos de compilação, para que você possa, por exemplo, compartilhe-os com outros membros da organização, seus clientes ou mantenha um arquivo morto.  Outro cenário é quando o trabalho de compilação em si requer outros arquivos, por exemplo, dependências para baixar como parte da entrada da compilação.

Neste tutorial, você usará o plug-in do armazenamento do Azure para Hudson CI disponibilizado pela Microsoft.

## <a name="introduction-to-hudson"></a>Introdução ao Hudson
A Hudson permite a integração contínua de um projeto de software, permitindo que os desenvolvedores integrem facilmente suas alterações de código e tenham compilações geradas de forma automática e frequente, aumentando assim a produtividade dos desenvolvedores. As compilações têm controle de versão e os artefatos de compilação podem ser carregados em vários repositórios. Este artigo mostrará como usar o armazenamento de BLOBs do Azure como o repositório dos artefatos de compilação. Ele também mostrará como baixar dependências do armazenamento de BLOBs do Azure.

Mais informações sobre o Hudson podem ser encontradas em [atender ao Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

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
  
  1. Em um computador habilitado para Java, baixe a guerra de Hudson <http://hudson-ci.org/>de.
  2. Em um prompt de comando que é aberto na pasta que contém a guerra da Hudson, execute o Hudson WAR. Por exemplo, se você tiver baixado a versão 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. No navegador, abra `http://localhost:8080/`. Isso abrirá o painel do Hudson.
  4. Após o primeiro uso da Hudson, conclua a configuração `http://localhost:8080/`inicial em.
  5. Depois de concluir a configuração inicial, cancele a instância em execução do Hudson War, inicie o Hudson War novamente e reabra o painel do Hudson, `http://localhost:8080/`que você usará para instalar e configurar o plug-in do armazenamento do Azure.
     
      Embora uma solução típica do Hudson CI seja configurada para ser executada como um serviço, a execução da guerra de Hudson na linha de comando será suficiente para este tutorial.
* Uma conta do Azure. Você pode se inscrever para uma conta do <https://www.azure.com>Azure em.
* Uma conta de armazenamento do Azure. Se você ainda não tiver uma conta de armazenamento, poderá criar uma usando as etapas em [criar uma conta de armazenamento](../common/storage-quickstart-create-account.md).
* A familiaridade com a solução Hudson CI é recomendada, mas não é necessária, pois o conteúdo a seguir usará um exemplo básico para mostrar as etapas necessárias ao usar o serviço blob como um repositório para artefatos de compilação do Hudson CI.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Como usar o serviço blob com o Hudson CI
Para usar o serviço blob com o Hudson, você precisará instalar o plug-in do armazenamento do Azure, configurar o plug-in para usar sua conta de armazenamento e, em seguida, criar uma ação de pós-compilação que carregue seus artefatos de compilação em sua conta de armazenamento. Essas etapas são descritas nas seções a seguir.

## <a name="how-to-install-the-azure-storage-plugin"></a>Como instalar o plug-in do armazenamento do Azure
1. No painel do Hudson, clique em **gerenciar Hudson**.
2. Na página **gerenciar Hudson** , clique em **Gerenciar plug-ins**.
3. Clique na guia **disponível** .
4. Clique em **outros**.
5. Na seção **carregadores de artefato** , selecione **armazenamento do Microsoft Azure plug-in**.
6. Clique em **Instalar**.
7. Após a conclusão da instalação, reinicie o Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Como configurar o plug-in do armazenamento do Azure para usar sua conta de armazenamento
1. No painel do Hudson, clique em **gerenciar Hudson**.
2. Na página **gerenciar Hudson** , clique em **Configurar sistema**.
3. Na seção **configuração da conta do armazenamento do Microsoft Azure** :
   
    a. Insira o nome da conta de armazenamento, que pode ser obtido do [portal do Azure](https://portal.azure.com).
   
    b. Insira sua chave de conta de armazenamento, também obtida do [portal do Azure](https://portal.azure.com).
   
    c. Use o valor padrão para **URL do ponto de extremidade do serviço blob** se você estiver usando a nuvem global do Azure. Se você estiver usando uma nuvem do Azure diferente, use o ponto de extremidade conforme especificado no [portal do Azure](https://portal.azure.com) para sua conta de armazenamento.
   
    d. Clique em **validar credenciais de armazenamento** para validar sua conta de armazenamento.
   
    e. Adicional Se você tiver contas de armazenamento adicionais que deseja disponibilizar para o Hudson CI, clique em **adicionar mais contas de armazenamento**.
   
    f. Clique em **salvar** para salvar as configurações.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Como criar uma ação de pós-compilação que carrega seus artefatos de compilação em sua conta de armazenamento
Para fins de instrução, primeiro precisaremos criar um trabalho que criará vários arquivos e, em seguida, adicionar a ação de pós-compilação para carregar os arquivos em sua conta de armazenamento.

1. No painel do Hudson, clique em **novo trabalho**.
2. Nomeie o trabalho **MyJob**, clique em **criar um trabalho de software de estilo livre**e, em seguida, clique em **OK**.
3. Na seção **Build** da configuração do trabalho, clique em **Adicionar etapa de compilação** e escolha **executar comando do lote do Windows**.
4. No **comando**, use os seguintes comandos:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. Na seção **ações de pós-compilação** da configuração do trabalho, clique em **carregar artefatos para Microsoft Azure armazenamento de BLOBs**.
6. Para **nome da conta de armazenamento**, selecione a conta de armazenamento a ser usada.
7. Para **nome do contêiner**, especifique o nome do contêiner. (O contêiner será criado se ele ainda não existir quando os artefatos de compilação forem carregados.) Você pode usar variáveis de ambiente, portanto, para este exemplo, insira **$ {JOB_NAME}** como o nome do contêiner.
   
    **Sugestão**
   
    Abaixo da seção de **comando** em que você inseriu um script para **executar o comando de lote do Windows** é um link para as variáveis de ambiente reconhecidas pelo Hudson. Clique nesse link para aprender os nomes e descrições de variáveis de ambiente. As variáveis de ambiente que contêm caracteres especiais, como a variável de ambiente **BUILD_URL** , não são permitidas como um nome de contêiner ou caminho virtual comum.
8. Clique em **tornar novo contêiner público por padrão** para este exemplo. (Se desejar usar um contêiner privado, você precisará criar uma assinatura de acesso compartilhado para permitir o acesso. Isso está além do escopo deste artigo. Você pode saber mais sobre assinaturas de acesso compartilhado em [usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md).)
9. Adicional Clique em **limpar contêiner antes de carregar** se desejar que o contêiner seja removido do conteúdo antes de os artefatos de compilação serem carregados (Deixe-o desmarcado se não quiser limpar o conteúdo do contêiner).
10. Para **lista de artefactos para carregar**, introduza **texto/*.txt**.
11. Para o **caminho virtual comum para artefatos carregados**, insira **$\_{ID da compilação}/\_$ {número de Build}** .
12. Clique em **salvar** para salvar as configurações.
13. No painel do Hudson, clique em **Compilar agora** para executar **MyJob**. Examine a saída do console para obter o status. As mensagens de status para o armazenamento do Azure serão incluídas na saída do console quando a ação de pós-compilação começar a carregar artefatos de compilação.
14. Após a conclusão bem-sucedida do trabalho, você pode examinar os artefatos de compilação abrindo o blob público.
    
    a. Inicie sessão no [portal do Azure](https://portal.azure.com).
    
    b. Clique em **armazenamento**.
    
    c. Clique no nome da conta de armazenamento que você usou para Hudson.
    
    d. Cliqueem contêineres.
    
    e. Clique no contêiner chamado **myJob**, que é a versão em minúsculas do nome do trabalho que você atribuiu quando criou o trabalho do Hudson. Nomes de contêiner e nomes de BLOBs são minúsculos (e diferenciam maiúsculas de minúsculas) no armazenamento do Azure. Na lista de BLOBs para o contêiner chamado **myJob** , você deve ver **Hello. txt** e **Date. txt**. Copie a URL para qualquer um desses itens e abra-a no navegador. Você verá o arquivo de texto que foi carregado como um artefato de compilação.

Somente uma ação de pós-compilação que carrega artefatos no armazenamento de BLOBs do Azure pode ser criada por trabalho. A ação de pós-compilação única para carregar artefatos no armazenamento de BLOBs do Azure pode especificar arquivos diferentes (incluindo curingas) e caminhos para arquivos dentro **da lista de artefatos a serem carregados** usando um ponto e vírgula como um separador. Por exemplo, se criar seu Hudson produz ficheiros JAR e os ficheiros TXT em sua área de trabalho **crie** pasta e pretender carregar tanto para o armazenamento de Blobs do Azure, utilize o seguinte para o **lista de artefactos para carregar** valor: **criar /\*.JAR;compilação/\*.txt**. Você também pode usar a sintaxe de dois-pontos duplos para especificar um caminho a ser usado no nome do blob. Por exemplo, se pretender que o Intune para obter carregados usando **binários** no caminho do blob e os ficheiros TXT para obter carregados usando **avisos** no caminho do blob, utilize o seguinte para o **lista de artefactos para carregar** valor: **criar/\*.jar::binaries;compilação/\*.txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Como criar uma etapa de compilação que é baixada do armazenamento de BLOBs do Azure
As etapas a seguir mostram como configurar uma etapa de compilação para baixar itens do armazenamento de BLOBs do Azure. Isso seria útil se você quiser incluir itens em sua compilação, por exemplo, JARs que você mantém no armazenamento de BLOBs do Azure.

1. Na seção **Build** da configuração do trabalho, clique em **Adicionar etapa de compilação** e escolha **baixar do armazenamento de BLOBs do Azure**.
2. Para **nome da conta de armazenamento**, selecione a conta de armazenamento a ser usada.
3. Para **nome do contêiner**, especifique o nome do contêiner que tem os blobs que você deseja baixar. Você pode usar variáveis de ambiente.
4. Para **nome do blob**, especifique o nome do blob. Você pode usar variáveis de ambiente. Além disso, você pode usar um asterisco, como um caractere curinga depois de especificar as letras iniciais do nome do blob. Por exemplo, **Project\\** * especificaria todos os BLOBs cujos nomes começam com **Project**.
5. Adicional Para **caminho de download**, especifique o caminho no computador Hudson no qual você deseja baixar arquivos do armazenamento de BLOBs do Azure. As variáveis de ambiente também podem ser usadas. (Se você não fornecer um valor para o **caminho de download**, os arquivos do armazenamento de BLOBs do Azure serão baixados para o espaço de trabalho do trabalho.)

Se você tiver itens adicionais que deseja baixar do armazenamento de BLOBs do Azure, poderá criar etapas de compilação adicionais.

Depois de executar uma compilação, você pode verificar a saída do console de histórico de compilação ou examinar seu local de download para ver se os BLOBs esperados foram baixados com êxito.

## <a name="components-used-by-the-blob-service"></a>Componentes usados pelo serviço blob
O seguinte fornece uma visão geral dos componentes do serviço BLOB.

* **Conta de armazenamento**: Todo o acesso ao armazenamento do Azure é feito por meio de uma conta de armazenamento. Este é o nível mais alto do namespace para acessar BLOBs. Uma conta pode conter um número ilimitado de contêineres, desde que seu tamanho total esteja abaixo de 100 TB.
* **Contêiner**: Um contêiner fornece um agrupamento de um conjunto de BLOBs. Todos os blobs tem de estar num contentor. Uma conta pode conter um número ilimitado de contentores. Um contentor pode armazenar um número ilimitado de blobs.
* **Blob**: Um arquivo de qualquer tipo e tamanho. Há dois tipos de BLOBs que podem ser armazenados no armazenamento do Azure: blobs de blocos e de páginas. A maioria dos arquivos são blobs de blocos. Um único blob de blocos pode ter até 200 GB de tamanho. Este tutorial usa blobs de bloco. Blobs de páginas, outro tipo de BLOB, podem ter até 1 TB de tamanho e são mais eficientes quando intervalos de bytes em um arquivo são modificados com frequência. Para obter mais informações sobre BLOBs, consulte [noções básicas sobre blobs de blocos, blobs de acréscimo e blobs de páginas](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formato da URL**: Os BLOBs são endereçáveis usando o seguinte formato de URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (O formato acima se aplica à nuvem global do Azure. Se você estiver usando uma nuvem do Azure diferente, use o ponto de extremidade dentro do [portal do Azure](https://portal.azure.com) para determinar o ponto de extremidade da URL.)
  
    No formato acima, `storageaccount` representa o nome da sua conta de armazenamento, `container_name` representa o nome do seu contêiner e `blob_name` representa o nome do seu BLOB, respectivamente. Dentro do nome do contêiner, você pode ter vários caminhos, separados por uma barra, **/** . O nome do contêiner de exemplo neste tutorial foi **MyJob**e **$ {Build\_ID}/$ {Build\_Number}** foi usado para o caminho virtual comum, resultando no blob com uma URL do seguinte formato:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Passos Seguintes
* [Conheça o Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [SDK do armazenamento do Azure para Java](https://github.com/azure/azure-storage-java)
* [Azure Storage Client SDK Reference](http://dl.windowsazure.com/storage/javadoc/) (Referência do SDK do Cliente do Armazenamento do Azure)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogue da Equipa de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/)

Para obter mais informações, visite [Azure para programadores Java](/java/azure).
