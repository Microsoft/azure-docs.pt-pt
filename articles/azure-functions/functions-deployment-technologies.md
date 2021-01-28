---
title: Tecnologias de implantação em Funções Azure
description: Saiba as diferentes formas de implantar código para as Funções Azure.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 4a65a00c28a20c9381d3dcc6fd7545137528d5c0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943629"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologias de implantação em Funções Azure

Pode utilizar algumas tecnologias diferentes para implementar o seu código de projeto Azure Functions para a Azure. Este artigo fornece uma visão geral dos métodos de implementação disponíveis para si e recomendações para o melhor método a utilizar em vários cenários. Fornece também uma lista exaustiva de detalhes e detalhes fundamentais sobre as tecnologias de implantação subjacentes. 

## <a name="deployment-methods"></a>Métodos de implantação

A tecnologia de implementação que utiliza para publicar código para a Azure é geralmente determinada pela forma como publica a sua aplicação. O método de implantação adequado é determinado por necessidades específicas e pelo ponto no ciclo de desenvolvimento. Por exemplo, durante o desenvolvimento e teste pode ser implantado diretamente a partir da sua ferramenta de desenvolvimento, como o Código do Estúdio Visual. Quando a sua aplicação está em produção, é mais provável que publique continuamente a partir do controlo de origem ou utilizando um pipeline de publicação automatizado, que inclui validação e testes adicionais.  

A tabela seguinte descreve os métodos de implantação disponíveis para o seu projeto Function.

