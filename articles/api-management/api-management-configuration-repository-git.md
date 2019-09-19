---
title: Configurar o serviço de gerenciamento de API usando o Git – Azure | Microsoft Docs
description: Saiba como salvar e configurar sua configuração de serviço de gerenciamento de API usando o git.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: 9bbd62bc05e03641c2abe9308d9238bef23877c2
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104971"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Como salvar e configurar a configuração do serviço de gerenciamento de API usando o Git

Cada instância do serviço de gerenciamento de API mantém um banco de dados de configuração que contém informações sobre a configuração e os metadados para a instância do serviço. As alterações podem ser feitas na instância do serviço alterando uma configuração no portal do Azure, usando um cmdlet do PowerShell ou fazendo uma chamada à API REST. Além desses métodos, você também pode gerenciar sua configuração de instância de serviço usando o Git, permitindo cenários de gerenciamento de serviços, como:

* Controle de versão de configuração-Baixe e armazene versões diferentes de sua configuração de serviço
* Alterações de configuração em massa-faça alterações em várias partes da configuração de seu serviço em seu repositório local e integre as alterações de volta ao servidor com uma única operação
* Ferramentas e fluxo de trabalho familiar – use as ferramentas e os fluxos de trabalho do git com os quais você já está familiarizado

O diagrama a seguir mostra uma visão geral das diferentes maneiras de configurar sua instância do serviço de gerenciamento de API.

![Configuração do git][api-management-git-configure]

Quando você faz alterações no serviço usando o portal do Azure, cmdlets do PowerShell ou a API REST, você está gerenciando o banco de dados de configuração `https://{name}.management.azure-api.net` de serviço usando o ponto de extremidade, conforme mostrado no lado direito do diagrama. O lado esquerdo do diagrama ilustra como você pode gerenciar sua configuração de serviço usando o git e o repositório Git para seu serviço `https://{name}.scm.azure-api.net`localizado em.

As etapas a seguir fornecem uma visão geral do gerenciamento da instância do serviço de gerenciamento de API usando o git.

1. Acessar a configuração do git em seu serviço
2. Salvar o banco de dados de configuração de serviço em seu repositório git
3. Clonar o repositório git em seu computador local
4. Faça pull do repositório mais recente para seu computador local e confirme e envie por push as alterações de volta para seu repositório
5. Implantar as alterações do seu repositório em seu banco de dados de configuração de serviço

Este artigo descreve como habilitar e usar o Git para gerenciar a configuração de seu serviço e fornece uma referência para os arquivos e pastas no repositório git.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Acessar a configuração do git em seu serviço

Para exibir e definir as definições de configuração do git, você pode clicar no menu **segurança** e navegar até a guia **repositório de configuração** .

![Habilitar GIT][api-management-enable-git]

> [!IMPORTANT]
> Os segredos que não estiverem definidos como valores nomeados serão armazenados no repositório e permanecerão em seu histórico até você desabilitar e reabilitar o acesso ao git. Os valores nomeados fornecem um local seguro para gerenciar valores de cadeia de caracteres constantes, incluindo segredos, em todas as políticas e configuração de API, para que você não precise armazená-los diretamente em suas instruções de política. Para obter mais informações, consulte [como usar valores nomeados em políticas de gerenciamento de API do Azure](api-management-howto-properties.md).
>
>

