---
title: Tecnologias de implantação no Azure Functions
description: Conheça as diferentes maneiras como você pode implantar código para Azure Functions.
author: georgewallace
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gwallace
ms.openlocfilehash: 7b95fae3c7b713a9f97c31578a3bb24a9062fa2e
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029865"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologias de implantação no Azure Functions

Você pode usar algumas tecnologias diferentes para implantar o código do projeto Azure Functions no Azure. Este artigo fornece uma lista completa dessas tecnologias, descreve quais tecnologias estão disponíveis para quais tipos de funções, explica o que acontece quando você usa cada método e fornece recomendações para o melhor método a ser usado em vários cenários . As várias ferramentas que dão suporte à implantação em Azure Functions são ajustadas para a tecnologia certa com base em seu contexto. Em geral, a implantação de zip é a tecnologia de implantação recomendada para Azure Functions.

## <a name="deployment-technology-availability"></a>Disponibilidade de tecnologia de implantação

O Azure Functions dá suporte ao desenvolvimento local de plataforma cruzada e à hospedagem no Windows e no Linux. Atualmente, três planos de hospedagem estão disponíveis:

+ [Consumo](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dedicado (Serviço de Aplicações)](functions-scale.md#app-service-plan)

Cada plano tem comportamentos diferentes. Nem todas as tecnologias de implantação estão disponíveis para cada tipo de Azure Functions. O gráfico a seguir mostra quais tecnologias de implantação têm suporte para cada combinação de sistema operacional e plano de hospedagem:

| Tecnologia de implantação | Consumo do Windows | Windows Premium | Windows dedicado  | Consumo do Linux | Linux Premium | Linux dedicado |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| URL do pacote externo<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Implantação de zip |✔|✔|✔|✔|✔|✔|
| Contêiner do Docker | | | | |✔|✔|
| Implantação web |✔|✔|✔| | | |
| Controlo de código fonte |✔|✔|✔| |✔|✔|
| Git<sup>1</sup> local |✔|✔|✔| |✔|✔|
| Sincronização de nuvens<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Edição do portal |✔|✔|✔| |<sup>✔ 2</sup>|<sup>✔ 2</sup>|

<sup>1</sup> Tecnologia de implantação que requer [sincronização manual do gatilho](#trigger-syncing).  
<sup>2</sup> A edição do portal está ativada apenas para os gatilhos HTTP e Temporizador para Funções no Linux utilizando planos Premium e Dedicados.

## <a name="key-concepts"></a>Conceitos-chave

Alguns conceitos importantes são essenciais para entender como as implantações funcionam em Azure Functions.

### <a name="trigger-syncing"></a>Disparar sincronização

Quando você altera qualquer um de seus gatilhos, a infraestrutura do Functions deve estar ciente das alterações. A sincronização ocorre automaticamente para muitas tecnologias de implantação. No entanto, em alguns casos, você deve sincronizar manualmente seus gatilhos. Ao implantar suas atualizações referenciando uma URL de pacote externo, git local, sincronização de nuvem ou FTP, você deve sincronizar manualmente seus gatilhos. Você pode sincronizar gatilhos de uma destas três maneiras:

* Reinicie seu aplicativo de funções no portal do Azure
* Envie um pedido HTTP POST para `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` utilizando a [chave principal](functions-bindings-http-webhook.md#authorization-keys).
* Envie um pedido HTTP POST para `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Substitua os espaços reservados por sua ID de assinatura, nome do grupo de recursos e o nome do seu aplicativo de funções.

### <a name="remote-build"></a>Build remoto

Azure Functions pode executar compilações automaticamente no código que recebe após implantações de zip. Essas compilações se comportam um pouco diferente dependendo se seu aplicativo está em execução no Windows ou no Linux. As construções remotas não são realizadas quando uma aplicação foi previamente definida para ser executada no modo [Run From Package.](run-functions-from-deployment-package.md) Para aprender a usar a construção remota, navegue para [o zip deploy](#zip-deploy).

> [!NOTE]
> Se você estiver tendo problemas com a compilação remota, pode ser porque seu aplicativo foi criado antes de o recurso ser disponibilizado (1º de agosto de 2019). Tente criar uma nova aplicação de funções ou executando `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` para atualizar a sua aplicação de funções. Esse comando pode levar duas tentativas de sucesso.

#### <a name="remote-build-on-windows"></a>Build remoto no Windows

Todas as aplicações de funções que funcionam no Windows têm uma pequena aplicação de gestão, o site SCM (ou [Kudu).](https://github.com/projectkudu/kudu) Esse site manipula grande parte da implantação e da lógica de compilação para Azure Functions.

Quando uma aplicação é implementada para windows, comandosC#específicos do idioma, como `dotnet restore` ( ) ou `npm install` (JavaScript) são executados.

#### <a name="remote-build-on-linux"></a>Build remoto no Linux

Para permitir a construção remota do Linux, devem ser definidas as [seguintes definições de aplicação:](functions-how-to-use-azure-function-app-settings.md#settings)

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Por predefinição, tanto as [funções azure como](functions-run-local.md) a extensão de [funções Azure para código](functions-create-first-function-vs-code.md#publish-the-project-to-azure) de estúdio visual executam construções remotas ao ser implantados para o Linux. Por isso, ambas as ferramentas criam automaticamente essas configurações para você no Azure. 

Quando as aplicações são construídas remotamente no Linux, [funcionam a partir do pacote de implementação](run-functions-from-deployment-package.md). 

##### <a name="consumption-plan"></a>Plano de consumo

Os aplicativos de funções do Linux em execução no plano de consumo não têm um site SCM/kudu, que limita as opções de implantação. No entanto, os aplicativos de funções no Linux em execução no plano de consumo oferecem suporte a compilações remotas.

##### <a name="dedicated-and-premium-plans"></a>Planos dedicado e Premium

As aplicações de função que funcionam no Linux no [plano Dedicado (App Service)](functions-scale.md#app-service-plan) e o [plano Premium](functions-scale.md#premium-plan) também têm um site sCM/Kudu limitado.

## <a name="deployment-technology-details"></a>Detalhes da tecnologia de implantação

Os métodos de implantação a seguir estão disponíveis no Azure Functions.

### <a name="external-package-url"></a>URL do pacote externo

Você pode usar uma URL de pacote externo para fazer referência a um arquivo de pacote remoto (. zip) que contém seu aplicativo de funções. O ficheiro é descarregado a partir do URL fornecido, e a aplicação é executada no modo [Run From Package.](run-functions-from-deployment-package.md)

>__Como usá-lo:__ Adicione `WEBSITE_RUN_FROM_PACKAGE` às definições de aplicação. O valor dessa configuração deve ser uma URL (o local do arquivo de pacote específico que você deseja executar). Pode adicionar configurações [no portal](functions-how-to-use-azure-function-app-settings.md#settings) ou [utilizando o Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Se utilizar o armazenamento Azure Blob, utilize um recipiente privado com uma assinatura de [acesso partilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para dar acesso às Funções à embalagem. Sempre que o aplicativo for reiniciado, ele buscará uma cópia do conteúdo. Sua referência deve ser válida durante o tempo de vida do aplicativo.

>__Quando usá-lo:__ O URL do pacote externo é o único método de implementação suportado para funções Azure em execução no Linux no plano de consumo, se o utilizador não quiser que ocorra uma [construção remota.](#remote-build) Ao atualizar o ficheiro do pacote que uma aplicação de função refere, tem de [sincronizar manualmente os gatilhos](#trigger-syncing) para dizer ao Azure que a sua aplicação mudou.

### <a name="zip-deploy"></a>Implantação de zip

Use a implantação de zip para enviar por push um arquivo. zip que contém seu aplicativo de funções para o Azure. Opcionalmente, pode configurar a sua aplicação para começar a funcionar a [partir do pacote](run-functions-from-deployment-package.md), ou especificar que ocorre uma [construção remota.](#remote-build)

>__Como usá-lo:__ Implemente utilizando a sua ferramenta cliente favorita: [Visual Studio Code,](functions-create-first-function-vs-code.md#publish-the-project-to-azure) [Visual Studio,](functions-develop-vs.md#publish-to-azure)as [Ferramentas Core funções Do Azure](functions-run-local.md)ou o [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Por predefinição, estas ferramentas utilizam a implantação de zíper e [funcionam a partir da embalagem](run-functions-from-deployment-package.md). As Ferramentas Core e a extensão do Código do Estúdio Visual permitem a [construção remota](#remote-build) ao ser implantado para o Linux. Para implementar manualmente um ficheiro .zip na sua aplicação de funções, siga as instruções de [implantação a partir de um ficheiro .zip ou URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>Quando implementar utilizando a implementação de zip, pode definir a sua aplicação para executar a [partir do pacote](run-functions-from-deployment-package.md). Para correr a partir do pacote, defina o valor de definição de aplicação `WEBSITE_RUN_FROM_PACKAGE` para `1`. Recomendamos a implantação de zip. Ele produz tempos de carregamento mais rápidos para seus aplicativos e é o padrão para VS Code, o Visual Studio e o CLI do Azure. 

>__Quando usá-lo:__ A implantação de zip é a tecnologia de implementação recomendada para funções Azure.

### <a name="docker-container"></a>Contêiner do Docker

Você pode implantar uma imagem de contêiner do Linux que contém seu aplicativo de funções.

>__Como usá-lo:__ Crie uma aplicação de função Linux no plano Premium ou Dedicado e especifique a imagem do recipiente a partir. Pode fazê-lo de duas formas:
>
>* Crie um aplicativo de funções do Linux em um plano de serviço Azure App no portal do Azure. Para **publicar**, selecione **Docker Image**e, em seguida, configure o recipiente. Insira o local onde a imagem está hospedada.
>* Crie um aplicativo de funções do Linux em um plano do serviço de aplicativo usando o CLI do Azure. Para saber como, consulte [Criar uma função no Linux utilizando uma imagem personalizada](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function).
>
>Para implantar numa aplicação existente utilizando um recipiente personalizado, em [Ferramentas Core funções Azure,](functions-run-local.md)utilize o comando [`func deploy`.](functions-run-local.md#publish)

>__Quando usá-lo:__ Utilize a opção de contentor Docker quando necessitar de mais controlo sobre o ambiente Linux onde funciona a sua aplicação de funções. Esse mecanismo de implantação está disponível somente para funções em execução no Linux.

### <a name="web-deploy-msdeploy"></a>Implantação da Web (MSDeploy)

Implantação da Web pacotes e implanta seus aplicativos do Windows em qualquer servidor IIS, incluindo seus aplicativos de função em execução no Windows no Azure.

>__Como usá-lo:__ Utilize [ferramentas de Estúdio Visual para funções Azure](functions-create-your-first-function-visual-studio.md). Limpe a caixa de verificação do ficheiro de **pacote (recomendado).**
>
>Também pode baixar o [Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) e `MSDeploy.exe` ligar diretamente.

>__Quando usá-lo:__ A Web Deploy é suportada e não tem problemas, mas o mecanismo preferido é a implementação do [zip com o Pacote Run From ativado](#zip-deploy). Para saber mais, consulte o guia de desenvolvimento do [Estúdio Visual.](functions-develop-vs.md#publish-to-azure)

### <a name="source-control"></a>Controlo de código fonte

Use o controle do código-fonte para conectar seu aplicativo de funções a um repositório git. Uma atualização de código nesse repositório dispara a implantação. Para mais informações, consulte o [Kudu Wiki.](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)

>__Como usá-lo:__ Utilize o Centro de Implantação na área de Funções do portal para configurar a publicação a partir do controlo de origem. Para mais informações, consulte a [implantação contínua para funções Azure](functions-continuous-deployment.md).

>__Quando usá-lo:__ Usar o controlo de fontes é a melhor prática para as equipas que colaboram nas suas aplicações de função. O controle do código-fonte é uma boa opção de implantação que permite pipelines de implantação mais sofisticados.

### <a name="local-git"></a>Git Local

Você pode usar o Git local para enviar código por push do computador local para Azure Functions usando o git.

>__Como usá-lo:__ Siga as instruções na [implantação local git para o Serviço de Aplicações Azure](../app-service/deploy-local-git.md).

>__Quando usá-lo:__ Em geral, recomendamos que utilize um método de implantação diferente. Quando publicar a partir de Git local, deve [sincronizar manualmente](#trigger-syncing)os gatilhos .

### <a name="cloud-sync"></a>Sincronização de nuvem

Use a sincronização de nuvem para sincronizar seu conteúdo do Dropbox e do OneDrive para Azure Functions.

>__Como usá-lo:__ Siga as instruções no [conteúdo sync a partir de uma pasta de nuvem](../app-service/deploy-content-sync.md).

>__Quando usá-lo:__ Em geral, recomendamos outros métodos de implantação. Quando publicar utilizando sincronização em nuvem, deve [sincronizar manualmente](#trigger-syncing)os gatilhos .

### <a name="ftp"></a>FTP

Você pode usar o FTP para transferir arquivos diretamente para o Azure Functions.

>__Como usá-lo:__ Siga as instruções de [implantação do conteúdo utilizando FTP/s](../app-service/deploy-ftp.md).

>__Quando usá-lo:__ Em geral, recomendamos outros métodos de implantação. Quando publicar utilizando FTP, deve [sincronizar manualmente](#trigger-syncing)os gatilhos .

### <a name="portal-editing"></a>Edição do portal

No editor baseado em portal, você pode editar diretamente os arquivos que estão em seu aplicativo de funções (essencialmente implantando sempre que você salvar suas alterações).

>__Como usá-lo:__ Para poder editar as suas funções no portal Azure, deve ter [criado as suas funções no portal](functions-create-first-azure-function.md). Para preservar uma única fonte de verdade, usar qualquer outro método de implantação torna sua função somente leitura e impede a edição continuada do Portal. Para voltar a um estado em que pode editar os seus ficheiros no portal Azure, pode rodar manualmente o modo de edição para `Read/Write` e remover quaisquer definições de aplicação relacionadas com a implementação (como `WEBSITE_RUN_FROM_PACKAGE`). 

>__Quando usá-lo:__ O portal é uma boa maneira de começar com as Funções Azure. Para um trabalho de desenvolvimento mais intenso, recomendamos que você use uma das seguintes ferramentas de cliente:
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Ferramentas nucleares de funções azure (linha de comando)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

A tabela a seguir mostra os sistemas operacionais e idiomas que dão suporte à edição do portal:

| | Consumo do Windows | Windows Premium | Windows dedicado | Consumo do Linux | Linux Premium | Linux dedicado |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C#Prescritiva |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (Pré-visualização) | | | | | | |
| PowerShell (visualização) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> A edição do portal só está ativada para os gatilhos HTTP e Timer para funções no Linux utilizando planos Premium e Dedicados.

## <a name="deployment-slots"></a>Blocos de implementação

Ao implantar seu aplicativo de funções no Azure, você pode implantar em um slot de implantação separado em vez de diretamente na produção. Para obter mais informações sobre as ranhuras de implantação, consulte a documentação de slots de implantação de [funções azure](../app-service/deploy-staging-slots.md) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes

Leia estes artigos para saber mais sobre como implantar seus aplicativos de funções: 

+ [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)
+ [Entrega contínua utilizando O Azure DevOps](functions-how-to-azure-devops.md)
+ [Implantações zip para funções Azure](deployment-zip-push.md)
+ [Execute as suas Funções Azure a partir de um ficheiro de pacote](run-functions-from-deployment-package.md)
+ [Automatizar a implementação de recursos para a sua aplicação de funções em Funções Azure](functions-infrastructure-as-code.md)
