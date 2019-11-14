---
title: Instalar o MongoDB em uma VM do Windows no Azure
description: Saiba como instalar o MongoDB em uma VM do Azure que executa o Windows Server 2012 R2 criado com o modelo de implantação do Gerenciador de recursos.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 37c1b58d364e7eadb33803ce7eac1f2b956ec1b6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038540"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalar e configurar o MongoDB em uma VM do Windows no Azure
O [MongoDB](https://www.mongodb.org) é um popular banco de dados NoSQL de software livre e de alto desempenho. Este artigo orienta você pela instalação e configuração do MongoDB em uma VM (máquina virtual) do Windows Server 2016 no Azure. Você também pode [instalar o MongoDB em uma VM do Linux no Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de instalar e configurar o MongoDB, você precisa criar uma VM e, idealmente, adicionar um disco de dados a ela. Consulte os artigos a seguir para criar uma VM e adicionar um disco de dados:

* Crie uma VM do Windows Server usando [o portal do Azure](quick-create-portal.md) ou [Azure PowerShell](quick-create-powershell.md).
* Anexe um disco de dados a uma VM do Windows Server usando [o portal do Azure](attach-managed-disk-portal.md) ou [Azure PowerShell](attach-disk-ps.md).

Para começar a instalar e configurar o MongoDB, [faça logon na sua VM do Windows Server](connect-logon.md) usando área de trabalho remota.

## <a name="install-mongodb"></a>Instalar MongoDB
> [!IMPORTANT]
> Os recursos de segurança do MongoDB, como autenticação e Associação de endereço IP, não são habilitados por padrão. Os recursos de segurança devem ser habilitados antes da implantação do MongoDB em um ambiente de produção. Para obter mais informações, consulte [segurança e autenticação do MongoDB](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Depois de se conectar à sua VM usando o Área de Trabalho Remota, abra o Internet Explorer na barra de tarefas.
2. Selecione **usar configurações de segurança, privacidade e compatibilidade recomendadas** quando o Internet Explorer for aberto pela primeira vez e clique em **OK**.
3. A configuração de segurança reforçada do Internet Explorer é habilitada por padrão. Adicione o site do MongoDB à lista de sites permitidos:
   
   * Selecione o ícone de **ferramentas** no canto superior direito.
   * Em **Opções da Internet**, selecione a guia **segurança** e, em seguida, selecione o ícone **sites confiáveis** .
   * Clique no botão **sites** . Adicione *https://\*. MongoDB.com* à lista de sites confiáveis e, em seguida, feche a caixa de diálogo.
     
     ![Definir configurações de segurança do Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Navegue até a página de [downloads do MongoDB](https://www.mongodb.com/downloads) (https://www.mongodb.com/downloads).
5. Se necessário, selecione a edição do **Community Server** e, em seguida, selecione a versão estável atual mais recente para o*Windows Server 2008 R2 64-bit e posterior*. Para baixar o instalador, clique em **baixar (MSI)** .
   
    ![Baixar o instalador do MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Execute o instalador após a conclusão do download.
6. Leia e aceite o contrato de licença. Quando for solicitado, selecione **concluir** instalação.
7. Se desejar, você também pode optar por instalar o Compass, uma interface gráfica para o MongoDB.
8. Na tela final, clique em **instalar**.

## <a name="configure-the-vm-and-mongodb"></a>Configurar a VM e o MongoDB
1. As variáveis de caminho não são atualizadas pelo instalador do MongoDB. Sem o local de `bin` do MongoDB em sua variável path, você precisa especificar o caminho completo sempre que usar um executável do MongoDB. Para adicionar o local à variável path:
   
   * Clique com o botão direito do mouse no menu **Iniciar** e selecione **sistema**.
   * Clique em **Configurações avançadas do sistema**e clique em **variáveis de ambiente**.
   * Em **variáveis do sistema**, selecione **caminho**e clique em **Editar**.
     
     ![Configurar variáveis de caminho](./media/install-mongodb/configure-path-variables.png)
     
     Adicione o caminho à sua pasta `bin` do MongoDB. O MongoDB é normalmente instalado em *C:\Program Files\MongoDB*. Verifique o caminho de instalação em sua VM. O exemplo a seguir adiciona o local de instalação padrão do MongoDB à variável `PATH`:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Certifique-se de adicionar o ponto-e-vírgula à esquerda (`;`) para indicar que você está adicionando um local à sua variável de `PATH`.

2. Crie diretórios de dados e de log do MongoDB no disco de dados. No menu **Iniciar** , selecione **prompt de comando**. Os exemplos a seguir criam os diretórios na unidade F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Inicie uma instância do MongoDB com o comando a seguir, ajustando o caminho para seus dados e diretórios de log de acordo:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Pode levar vários minutos para que o MongoDB aloque os arquivos de diário e comece a escutar conexões. Todas as mensagens de log são direcionadas para o arquivo *F:\MongoLogs\mongolog.log* à medida que `mongod.exe` servidor é iniciado e aloca arquivos de diário.
   
   > [!NOTE]
   > O prompt de comando permanece focado nessa tarefa enquanto a instância do MongoDB está em execução. Deixe a janela de prompt de comando aberta para continuar executando o MongoDB. Ou instale o MongoDB como serviço, conforme detalhado na próxima etapa.

4. Para obter uma experiência mais robusta do MongoDB, instale o `mongod.exe` como um serviço. A criação de um serviço significa que você não precisa deixar um prompt de comando executando cada vez que desejar usar o MongoDB. Crie o serviço da seguinte maneira, ajustando o caminho para seus dados e diretórios de log de acordo:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    O comando anterior cria um serviço chamado MongoDB, com uma descrição de "Mongo DB". Os parâmetros a seguir também são especificados:
   
   * A opção `--dbpath` especifica o local do diretório de dados.
   * A opção `--logpath` deve ser usada para especificar um arquivo de log, pois o serviço em execução não tem uma janela de comando para exibir a saída.
   * A opção `--logappend` especifica que uma reinicialização do serviço faz com que a saída seja acrescentada ao arquivo de log existente.
   
   Para iniciar o serviço MongoDB, execute o seguinte comando:
   
    ```
    net start MongoDB
    ```
   
    Para obter mais informações sobre como criar o serviço MongoDB, consulte [configurar um serviço do Windows para o MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testar a instância do MongoDB
Com o MongoDB em execução como uma única instância ou instalado como um serviço, agora você pode começar a criar e usar seus bancos de dados. Para iniciar o Shell administrativo do MongoDB, abra outra janela do prompt de comando no menu **Iniciar** e digite o seguinte comando:

```
mongo  
```

Você pode listar os bancos de dados com o comando `db`. Insira alguns dados da seguinte maneira:

```
db.foo.insert( { a : 1 } )
```

Pesquise dados da seguinte maneira:

```
db.foo.find()
```

O resultado é semelhante ao seguinte exemplo:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Saia do console do `mongo` da seguinte maneira:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configurar regras de firewall e grupo de segurança de rede
Agora que o MongoDB está instalado e em execução, abra uma porta no firewall do Windows para que você possa se conectar remotamente ao MongoDB. Para criar uma nova regra de entrada para permitir a porta TCP 27017, abra um prompt administrativo do PowerShell e insira o seguinte comando:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Você também pode criar a regra usando a ferramenta de gerenciamento gráfico do **Firewall do Windows com segurança avançada** . Crie uma nova regra de entrada para permitir a porta TCP 27017.

Se necessário, crie uma regra de grupo de segurança de rede para permitir o acesso ao MongoDB de fora da sub-rede da rede virtual do Azure existente. Você pode criar as regras do grupo de segurança de rede usando o [portal do Azure](nsg-quickstart-portal.md) ou [Azure PowerShell](nsg-quickstart-powershell.md). Assim como as regras de firewall do Windows, permita a porta TCP 27017 para a interface de rede virtual da sua VM do MongoDB.

> [!NOTE]
> A porta TCP 27017 é a porta padrão usada pelo MongoDB. Você pode alterar essa porta usando o parâmetro `--port` ao iniciar `mongod.exe` manualmente ou de um serviço. Se você alterar a porta, certifique-se de atualizar as regras do firewall do Windows e do grupo de segurança de rede nas etapas anteriores.


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você aprendeu a instalar e configurar o MongoDB em sua VM do Windows. Agora você pode acessar o MongoDB em sua VM do Windows, seguindo os tópicos avançados na [documentação do MongoDB](https://docs.mongodb.com/manual/).

