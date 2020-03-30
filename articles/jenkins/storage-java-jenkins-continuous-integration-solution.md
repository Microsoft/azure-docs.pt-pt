---
title: Utilização do Armazenamento Azure com uma solução de integração contínua jenkins
description: Este tutorial mostra como usar o serviço de blob Azure como um repositório para construir artefactos criados por uma solução de integração contínua Jenkins.
keywords: jenkins, azul, devops, armazenamento, cicd
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: df1d59c40fd09fb055db9d7622d86ff9c82991b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624686"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Utilização do Armazenamento Azure com uma solução de integração contínua jenkins

Este artigo ilustra como usar o armazenamento Blob como um repositório de artefactos de construção criados por uma solução de integração contínua jenkins (CI) ou como uma fonte de ficheiros descarregados para serem usados num processo de construção. Um dos cenários em que você acha ria esta solução útil é quando você está codificando em um ambiente de desenvolvimento ágil (usando Java ou outras línguas), as construções estão funcionando com base na integração contínua, e você precisa de um repositório para seus artefactos de construção, de modo que poderia, por exemplo, partilhá-los com outros membros da organização, os seus clientes, ou manter um arquivo. Outro cenário é quando o seu próprio trabalho de construção requer outros ficheiros, por exemplo, dependências para descarregar como parte da entrada de construção.

Neste tutorial, estará a utilizar o Plugin de Armazenamento Azure para o CI Jenkins disponibilizado pela Microsoft.

## <a name="jenkins-overview"></a>Visão geral de Jenkins

Jenkins permite a integração contínua de um projeto de software, permitindo que os desenvolvedores integrem facilmente as suas alterações de código e tenham construído si de forma automática e frequente, aumentando assim a produtividade dos desenvolvedores. As construções são versonizadas e os artefactos de construção podem ser enviados para vários repositórios. Este artigo mostra como usar o armazenamento de blob Azure como o repositório dos artefactos de construção. Também mostrará como descarregar dependências do armazenamento de blob Azure.

Mais informações sobre jenkins podem ser encontradas no [Meet Jenkins.](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)

## <a name="benefits-of-using-the-blob-service"></a>Benefícios da utilização do serviço Blob

Os benefícios de usar o serviço Blob para acolher os seus artefactos ágeis de construção de desenvolvimento incluem:

* Alta disponibilidade dos seus artefactos de construção e/ou dependências transferíveis.
* Desempenho quando a sua solução Jenkins CI envia os seus artefactos de construção.
* Desempenho quando os seus clientes e parceiros descarregam os seus artefactos de construção.
* Controlo sobre as políticas de acesso ao utilizador, com uma escolha entre acesso anónimo, acesso partilhado baseado em expiração, acesso privado, etc.

## <a name="prerequisites"></a>Pré-requisitos

* Uma solução de integração contínua jenkins.
  
    Se não tiver uma solução Jenkins CI, pode executar uma solução Jenkins CI utilizando a seguinte técnica:
  
  1. Numa máquina ativada por Java, baixe <https://jenkins-ci.org>jenkins.war de .
  2. Num pedido de comando que é aberto à pasta que contém jenkins.war, corra:
     
      `java -jar jenkins.war`

  3. No seu navegador, abra `http://localhost:8080/` para abrir o painel Jenkins, que utilizará para instalar e configurar o plugin de Armazenamento Azure.
     
      Enquanto uma solução típica do Jenkins CI seria criada para funcionar como um serviço, executar a guerra jenkins na linha de comando será suficiente para este tutorial.
* Uma conta do Azure. Pode inscrever-se numa conta <https://www.azure.com>Azure em .
* Uma conta de armazenamento do Azure. Se ainda não tiver uma conta de armazenamento, pode criar uma utilizando os passos da [Conta Criar uma Conta](../storage/common/storage-account-create.md)de Armazenamento .
* A familiaridade com a solução Jenkins CI é recomendada, mas não é necessária, uma vez que o seguinte conteúdo usará um exemplo básico para mostrar-lhe os passos necessários ao usar o serviço Blob como um repositório para artefactos de construção de Ci Jenkins.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Como usar o serviço Blob com jenkins CI
Para utilizar o serviço Blob com o Jenkins, terá de instalar o plugin de Armazenamento Azure, configurar o plugin para utilizar a sua conta de armazenamento e, em seguida, criar uma ação pós-construção que carregue os seus artefactos de construção para a sua conta de armazenamento. Estes passos são descritos nas seguintes secções.

