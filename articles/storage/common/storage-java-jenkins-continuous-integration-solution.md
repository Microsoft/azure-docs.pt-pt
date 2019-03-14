---
title: Utilizar o armazenamento do Azure com uma solução de integração contínua Jenkins
description: Este tutorial explica como utilizar o serviço de Blobs do Azure como um repositório de artefactos criados por uma solução de integração contínua Jenkins de compilação.
ms.topic: article
ms.author: tarcher
author: tarcher
services: devops
ms.service: storage
custom: jenkins
ms.date: 07/31/2018
ms.subservice: common
ms.openlocfilehash: 2aeab9be28a61f4e185be7192e7965cbf02bfed9
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791992"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Utilizar o armazenamento do Azure com uma solução de integração contínua Jenkins

Este artigo ilustra como utilizar o armazenamento de BLOBs como um repositório de artefactos de construção criado por uma solução do Jenkins de integração contínua (CI) ou como uma origem de ficheiros que pode ser baixados para uso num processo de compilação. Um dos cenários em que descobrirá que essa solução útil é quando escreve o código num ambiente de desenvolvimento do agile (com o Java ou outras linguagens), compilações estão em execução com base na integração contínua e precisa de um repositório para seus artefactos de compilação, para que Por exemplo, pode partilhá-los com outros membros da organização, os seus clientes, ou manter um arquivo morto. Outro cenário é quando a tarefa de compilação em si requer outros arquivos, por exemplo, as dependências para transferir como parte da compilação de entrada.

Neste tutorial, irá utilizar o plug-in de armazenamento do Azure para Jenkins CI disponibilizado pela Microsoft.

## <a name="jenkins-overview"></a>Descrição geral do Jenkins
Jenkins permite a integração contínua de um projeto de software, permitindo aos programadores integrar facilmente as alterações de código e tem compilações produzidos automaticamente e com frequência, assim, aumentar a produtividade dos desenvolvedores. As compilações são com versão e artefactos de compilação podem ser carregados para vários repositórios. Este artigo mostra como utilizar o armazenamento de Blobs do Azure como repositório de artefactos de compilação. Ela também mostrará como transferir as dependências do armazenamento de Blobs do Azure.

