---
title: Instale o MongoDB num VM do Windows em Azure
description: Saiba como instalar o MongoDB num Azure VM que executa o Windows Server 2012 R2 criado com o modelo de implementação do Gestor de Recursos.
documentationcenter: ''
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: d02fa8fa23b587db06f3d2d1e08f0a8565471123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510424"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instale e configuure o MongoDB num Windows VM em Azure
[MongoDB](https://www.mongodb.org) é uma base de dados noSQL de alta origem. Este artigo guia-o através da instalação e configuração do MongoDB numa máquina virtual (VM) do Windows Server 2016 em Azure. Também pode [instalar o MongoDB num Linux VM em Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de instalar e configurar o MongoDB, é necessário criar um VM e, idealmente, adicionar-lhe um disco de dados. Consulte os seguintes artigos para criar um VM e adicione um disco de dados:

* Criar um VM do Servidor do Windows utilizando [o portal Azure](quick-create-portal.md) ou [Azure PowerShell](quick-create-powershell.md).
* Anexar um disco de dados a um VM do Servidor do Windows utilizando [o portal Azure](attach-managed-disk-portal.md) ou [Azure PowerShell](attach-disk-ps.md).

Para começar a instalar e configurar o MongoDB, [inicie sessão no seu Windows Server VM](connect-logon.md) utilizando o Remote Desktop.

## <a name="install-mongodb"></a>Instalar o MongoDB
> [!IMPORTANT]
> As funcionalidades de segurança mongoDB, tais como autenticação e ligação de endereços IP, não são ativadas por padrão. As funcionalidades de segurança devem ser ativadas antes de implantar o MongoDB num ambiente de produção. Para mais informações, consulte [a MongoDB Security and Authentication](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Depois de ter ligado ao seu VM utilizando o Remote Desktop, abra o Internet Explorer a partir da barra de tarefas.
2. **Selecione Utilize as definições recomendadas de segurança, privacidade e compatibilidade** quando o Internet Explorer abrir pela primeira vez e clique **em OK**.
3. A configuração de segurança melhorada do Internet Explorer é ativada por padrão. Adicione o website do MongoDB à lista de sites permitidos:

   * Selecione o ícone **Ferramentas** no canto superior direito.
   * Nas **Opções de Internet,** selecione o separador **Segurança** e, em seguida, selecione o ícone **'Sites Fidedignos'.**
   * Clique no botão **'Sites'.** Adicione *https:// \* .mongodb.com* à lista de sites fidedignos e, em seguida, feche a caixa de diálogo.

     ![Configurar definições de segurança do Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Navegue pela página [MongoDB - Downloads](https://www.mongodb.com/downloads) https://www.mongodb.com/downloads) (.
5. Se necessário, selecione a edição **do Servidor Comunitário** e, em seguida, selecione a versão estável mais recente para Windows Server*2008 R2 64-bit e mais tarde*. Para descarregar o instalador, clique em **DOWNLOAD (msi)**.

    ![Baixar o instalador mongoDB](./media/install-mongodb/download-mongodb.png)

    Executar o instalador depois de o download estar completo.
6. Leia e aceite o contrato de licença. Quando lhe for solicitado, selecione **instalar-se completamente.**
7. Se desejar, pode optar por instalar também a Bússola, uma interface gráfica para o MongoDB.
8. No ecrã final, clique **em Instalar**.

## <a name="configure-the-vm-and-mongodb"></a>Configure o VM e o MongoDB
1. As variáveis de trajetória não são atualizadas pelo instalador MongoDB. Sem a localização mongoDB `bin` na variável do seu percurso, precisa de especificar o caminho completo cada vez que utilizar um MongoDB executável. Para adicionar a localização à variável do seu percurso:

   * Clique com o botão direito no menu **Iniciar** e selecione **Sistema**.
   * Clique **nas definições avançadas do sistema**e, em seguida, clique em **Variáveis ambiente**.
   * Nas **variáveis do Sistema**, selecione **Caminho**e, em seguida, clique em **Editar**.

     ![Configure variáveis PATH](./media/install-mongodb/configure-path-variables.png)

     Adicione o caminho à sua pasta MongoDB. `bin` O MongoDB é normalmente instalado em *C:\Program Files\MongoDB*. Verifique o caminho de instalação no seu VM. O exemplo a seguir adiciona o local de instalação padrão do MongoDB à `PATH` variável:

     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```

     > [!NOTE]
     > Certifique-se de adicionar o ponto de referência principal `;` para indicar que está a adicionar uma localização à sua `PATH` variável.

2. Crie dados mongoDB e faça login nos seus discos de dados. A partir do menu **Iniciar,** selecione **Comando Rápido**. Os seguintes exemplos criam os diretórios na unidade F:

    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Inicie uma instância MongoDB com o seguinte comando, ajustando o caminho para os seus dados e registar diretórios em conformidade:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```

    Pode levar vários minutos para o MongoDB alocar os ficheiros do diário e começar a ouvir as ligações. Todas as mensagens de registo são direcionadas para o ficheiro *F:\MongoLogs\mongolog.log* à medida que o `mongod.exe` servidor começa e atribui ficheiros de diário.

   > [!NOTE]
   > O pedido de comando mantém-se focado nesta tarefa enquanto a sua instância MongoDB está em execução. Deixe a janela de aviso de comando aberta para continuar a funcionar o MongoDB. Ou, instalar o MongoDB como serviço, conforme detalhado no passo seguinte.

4. Para uma experiência MongoDB mais robusta, instale o `mongod.exe` como um serviço. Criar um serviço significa que não precisa deixar um pedido de comando a funcionar cada vez que pretender utilizar o MongoDB. Crie o serviço da seguinte forma, ajustando o caminho para os seus dados e registar os diretórios em conformidade:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```

    O comando anterior cria um serviço chamado MongoDB, com uma descrição de "Mongo DB". São também especificados os seguintes parâmetros:

   * A `--dbpath` opção especifica a localização do diretório de dados.
   * A `--logpath` opção deve ser utilizada para especificar um ficheiro de registo, porque o serviço de funcionamento não tem uma janela de comando para visualizar a saída.
   * A `--logappend` opção especifica que um reinício do serviço faz com que a saída se apromia ao ficheiro de registo existente.

   Para iniciar o serviço MongoDB, executar o seguinte comando:

    ```
    net start MongoDB
    ```

    Para obter mais informações sobre a criação do serviço MongoDB, consulte [configurar um Serviço windows para MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Teste a instância mongoDB
Com o MongoDB a funcionar como uma única instância ou instalado como um serviço, pode agora começar a criar e a utilizar as suas bases de dados. Para iniciar a concha administrativa mongoDB, abra outra janela de pedido de comando a partir do menu **Iniciar** e insira o seguinte comando:

```
mongo
```

Pode listar as bases de dados com o `db` comando. Insira alguns dados da seguinte forma:

```
db.foo.insert( { a : 1 } )
```

Procurar dados da seguinte forma:

```
db.foo.find()
```

O resultado é semelhante ao seguinte exemplo:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Saia da consola da `mongo` seguinte forma:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configure as regras do Firewall e do Grupo de Segurança da Rede
Agora que o MongoDB está instalado e em execução, abra uma porta no Windows Firewall para que possa ligar-se remotamente ao MongoDB. Para criar uma nova regra de entrada para permitir a porta TCP 27017, abra um pedido administrativo powerShell e insira o seguinte comando:

```powershell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Também pode criar a regra utilizando a ferramenta de gestão gráfica de Segurança Avançada do **Windows Firewall.** Crie uma nova regra de entrada para permitir a porta TCP 27017.

Se necessário, crie uma regra do Grupo de Segurança de Rede para permitir o acesso ao MongoDB a partir do exterior da sub-rede virtual Azure existente. Pode criar as regras do Grupo de Segurança da Rede utilizando o [portal Azure](nsg-quickstart-portal.md) ou [o Azure PowerShell](nsg-quickstart-powershell.md). Tal como as regras do Windows Firewall, permita a porta TCP 27017 para a interface de rede virtual do seu VM MongoDB.

> [!NOTE]
> A porta TCP 27017 é a porta padrão utilizada pela MongoDB. Pode alterar esta porta utilizando o `--port` parâmetro quando iniciar `mongod.exe` manualmente ou a partir de um serviço. Se alterar a porta, certifique-se de atualizar as regras do Windows Firewall e do Grupo de Segurança da Rede nas etapas anteriores.


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a instalar e configurar o MongoDB no seu Windows VM. Pode agora aceder ao MongoDB no seu Windows VM, seguindo os tópicos avançados na documentação do [MongoDB](https://docs.mongodb.com/manual/).

