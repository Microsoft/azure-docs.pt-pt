---
title: Guia de Introdução do Node.js
description: Saiba como criar uma aplicação Web Node.js simples e implementá-la num serviço em nuvem do Azure.
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 50951a87-fed4-48e0-bcfa-453b9e50452e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: 270f400f78b971e022a59e5e9a04a76dfa929941
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761168"
---
# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>Compilar e implementar uma aplicação Node.js num Serviço em Nuvem do Azure

Este tutorial mostra como criar uma aplicação Node.js simples para execução num Serviço em Nuvem do Azure. Os Cloud Services são os blocos modulares de aplicações em nuvem dimensionáveis no Azure. Permitem a separação, bem como a gestão e ampliação independentes de componentes front-end e back-end da aplicação.  Os Cloud Services fornecem uma máquina virtual dedicada robusta para alojar cada função de forma fiável.

Para obter mais informações sobre os Cloud Services e a sua comparação com Virtual Machines e Web Sites do Azure, consulte [Comparação de Web Sites, Cloud Services e Virtual Machines do Azure].

> [!TIP]
> Pretende compilar um site simples? Se o seu cenário envolver apenas um front-end de site simples, considere a [utilização de uma aplicação Web simples]. Pode facilmente atualizar para um Serviço em Nuvem à medida que a aplicação Web aumenta e os seus requisitos se alteram.

Este tutorial descreve como compilar uma aplicação Web simples alojada numa função da Web. Deverá utilizar o emulador de computação para testar a aplicação localmente e, em seguida, implementá-la utilizando as ferramentas da linha de comandos do PowerShell.

A aplicação é uma aplicação “olá, mundo” simples:

![Um browser a apresentar a página Web “Olá, Mundo”][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>Pré-requisitos
> [!NOTE]
> Este tutorial utiliza o Azure PowerShell, que requer o Windows.

* Instale e configure o [Azure PowerShell].
* Transfira e instale o [Azure SDK para .NET 2.7]. Na configuração da instalação, selecione:
  * MicrosoftAzureAuthoringTools
  * MicrosoftAzureComputeEmulator

## <a name="create-an-azure-cloud-service-project"></a>Criar um projeto do Serviço em Nuvem do Azure
Execute as seguintes tarefas para criar um novo projeto do Serviço em Nuvem do Azure, juntamente com andaime Node.js básico:

1. Execute o **Windows PowerShell** como Administrador; no **Menu Iniciar** ou **Ecrã Inicial**, procure **Windows PowerShell**.
2. [Ligue o PowerShell] à sua subscrição.
3. Introduza o seguinte cmdlet do PowerShell para criar o projeto:

        New-AzureServiceProject helloworld

    ![The result of the New-AzureService helloworld command][The result of the New-AzureService helloworld command]

    O cmdlet **New-AzureServiceProject** gera uma estrutura básica para publicar uma aplicação Node.js num Serviço em Nuvem. Contém os ficheiros de configuração necessários para publicação no Azure. O cmdlet também altera o diretório de trabalho para o diretório do serviço.

    O cmdlet cria os seguintes ficheiros:

   * **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** and **ServiceDefinition.csdef**: Ficheiros de específicos do Azure necessários para a publicação da sua aplicação. Para obter mais informações, consulte [Descrição Geral da Criação de um Serviço Alojado do Azure].
   * **deploymentSettings.json**: Armazena as definições locais que são utilizadas pelos cmdlets de implementação do Azure PowerShell.
4. Introduza o comando seguinte para adicionar uma nova função da Web:

       Add-AzureNodeWebRole

   ![The output of the Add-AzureNodeWebRole command][The output of the Add-AzureNodeWebRole command]

   O cmdlet **Add-AzureNodeWebRole** cria uma aplicação Node.js básica. Também modifica os ficheiros **.csfg** e **.csdef** para adicionar entradas de configuração para a nova função.

   > [!NOTE]
   > Se não especificar um nome de função, será utilizado um nome predefinido. Pode fornecer um nome como o primeiro parâmetro do cmdlet: `Add-AzureNodeWebRole MyRole`

A aplicação Node.js é definida no ficheiro **server.js**, localizado no diretório da função da Web (**WebRole1**, por predefinição). Apresentamos o código a seguir:

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

Este código é, essencialmente, igual ao exemplo de “Olá, Mundo” no site [nodejs.org], exceto que utiliza o número da porta atribuído pelo ambiente de nuvem.

## <a name="deploy-the-application-to-azure"></a>Implementar a aplicação no Azure

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Pode [ativar os benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="download-the-azure-publishing-settings"></a>Transferir as definições de publicação do Azure
Para implementar a aplicação no Azure, primeiro tem de transferir as definições de publicação para a sua subscrição do Azure.

1. Execute o seguinte cmdlet do Azure PowerShell:

       Get-AzurePublishSettingsFile

   Este procedimento permitirá utilizar o browser para navegar para a página de transferência de definições de publicação. Poderá ser-lhe pedido para iniciar sessão com uma Conta Microsoft. Se assim for, utilize a conta associada à subscrição do Azure.

   Guarde o perfil transferido numa localização do ficheiro facilmente acessível.
2. Execute o seguinte cmdlet para importar o perfil de publicação transferido:

       Import-AzurePublishSettingsFile [path to file]

    > [!NOTE]
    > Depois de importar as definições de publicação, considere eliminar o ficheiro .publishSettings transferido, porque contém informações que podem permitir a alguém aceder à sua conta.

### <a name="publish-the-application"></a>Publicar a aplicação
Para publicar, execute os seguintes comandos:

      $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

* **-ServiceName**: especifica o nome da implementação. Tem de ser um nome exclusivo; caso contrário, o processo de publicação falha. O comando **Get-Data** adiciona uma cadeia de data/hora que deve tornar o nome exclusivo.
* **-Location**: especifica o datacenter onde a aplicação será alojada. Para ver uma lista de datacenters disponíveis, utilize o cmdlet **Get-AzureLocation**.
* **-Launch**: abre uma janela do browser e navega para o serviço alojado após a conclusão da implementação.

Se publicação for bem sucedida, verá uma resposta semelhante ao seguinte:

![The output of the Publish-AzureService command][The output of the Publish-AzureService command]

> [!NOTE]
> Pode demorar alguns minutos para que a aplicação seja implementada e fique disponível quando publicada pela primeira vez.

Depois de concluída a implementação, a janela do browser será apresentada e navegará para o serviço em nuvem.

![A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

A aplicação está agora em execução no Azure.

O cmdlet **Publish-AzureServiceProject** efetua os seguintes passos:

1. Cria um pacote para implementação. O pacote contém todos os ficheiros da sua pasta da aplicação.
2. Cria uma nova **Conta do Storage**, se ainda não existir. A conta do Storage do Azure é utilizada para armazenar o pacote de aplicação durante a implementação. Pode eliminar a conta do Storage em segurança após a implementação ser efetuada.
3. Cria um novo **serviço em nuvem**, se ainda não existir um. Um **serviço em nuvem** é o contentor no qual a aplicação será alojada quando for implementada no Azure. Para obter mais informações, consulte [Descrição Geral da Criação de um Serviço Alojado do Azure].
4. Publica o pacote de implementação no Azure.

## <a name="stopping-and-deleting-your-application"></a>Parar e eliminar a aplicação
Depois de implementar a aplicação, poderá pretender desativá-la, para evitar custos adicionais. O Azure cobra as instâncias de função da Web por hora de tempo do servidor consumido. O tempo do servidor é consumido logo que a aplicação é implementada, mesmo que as instâncias não estejam em execução e estejam paradas.

1. Na janela do Windows PowerShell, pare a implementação do serviço criada na secção anterior com o seguinte cmdlet:

       Stop-AzureService

   A paragem do serviço pode demorar vários minutos. Quando o serviço estiver parado, receberá uma mensagem a indicar que foi parado.

   ![The status of the Stop-AzureService command][The status of the Stop-AzureService command]
2. Para eliminar o serviço, chame o cmdlet seguinte:

       Remove-AzureService

   Quando lhe for pedido, introduza **S** para eliminar o serviço.

   A eliminação do serviço pode demorar vários minutos. Quando o serviço for eliminado, receberá uma mensagem a indicar que o serviço foi eliminado.

   ![The status of the Remove-AzureService command][The status of the Remove-AzureService command]

   > [!NOTE]
   > A eliminação do serviço não elimina a conta do Storage que foi criada quando o serviço foi inicialmente publicado. por isso, continuará a ser-lhe cobrado o armazenamento utilizado. Se mais nada estiver a utilizar o armazenamento, poderá querer eliminá-lo.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte o [Centro de Programadores do Node.js].

<!-- URL List -->

[Comparação de Web Sites, Cloud Services e Virtual Machines do Azure]: ../app-service/overview-compare.md
[utilização de uma aplicação Web simples]: ../app-service/app-service-web-get-started-nodejs.md
[Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Azure SDK para .NET 2.7]: https://www.microsoft.com/en-us/download/details.aspx?id=48178
[Ligue o PowerShell]: /powershell/azureps-cmdlets-docs
[nodejs.org]: https://nodejs.org/
[Descrição Geral da Criação de um Serviço Alojado do Azure]: https://azure.microsoft.com/documentation/services/cloud-services/
[Centro de Programadores do Node.js]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