Podem encontrar mais informações acerca do Jenkins em [cumprir Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Benefícios da utilização do serviço de BLOBs
Benefícios da utilização do serviço de BLOBs para hospedar seus artefactos de compilação do desenvolvimento ágil:

* Elevada disponibilidade de seus artefactos de compilação e/ou dependências que pode ser baixadas.
* Desempenho quando a sua solução do Jenkins CI carrega artefactos de compilação.
* Desempenho quando os seus clientes e parceiros transferir os artefactos de compilação.
* Controlo sobre políticas de acesso de utilizador, com uma escolha entre o acesso anônimo, acesso de assinatura de acesso partilhado com base em expiração, privada acesso, etc.

## <a name="prerequisites"></a>Pré-requisitos
* Uma solução de integração contínua do Jenkins.
  
    Se não tiver atualmente uma solução Jenkins CI, pode executar uma solução do Jenkins CI usando a técnica seguinte:
  
  1. Numa máquina ativada do Java, baixe jenkins.war de <http://jenkins-ci.org>.
  2. No prompt de comando que é aberto para a pasta que contém jenkins.war, execute:
     
      `java -jar jenkins.war`

  3. No browser, abra `http://localhost:8080/` para abrir o dashboard do Jenkins, que irá utilizar para instalar e configurar o plug-in do armazenamento do Azure.
     
      Embora uma solução típica do Jenkins CI poderia ser configurada para ser executado como um serviço, em execução na guerra Jenkins na linha de comandos será suficiente para este tutorial.
* Uma conta do Azure. Pode inscrever-se numa conta do Azure em <http://www.azure.com>.
* Uma conta de armazenamento do Azure. Se ainda não tiver uma conta de armazenamento, pode criar uma com os passos indicados em [criar uma conta de armazenamento](../common/storage-quickstart-create-account.md).
* Familiaridade com a solução do Jenkins CI é recomendada, mas não obrigatório, como o seguinte conteúdo irá utilizar um exemplo básico para mostrar as etapas necessárias ao utilizar o serviço de BLOBs como um repositório para o Jenkins CI artefactos de compilação.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Como utilizar o serviço de Blobs com o Jenkins CI
Para utilizar o serviço de Blobs com o Jenkins, terá de instalar o plug-in do armazenamento do Azure, configurar o plug-in para utilizar a sua conta de armazenamento e, em seguida, crie uma ação de pós-compilação que carrega artefactos de compilação para a sua conta de armazenamento. Estes passos são descritos nas secções seguintes.

## <a name="how-to-install-the-azure-storage-plugin"></a>Como instalar o plug-in do armazenamento do Azure
1. No painel do Jenkins, selecione **gerir Jenkins**.
2. Na **gerir Jenkins** página, selecione **gerir plug-ins**.
3. Selecione o separador **Available** (Disponíveis).
4. Na **carregadores de artefacto** secção, verificação **Plug-in do armazenamento do Microsoft Azure**.
5. Selecione **instalar sem reinício** ou **agora a transferir e instalar após o reinício**.
6. Reinicie o Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Como configurar o plug-in do armazenamento do Azure para utilizar a sua conta de armazenamento
1. No painel do Jenkins, selecione **gerir Jenkins**.
2. Na **gerir Jenkins** página, selecione **configurar o sistema**.
3. Na **configuração de conta de armazenamento do Microsoft Azure** secção:
   1. Introduza o nome de conta de armazenamento, que pode obter a partir da [Portal do Azure](https://portal.azure.com).
   2. Introduza a chave de conta de armazenamento, também obtidas por meio dos [Portal do Azure](https://portal.azure.com).
   3. Utilize o valor predefinido para **URL de ponto final do serviço Blob** se estiver a utilizar a cloud do Azure global. Se estiver a utilizar uma cloud do Azure diferente, utilize o ponto de extremidade, conforme especificado na [Portal do Azure](https://portal.azure.com) para a sua conta de armazenamento. 
   4. Selecione **validar as credenciais de armazenamento** para validar a conta de armazenamento. 
   5. [Opcional] Se tiver contas de armazenamento adicional que pretende que sejam disponibilizadas para o Jenkins CI, selecione **adicionar mais contas de armazenamento**.
   6. Selecione **guardar** para guardar as definições.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Como criar uma ação de pós-compilação que carrega artefactos de compilação para a sua conta de armazenamento
Para fins educativos, tem primeiro de criar uma tarefa que irá criar vários arquivos e, em seguida, adicionar a ação de pós-compilação para carregar os ficheiros para a sua conta de armazenamento.

1. No painel do Jenkins, selecione **Novo Item**.
2. Nome da tarefa **MyJob**, selecione **criar um projeto de software de estilo livre**e, em seguida, selecione **OK**.
3. Na **crie** secção de configuração da tarefa, selecione **Adicionar passo de compilação** e selecione **Windows executar comando de batch**.
4. Na **comando**, utilize os seguintes comandos:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. Na **ações de pós-compilação** secção de configuração da tarefa, selecione **adicionar ação posterior à compilação** e selecione **carregar artefactos para o armazenamento de Blobs do Azure**.
6. Para **nome da conta de armazenamento**, selecione a conta de armazenamento a utilizar.
7. Para **nome do contentor**, especifique o nome do contentor. (O contentor será criado se ainda não exista quando são carregados os artefactos de compilação.) Pode utilizar variáveis de ambiente, então, neste exemplo introduza `${JOB_NAME}` como o nome do contentor.
   
    **Sugestão**
   
    Abaixo da **comando** secção onde inseriu um script para **comando batch de executar o Windows** é uma ligação para as variáveis de ambiente reconhecido pelo Jenkins. Selecione essa ligação para saber os nomes de variáveis de ambiente e as descrições. Variáveis de ambiente que contêm caracteres especiais, como o **BUILD_URL** variável de ambiente, não são permitidos como um nome de contentor ou o caminho virtual comum.
8. Selecione **tornar o novo contentor público por predefinição** para este exemplo. (Se quiser usar um contêiner privado, terá de criar uma assinatura de acesso partilhado para permitir o acesso, o que está além do escopo deste artigo. Pode saber mais sobre as assinaturas de acesso partilhado no [usando partilhado assinaturas de acesso (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Opcional] Selecione **limpa contentor antes de carregar** se pretender que o contentor para ser limpos do conteúdo antes de artefactos de compilação são carregados (deixe a opção desmarcada se não pretender limpar o conteúdo do contêiner).
10. Para **lista de artefactos para carregar**, introduza `text/*.txt`.
11. Para **caminho virtual comum para artefactos carregados**, para efeitos deste tutorial, introduza `${BUILD\_ID}/${BUILD\_NUMBER}`.
12. Selecione **guardar** para guardar as definições.
13. No dashboard do Jenkins, selecione **compilar agora** para ser executado **MyJob**. Examine o resultado da consola para obter o estado. Mensagens de estado para o armazenamento do Azure serão incluídas na saída da consola, quando a ação de pós-compilação começa a carregar os artefactos de compilação.
14. Após a conclusão bem-sucedida da tarefa, pode examinar os artefactos de compilação, abrindo o blob público.
    1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
    2. Selecione **Armazenamento**.
    3. Selecione o nome de conta de armazenamento que utilizou para o Jenkins.
    4. Selecione **contentores**.
    5. Selecione o contentor com o nome **myjob**, que é a versão em minúsculas do nome da tarefa que atribuiu quando criou a tarefa do Jenkins. Nomes de contentores e BLOBs são minúsculas (e diferencia maiúsculas de minúsculas) no armazenamento do Azure. Na lista de blobs para o contentor com o nome **myjob**, deverá ver **hello.txt** e **date.txt**. Copie o URL para qualquer um desses itens e abra-o no seu browser. Verá o ficheiro de texto que foi carregado como um artefacto de compilação.

Apenas uma ação de pós-compilação que carrega artefactos para o armazenamento de Blobs do Azure pode ser criada por tarefa. A única ação de pós-compilação para carregar os artefactos para o armazenamento de Blobs do Azure pode especificar diferentes arquivos (incluindo carateres universais) e caminhos para ficheiros de dentro **lista de artefactos para carregar** com ponto e vírgula como separador. Por exemplo, se a compilação dos Jenkins produz ficheiros JAR e os ficheiros TXT em sua área de trabalho **crie** pasta e pretender carregar tanto para o armazenamento de Blobs do Azure, utilize o seguinte valor para o **lista de artefactos para carregar** opção: `build/\*.jar;build/\*.txt`. Também pode utilizar a sintaxe de double-vírgula para especificar um caminho a utilizar no nome do blob. Por exemplo, se pretender que o Intune para obter carregados usando **binários** no caminho do blob e os ficheiros TXT para obter carregados usando **avisos** no caminho do blob, utilize o seguinte valor para o **lista de Artefactos para carregar** opção: `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Como criar um passo de compilação que transfere do armazenamento de Blobs do Azure
Os passos seguintes mostram para configurar uma etapa de compilação para transferir itens do armazenamento de Blobs do Azure, o que é útil se desejar incluir itens em sua compilação. Um exemplo de como utilizar este padrão é JARs que talvez queira manter no armazenamento de Blobs do Azure.

1. Na **crie** secção de configuração da tarefa, selecione **Adicionar passo de compilação** e selecione **transferir a partir do armazenamento de Blobs do Azure**.
2. Para **nome da conta de armazenamento**, selecione a conta de armazenamento a utilizar.
3. Para **nome do contentor**, especifique o nome do contentor que tem os blobs que pretende transferir. Pode utilizar variáveis de ambiente.
4. Para **nome do Blob**, especifique o nome do blob. Pode utilizar variáveis de ambiente. Além disso, pode utilizar um asterisco como caráter universal depois de especificar o letter(s) inicial do nome do blob. Por exemplo, **project\*** especificaria todos os blobs cujos nomes começam com **projeto**.
5. [Opcional] Para **caminho de transferência**, especifique o caminho na máquina do Jenkins em que pretende transferir ficheiros do armazenamento de Blobs do Azure. Variáveis de ambiente também podem ser utilizadas. (Se não fornecer um valor para **caminho de transferência**, os ficheiros de armazenamento de Blobs do Azure serão transferidos para a área de trabalho da tarefa.)

Se tiver outros itens que pretende transferir a partir do armazenamento de Blobs do Azure, pode criar passos de compilação adicional.

Depois de executar uma compilação, pode verificar a saída de consola do histórico de compilação, ou examinar sua localização de transferência, para ver se os blobs destinados foram transferidos com êxito.  

## <a name="components-used-by-the-blob-service"></a>Componentes utilizados pelo serviço de BLOBs
Esta seção fornece uma descrição geral dos componentes do serviço de Blobs.

* **Conta de armazenamento**: Todos os acessos ao armazenamento do Azure é feito através de uma conta de armazenamento. Uma conta de armazenamento é o mais alto nível do espaço de nomes para aceder a blobs. Uma conta pode conter um número ilimitado de contentores, desde que seu tamanho total é de menos de 100 TB.
* **contentor**: Um contentor fornece um agrupamento de um conjunto de blobs. Todos os blobs tem de estar num contentor. Uma conta pode conter um número ilimitado de contentores. Um contentor pode armazenar um número ilimitado de blobs.
* **Blob**: Um ficheiro de qualquer tipo e tamanho. Existem dois tipos de blobs que podem ser armazenados no armazenamento do Azure: blobs de bloco e de página. A maioria dos arquivos são blobs de blocos. Um único blob de blocos pode ser até 200 GB de tamanho. Este tutorial utiliza os blobs de blocos. Blobs de páginas, outro tipo de blob, podem ser até 1 TB de tamanho e são mais eficiente quando intervalos de bytes num arquivo são modificados com frequência. Para obter mais informações sobre blobs, veja [Noções básicas sobre Blobs de blocos, Blobs de acréscimo e Blobs de páginas](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formato de URL**: Os BLOBs são endereçáveis através do formato de URL seguinte:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (O formato acima aplica-se para a cloud do Azure global. Se estiver a utilizar uma cloud do Azure diferente, utilize o ponto final dentro de [Portal do Azure](https://portal.azure.com) para determinar o ponto de final do URL.)
  
    No formato acima, `storageaccount` representa o nome da sua conta de armazenamento `container_name` representa o nome do seu contentor, e `blob_name` representa o nome do seu blob, respectivamente. Dentro do nome do contentor, pode ter vários caminhos, separados por uma barra, **/**. O nome do contentor de exemplo utilizado para este tutorial foi **MyJob**, e **${CRIE\_ID} / ${COMPILAR\_número}** foi utilizado para o caminho virtual comum, resultando no blob de ter um URL o formato seguinte:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Resolver problemas nos plug-ins do Jenkins

Se se deparar com erros nos plug-ins do Jenkins, comunique os problemas com os componentes específicos no [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Passos Seguintes
* [Conheça o Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Armazenamento do Azure SDK para Java](https://github.com/azure/azure-storage-java)
* [Azure Storage Client SDK Reference](http://dl.windowsazure.com/storage/javadoc/) (Referência do SDK do Cliente do Armazenamento do Azure)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogue da Equipa de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/)

Para obter mais informações, visite [Azure para programadores Java](/java/azure).