| Tipo de implantação &nbsp; | Métodos | Melhor para... |
| -- | -- | -- |
| Baseado em ferramentas | &bull;&nbsp;[Publicação visual &nbsp; studio &nbsp; code &nbsp;](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Publicação do Estúdio Visual](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Publicação de Ferramentas Centrais](functions-run-local.md#publish) | Implementações durante o desenvolvimento e outras implementações de ad-hock. As implementações são geridas localmente pela ferramenta. | 
| Serviço de Aplicações gerido| &bull;&nbsp;[Centro de Implantação &nbsp; &nbsp; (CI/CD)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[&nbsp;Implantações de contentores](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  Implantação contínua (CI/CD) do controlo de origem ou de um registo de contentores. As implementações são geridas pela plataforma de Serviço de Aplicações (Kudu).|
| Gasodutos externos|&bull;&nbsp;[Gasodutos Azure](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[Ações do GitHub](functions-how-to-github-actions.md) | Os oleodutos de produção e DevOps que incluem validação adicional, testes e outras ações são executados como parte de uma implementação automatizada. As implementações são geridas pelo gasoduto. |

Embora as implementações específicas de funções utilizem a melhor tecnologia com base no seu contexto, a maioria dos métodos de implementação baseia-se na [implementação de zíperes](#zip-deploy).

## <a name="deployment-technology-availability"></a>Disponibilidade de tecnologia de implantação

A Azure Functions suporta o desenvolvimento local de plataformas cruzadas e hospedagem no Windows e Linux. Atualmente, três planos de hospedagem estão disponíveis:

+ [Consumo](consumption-plan.md)
+ [Premium](functions-premium-plan.md)
+ [Dedicado (Serviço de Aplicações)](dedicated-plan.md)

Cada plano tem comportamentos diferentes. Nem todas as tecnologias de implantação estão disponíveis para cada sabor das Funções Azure. O gráfico que se segue mostra quais as tecnologias de implantação suportadas para cada combinação de sistema operativo e plano de hospedagem:

| Tecnologia de implantação | Consumo de Janelas | Windows Premium | Windows Dedicado  | Consumo de Linux | Linux Premium | Linux Dedicado |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Pacote externo URL<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Implantação zip |✔|✔|✔|✔|✔|✔|
| Recipiente de estivador | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| Controlo de código fonte |✔|✔|✔| |✔|✔|
| Git local<sup>1</sup> |✔|✔|✔| |✔|✔|
| Sincronização de<sup>nuvem 1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Edição de portal |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> Tecnologia de implantação que requer [sincronização manual do gatilho](#trigger-syncing).
<sup>2</sup> A edição do portal está ativada apenas para desativações HTTP e Timer para funções no Linux utilizando planos Premium e Dedicados.

## <a name="key-concepts"></a>Conceitos-chave

Alguns conceitos-chave são cruciais para entender como as implementações funcionam em Funções Azure.

### <a name="trigger-syncing"></a>Sincronização do gatilho

Quando altera qualquer um dos seus gatilhos, a infraestrutura de Funções deve estar ciente das alterações. A sincronização acontece automaticamente para muitas tecnologias de implantação. No entanto, em alguns casos, deve sincronizar manualmente os seus gatilhos. Quando implementar as suas atualizações fazendo referência a um URL de pacote externo, a Git local, a sincronização da nuvem ou a FTP, tem de sincronizar manualmente os seus gatilhos. Pode sincronizar os gatilhos de uma de três maneiras:

* Reinicie a sua aplicação de função no portal Azure
* Envie um pedido HTTP POST para `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` utilizar a [tecla master](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Envie um pedido HTTP POST para `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` . Substitua os espaços reservados pelo seu ID de subscrição, nome de grupo de recursos e o nome da sua aplicação de função.

### <a name="remote-build"></a>Construção remota

As Funções Azure podem executar automaticamente as construções no código que recebe após as implementações de zip. Estas construções comportam-se de forma ligeiramente diferente, dependendo se a sua aplicação está a funcionar no Windows ou linux. As construções remotas não são executadas quando uma aplicação foi previamente definida para ser executada no modo [Run From Package.](run-functions-from-deployment-package.md) Para aprender a utilizar a construção remota, navegue para [implementar zip](#zip-deploy).

> [!NOTE]
> Se está a ter problemas com a construção remota, pode ser porque a sua app foi criada antes da funcionalidade ser disponibilizada (1 de agosto de 2019). Tente criar uma nova aplicação de função ou correr `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` para atualizar a sua aplicação de função. Este comando pode levar duas tentativas para ter sucesso.

#### <a name="remote-build-on-windows"></a>Construção remota no Windows

Todas as aplicações de função em execução no Windows têm uma pequena aplicação de gestão, o site SCM (ou [Kudu).](https://github.com/projectkudu/kudu) Este site lida com grande parte da implementação e constrói lógica para as Funções Azure.

Quando uma aplicação é implantada no Windows, são executados comandos específicos da linguagem, como `dotnet restore` (C#) ou `npm install` (JavaScript).

#### <a name="remote-build-on-linux"></a>Construção remota em Linux

Para permitir a construção remota no Linux, devem ser definidas as [seguintes definições de aplicação:](functions-how-to-use-azure-function-app-settings.md#settings)

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Por predefinição, tanto [as ferramentas principais do Azure Functions](functions-run-local.md) como a [Extensão de Funções Azure para Código do Estúdio Visual](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) executam construções remotas ao implementar em Linux. Por isso, ambas as ferramentas criam automaticamente estas definições para si em Azure.

Quando as aplicações são construídas remotamente no Linux, [elas funcionam a partir do pacote de implementação](run-functions-from-deployment-package.md).

##### <a name="consumption-plan"></a>Plano de consumo

As aplicações de função Linux que estão a decorrer no plano de Consumo não têm um site SCM/Kudu, o que limita as opções de implementação. No entanto, as aplicações de função no Linux em execução no plano de Consumo suportam construções remotas.

##### <a name="dedicated-and-premium-plans"></a>Planos dedicados e premium

As aplicações de função que funcionam no Linux no [plano Dedicado (Serviço de Aplicações)](dedicated-plan.md) e o [plano Premium](functions-premium-plan.md) também têm um site SCM/Kudu limitado.

## <a name="deployment-technology-details"></a>Detalhes da tecnologia de implantação

Os seguintes métodos de implantação estão disponíveis em Funções Azure.

### <a name="external-package-url"></a>URL de pacote externo

Pode utilizar um URL de pacote externo para fazer referência a um ficheiro de pacote remoto (.zip) que contenha a sua aplicação de função. O ficheiro é descarregado a partir do URL fornecido, e a aplicação é executada no modo [Run From Package.](run-functions-from-deployment-package.md)

>__Como usá-lo:__ Adicione [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) às definições da sua aplicação. O valor desta definição deve ser um URL (a localização do ficheiro de pacote específico que pretende executar). Pode adicionar definições [no portal](functions-how-to-use-azure-function-app-settings.md#settings) ou utilizando o [CLI Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).
>
>Se utilizar o armazenamento Azure Blob, utilize um recipiente privado com uma [assinatura de acesso partilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para dar às Funções acesso ao pacote. Sempre que a aplicação reinicia, recolhe uma cópia do conteúdo. A sua referência deve ser válida para o tempo de vida do pedido.

>__Quando usá-lo:__ O URL de pacote externo é o único método de implementação suportado para funções Azure em funcionamento no Linux no plano de Consumo, se o utilizador não quiser que ocorra uma [construção remota.](#remote-build) Quando atualiza o ficheiro do pacote que uma aplicação de função faz referência, tem de [sincronizar manualmente os gatilhos](#trigger-syncing) para dizer ao Azure que a sua aplicação foi alterada.

### <a name="zip-deploy"></a>Implantação zip

Utilize o zip implementar para empurrar um ficheiro .zip que contenha a sua aplicação de função para Azure. Opcionalmente, pode definir a sua aplicação para começar a [correr a partir do pacote,](run-functions-from-deployment-package.md)ou especificar que ocorre uma [construção remota.](#remote-build)

>__Como usá-lo:__ Implemente utilizando a sua ferramenta cliente favorita: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), Visual [Studio,](functions-develop-vs.md#publish-to-azure)ou a partir da linha de comando utilizando as [Ferramentas Principais de Funções Azure](functions-run-local.md#project-file-deployment). Por predefinição, estas ferramentas utilizam a implementação de zíper e [funcionam a partir do pacote](run-functions-from-deployment-package.md). As Ferramentas Core e a extensão visual Studio Code permitem a [construção remota](#remote-build) ao implementar em Linux. Para implementar manualmente um ficheiro .zip na sua aplicação de função, siga as instruções em [Implementar a partir de um ficheiro de .zip ou URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>Quando implementar utilizando o zip, pode definir a sua aplicação para executar a [partir do pacote](run-functions-from-deployment-package.md). Para executar a partir do pacote, defina o `WEBSITE_RUN_FROM_PACKAGE` valor de definição de aplicação .md#website_run_from_package para `1` . Recomendamos a colocação de zíper. Produz tempos de carregamento mais rápidos para as suas aplicações, e é o padrão para VS Code, Visual Studio e o Azure CLI.

>__Quando usá-lo:__ A implementação zip é a tecnologia de implantação recomendada para funções Azure.

### <a name="docker-container"></a>Recipiente de estivador

Pode implantar uma imagem de recipiente Linux que contém a sua aplicação de função.

>__Como usá-lo:__ Crie uma aplicação de função Linux no plano Premium ou Dedicado e especifique qual a imagem do recipiente a ser executada. Pode fazê-lo de duas formas:
>
>* Crie uma aplicação de função Linux num plano de Serviço de Aplicações Azure no portal Azure. Para **publicar**, selecione **Docker Image**, e, em seguida, configurar o recipiente. Entre no local onde a imagem está hospedada.
>* Crie uma aplicação de função Linux num plano de Serviço de Aplicações utilizando o CLI Azure. Para aprender como, consulte [Criar uma função no Linux utilizando uma imagem personalizada](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function).
>
>Para implementar numa aplicação existente utilizando um recipiente personalizado, em [Ferramentas Principais de Funções Azure,](functions-run-local.md)utilize o [`func deploy`](functions-run-local.md#publish) comando.

>__Quando usá-lo:__ Utilize a opção do recipiente Docker quando precisar de mais controlo sobre o ambiente Linux onde a sua aplicação de função é executado. Este mecanismo de implantação está disponível apenas para funções em funcionamento no Linux.

### <a name="web-deploy-msdeploy"></a>Implementação web (MSDeploy)

Implementar pacotes web e implementar as suas aplicações Windows em qualquer servidor IIS, incluindo as suas aplicações de função em execução no Windows em Azure.

>__Como usá-lo:__ Utilize [ferramentas de Estúdio Visual para Funções Azure](functions-create-your-first-function-visual-studio.md). Limpe a caixa de verificação **do ficheiro de embalagem (recomendado).**
>
>Também pode baixar [Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) e ligar `MSDeploy.exe` diretamente.

>__Quando usá-lo:__ Web Deploy é suportado e não tem problemas, mas o mecanismo preferido é [o zip implementar com Run From Package ativado](#zip-deploy). Para saber mais, consulte o guia de desenvolvimento do [Estúdio Visual.](functions-develop-vs.md#publish-to-azure)

### <a name="source-control"></a>Controlo de código fonte

Utilize o controlo de origem para ligar a sua aplicação de função a um repositório Git. Uma atualização ao código nesse repositório desencadeia a implantação. Para mais informações, consulte o [Kudu Wiki.](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)

>__Como usá-lo:__ Utilize o Centro de Implantação na área de Funções do portal para configurar a publicação a partir do controlo de origem. Para obter mais informações, consulte [a implementação contínua para funções Azure](functions-continuous-deployment.md).

>__Quando usá-lo:__ A utilização do controlo de fontes é a melhor prática para as equipas que colaboram nas suas aplicações de função. O controlo de origem é uma boa opção de implantação que permite oleodutos de implantação mais sofisticados.

### <a name="local-git"></a>Git Local

Pode utilizar o Git local para empurrar o código da sua máquina local para as Funções Azure utilizando o Git.

>__Como usá-lo:__ Siga as instruções na [implementação do Git Local para o Serviço de Aplicações Azure.](../app-service/deploy-local-git.md)

>__Quando usá-lo:__ Em geral, recomendamos que utilize um método de implantação diferente. Quando publicar a partir de Git local, deve [sincronizar manualmente os gatilhos](#trigger-syncing).

### <a name="cloud-sync"></a>Sincronização em nuvem

Utilize sincronização de nuvem para sincronizar o seu conteúdo de Funções Dropbox e OneDrive para Azure.

>__Como usá-lo:__ Siga as instruções em [Sync conteúdo de uma pasta de nuvem](../app-service/deploy-content-sync.md).

>__Quando usá-lo:__ Em geral, recomendamos outros métodos de implantação. Quando publicar utilizando sincronização de nuvem, tem de [sincronizar manualmente os gatilhos](#trigger-syncing).

### <a name="ftp"></a>FTP

Pode utilizar o FTP para transferir diretamente ficheiros para as Funções Azure.

>__Como usá-lo:__ Siga as instruções no [Conteúdo de Implantação utilizando FTP/s](../app-service/deploy-ftp.md).

>__Quando usá-lo:__ Em geral, recomendamos outros métodos de implantação. Quando publicar utilizando FTP, tem de [sincronizar manualmente os gatilhos](#trigger-syncing).

### <a name="portal-editing"></a>Edição de portal

No editor baseado no portal, pode editar diretamente os ficheiros que estão na sua aplicação de função (essencialmente implantando cada vez que guarda as suas alterações).

>__Como usá-lo:__ Para poder editar as suas funções no portal Azure, deve ter [criado as suas funções no portal](./functions-get-started.md). Para preservar uma única fonte de verdade, a utilização de qualquer outro método de implantação torna a sua função apenas lida e impede a edição contínua do portal. Para voltar a um estado em que pode editar os seus ficheiros no portal Azure, pode voltar a ligar manualmente o modo de edição `Read/Write` e remover quaisquer definições de aplicação relacionadas com a implementação (como [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) .

>__Quando usá-lo:__ O portal é uma boa maneira de começar com as Funções Azure. Para um trabalho de desenvolvimento mais intenso, recomendamos que utilize uma das seguintes ferramentas de cliente:
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Ferramentas principais de funções Azure (linha de comando)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

A tabela a seguir mostra os sistemas operativos e os idiomas que suportam a edição do portal:

| Linguagem | Consumo de Janelas | Windows Premium | Windows Dedicado | Consumo de Linux | Linux Premium | Linux Dedicado |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# Script |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (Pré-visualização) | | | | | | |
| PowerShell (Pré-visualização) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> A edição do portal é ativada apenas para desativações HTTP e Timer para Funções no Linux utilizando planos Premium e Dedicados.

## <a name="deployment-behaviors"></a>Comportamentos de implantação

Quando se faz uma implantação, todas as execuções existentes são autorizadas a completar ou a esgotar-se, após o que o novo código é carregado para iniciar pedidos de processamento.

Se precisar de mais controlo sobre esta transição, deve utilizar slots de implantação.

## <a name="deployment-slots"></a>Blocos de implementação

Quando implementar a sua aplicação de função para Azure, pode implementar para uma ranhura de implementação separada em vez de diretamente para a produção. Para obter mais informações sobre slots de implementação, consulte a documentação das [ranhuras de implantação de funções Azure](functions-deployment-slots.md) para obter mais detalhes.

## <a name="next-steps"></a>Próximos passos

Leia estes artigos para saber mais sobre a implementação das suas aplicações de função:

+ [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)
+ [Entrega contínua utilizando Azure DevOps](functions-how-to-azure-devops.md)
+ [Implementações zip para funções Azure](deployment-zip-push.md)
+ [Executar as suas Funções Azure a partir de um ficheiro de pacote](run-functions-from-deployment-package.md)
+ [Automatizar a implementação de recursos para a sua aplicação de funções em Funções Azure](functions-infrastructure-as-code.md)
