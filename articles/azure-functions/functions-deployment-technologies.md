---
title: Tecnologias de implantação nas funções do Azure | Documentos da Microsoft
description: Conheça as diferentes formas que pode implementar código para as funções do Azure.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 47d8bf33fd686942326db3b1cc606978bf47a1bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594402"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologias de implantação nas funções do Azure

Pode usar algumas tecnologias diferentes para implementar o código de projeto de funções do Azure para o Azure. Este artigo fornece uma lista exaustiva dessas tecnologias, descreve quais tecnologias estão disponíveis para quais tipos de funções, explica o que acontece quando utilizar cada método e fornece recomendações para o melhor método utilizar em vários cenários . As várias ferramentas que suportam a implementação para as funções do Azure são ajustadas para a tecnologia certa com base no seu contexto.

## <a name="deployment-technology-availability"></a>Disponibilidade de tecnologia de implementação

> [!IMPORTANT]
> As funções do Azure suporta várias plataformas desenvolvimento local e de hospedagem no Windows e Linux. Atualmente, as três planos de alojamento que estão disponíveis: [Consumo](functions-scale.md#consumption-plan), [Premium](functions-scale.md#premium-plan), e [dedicado (serviço de aplicações do Azure)](functions-scale.md#app-service-plan). Cada plano tem diferentes comportamentos. Nem todas as tecnologias de implantação estão disponíveis para cada sabor das funções do Azure.

| Tecnologia de implantação | Consumo de Windows | Premium do Windows (pré-visualização) | Windows dedicado  | Consumo de Linux (pré-visualização) | Linux dedicado |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| O URL do pacote externo<sup>1</sup> |✔|✔|✔|✔|✔|
| Implementar zip |✔|✔|✔| |✔|
| Contentor do docker | | | | |✔|
| Web Deploy |✔|✔|✔| | |
| Controlo de origem |✔|✔|✔| |✔|
| Local Git<sup>1</sup> |✔|✔|✔| |✔|
| Sincronização da cloud<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| A edição de portal |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> tecnologia de implementação que requer [sincronização do acionador manual](#trigger-syncing).  
<sup>2</sup> portal edição está ativada apenas para acionadores HTTP e temporizador para as funções no Linux com o plano dedicado.

## <a name="key-concepts"></a>Conceitos-chave

Alguns dos principais conceitos são fundamentais para entender como implementações funcionam nas funções do Azure.

### <a name="trigger-syncing"></a>A sincronização de Acionador

Quando altera qualquer uma das suas acionadores, a infraestrutura de funções deve estar ciente das alterações. Sincronização ocorre automaticamente para muitas tecnologias de implantação. No entanto, em alguns casos, deve sincronizar manualmente o seu acionadores. Ao implementar as atualizações ao referenciar um URL do pacote externo, local Git, da sincronização da cloud ou FTP, tem de sincronizar manualmente sua acionadores. Pode sincronizar os acionadores de uma das três formas:

* Reinicie a aplicação de função no portal do Azure
* Enviar um pedido de HTTP POST para `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` utilizando o [chave mestra](functions-bindings-http-webhook.md#authorization-keys).
* Enviar um pedido de HTTP POST para `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Substitua os marcadores de posição pelo seu ID de subscrição, nome do grupo de recursos e o nome da sua aplicação de função.

## <a name="deployment-technology-details"></a>Detalhes da tecnologia de implementação 

Os seguintes métodos de implementação estão disponíveis nas funções do Azure.

### <a name="external-package-url"></a>URL do pacote externo

Pode utilizar um URL do pacote externo para fazer referência a um ficheiro de pacote remoto (. zip) que contém a aplicação de funções. O ficheiro é transferido do URL fornecido e a aplicação é executada [execução do pacote](run-functions-from-deployment-package.md) modo.

>__Como usá-lo:__ Adicionar `WEBSITE_RUN_FROM_PACKAGE` para as definições da aplicação. O valor desta definição deve ser um URL (a localização do ficheiro de pacote específico que pretende executar). Pode adicionar as definições de qualquer um dos [no portal](functions-how-to-use-azure-function-app-settings.md#settings) ou [ao utilizar a CLI do Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Se utilizar o armazenamento de Blobs do Azure, utilize um contêiner privado com um [assinatura de acesso partilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para conceder acesso de funções para o pacote. Sempre que o reinício da aplicação, obtém uma cópia do conteúdo. Sua referência tem de ser válida para o tempo de vida do aplicativo.

>__Quando utilizá-lo:__ O URL do pacote externo é o método de implementação suportados apenas para as funções do Azure em execução no Linux no plano de consumo (pré-visualização). Ao atualizar o ficheiro de pacote que faça referência a uma aplicação de funções, deve [sincronizar manualmente o acionadores](#trigger-syncing) para indicar ao Auzre que seu aplicativo foi alterado.

### <a name="zip-deploy"></a>Implementar zip

Implementar zip de utilização para enviar um ficheiro. zip que contém a aplicação de funções para o Azure. Opcionalmente, pode definir a aplicação para iniciar em [execução do pacote](run-functions-from-deployment-package.md) modo.

>__Como usá-lo:__ Implemente com a sua ferramenta de cliente favoritas: [Código VS](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure), ou a [da CLI do Azure](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Para implementar manualmente um ficheiro. zip para a aplicação de funções, siga as instruções em [implementar a partir de um ficheiro. zip ou uma URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>Quando implantar usando o zip implementar, pode definir a aplicação seja executada [execução do pacote](run-functions-from-deployment-package.md) modo. Para definir o modo de execução do pacote, defina o `WEBSITE_RUN_FROM_PACKAGE` valor de definição de aplicação para `1`. Recomendamos a implementação do zip. Isso resulta em tempos de carregamento mais rápidos para as suas aplicações e é o padrão para o VS Code, Visual Studio e a CLI do Azure.

>__Quando utilizá-lo:__ Implementar zip é a tecnologia de implementação recomendado para as funções do Azure em execução no Windows e para as funções do Azure em execução no Linux no plano de dedicado.

### <a name="docker-container"></a>Contentor do docker

Pode implantar uma imagem de contentor do Linux que contém a aplicação de funções.

>__Como usá-lo:__ Criar uma aplicação de funções do Linux no plano de dedicado e especifique a imagem de contentor para executar a partir de. Pode fazê-lo de duas formas:
>
>* Crie uma aplicação de funções do Linux num plano do serviço de aplicações do Azure no portal do Azure. Para **Publish**, selecione **imagem do Docker**e, em seguida, configure o contentor. Introduza a localização onde a imagem é alojada.
>* Crie uma aplicação de funções do Linux num plano do serviço de aplicações com a CLI do Azure. Para saber como, veja [criar uma função no Linux com uma imagem personalizada](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Para implementar a uma aplicação existente com um contentor personalizado, no [ferramentas de núcleo de funções do Azure](functions-run-local.md), utilize o [ `func deploy` ](functions-run-local.md#publish) comando.

>__Quando utilizá-lo:__ Utilize a opção de contentor do Docker quando precisar de mais controlo sobre o ambiente do Linux em que a aplicação function app é executada. Esse mecanismo de implementação está disponível apenas para as funções em execução no Linux num plano do serviço de aplicações.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

Web implementar pacotes e implementa seus aplicativos do Windows a qualquer servidor IIS, incluindo as suas aplicações de funções em execução no Windows no Azure.

>__Como usá-lo:__ Uso [ferramentas do Visual Studio das funções do Azure](functions-create-your-first-function-visual-studio.md). Limpar o **execute a partir de ficheiro de pacote (recomendado)** caixa de verificação.
>
>Também pode baixar [3.6 de implementar Web](https://www.iis.net/downloads/microsoft/web-deploy) e chamar `MSDeploy.exe` diretamente.

>__Quando utilizá-lo:__ Web Deploy é suportada e não tem problemas, mas é o mecanismo preferencial [zip implementar com a execução do pacote habilitado](#zip-deploy). Para obter mais informações, consulte a [guia de desenvolvimento do Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Controlo de origem

Utilize o controlo de origem para ligar a aplicação de função para um repositório de Git. Uma atualização para o código desse repositório aciona a implementação. Para obter mais informações, consulte a [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Como usá-lo:__ Utilize o Centro de implementação no portal de funções do Azure para configurar a publicação no controle da fonte. Para obter mais informações, consulte [implementação contínua para funções do Azure](functions-continuous-deployment.md).

>__Quando utilizá-lo:__ Usando o controle de origem é a melhor prática para as equipes que colaborar nas suas aplicações de função. Controlo de origem é uma opção de implementação boa que permite que os pipelines de implantação mais sofisticados.

### <a name="local-git"></a>Git Local

Pode utilizar local Git para o código de push de seu computador local para as funções do Azure utilizando o Git.

>__Como usá-lo:__ Siga as instruções em [implementação de Local Git para o serviço de aplicações do Azure](../app-service/deploy-local-git.md).

>__Quando utilizá-lo:__ Em geral, recomendamos que utilize um método de implementação diferentes. Quando publicar a partir do local Git, deve [sincronizar manualmente o acionadores](#trigger-syncing).

### <a name="cloud-sync"></a>Sincronização da cloud

Utilização da cloud sincronização ao sincronizar o conteúdo da Dropbox e OneDrive para as funções do Azure.

>__Como usá-lo:__ Siga as instruções em [sincronizar conteúdos de uma pasta de cloud](../app-service/deploy-content-sync.md).

>__Quando utilizá-lo:__ Em geral, recomendamos que outros métodos de implantação. Quando publica através da sincronização da cloud, deve [sincronizar manualmente o acionadores](#trigger-syncing).

### <a name="ftp"></a>FTP

Pode utilizar o FTP para transferir diretamente ficheiros para as funções do Azure.

>__Como usá-lo:__ Siga as instruções em [implementar conteúdo com FTP/s](../app-service/deploy-ftp.md).

>__Quando utilizá-lo:__ Em geral, recomendamos que outros métodos de implantação. Quando publica através de FTP, deve [sincronizar manualmente o acionadores](#trigger-syncing).

### <a name="portal-editing"></a>A edição de portal

No editor baseado no portal, pode editar diretamente os ficheiros que estão na sua aplicação de função (essencialmente implantar sempre que guardar as alterações).

>__Como usá-lo:__ Para conseguir editar as suas funções no portal do Azure, tem de ter [criado as suas funções no portal do](functions-create-first-azure-function.md). Para preservar uma única fonte de verdade, utilizar qualquer outro método de implementação torna sua função só de leitura e impede a edição de portal contínuo. Para voltar para um Estado no qual pode editar os ficheiros no portal do Azure, pode ativar manualmente o modo de edição para `Read/Write` e remover as definições relacionadas com a implementação de aplicação (como `WEBSITE_RUN_FROM_PACKAGE`). 

>__Quando utilizá-lo:__ O portal é uma boa maneira de começar com as funções do Azure. Para projetos de programação mais intenso, recomendamos que utilize o cliente de ferramentas:
>
>* [Começar a utilizar o VS Code](functions-create-first-function-vs-code.md)
>* [Começar a utilizar as ferramentas de núcleo de funções do Azure](functions-run-local.md)
>* [Introdução à utilização do Visual Studio](functions-create-your-first-function-visual-studio.md)

A tabela seguinte mostra os sistemas operativos e os idiomas que edição do portal de suporte:

| | Consumo de Windows | Premium do Windows (pré-visualização) | Windows dedicado | Consumo de Linux (pré-visualização) | Linux dedicado |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| C#Script |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python (Pré-visualização) | | | | | |
| PowerShell (pré-visualização) |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> Edição portal está ativada apenas para HTTP e temporizador acionadores para funções no Linux com o plano dedicado.

## <a name="deployment-slots"></a>Blocos de implementação

Quando implementar a aplicação de funções do Azure, pode implementar para um bloco de implementação separados em vez de diretamente para a produção. Para obter mais informações sobre as ranhuras de implementação, consulte [ranhuras de serviço de aplicações do Azure](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Níveis de ranhuras de implementação de suporte

Existem dois níveis de suporte para ranhuras de implementação:

* **Disponibilidade geral (GA)** : Totalmente aprovados e com suporte para utilização em produção.
* **Pré-visualização**: Ainda não suportado, mas é esperado para alcançar o estado de disponibilidade geral no futuro.

| Plano de alojamento/SO | Nível de suporte |
| --------------- | ------ |
| Consumo de Windows | Pré-visualização |
| Premium do Windows (pré-visualização) | Pré-visualização |
| Windows dedicado | Disponibilidade geral |
| Consumo de Linux | Não suportado |
| Linux dedicado | Disponibilidade geral |

## <a name="next-steps"></a>Passos seguintes

Leia os seguintes artigos para saber mais sobre como implementar as suas aplicações de funções: 

+ [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)
+ [Entrega contínua através do Azure DevOps](functions-how-to-azure-devops.md)
+ [Implementações de zip para as funções do Azure](deployment-zip-push.md)
+ [Execute as suas funções do Azure a partir de um ficheiro de pacote](run-functions-from-deployment-package.md)
+ [Automatizar a implementação de recursos para a sua aplicação de função nas funções do Azure](functions-infrastructure-as-code.md)
