---
title: Instalar o MongoDB num Windows VM no Azure | Documentos da Microsoft
description: Saiba como instalar o MongoDB numa VM do Azure, o Windows Server 2012 R2 criada com o modelo de implementação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: afd8e6b47fb86985acde062af1fb38ec3af4e902
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998493"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalar e configurar o MongoDB numa VM do Windows no Azure
[MongoDB](https://www.mongodb.org) é um banco popular dados NoSQL de código-fonte aberto de alto desempenho. Este artigo o orienta através da instalação e configuração MongoDB numa máquina virtual (VM) do Windows Server 2016 no Azure. Também pode [instalar o MongoDB numa VM do Linux no Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de instalar e configurar o MongoDB, terá de criar uma VM e, idealmente, adicione um disco de dados ao mesmo. Veja os artigos seguintes para criar uma VM e adicione um disco de dados:

* Criar uma VM do Windows Server com [do portal do Azure](quick-create-portal.md) ou [Azure PowerShell](quick-create-powershell.md).
* Anexar um disco de dados a uma VM do Windows Server usando [do portal do Azure](attach-managed-disk-portal.md) ou [Azure PowerShell](attach-disk-ps.md).

Para começar a instalar e configurar o MongoDB, [inicie sessão no Windows Server VM](connect-logon.md) utilizando o ambiente de trabalho remoto.

## <a name="install-mongodb"></a>Instalar MongoDB
> [!IMPORTANT]
> Recursos de segurança do MongoDB, como a autenticação e o enlace de endereço IP, não estão ativados por predefinição. Recursos de segurança devem ser ativados antes de implantar o MongoDB num ambiente de produção. Para obter mais informações, consulte [MongoDB segurança e autenticação](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Depois de se ligar a sua VM com o ambiente de trabalho remoto, abra o Internet Explorer da barra de tarefas.
2. Selecione **utilizar as definições de segurança, privacidade e compatibilidade recomendadas** quando o Internet Explorer primeiro abre e clique em **OK**.
3. Configuração de segurança avançada do Internet Explorer está ativada por predefinição. Adicione o site do MongoDB à lista de sites autorizados:
   
   * Selecione o **ferramentas** ícone no canto superior direito.
   * Na **opções da Internet**, selecione a **Security** separador e, em seguida, selecione o **Sites confiáveis** ícone.
   * Clique nas **Sites** botão. Adicione *https://\*. mongodb.com* à lista de sites fidedignos e, em seguida, feche a caixa de diálogo.
     
     ![Configurar definições de segurança do Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Navegue para o [MongoDB - Downloads](https://www.mongodb.com/downloads) página (https://www.mongodb.com/downloads).
5. Se for necessário, selecione o **Community Server** edition e em seguida, selecione a estabilidade atual mais recente de versão do*Windows Server 2008 R2 de 64 bits e posterior*. Para transferir o instalador, clique em **DOWNLOAD (msi)**.
   
    ![Transferir o instalador do MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Depois de concluída a transferência, execute o instalador.
6. Leia e aceite o contrato de licença. Quando lhe for pedido, selecione **Complete** instalar.
7. Se assim o desejar, pode optar por instalar também a bússola, uma interface gráfica para MongoDB.
8. No ecrã final, clique em **instalar**.

## <a name="configure-the-vm-and-mongodb"></a>Configurar a VM e MongoDB
1. As variáveis de caminho não são atualizadas pelo instalador do MongoDB. Sem o MongoDB `bin` local na variável do caminho, tem de especificar o caminho completo sempre que usar um executável do MongoDB. Para adicionar a localização para a variável de caminho:
   
   * Com o botão direito a **começar** menu e selecione **sistema**.
   * Clique em **definições de sistema avançadas**e, em seguida, clique em **variáveis de ambiente**.
   * Sob **variáveis do sistema**, selecione **caminho**e, em seguida, clique em **editar**.
     
     ![Configurar variáveis de caminho](./media/install-mongodb/configure-path-variables.png)
     
     Adicionar o caminho para o MongoDB `bin` pasta. MongoDB é normalmente instalado na *C:\Program Files\MongoDB*. Verifique se o caminho de instalação na sua VM. O exemplo seguinte adiciona o padrão de localização para instalar o MongoDB a `PATH` variável:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Certifique-se de que adicionar o principal ponto e vírgula (`;`) para indicar que está a adicionar uma localização para seu `PATH` variável.

2. Crie diretórios de dados e de registo do MongoDB no seu disco de dados. Do **começar** menu, selecione **linha de comandos**. Os exemplos seguintes criar os diretórios na unidade f:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Iniciar uma instância de MongoDB com o seguinte comando, ajustando o caminho para os seus dados e diretórios de registo em conformidade:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Pode demorar vários minutos para o MongoDB alocar os ficheiros de diário e começar a escutar ligações. Todas as mensagens de registo são direcionadas para o *F:\MongoLogs\mongolog.log* file como `mongod.exe` servidor é iniciado e se aloca os ficheiros de diário.
   
   > [!NOTE]
   > A linha de comandos permanece focada nesta tarefa enquanto a instância do MongoDB está em execução. Deixe a janela de linha de comandos aberta para continuar a executar o MongoDB. Em alternativa, instalar o MongoDB como serviço, conforme detalhado no próximo passo.

4. Para uma experiência mais robusta do MongoDB, instalar o `mongod.exe` como um serviço. Criação de um serviço significa que não precisa deixar um prompt de comando executando cada vez que pretende utilizar o MongoDB. Crie o serviço como a seguir, ajustando o caminho para os seus diretórios de dados e de registo em conformidade:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    O comando anterior cria um serviço com o nome MongoDB, com uma descrição de "Mongo DB". Também são especificados os seguintes parâmetros:
   
   * O `--dbpath` opção especifica a localização do diretório de dados.
   * O `--logpath` opção tem de ser utilizada para especificar um ficheiro de registo, porque o serviço em execução não tem uma janela de comando para apresentar os resultados.
   * O `--logappend` opção especifica que um reinício do serviço faz com que a saída acrescentar ao ficheiro de registo existente.
   
   Para iniciar o serviço de MongoDB, execute o seguinte comando:
   
    ```
    net start MongoDB
    ```
   
    Para obter mais informações sobre como criar o serviço de MongoDB, consulte [configurar um serviço do Windows para MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testar a instância de MongoDB
Com o MongoDB em execução como uma única instância ou instalado como um serviço, pode agora começar a criar e utilizar as suas bases de dados. Para iniciar o shell do MongoDB administrativo, abra outra janela do prompt de comando do **iniciar** menu e introduza o seguinte comando:

```
mongo  
```

Pode listar as bases de dados com o `db` comando. Inserir alguns dados da seguinte forma:

```
db.foo.insert( { a : 1 } )
```

Procure dados da seguinte forma:

```
db.foo.find()
```

O resultado é semelhante ao seguinte exemplo:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Saída do `mongo` consola da seguinte forma:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configurar a firewall e regras do grupo de segurança de rede
Agora que o MongoDB é instalado e em execução, abra uma porta na Firewall do Windows para que possa ligar remotamente ao MongoDB. Para criar uma nova regra de entrada para permitir que a porta TCP 27017, abra uma linha de comandos administrativa do PowerShell e introduza o seguinte comando:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Também pode criar a regra ao utilizar o **Firewall do Windows com segurança avançada** ferramenta de gestão gráficas. Crie uma nova regra de entrada para permitir que a porta TCP 27017.

Se for necessário, crie uma regra de grupo de segurança de rede para permitir o acesso ao MongoDB a partir de fora a sub-rede de rede virtual do Azure existente. Pode criar as regras do grupo de segurança de rede com o [portal do Azure](nsg-quickstart-portal.md) ou [Azure PowerShell](nsg-quickstart-powershell.md). Tal como acontece com as regras de Firewall do Windows, permitir que a porta TCP 27017 à interface de rede virtual da sua VM do MongoDB.

> [!NOTE]
> Porta TCP 27017 é a porta predefinida utilizada por MongoDB. Pode alterar esta porta, utilizando o `--port` parâmetro ao iniciar `mongod.exe` manualmente ou através de um serviço. Se alterar a porta, certifique-se atualizar as regras de Firewall do Windows e o grupo de segurança de rede nos passos anteriores.


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a instalar e configurar o MongoDB na sua VM do Windows. Agora pode aceder MongoDB na sua VM do Windows, ao seguir os tópicos avançados a [documentação do MongoDB](https://docs.mongodb.com/manual/).