## <a name="how-to-install-the-azure-storage-plugin"></a>Como instalar o plugin de armazenamento Azure
1. Dentro do painel jenkins, selecione **Manage Jenkins**.
2. Na página **Manage Jenkins,** selecione **Manage Plugins**.
3. Selecione o separador **Available** (Disponíveis).
4. Na secção **Deuploaders de Artefactos,** verifique o **plugin de armazenamento do Microsoft Azure**.
5. Selecione instalar **sem reiniciar** ou **descarregue agora e instale depois**de reiniciar .
6. Reinicie jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Como configurar o plugin de Armazenamento Azure para usar a sua conta de armazenamento
1. Dentro do painel jenkins, selecione **Manage Jenkins**.
2. Na página **Manage Jenkins,** selecione **Configure System**.
3. Na secção de configuração da conta de **armazenamento do Microsoft Azure:**
   1. Insira o nome da sua conta de armazenamento, que pode obter no [portal Azure](https://portal.azure.com).
   2. Introduza a chave da sua conta de armazenamento, também obtida a partir do [portal Azure.](https://portal.azure.com)
   3. Utilize o valor padrão para **o URL de ponto final do serviço Blob** se estiver a utilizar a nuvem azure global. Se estiver a utilizar uma nuvem Azure diferente, utilize o ponto final conforme especificado no [portal Azure](https://portal.azure.com) para a sua conta de armazenamento. 
   4. Selecione **Validar credenciais** de armazenamento para validar a sua conta de armazenamento. 
   5. [Opcional] Se tiver contas de armazenamento adicionais que deseja disponibilizar ao seu CI Jenkins, selecione **Adicionar mais Contas**de Armazenamento .
   6. Selecione **Guardar** para guardar as suas definições.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Como criar uma ação pós-construção que carregue os seus artefactos de construção para a sua conta de armazenamento
Para fins instrutivos, primeiro é necessário criar um trabalho que crie vários ficheiros e, em seguida, adicione a ação pós-construção para fazer o upload dos ficheiros para a sua conta de armazenamento.

1. Dentro do painel jenkins, selecione **New Item**.
2. Nomeie o trabalho **MyJob,** **selecione Construir um projeto de software de estilo livre,** e depois selecione **OK**.
3. Na secção **Construção** da configuração de trabalho, selecione Adicionar passo de **construção** e selecionar **executar comando de lote windows**.
4. No **Comando,** utilize os seguintes comandos:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. Na secção **de Ações Pós-Construção** da configuração do trabalho, selecione **Adicionar ação pós-construção** e selecione **artefactos de upload para armazenamento De Blob Azure**.
6. Para obter o nome da **conta de armazenamento,** selecione a conta de armazenamento a utilizar.
7. Para **o nome do recipiente,** especifique o nome do recipiente. (O recipiente será criado se ainda não existir quando os artefactos de construção forem carregados.) Pode utilizar variáveis ambientais, por `${JOB_NAME}` isso, para este exemplo, introduza como nome do recipiente.
   
    **Dica**
   
    Abaixo da secção **Comando** onde introduziu um script para executar o comando do **lote do Windows** está uma ligação às variáveis ambientais reconhecidas por Jenkins. Selecione esse link para aprender os nomes e descrições variáveis do ambiente. Variáveis ambientais que contenham caracteres especiais, como a **variável ambiente BUILD_URL,** não são permitidas como um nome de contentor ou um caminho virtual comum.
8. Selecione **Tornar o novo recipiente público por defeito** para este exemplo. (Se quiser utilizar um recipiente privado, terá de criar uma assinatura de acesso partilhado para permitir o acesso, que está fora do âmbito deste artigo. Pode saber mais sobre assinaturas de acesso partilhado na Utilização de Assinaturas de [Acesso Partilhado (SAS)](../storage/common/storage-sas-overview.md).)
9. [Opcional] Selecione **Recipiente Limpar antes** de carregar se pretender que o recipiente seja limpo do conteúdo antes de serem enviados artefactos de construção (deixe-o descontrolado se não quiser limpar o conteúdo do recipiente).
10. Para que a Lista de `text/*.txt` **Artefactos faça upload,** introduza .
11. Para o **caminho virtual comum para artefactos carregados,** para efeitos deste tutorial, entre . `${BUILD\_ID}/${BUILD\_NUMBER}`
12. Selecione **Guardar** para guardar as suas definições.
13. No painel jenkins, selecione **Build Now** para executar **MyJob**. Examine a saída da consola para obter o estado. As mensagens de estado para o armazenamento do Azure serão incluídas na saída da consola quando a ação pós-construção começar a carregar artefactos de construção.
14. Após a conclusão bem sucedida do trabalho, você pode examinar os artefactos de construção abrindo a bolha pública.
    1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
    2. Selecione **Armazenamento**.
    3. Selecione o nome da conta de armazenamento que usou para o Jenkins.
    4. Selecione **Recipientes**.
    5. Selecione o recipiente chamado **myjob**, que é a versão minúscula do nome de trabalho que atribuiu quando criou o emprego de Jenkins. Os nomes dos contentores e os nomes de bolhas são minúsculos (e sensíveis a casos) no armazenamento do Azure. Dentro da lista de bolhas para o contentor chamado **myjob,** você deve ver **hello.txt** e **date.txt**. Copie o URL para qualquer um destes itens e abra-o no seu navegador. Verá o ficheiro de texto que foi enviado como um artefacto de construção.

Apenas uma ação pós-construção que envia artefactos para o armazenamento de blob Azure pode ser criada por trabalho. A única ação pós-construção para enviar artefactos para o armazenamento de blob Azure pode especificar diferentes ficheiros (incluindo wildcards) e caminhos para ficheiros dentro **da Lista de Artefactos para carregar** usando um semi-cólon como separador. Por exemplo, se a sua construção Jenkins produzir ficheiros JAR e ficheiros TXT na pasta de **construção** do seu espaço de trabalho, e quiser fazer o upload tanto para o armazenamento de blob Azure, use o seguinte valor para a **Lista de Artefactos para carregar a** opção: `build/\*.jar;build/\*.txt`. Também pode usar sintaxe de dois cólons para especificar um caminho a utilizar dentro do nome blob. Por exemplo, se pretender que os JARs sejam carregados usando **binários** no caminho da bolha e os ficheiros TXT sejam carregados usando **avisos** no caminho da bolha, utilize o seguinte valor para a **lista de artefactos para carregar a** opção: `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Como criar um passo de construção que descarrega do armazenamento de blob Azure
Os seguintes passos ilustram para configurar um passo de construção para descarregar itens do armazenamento de blob Azure, o que é útil se quiser incluir itens na sua construção. Um exemplo de utilização deste padrão são os JARs que você pode querer persistir no armazenamento de blob Azure.

1. Na secção **Construção** da configuração de trabalho, selecione Adicionar passo de **construção** e selecione **Download a partir do armazenamento Azure Blob**.
2. Para obter o nome da **conta de armazenamento,** selecione a conta de armazenamento a utilizar.
3. Para **o nome do recipiente,** especifique o nome do recipiente que tem as bolhas que pretende descarregar. Pode usar variáveis ambientais.
4. Para **o nome Blob,** especifique o nome blob. Pode usar variáveis ambientais. Além disso, pode utilizar um asterisco, como um wildcard depois de especificar a letra ou s inicial do nome blob. Por exemplo, **o\\projeto*** especificaria todas as bolhas cujos nomes começam com o **projeto**.
5. [Opcional] Para descarregar o **caminho,** especifique o caminho na máquina Jenkins onde pretende descarregar ficheiros a partir do armazenamento de blob Azure. As variáveis ambientais também podem ser usadas. (Se não fornecer um valor para o **caminho de descarregamento,** os ficheiros do armazenamento de blob Azure serão descarregados para o espaço de trabalho do trabalho.)

Se tiver itens adicionais que pretende descarregar no armazenamento de blob Azure, pode criar passos adicionais de construção.

Depois de executar uma construção, pode verificar a saída da consola de histórico de construção, ou olhar para a sua localização de descarregamento, para ver se as bolhas que esperava foram descarregadas com sucesso.  

## <a name="components-used-by-the-blob-service"></a>Componentes utilizados pelo serviço Blob
Esta secção fornece uma visão geral dos componentes do serviço Blob.

* **Conta de Armazenamento**: Todo o acesso ao Armazenamento Azure é feito através de uma conta de armazenamento. Uma conta de armazenamento é o nível mais alto do espaço de nome para aceder a bolhas. Uma conta pode conter um número ilimitado de contentores, desde que o seu tamanho total seja inferior a 100 TB.
* **Recipiente**: Um recipiente proporciona um agrupamento de um conjunto de bolhas. Todos os blobs tem de estar num contentor. Uma conta pode conter um número ilimitado de contentores. Um contentor pode armazenar um número ilimitado de blobs.
* **Blob**: Um ficheiro de qualquer tipo e tamanho. Existem dois tipos de bolhas que podem ser armazenadas no Armazenamento Azure: blocos e bolhas de página. A maioria dos ficheiros são blocos. Uma única bolha de bloco pode ter até 200 GB de tamanho. Este tutorial usa bolhas de bloco. As bolhas de página, outro tipo de bolha, podem ter até 1 TB de tamanho, e são mais eficientes quando as gamas de bytes num ficheiro são modificadas frequentemente. Para mais informações sobre bolhas, consulte [Understanding Block Blobs, Append Blobs e Page Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formato URL**: As bolhas são endereçadas utilizando o seguinte formato URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (O formato acima aplica-se à nuvem azure global. Se estiver a utilizar uma nuvem Azure diferente, utilize o ponto final dentro do [portal Azure](https://portal.azure.com) para determinar o seu ponto final URL.)
  
    No formato `storageaccount` acima, representa o nome `container_name` da sua conta de armazenamento, representa o nome do seu recipiente, e `blob_name` representa o nome da sua bolha, respectivamente. Dentro do nome do recipiente, pode ter múltiplos caminhos, separados por um corte dianteiro, **/**. O nome do recipiente de exemplo usado para este tutorial foi **MyJob**, e **${BUILD\_\_ID}/${BUILD NUMBER}** foi usado para o caminho virtual comum, resultando na bolha ter um URL do seguinte formulário:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Resolver problemas nos plug-ins do Jenkins

Se se deparar com erros nos plug-ins do Jenkins, comunique os problemas com os componentes específicos no [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Jenkins em Azure](/azure/Jenkins/)