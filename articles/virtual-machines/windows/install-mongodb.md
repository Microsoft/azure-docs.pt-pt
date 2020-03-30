---
title: Instale o MongoDB num Windows VM em Azure
description: Saiba como instalar o MongoDB num Azure VM que executa o Windows Server 2012 R2 criado com o modelo de implementação do Gestor de Recursos.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038540"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instale e configure MongoDB num VM do Windows em Azure
[MongoDB](https://www.mongodb.org) é uma popular base de dados noSQL de alto desempenho. Este artigo guia-o através da instalação e configuração do MongoDB numa máquina virtual do Windows Server 2016 (VM) em Azure. Também pode [instalar o MongoDB num Linux VM em Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de instalar e configurar o MongoDB, precisa de criar um VM e, idealmente, adicionar-lhe um disco de dados. Consulte os seguintes artigos para criar um VM e adicione um disco de dados:

* Crie um VM do Servidor Windows utilizando [o portal Azure](quick-create-portal.md) ou [o Azure PowerShell](quick-create-powershell.md).
* Fixe um disco de dados a um VM do Servidor windows utilizando [o portal Azure](attach-managed-disk-portal.md) ou [o Azure PowerShell](attach-disk-ps.md).

Para começar a instalar e configurar o MongoDB, [inicie sessão no VM](connect-logon.md) do Windows Server utilizando o Remote Desktop.

## <a name="install-mongodb"></a>Instalar o MongoDB
> [!IMPORTANT]
> As funcionalidades de segurança do MongoDB, tais como a autenticação e a ligação de endereços IP, não são ativadas por predefinição. As funcionalidades de segurança devem ser ativadas antes de implantar o MongoDB num ambiente de produção. Para mais informações, consulte [A Segurança e Autenticação mongoDB](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Depois de ter ligado ao seu VM utilizando o Remote Desktop, abra o Internet Explorer a partir da barra de tarefas.
2. Selecione Utilize as **definições recomendadas** de segurança, privacidade e compatibilidade quando o Internet Explorer abrir pela primeira vez e clique em **OK**.
3. A configuração de segurança melhorada do Internet Explorer é ativada por padrão. Adicione o website da MongoDB à lista de sites permitidos:
   
   * Selecione o ícone **Ferramentas** no canto superior direito.
   * Nas Opções de **Internet,** selecione o separador **Segurança** e, em seguida, selecione o ícone **'Sites Fidedignos'.**
   * Clique no botão **Sites.** Adicione *\*https:// .mongodb.com* à lista de sites fidedignos e, em seguida, feche a caixa de diálogo.
     
     ![Configure as definições de segurança do Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Navegue na página [MongoDB - Downloads](https://www.mongodb.com/downloads) (https://www.mongodb.com/downloads).
5. Se necessário, selecione a edição **do Servidor Comunitário** e, em seguida, selecione o mais recente lançamento estável para windows Server*2008 R2 64-bit e mais tarde*. Para descarregar o instalador, clique em **DOWNLOAD (msi)**.
   
    ![Descarregue o instalador MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Executar o instalador depois de o download estar completo.
6. Leia e aceite o contrato de licença. Quando for solicitado, selecione **Complete** instalar.
7. Se desejar, pode optar por instalar também a Bússola, uma interface gráfica para o MongoDB.
8. No ecrã final, clique em **Instalar**.

## <a name="configure-the-vm-and-mongodb"></a>Configure o VM e o MongoDB
1. As variáveis de caminho não são atualizadas pelo instalador MongoDB. Sem a localização `bin` MongoDB na variável do seu percurso, precisa especificar o caminho completo cada vez que utilizar um MongoDB executável. Para adicionar a localização à variável do seu caminho:
   
   * Clique no menu **Iniciar** e selecione **Sistema**.
   * Clique em **definições avançadas do sistema**, e, em seguida, clique em **Variáveis de Ambiente**.
   * Sob **as variáveis do Sistema,** selecione **Caminho,** e, em seguida, clique em **Editar**.
     
     ![Configurar variáveis PATH](./media/install-mongodb/configure-path-variables.png)
     
     Adicione o caminho para a `bin` sua pasta MongoDB. O MongoDB é normalmente instalado em *C:\Program Files\MongoDB*. Verifique o percurso de instalação no seu VM. O exemplo seguinte adiciona a localização de `PATH` instalação padrão do MongoDB à variável:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Certifique-se de adicionar o`;`ponto e vírgula principal `PATH` para indicar que está a adicionar uma localização à sua variável.

2. Crie dados mongoDB e diretórios de registo no seu disco de dados. A partir do menu **Iniciar,** selecione **Comando Solicitação**. Os seguintes exemplos criam os diretórios na unidade F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Inicie uma instância MongoDB com o seguinte comando, ajustando o caminho para os seus dados e diretórios de registo em conformidade:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Pode levar vários minutos para a MongoDB alocar os ficheiros do diário e começar a ouvir ligações. Todas as mensagens de registo são direcionadas para o ficheiro *F:\MongoLogs\mongolog.log* à medida `mongod.exe` que o servidor começa e atribui ficheiros de diário.
   
   > [!NOTE]
   > O pedido de comando mantém-se focado nesta tarefa enquanto a sua instância MongoDB está em execução. Deixe a janela de solicitação de comando aberta para continuar a executar MongoDB. Ou, instale o MongoDB como serviço, conforme detalhado no próximo passo.

4. Para uma experiência MongoDB mais `mongod.exe` robusta, instale o como um serviço. Criar um serviço significa que não precisa de deixar um pedido de comando a funcionar sempre que quiser utilizar o MongoDB. Crie o serviço da seguinte forma, ajustando o caminho para os seus dados e diretórios de registo em conformidade:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    O comando anterior cria um serviço chamado MongoDB, com uma descrição de "Mongo DB". São igualmente especificados os seguintes parâmetros:
   
   * A `--dbpath` opção especifica a localização do diretório de dados.
   * A `--logpath` opção deve ser utilizada para especificar um ficheiro de registo, uma vez que o serviço de funcionamento não tem uma janela de comando para visualizar a saída.
   * A `--logappend` opção especifica que um reinício do serviço faz com que a saída adere ao ficheiro de registo existente.
   
   Para iniciar o serviço MongoDB, executar o seguinte comando:
   
    ```
    net start MongoDB
    ```
   
    Para obter mais informações sobre a criação do serviço MongoDB, consulte [Configure um Serviço Windows para MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Teste a instância mongoDB
Com o MongoDB a funcionar como uma única instância ou instalado como um serviço, pode agora começar a criar e utilizar as suas bases de dados. Para iniciar a concha administrativa MongoDB, abra outra janela de comando do menu **Iniciar** e introduza o seguinte comando:

```
mongo  
```

Pode listar as bases `db` de dados com o comando. Insira alguns dados da seguinte forma:

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

Saia `mongo` da consola da seguinte forma:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configure as regras do grupo de segurança de firewall e rede
Agora que o MongoDB está instalado e em execução, abra uma porta no Windows Firewall para que possa ligar-se remotamente ao MongoDB. Para criar uma nova regra de entrada para permitir a porta TCP 27017, abra um pedido administrativo powerShell e entre no seguinte comando:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Também pode criar a regra utilizando o Firewall do Windows com a ferramenta de gestão gráfica **Advanced Security.** Crie uma nova regra de entrada para permitir a porta TCP 27017.

Se necessário, crie uma regra do Grupo de Segurança da Rede que permita o acesso ao MongoDB a partir de fora da subnet de rede virtual Azure existente. Pode criar as regras do Grupo de Segurança da Rede utilizando o [portal Azure](nsg-quickstart-portal.md) ou [o Azure PowerShell](nsg-quickstart-powershell.md). Tal como acontece com as regras do Windows Firewall, permita a porta TCP 27017 para a interface de rede virtual do seu VM MongoDB.

> [!NOTE]
> A porta TCP 27017 é a porta padrão utilizada pela MongoDB. Pode alterar esta porta `--port` utilizando o parâmetro `mongod.exe` quando começar manualmente ou a partir de um serviço. Se alterar a porta, certifique-se de atualizar as regras do Windows Firewall e Network Security Group nas etapas anteriores.


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a instalar e configurar o MongoDB no seu Windows VM. Pode agora aceder ao MongoDB no seu Windows VM, seguindo os tópicos avançados na [documentação do MongoDB.](https://docs.mongodb.com/manual/)

