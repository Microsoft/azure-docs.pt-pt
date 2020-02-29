---
title: Tecnologias de implantação em Funções Azure
description: Aprenda as diferentes formas de implementar código para funções Azure.
author: georgewallace
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gwallace
ms.openlocfilehash: 43352117d149abbe41ba7bf49a1ffb68e46d2707
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190758"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologias de implantação em Funções Azure

Pode utilizar algumas tecnologias diferentes para implementar o seu código de projeto Funções Azure para o Azure. Este artigo fornece uma lista exaustiva dessas tecnologias, descreve quais as tecnologias disponíveis para as quais os sabores das Funções, explica o que acontece quando se utiliza cada método, e fornece recomendações para o melhor método de utilização em vários cenários . As várias ferramentas que suportam a implantação para as Funções Azure estão sintonizadas com a tecnologia certa com base no seu contexto. Em geral, a implantação de zip é a tecnologia de implantação recomendada para funções Azure.

## <a name="deployment-technology-availability"></a>Disponibilidade de tecnologia de implantação

A Azure Functions suporta o desenvolvimento local transversal e hospeda-se no Windows e Linux. Atualmente, estão disponíveis três planos de hospedagem:

+ [Consumo](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dedicado (Serviço de Aplicações)](functions-scale.md#app-service-plan)

Cada plano tem comportamentos diferentes. Nem todas as tecnologias de implementação estão disponíveis para cada sabor das Funções Azure. O gráfico seguinte mostra quais as tecnologias de implantação que são suportadas para cada combinação do sistema operativo e do plano de hospedagem:

| Tecnologia de implantação | Consumo de janelas | Windows Premium | Windows Dedicado  | Consumo de Linux | Prémio Linux | Linux Dedicado |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| URL do pacote externo<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip implantação |✔|✔|✔|✔|✔|✔|
| Recipiente de estivador | | | | |✔|✔|
| Implantação web |✔|✔|✔| | | |
| Controlo de código fonte |✔|✔|✔| |✔|✔|
| Git<sup>1</sup> local |✔|✔|✔| |✔|✔|
| Sincronização de nuvens<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Edição do portal |✔|✔|✔| |<sup>✔ 2</sup>|<sup>✔ 2</sup>|

<sup>1</sup> Tecnologia de implantação que requer [sincronização manual do gatilho](#trigger-syncing).  
<sup>2</sup> A edição do portal está ativada apenas para os gatilhos HTTP e Temporizador para Funções no Linux utilizando planos Premium e Dedicados.

## <a name="key-concepts"></a>Conceitos-chave

Alguns conceitos-chave são fundamentais para entender como as implementações funcionam em Funções Azure.

### <a name="trigger-syncing"></a>Sincronização do gatilho

Quando alterar qualquer um dos seus gatilhos, a infraestrutura functiondeve estar ciente das alterações. A sincronização acontece automaticamente para muitas tecnologias de implementação. No entanto, em alguns casos, deve sincronizar manualmente os seus gatilhos. Quando implementar as suas atualizações fazendo referência a um URL de pacote externo, Git local, sincronização em nuvem ou FTP, deve sincronizar manualmente os seus gatilhos. Pode sincronizar os gatilhos de uma de três maneiras:

* Reinicie a sua aplicação de funções no portal Azure
* Envie um pedido HTTP POST para `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` utilizando a [chave principal](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Envie um pedido HTTP POST para `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Substitua os espaços reservados pelo seu ID de subscrição, nome do grupo de recursos e o nome da sua aplicação de função.

### <a name="remote-build"></a>Construção remota

As Funções Azure podem executar automaticamente as construções no código que recebe após as implementações em zip. Estas construções comportam-se de forma ligeiramente diferente, dependendo se a sua aplicação está a funcionar no Windows ou no Linux. As construções remotas não são realizadas quando uma aplicação foi previamente definida para ser executada no modo [Run From Package.](run-functions-from-deployment-package.md) Para aprender a usar a construção remota, navegue para [o zip deploy](#zip-deploy).

> [!NOTE]
> Se está a ter problemas com a construção remota, pode ser porque a sua aplicação foi criada antes de a funcionalidade ter sido disponibilizada (1 de agosto de 2019). Tente criar uma nova aplicação de funções ou executando `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` para atualizar a sua aplicação de funções. Este comando pode levar duas tentativas para ter sucesso.

#### <a name="remote-build-on-windows"></a>Construção remota no Windows

Todas as aplicações de funções que funcionam no Windows têm uma pequena aplicação de gestão, o site SCM (ou [Kudu).](https://github.com/projectkudu/kudu) Este site lida com grande parte da implantação e constrói lógica para funções Azure.

Quando uma aplicação é implementada para windows, comandosC#específicos do idioma, como `dotnet restore` ( ) ou `npm install` (JavaScript) são executados.

#### <a name="remote-build-on-linux"></a>Construção remota em Linux

Para permitir a construção remota do Linux, devem ser definidas as [seguintes definições de aplicação:](functions-how-to-use-azure-function-app-settings.md#settings)

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Por predefinição, tanto as [funções azure como](functions-run-local.md) a extensão de [funções Azure para código](functions-create-first-function-vs-code.md#publish-the-project-to-azure) de estúdio visual executam construções remotas ao ser implantados para o Linux. Por isso, ambas as ferramentas criam automaticamente estas definições para si no Azure. 

Quando as aplicações são construídas remotamente no Linux, [funcionam a partir do pacote de implementação](run-functions-from-deployment-package.md). 

##### <a name="consumption-plan"></a>Plano de consumo

As aplicações de função Linux que funcionam no plano de consumo não têm um site SCM/Kudu, o que limita as opções de implementação. No entanto, as aplicações de função no Linux em execução no plano de consumo suportam construções remotas.

##### <a name="dedicated-and-premium-plans"></a>Planos dedicados e Premium

As aplicações de função que funcionam no Linux no [plano Dedicado (App Service)](functions-scale.md#app-service-plan) e o [plano Premium](functions-scale.md#premium-plan) também têm um site sCM/Kudu limitado.

## <a name="deployment-technology-details"></a>Detalhes da tecnologia de implantação

Os seguintes métodos de implantação estão disponíveis nas Funções Azure.

### <a name="external-package-url"></a>URL de pacote externo

Pode utilizar um URL de pacote externo para fazer referência a um ficheiro de pacote remoto (.zip) que contenha a sua aplicação de função. O ficheiro é descarregado a partir do URL fornecido, e a aplicação é executada no modo [Run From Package.](run-functions-from-deployment-package.md)

>__Como usá-lo:__ Adicione `WEBSITE_RUN_FROM_PACKAGE` às definições de aplicação. O valor desta definição deve ser um URL (a localização do ficheiro de pacote específico que pretende executar). Pode adicionar configurações [no portal](functions-how-to-use-azure-function-app-settings.md#settings) ou [utilizando o Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Se utilizar o armazenamento Azure Blob, utilize um recipiente privado com uma assinatura de [acesso partilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para dar acesso às Funções à embalagem. Sempre que a aplicação recomeça, ele vai buscar uma cópia do conteúdo. A sua referência deve ser válida para o tempo de vida da aplicação.

>__Quando usá-lo:__ O URL do pacote externo é o único método de implementação suportado para funções Azure em execução no Linux no plano de consumo, se o utilizador não quiser que ocorra uma [construção remota.](#remote-build) Ao atualizar o ficheiro do pacote que uma aplicação de função refere, tem de [sincronizar manualmente os gatilhos](#trigger-syncing) para dizer ao Azure que a sua aplicação mudou.

### <a name="zip-deploy"></a>Zip implantação

Utilize o zip deploy para empurrar um ficheiro .zip que contenha a sua aplicação de função para o Azure. Opcionalmente, pode configurar a sua aplicação para começar a funcionar a [partir do pacote](run-functions-from-deployment-package.md), ou especificar que ocorre uma [construção remota.](#remote-build)

>__Como usá-lo:__ Implemente utilizando a sua ferramenta cliente favorita: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), Visual [Studio,](functions-develop-vs.md#publish-to-azure)ou da linha de comando utilizando as [Ferramentas Core funções Azure](functions-run-local.md#project-file-deployment). Por predefinição, estas ferramentas utilizam a implantação de zíper e [funcionam a partir da embalagem](run-functions-from-deployment-package.md). As Ferramentas Core e a extensão do Código do Estúdio Visual permitem a [construção remota](#remote-build) ao ser implantado para o Linux. Para implementar manualmente um ficheiro .zip na sua aplicação de funções, siga as instruções de [implantação a partir de um ficheiro .zip ou URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>Quando implementar utilizando a implementação de zip, pode definir a sua aplicação para executar a [partir do pacote](run-functions-from-deployment-package.md). Para correr a partir do pacote, defina o valor de definição de aplicação `WEBSITE_RUN_FROM_PACKAGE` para `1`. Recomendamos a implantação de zip. Produz tempos de carregamento mais rápidos para as suas aplicações, e é o padrão para vs Code, Visual Studio e o Azure CLI. 

>__Quando usá-lo:__ A implantação de zip é a tecnologia de implementação recomendada para funções Azure.

### <a name="docker-container"></a>Recipiente de estivador

Pode implantar uma imagem de recipiente Linux que contenha a sua aplicação de função.

>__Como usá-lo:__ Crie uma aplicação de função Linux no plano Premium ou Dedicado e especifique a imagem do recipiente a partir. Pode fazê-lo de duas formas:
>
>* Crie uma aplicação de função Linux num plano de Serviço de Aplicações Azure no portal Azure. Para **publicar**, selecione **Docker Image**e, em seguida, configure o recipiente. Insira o local onde a imagem está hospedada.
>* Crie uma aplicação de função Linux num plano de Serviço de Aplicações utilizando o Azure CLI. Para saber como, consulte [Criar uma função no Linux utilizando uma imagem personalizada](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function).
>
>Para implantar numa aplicação existente utilizando um recipiente personalizado, em [Ferramentas Core funções Azure,](functions-run-local.md)utilize o comando [`func deploy`.](functions-run-local.md#publish)

>__Quando usá-lo:__ Utilize a opção de contentor Docker quando necessitar de mais controlo sobre o ambiente Linux onde funciona a sua aplicação de funções. Este mecanismo de implantação está disponível apenas para funções em funcionamento no Linux.

### <a name="web-deploy-msdeploy"></a>Implantação web (MSDeploy)

Web Implemente pacotes e implementa as suas aplicações do Windows em qualquer servidor IIS, incluindo as suas aplicações de funções que funcionam no Windows em Azure.

>__Como usá-lo:__ Utilize [ferramentas de Estúdio Visual para funções Azure](functions-create-your-first-function-visual-studio.md). Limpe a caixa de verificação do ficheiro de **pacote (recomendado).**
>
>Também pode baixar o [Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) e `MSDeploy.exe` ligar diretamente.

>__Quando usá-lo:__ A Web Deploy é suportada e não tem problemas, mas o mecanismo preferido é a implementação do [zip com o Pacote Run From ativado](#zip-deploy). Para saber mais, consulte o guia de desenvolvimento do [Estúdio Visual.](functions-develop-vs.md#publish-to-azure)

### <a name="source-control"></a>Controlo de código fonte

Utilize o controlo de fonte para ligar a sua aplicação de função a um repositório Git. Uma atualização do código nesse repositório desencadeia a implantação. Para mais informações, consulte o [Kudu Wiki.](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)

>__Como usá-lo:__ Utilize o Centro de Implantação na área de Funções do portal para configurar a publicação a partir do controlo de origem. Para mais informações, consulte a [implantação contínua para funções Azure](functions-continuous-deployment.md).

>__Quando usá-lo:__ Usar o controlo de fontes é a melhor prática para as equipas que colaboram nas suas aplicações de função. O controlo de fontes é uma boa opção de implantação que permite gasodutos de implantação mais sofisticados.

### <a name="local-git"></a>Git Local

Pode utilizar git local para empurrar o código da sua máquina local para funções azure utilizando Git.

>__Como usá-lo:__ Siga as instruções na [implantação local git para o Serviço de Aplicações Azure](../app-service/deploy-local-git.md).

>__Quando usá-lo:__ Em geral, recomendamos que utilize um método de implantação diferente. Quando publicar a partir de Git local, deve [sincronizar manualmente](#trigger-syncing)os gatilhos .

### <a name="cloud-sync"></a>Sincronização de nuvens

Utilize sincronização de nuvem para sincronizar o seu conteúdo desde as funções Dropbox e OneDrive para Azure.

>__Como usá-lo:__ Siga as instruções no [conteúdo sync a partir de uma pasta de nuvem](../app-service/deploy-content-sync.md).

>__Quando usá-lo:__ Em geral, recomendamos outros métodos de implantação. Quando publicar utilizando sincronização em nuvem, deve [sincronizar manualmente](#trigger-syncing)os gatilhos .

### <a name="ftp"></a>FTP

Pode utilizar o FTP para transferir diretamente ficheiros para funções Azure.

>__Como usá-lo:__ Siga as instruções de [implantação do conteúdo utilizando FTP/s](../app-service/deploy-ftp.md).

>__Quando usá-lo:__ Em geral, recomendamos outros métodos de implantação. Quando publicar utilizando FTP, deve [sincronizar manualmente](#trigger-syncing)os gatilhos .

### <a name="portal-editing"></a>Edição do portal

No editor baseado no portal, pode editar diretamente os ficheiros que estão na sua aplicação de função (essencialmente implementando cada vez que guarda as suas alterações).

>__Como usá-lo:__ Para poder editar as suas funções no portal Azure, deve ter [criado as suas funções no portal](functions-create-first-azure-function.md). Para preservar uma única fonte de verdade, usar qualquer outro método de implementação faz a sua função apenas leitura e impede a edição contínua do portal. Para voltar a um estado em que pode editar os seus ficheiros no portal Azure, pode rodar manualmente o modo de edição para `Read/Write` e remover quaisquer definições de aplicação relacionadas com a implementação (como `WEBSITE_RUN_FROM_PACKAGE`). 

>__Quando usá-lo:__ O portal é uma boa maneira de começar com as Funções Azure. Para um trabalho de desenvolvimento mais intenso, recomendamos que utilize uma das seguintes ferramentas de cliente:
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Ferramentas nucleares de funções azure (linha de comando)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

A tabela que se segue mostra os sistemas operativos e idiomas que suportam a edição do portal:

| | Consumo de janelas | Windows Premium | Windows Dedicado | Consumo de Linux | Prémio Linux | Linux Dedicado |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C#Roteiro |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (Pré-visualização) | | | | | | |
| PowerShell (Pré-visualização) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> A edição do portal só está ativada para os gatilhos HTTP e Timer para funções no Linux utilizando planos Premium e Dedicados.

## <a name="deployment-slots"></a>Blocos de implementação

Quando implementar a sua aplicação de funções para o Azure, pode ser implantado para uma ranhura de implementação separada em vez de diretamente para a produção. Para obter mais informações sobre as ranhuras de implantação, consulte a documentação de slots de implantação de [funções azure](../app-service/deploy-staging-slots.md) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes

Leia estes artigos para saber mais sobre a implementação das suas aplicações de funções: 

+ [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)
+ [Entrega contínua utilizando O Azure DevOps](functions-how-to-azure-devops.md)
+ [Implantações zip para funções Azure](deployment-zip-push.md)
+ [Execute as suas Funções Azure a partir de um ficheiro de pacote](run-functions-from-deployment-package.md)
+ [Automatizar a implementação de recursos para a sua aplicação de funções em Funções Azure](functions-infrastructure-as-code.md)