Para obter informações sobre como habilitar ou desabilitar o acesso do git usando a API REST, confira [Ativar ou desativar o acesso ao git usando a API REST](/rest/api/apimanagement/2019-01-01/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Para salvar a configuração de serviço no repositório git

A primeira etapa antes de clonar o repositório é salvar o estado atual da configuração do serviço no repositório. Clique em **salvar no repositório**.

Faça as alterações desejadas na tela de confirmação e clique em **OK** para salvar.

Após alguns instantes, a configuração é salva e o status de configuração do repositório é exibido, incluindo a data e hora da última alteração de configuração e a última sincronização entre a configuração do serviço e o repositório.

Depois que a configuração é salva no repositório, ela pode ser clonada.

Para obter informações sobre como executar essa operação usando a API REST, consulte [confirmar instantâneo de configuração usando a API REST](/rest/api/apimanagement/2019-01-01/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Para clonar o repositório em seu computador local

Para clonar um repositório, você precisa da URL para o repositório, um nome de usuário e uma senha. Para obter o nome de usuário e outras credenciais, clique em **credenciais de acesso** próximo à parte superior da página.

Para gerar uma senha, primeiro verifique se a **expiração** está definida para a data e hora de expiração desejadas e clique em **gerar**.

> [!IMPORTANT]
> Anote essa senha. Depois de sair dessa página, a senha não será exibida novamente.
>

Os exemplos a seguir usam a ferramenta git bash do [git para Windows](https://www.git-scm.com/downloads) , mas você pode usar qualquer ferramenta git com a qual esteja familiarizado.

Abra a ferramenta git na pasta desejada e execute o comando a seguir para clonar o repositório git em seu computador local, usando o comando fornecido pelo portal do Azure.

```
git clone https://{name}.scm.azure-api.net/
```

Forneça o nome de usuário e a senha quando solicitado.

Se você receber erros, tente modificar `git clone` o comando para incluir o nome de usuário e a senha, conforme mostrado no exemplo a seguir.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Se isso fornecer um erro, tente codificar a URL da parte de senha do comando. Uma maneira rápida de fazer isso é abrir o Visual Studio e emitir o comando a seguir na **janela imediata**. Para abrir a **janela imediata**, abra qualquer solução ou projeto no Visual Studio (ou crie um novo aplicativo de console vazio) e escolha **Windows**, **imediatamente** no menu **depurar** .

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

Use a senha codificada junto com seu nome de usuário e o local do repositório para construir o comando git.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

Depois que o repositório for clonado, você poderá exibir e trabalhar com ele no sistema de arquivos local. Para obter mais informações, consulte [referência de estrutura de arquivo e pasta do repositório git local](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Para atualizar seu repositório local com a configuração de instância de serviço mais atual

Se você fizer alterações na instância do serviço de gerenciamento de API no portal do Azure ou usando a API REST, deverá salvar essas alterações no repositório antes de atualizar seu repositório local com as alterações mais recentes. Para fazer isso, clique em **salvar configuração no repositório** na guia **repositório de configuração** no portal do Azure e, em seguida, emita o seguinte comando no repositório local.

```
git pull
```

Antes de `git pull` executar, verifique se você está na pasta do seu repositório local. Se você acabou de concluir o `git clone` comando, deverá alterar o diretório para o repositório executando um comando semelhante ao seguinte.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Para enviar por push as alterações do repositório local para o repositório do servidor
Para enviar por push as alterações do repositório local para o repositório do servidor, você deve confirmar suas alterações e enviá-las por push para o repositório do servidor. Para confirmar suas alterações, abra a ferramenta de comando do git, alterne para o diretório do repositório local e emita os comandos a seguir.

```
git add --all
git commit -m "Description of your changes"
```

Para enviar por push todas as confirmações para o servidor, execute o comando a seguir.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Para implantar qualquer alteração de configuração de serviço na instância do serviço de gerenciamento de API

Depois que as alterações locais são confirmadas e enviadas por push para o repositório do servidor, você pode implantá-las na instância do serviço de gerenciamento de API.

Para obter informações sobre como executar essa operação usando a API REST, consulte [implantar alterações do git no banco de dados de configuração usando a API REST](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Referência da estrutura de arquivos e pastas do repositório git local

Os arquivos e pastas no repositório git local contêm as informações de configuração sobre a instância do serviço.

| Item | Descrição |
| --- | --- |
| pasta de gerenciamento de API raiz |Contém a configuração de nível superior para a instância de serviço |
| pasta de APIs |Contém a configuração para as APIs na instância do serviço |
| pasta de grupos |Contém a configuração dos grupos na instância do serviço |
| pasta de políticas |Contém as políticas na instância do serviço |
| pasta portalStyles |Contém a configuração para as personalizações do portal do desenvolvedor na instância do serviço |
| pasta produtos |Contém a configuração dos produtos na instância do serviço |
| pasta de modelos |Contém a configuração dos modelos de email na instância do serviço |

Cada pasta pode conter um ou mais arquivos e, em alguns casos, uma ou mais pastas, por exemplo, uma pasta para cada API, produto ou grupo. Os arquivos dentro de cada pasta são específicos para o tipo de entidade descrito pelo nome da pasta.

| Tipo de ficheiro | Objetivo |
| --- | --- |
| json |Informações de configuração sobre a respectiva entidade |
| html |Descrições sobre a entidade, geralmente exibidas no portal do desenvolvedor |
| xml |Instruções de política |
| CSS |Folhas de estilo para personalização do portal do desenvolvedor |

Esses arquivos podem ser criados, excluídos, editados e gerenciados no sistema de arquivos local, e as alterações são implantadas de volta para sua instância do serviço de gerenciamento de API.

> [!NOTE]
> As entidades a seguir não estão contidas no repositório git e não podem ser configuradas usando o git.
>
> * [Utilizadores](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user)
> * [Subscrições](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription)
> * [Valores nomeados](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/property)
> * Entidades do portal do desenvolvedor que não sejam estilos
>

### <a name="root-api-management-folder"></a>Pasta de gerenciamento de API raiz
A pasta `api-management` raiz contém um `configuration.json` arquivo que contém informações de nível superior sobre a instância de serviço no formato a seguir.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

As quatro primeiras configurações (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled` `UserRegistrationTermsConsentRequired`e) são mapeadas para as configurações a seguir na guia **identidades** da seção **segurança** .

| Configuração de identidade | Mapeia para |
| --- | --- |
| RegistrationEnabled |Presença de **nome de usuário e** provedor de identidade de senha |
| UserRegistrationTerms |Caixa **de texto termos de uso na inscrição do usuário** |
| UserRegistrationTermsEnabled |Caixa **de seleção Mostrar termos de uso na página de inscrição** |
| UserRegistrationTermsConsentRequired |Caixa de seleção **exigir consentimento** |
| RequireUserSigninEnabled |Caixa **de seleção redirecionar usuários anônimos para a página de entrada** |

As próximas quatro configurações (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`e `DelegationValidationKey`) são mapeadas para as configurações a seguir na guia **delegação** da seção **segurança** .

| Configuração de delegação | Mapeia para |
| --- | --- |
| DelegationEnabled |Caixa **de seleção delegar entrada & inscrição** |
| DelegationUrl |TextBox de **URL de ponto de extremidade de delegação** |
| DelegatedSubscriptionEnabled |**Delegar** caixa de seleção de assinatura do produto |
| DelegationValidationKey |Caixa de texto **chave de validação de representante** |

A configuração final, `$ref-policy`, é mapeada para o arquivo de instruções de política global para a instância de serviço.

### <a name="apis-folder"></a>pasta de APIs
A `apis` pasta contém uma pasta para cada API na instância do serviço, que contém os itens a seguir.

* `apis\<api name>\configuration.json`-Essa é a configuração para a API e contém informações sobre a URL do serviço de back-end e as operações. Essas são as mesmas informações que seriam retornadas se você chamar [obter uma API específica](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apis/get) com `export=true` no `application/json` formato.
* `apis\<api name>\api.description.html`-Esta é a descrição da API e corresponde à `description` propriedade da entidade da [API](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._entity_property).
* `apis\<api name>\operations\`-Esta pasta contém `<operation name>.description.html` arquivos que são mapeados para as operações na API. Cada arquivo contém a descrição de uma única operação na API, que é mapeada para a `description` propriedade da [entidade de operação](https://docs.microsoft.com/rest/api/visualstudio/operations/list#operationproperties) na API REST.

### <a name="groups-folder"></a>pasta de grupos
A `groups` pasta contém uma pasta para cada grupo definido na instância do serviço.

* `groups\<group name>\configuration.json`-Esta é a configuração para o grupo. Essas são as mesmas informações que seriam retornadas se você chamasse a operação [obter um grupo específico](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/group/get) .
* `groups\<group name>\description.html`-Esta é a descrição do grupo e corresponde à `description` propriedade da entidade de [grupo](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity).

### <a name="policies-folder"></a>pasta de políticas
A `policies` pasta contém as instruções de política para sua instância de serviço.

* `policies\global.xml`-contém políticas definidas em escopo global para sua instância de serviço.
* `policies\apis\<api name>\`-Se você tiver alguma política definida no escopo da API, elas estarão contidas nessa pasta.
* `policies\apis\<api name>\<operation name>\`pasta-se você tiver políticas definidas no escopo de operação, elas estarão contidas nessa pasta em `<operation name>.xml` arquivos que são mapeados para as instruções de política para cada operação.
* `policies\products\`-Se você tiver políticas definidas no escopo do produto, elas estarão contidas nessa pasta, que contém `<product name>.xml` os arquivos que são mapeados para as instruções de política de cada produto.

### <a name="portalstyles-folder"></a>pasta portalStyles
A `portalStyles` pasta contém as folhas de estilo e de configuração para as personalizações do portal do desenvolvedor para a instância de serviço.

* `portalStyles\configuration.json`-contém os nomes das folhas de estilo usadas pelo portal do desenvolvedor
* `portalStyles\<style name>.css`-cada `<style name>.css` arquivo contém estilos para o portal do desenvolvedor`Preview.css` ( `Production.css` e por padrão).

### <a name="products-folder"></a>pasta produtos
A `products` pasta contém uma pasta para cada produto definido na instância do serviço.

* `products\<product name>\configuration.json`-Esta é a configuração para o produto. Essas são as mesmas informações que seriam retornadas se você chamasse a operação [obter um produto específico](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/product/get) .
* `products\<product name>\product.description.html`-Esta é a descrição do produto e corresponde à `description` propriedade da [entidade Product](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) na API REST.

### <a name="templates"></a>modelos
A `templates` pasta contém a configuração dos [modelos de email](api-management-howto-configure-notifications.md) da instância do serviço.

* `<template name>\configuration.json`-Esta é a configuração para o modelo de email.
* `<template name>\body.html`-Este é o corpo do modelo de email.

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre outras maneiras de gerenciar sua instância de serviço, consulte:

* Gerenciar sua instância de serviço usando os seguintes cmdlets do PowerShell
  * [Referência do cmdlet do Powershell de implementação do serviço](https://docs.microsoft.com/powershell/module/wds)
  * [Referência de cmdlet do PowerShell de gerenciamento de serviços](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)
* Gerenciar sua instância de serviço usando a API REST
  * [Referência da API REST do gerenciamento de API](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




