---
title: Como utilizar Hudson com armazenamento de BLOBs | Documentos da Microsoft
description: Descreve como utilizar Hudson com o armazenamento de Blobs do Azure como repositório de artefactos de compilação.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: d00bf87a80e13808c42a5839ad0f4508ad7214b9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011102"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Utilizar o Armazenamento do Azure com uma solução Hudson de Integração Contínua
## <a name="overview"></a>Descrição geral
As informações seguintes mostram como utilizar o armazenamento de BLOBs como um repositório de artefactos de construção criado por uma solução Hudson de integração contínua (CI) ou como uma origem de ficheiros que pode ser baixados para uso num processo de compilação. É um dos cenários em que descobrirá que isso útil quando escreve o código num ambiente de desenvolvimento do agile (com o Java ou outras linguagens), compilações estão em execução com base na integração contínua e precisa de um repositório para seus artefactos de compilação, para que poderia, Por exemplo, partilhá-los com outros membros da organização, os seus clientes, ou manter um arquivo morto.  Outro cenário é quando a tarefa de compilação em si requer outros arquivos, por exemplo, as dependências para transferir como parte da compilação de entrada.

Neste tutorial irá utilizar o plug-in do armazenamento do Azure para Hudson CI disponibilizado pela Microsoft.

## <a name="introduction-to-hudson"></a>Introdução ao Hudson
Hudson permite a integração contínua de um projeto de software, permitindo aos programadores integrar facilmente as alterações de código e tem compilações produzidos automaticamente e com frequência, assim, aumentar a produtividade dos desenvolvedores. As compilações são com versão e artefactos de compilação podem ser carregados para vários repositórios. Este artigo mostra como utilizar o armazenamento de Blobs do Azure como repositório de artefactos de compilação. Ela também mostrará como transferir as dependências do armazenamento de Blobs do Azure.

Obter mais informações sobre Hudson podem ser encontradas em [Hudson cumprir](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Benefícios da utilização do serviço de BLOBs
Benefícios da utilização do serviço de BLOBs para hospedar seus artefactos de compilação do desenvolvimento ágil:

* Elevada disponibilidade de seus artefactos de compilação e/ou dependências que pode ser baixadas.
* Desempenho quando a sua solução Hudson CI carrega artefactos de compilação.
* Desempenho quando os seus clientes e parceiros transferir os artefactos de compilação.
* Controlo sobre políticas de acesso de utilizador, com uma escolha entre o acesso anônimo, acesso de assinatura de acesso partilhado com base em expiração, privada acesso, etc.

## <a name="prerequisites"></a>Pré-requisitos
É necessário o seguinte para utilizar o serviço de Blobs com a sua solução Hudson CI:

* Uma solução Hudson de integração contínua.
  
    Se não tiver atualmente uma solução Hudson CI, pode executar uma solução Hudson CI usando a técnica seguinte:
  
  1. Numa máquina ativada do Java, transfira o WAR Hudson de <http://hudson-ci.org/>.
  2. No prompt de comando que é aberto para a pasta que contém o WAR Hudson, execute o WAR Hudson. Por exemplo, se tiver baixado a versão 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. No browser, abra `http://localhost:8080/`. Esta ação irá abrir o dashboard de Hudson.
  4. Após a primeira utilização de Hudson, concluir a configuração inicial em `http://localhost:8080/`.
  5. Depois de concluir a configuração inicial, cancelar a instância em execução de WAR o Hudson, inicie novamente o WAR Hudson e volte a abrir o dashboard de Hudson `http://localhost:8080/`, que irá utilizar para instalar e configurar o plug-in do armazenamento do Azure.
     
      Embora uma solução Hudson CI típica poderia ser configurada para ser executado como um serviço, em execução na guerra Hudson na linha de comandos será suficiente para este tutorial.
* Uma conta do Azure. Pode inscrever-se numa conta do Azure em <https://www.azure.com>.
* Uma conta de armazenamento do Azure. Se ainda não tiver uma conta de armazenamento, pode criar uma com os passos indicados em [criar uma conta de armazenamento](../common/storage-quickstart-create-account.md).
* Familiaridade com a solução Hudson CI é recomendada, mas não obrigatório, como o seguinte conteúdo irá utilizar um exemplo básico para mostrar as etapas necessárias ao utilizar o serviço de BLOBs como um repositório para Hudson CI artefactos de compilação.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Como utilizar o serviço de Blobs com Hudson CI
Para utilizar o serviço de Blobs com Hudson, terá de instalar o plug-in do armazenamento do Azure, configurar o plug-in para utilizar a sua conta de armazenamento e, em seguida, crie uma ação de pós-compilação que carrega artefactos de compilação para a sua conta de armazenamento. Estes passos são descritos nas secções seguintes.

## <a name="how-to-install-the-azure-storage-plugin"></a>Como instalar o plug-in do armazenamento do Azure
1. No painel Hudson, clique em **Hudson gerir**.
2. Sobre o **gerir Hudson** página, clique em **gerir plug-ins**.
3. Clique nas **disponível** separador.
4. Clique em **outros**.
5. Na **carregadores de artefacto** secção, selecione **Plug-in do armazenamento do Microsoft Azure**.
6. Clique em **Instalar**.
7. Após a instalação estiver concluída, reinicie Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Como configurar o plug-in do armazenamento do Azure para utilizar a sua conta de armazenamento
1. No painel Hudson, clique em **Hudson gerir**.
2. Sobre o **gerir Hudson** página, clique em **configurar o sistema**.
3. Na **configuração de conta de armazenamento do Microsoft Azure** secção:
   
    a. Introduza o nome de conta de armazenamento, que pode obter a partir da [Portal do Azure](https://portal.azure.com).
   
    b. Introduza a chave de conta de armazenamento, também obtidas por meio dos [Portal do Azure](https://portal.azure.com).
   
    c. Utilize o valor predefinido para **URL de ponto final do serviço Blob** se estiver a utilizar a cloud do Azure pública. Se estiver a utilizar uma cloud do Azure diferente, utilize o ponto de extremidade, conforme especificado na [Portal do Azure](https://portal.azure.com) para a sua conta de armazenamento.
   
    d. Clique em **validar as credenciais de armazenamento** para validar a conta de armazenamento.
   
    e. [Opcional] Se tiver mais contas de armazenamento que pretende que fiquem disponíveis para sua Hudson CI, clique em **adicionar mais contas de armazenamento**.
   
    f. Clique em **guardar** para guardar as definições.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Como criar uma ação de pós-compilação que carrega artefactos de compilação para a sua conta de armazenamento
Para fins de instrução, primeiro, precisaremos criar uma tarefa que irá criar vários arquivos e, em seguida, adicionar a ação de pós-compilação para carregar os ficheiros para a sua conta de armazenamento.

1. No painel Hudson, clique em **nova tarefa**.
2. Nome da tarefa **MyJob**, clique em **criar uma tarefa de software de estilo livre**e, em seguida, clique em **OK**.
3. Na **crie** secção da configuração da tarefa, clique em **Adicionar passo de compilação** e escolha **Windows executar comandos do batch**.
4. Na **comando**, utilize os seguintes comandos:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. Na **ações de pós-compilação** secção de configuração da tarefa, clique em **carregar artefactos para o armazenamento de Blobs do Microsoft Azure**.
6. Para **nome da conta de armazenamento**, selecione a conta de armazenamento a utilizar.
7. Para **nome do contentor**, especifique o nome do contentor. (O contentor será criado se ainda não exista quando são carregados os artefactos de compilação.) Pode utilizar variáveis de ambiente, então, neste exemplo introduza **${JOB_NAME}** como o nome do contentor.
   
    **Sugestão**
   
    Abaixo da **comando** secção onde inseriu um script para **comando batch de executar o Windows** é uma ligação para as variáveis de ambiente reconhecido pelo Hudson. Clique nessa ligação para saber os nomes de variáveis de ambiente e as descrições. Tenha em atenção que as variáveis de ambiente que contêm especial carateres, como o **BUILD_URL** variável de ambiente, não são permitidos como um nome de contentor ou o caminho virtual comum.
8. Clique em **tornar o novo contentor público por predefinição** para este exemplo. (Se quiser usar um contêiner privado, terá de criar uma assinatura de acesso partilhado para permitir o acesso. Que está além do escopo deste artigo. Pode saber mais sobre as assinaturas de acesso partilhado no [usando partilhado assinaturas de acesso (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Opcional] Clique em **limpa contentor antes de carregar** se pretender que o contentor para ser limpos do conteúdo antes de artefactos de compilação são carregados (deixe a opção desmarcada se não pretender limpar o conteúdo do contêiner).
10. Para **lista de artefactos para carregar**, introduza **texto/*.txt**.
11. Para **caminho virtual comum para artefactos carregados**, introduza **${CRIE\_ID} / ${COMPILAR\_número}**.
12. Clique em **guardar** para guardar as definições.
13. No dashboard do Hudson, clique em **compilar agora** para ser executado **MyJob**. Examine o resultado da consola para obter o estado. Mensagens de estado do armazenamento do Azure serão incluídas na saída da consola, quando a ação de pós-compilação começa a carregar os artefactos de compilação.
14. Após a conclusão bem-sucedida da tarefa, pode examinar os artefactos de compilação, abrindo o blob público.
    
    a. Inicie sessão no [Portal do Azure](https://portal.azure.com).
    
    b. Clique em **armazenamento**.
    
    c. Clique no nome de conta de armazenamento que utilizou para Hudson.
    
    d. Clique em **contentores**.
    
    e. Clique em contentor com o nome **myjob**, que é a versão em minúsculas do nome da tarefa que atribuiu quando criou a tarefa de Hudson. Nomes de contentores e BLOBs são minúsculas (e diferencia maiúsculas de minúsculas) no armazenamento do Azure. Na lista de blobs para o contentor com o nome **myjob** deverá ver **hello.txt** e **date.txt**. Copie o URL para qualquer um desses itens e abra-o no seu browser. Verá o ficheiro de texto que foi carregado como um artefacto de compilação.

Apenas uma ação de pós-compilação que carrega artefactos para o armazenamento de Blobs do Azure pode ser criada por tarefa. Tenha em atenção que a única ação de pós-compilação para carregar os artefactos para o armazenamento de Blobs do Azure pode especificar diferentes arquivos (incluindo carateres universais) e caminhos para ficheiros de dentro **lista de artefactos para carregar** com ponto e vírgula como separador. Por exemplo, se criar seu Hudson produz ficheiros JAR e os ficheiros TXT em sua área de trabalho **crie** pasta e pretender carregar tanto para o armazenamento de Blobs do Azure, utilize o seguinte para o **lista de artefactos para carregar** valor: **criar /\*.JAR;compilação/\*.txt**. Também pode utilizar a sintaxe de double-vírgula para especificar um caminho a utilizar no nome do blob. Por exemplo, se pretender que o Intune para obter carregados usando **binários** no caminho do blob e os ficheiros TXT para obter carregados usando **avisos** no caminho do blob, utilize o seguinte para o **lista de artefactos para carregar** valor: **criar/\*.jar::binaries;compilação/\*.txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Como criar um passo de compilação que transfere do armazenamento de Blobs do Azure
Os passos seguintes mostram como configurar um passo de compilação para transferir itens do armazenamento de Blobs do Azure. Isso seria útil se desejar incluir itens em sua compilação, por exemplo, JARs manter no armazenamento de Blobs do Azure.

1. No **crie** secção de configuração da tarefa, clique em **Adicionar passo de compilação** e escolha **transferir a partir do armazenamento de Blobs do Azure**.
2. Para **nome da conta de armazenamento**, selecione a conta de armazenamento a utilizar.
3. Para **nome do contentor**, especifique o nome do contentor que tem os blobs que pretende transferir. Pode utilizar variáveis de ambiente.
4. Para **nome do Blob**, especifique o nome do blob. Pode utilizar variáveis de ambiente. Além disso, pode utilizar um asterisco como caráter universal depois de especificar o letter(s) inicial do nome do blob. Por exemplo, **project\\*** de especificar todos os blobs cujos nomes começam com **projeto**.
5. [Opcional] Para **caminho de transferência**, especifique o caminho na máquina Hudson onde pretende transferir ficheiros do armazenamento de Blobs do Azure. Variáveis de ambiente também podem ser utilizadas. (Se não fornecer um valor para **caminho de transferência**, os ficheiros do armazenamento de Blobs do Azure serão transferidos para a área de trabalho da tarefa.)

Se tiver outros itens que pretende transferir a partir do armazenamento de Blobs do Azure, pode criar passos de compilação adicional.

Depois de executar uma compilação, pode verificar a saída de consola do histórico de compilação, ou examinar sua localização de transferência, para ver se os blobs destinados foram transferidos com êxito.

## <a name="components-used-by-the-blob-service"></a>Componentes utilizados pelo serviço de BLOBs
A seguir, fornecemos uma visão geral dos componentes do serviço de Blobs.

* **Conta de armazenamento**: Todos os acessos ao armazenamento do Azure é feito através de uma conta de armazenamento. Este é o mais alto nível do espaço de nomes para aceder a blobs. Uma conta pode conter um número ilimitado de contentores, desde que seu tamanho total é de menos de 100 TB.
* **contentor**: Um contentor fornece um agrupamento de um conjunto de blobs. Todos os blobs tem de estar num contentor. Uma conta pode conter um número ilimitado de contentores. Um contentor pode armazenar um número ilimitado de blobs.
* **Blob**: Um ficheiro de qualquer tipo e tamanho. Existem dois tipos de blobs que podem ser armazenados no armazenamento do Azure: blobs de bloco e de página. A maioria dos arquivos são blobs de blocos. Um único blob de blocos pode ser até 200 GB de tamanho. Este tutorial utiliza os blobs de blocos. Blobs de páginas, outro tipo de blob, podem ser até 1 TB de tamanho e são mais eficiente quando intervalos de bytes num arquivo são modificados com frequência. Para obter mais informações sobre blobs, veja [Noções básicas sobre Blobs de blocos, Blobs de acréscimo e Blobs de páginas](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formato de URL**: Os BLOBs são endereçáveis através do formato de URL seguinte:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (O formato acima aplica-se para a cloud do Azure pública. Se estiver a utilizar uma cloud do Azure diferente, utilize o ponto final dentro de [Portal do Azure](https://portal.azure.com) para determinar o ponto de final do URL.)
  
    No formato acima, `storageaccount` representa o nome da sua conta de armazenamento `container_name` representa o nome do seu contentor, e `blob_name` representa o nome do seu blob, respectivamente. Dentro do nome do contentor, pode ter vários caminhos, separados por uma barra, **/**. O nome do contentor de exemplo neste tutorial foi **MyJob**, e **${CRIE\_ID} / ${COMPILAR\_número}** foi utilizado para o caminho virtual comum, resultando no blob de ter um URL do formulário seguinte:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Passos Seguintes
* [Cumprir Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Armazenamento do Azure SDK para Java](https://github.com/azure/azure-storage-java)
* [Azure Storage Client SDK Reference](http://dl.windowsazure.com/storage/javadoc/) (Referência do SDK do Cliente do Armazenamento do Azure)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogue da Equipa de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/)

Para obter mais informações, visite [Azure para programadores Java](/java/azure).
