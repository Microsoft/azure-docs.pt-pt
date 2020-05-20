---
title: Configure o seu serviço de Gestão API utilizando git - Azure / Microsoft Docs
description: Aprenda a guardar e configurar a configuração do serviço de Gestão API utilizando git.
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
ms.openlocfilehash: c1a9f3e76622523dde03cc2a639cce33227dff5f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649213"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Como guardar e configurar a sua configuração do serviço de Gestão de API com Git

Cada instância de serviço de Gestão API mantém uma base de dados de configuração que contém informações sobre a configuração e metadados para a instância de serviço. Podem ser feitas alterações na instância de serviço alterando uma definição no portal Azure, utilizando um cmdlet PowerShell ou fazendo uma chamada REST API. Além destes métodos, também pode gerir a configuração da sua instância de serviço utilizando git, permitindo cenários de gestão de serviços como:

* Versão de configuração - descarregue e guarde diferentes versões da sua configuração de serviço
* Alterações na configuração a granel - faça alterações em várias partes da configuração do seu serviço no seu repositório local e integre as alterações de volta ao servidor com uma única operação
* Cadeia familiar de ferramentas Git e fluxo de trabalho - use a ferramenta Git e fluxos de trabalho que já conhece

O diagrama seguinte mostra uma visão geral das diferentes formas de configurar a sua instância de serviço de Gestão API.

![Configuração Git][api-management-git-configure]

Quando efetua alterações no seu serviço utilizando o portal Azure, os cmdlets PowerShell ou a API REST, está a gerir a base de dados de configuração do seu serviço utilizando o `https://{name}.management.azure-api.net` ponto final, como mostra o lado direito do diagrama. O lado esquerdo do diagrama ilustra como pode gerir a configuração do seu serviço utilizando o repositório Git e Git para o seu serviço localizado em `https://{name}.scm.azure-api.net` .

Os seguintes passos fornecem uma visão geral da gestão da sua instância de serviço de Gestão API utilizando git.

1. Aceda à configuração Git no seu serviço
2. Guarde a sua base de dados de configuração de serviço para o seu repositório Git
3. Clone o repo Git para a sua máquina local
4. Puxe o último repo para a sua máquina local, e comprometa e empurre as mudanças de volta para o seu repo
5. Implemente as alterações do seu repo na sua base de dados de configuração de serviço

Este artigo descreve como ativar e usar git para gerir a configuração do seu serviço e fornece uma referência para os ficheiros e pastas no repositório Git.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Aceda à configuração Git no seu serviço

Para visualizar e configurar as definições de configuração git, pode clicar no menu **'Segurança'** e navegar para o **separador de repositório de configuração.**

![Ativar GIT][api-management-enable-git]

> [!IMPORTANT]
> Quaisquer segredos que não sejam definidos como Valores Nomeados serão armazenados no repositório e permanecerão na sua história até que desative e reative o acesso ao Git. Os Valores Nomeados fornecem um lugar seguro para gerir valores de cordas constantes, incluindo segredos, em todas as configurações e políticas da API, para que não tenha que armazená-los diretamente nas suas declarações políticas. Para mais informações, consulte [Como utilizar valores nomeados nas políticas de Gestão de API azure.](api-management-howto-properties.md)
>
>

Para obter informações sobre o acesso de Git a permitir ou desativar o acesso à Git utilizando a API REST, consulte [o acesso ativa ou desativado git utilizando a API REST](/rest/api/apimanagement/2019-12-01/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Para salvar a configuração do serviço para o repositório Git

O primeiro passo antes de clonar o repositório é salvar o estado atual da configuração do serviço para o repositório. Clique em **Guardar para repositório**.

Faça as alterações desejadas no ecrã de confirmação e clique em **Ok** para guardar.

Após alguns momentos, a configuração é guardada, e o estado de configuração do repositório é apresentado, incluindo a data e hora da última alteração de configuração e a última sincronização entre a configuração do serviço e o repositório.

Uma vez que a configuração é guardada no repositório, pode ser clonada.

Para obter informações sobre a realização desta operação utilizando a API REST, consulte API de [configuração Comprometa-se utilizando a API REST](/rest/api/apimanagement/2019-12-01/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Para clonar o repositório para a sua máquina local

Para clonar um repositório, precisa do URL para o seu repositório, um nome de utilizador e uma palavra-passe. Para obter o nome do utilizador e outras credenciais, clique nas **credenciais de Acesso** perto do topo da página.

Para gerar uma palavra-passe, certifique-se primeiro de que a **Expiração** está definida para a data e hora de validade desejada e, em seguida, clique em **Generate**.

> [!IMPORTANT]
> Tome nota desta senha. Uma vez que saia desta página, a palavra-passe não será exibida novamente.
>

Os exemplos seguintes utilizam a ferramenta Git Bash de [Git para Windows,](https://www.git-scm.com/downloads) mas pode utilizar qualquer ferramenta Git que esteja familiarizado.

Abra a sua ferramenta Git na pasta desejada e execute o seguinte comando para clonar o repositório git à sua máquina local, utilizando o comando fornecido pelo portal Azure.

```
git clone https://{name}.scm.azure-api.net/
```

Forneça o nome de utilizador e a palavra-passe quando solicitado.

Se receber algum erro, tente modificar o seu `git clone` comando para incluir o nome de utilizador e a palavra-passe, como mostra o seguinte exemplo.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Se isto for um erro, tente URL codificando a parte da palavra-passe do comando. Uma maneira rápida de fazer isso é abrir o Estúdio Visual, e emitir o seguinte comando na **Janela Imediata**. Para abrir a **Janela Imediata,** abra qualquer solução ou projeto no Estúdio Visual (ou crie uma nova aplicação de consola vazia) e escolha **o Windows**, **Imediatamente** a partir do menu **Debug.**

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

Utilize a palavra-passe codificada juntamente com o seu nome de utilizador e localização de repositório para construir o comando git.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

Uma vez clonado o repositório, pode ver e trabalhar com ele no seu sistema de ficheiros local. Para mais informações, consulte o arquivo e a [referência da estrutura da pasta do repositório git local](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Para atualizar o seu repositório local com a configuração mais atual da instância de serviço

Se fizer alterações na sua instância de serviço de Gestão API no portal Azure ou utilizar a API REST, deve guardar estas alterações para o repositório antes de atualizar o seu repositório local com as mais recentes alterações. Para isso, clique em **Guardar a configuração para repositório** no **separador de repositório** de configuração no portal Azure e, em seguida, emita o seguinte comando no seu repositório local.

```
git pull
```

Antes de `git pull` correr, certifique-se de que está na pasta para o seu repositório local. Se acabou de completar o `git clone` comando, então tem de mudar o diretório para o seu repo, executando um comando como o seguinte.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Para empurrar as alterações do seu repo local para o repo do servidor
Para empurrar as alterações do seu repositório local para o repositório do servidor, tem de comprometer as suas alterações e, em seguida, empurrá-las para o repositório do servidor. Para comprometer as suas alterações, abra a sua ferramenta de comando Git, mude para o diretório do seu repositório local e emita os seguintes comandos.

```
git add --all
git commit -m "Description of your changes"
```

Para empurrar todos os compromissos para o servidor, execute o seguinte comando.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Para implementar quaisquer alterações de configuração de serviço na instância de serviço de Gestão API

Uma vez que as alterações locais sejam comprometidas e empurradas para o repositório do servidor, pode implementá-las na sua instância de serviço de Gestão API.

Para obter informações sobre a realização desta operação utilizando a API REST, consulte [as alterações de Utilização de Git na base](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/tenantconfiguration)de dados de configuração utilizando a API REST .

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Referência de estrutura de arquivo e pasta do repositório git local

Os ficheiros e pastas do repositório git local contêm as informações de configuração sobre a instância de serviço.

| Item | Descrição |
| --- | --- |
| pasta de gestão de api raiz |Contém configuração de alto nível para a instância de serviço |
| pasta apis |Contém a configuração para os apis na instância de serviço |
| pasta grupos |Contém a configuração para os grupos na instância de serviço |
| pasta políticas |Contém as políticas na instância de serviço |
| pasta portalStyles |Contém a configuração para as personalizações do portal do desenvolvedor na instância de serviço |
| pasta de produtos |Contém a configuração para os produtos na instância de serviço |
| pasta de modelos |Contém a configuração para os modelos de e-mail na instância de serviço |

Cada pasta pode conter um ou mais ficheiros e, em alguns casos, uma ou mais pastas, por exemplo, uma pasta para cada API, produto ou grupo. Os ficheiros dentro de cada pasta são específicos para o tipo de entidade descrito pelo nome da pasta.

| Tipo de ficheiro | Objetivo |
| --- | --- |
| json |Informação de configuração sobre a respetiva entidade |
| html |Descrições sobre a entidade, muitas vezes exibidas no portal do desenvolvedor |
| xml |Instruções de política |
| css |Folhas de estilo para personalização do portal do desenvolvedor |

Estes ficheiros podem ser criados, eliminados, editados e geridos no seu sistema de ficheiros local, e as alterações implementadas de volta para a sua instância de serviço de Gestão API.

> [!NOTE]
> As seguintes entidades não estão contidas no repositório Git e não podem ser configuradas utilizando git.
>
> * [Utilizadores](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/user)
> * [Subscrições](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription)
> * Valores Nomeados
> * Entidades do portal de desenvolvimento que não os estilos
>

### <a name="root-api-management-folder"></a>Pasta de gestão de api-raiz
A `api-management` pasta-raiz contém um `configuration.json` ficheiro que contém informações de alto nível sobre a instância de serviço no seguinte formato.

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

As quatro primeiras definições `RegistrationEnabled` `UserRegistrationTerms` (, `UserRegistrationTermsEnabled` , e ) `UserRegistrationTermsConsentRequired` mapeiam as seguintes definições no separador **Identidades** na secção **de Segurança.**

| Definição de identidade | Mapas para |
| --- | --- |
| RegistrationEnabled |Presença de **Username e** fornecedor de identidade de senha |
| UserRegistrationTerms |**Termos de utilização na** caixa de texto de inscrição do utilizador |
| Registo do utilizadorTermos Habilitados |**Mostrar os termos de utilização na caixa de** verificação da página de inscrição |
| Registo do UtilizadorTermosConsentimentoExigido |**Exigir** caixa de verificação de consentimento |
| RequerUserSigninEnabled |**Redirecione utilizadores anónimos para iniciar sessão na caixa de** verificação de página |

As próximas quatro definições `DelegationEnabled` `DelegationUrl` (, `DelegatedSubscriptionEnabled` , e ) `DelegationValidationKey` mapeiam as seguintes definições no separador **delegação** na secção **de Segurança.**

| Definição de delegação | Mapas para |
| --- | --- |
| Delegação Habilitada |**Delegar caixa de verificação de inscrição &** |
| DelegaçãoUrl |Caixa de texto URL de **ponto final de delegação** |
| Subscrição Delegada |Caixa de **verificação de subscrição de produto delegado** |
| Chave de Validação de Delegações |Caixa de texto **chave de validação de delegados** |

A definição `$ref-policy` final, os mapas para as declarações de política global arquivam a instância de serviço.

### <a name="apis-folder"></a>pasta apis
A `apis` pasta contém uma pasta para cada API na instância de serviço, que contém os seguintes itens.

* `apis\<api name>\configuration.json`- esta é a configuração da API e contém informações sobre o URL do serviço de backend e as operações. Esta é a mesma informação que seria devolvida se chamasse [saquear uma API específica](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/apis/get) `export=true` com em `application/json` formato.
* `apis\<api name>\api.description.html`- esta é a descrição da API e corresponde ao `description` imóvel da [entidade API.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.entityproperty)
* `apis\<api name>\operations\`- esta pasta contém `<operation name>.description.html` ficheiros que mapeiam as operações na API. Cada ficheiro contém a descrição de uma única operação na API, que `description` mapeia a propriedade da [entidade operacional](https://docs.microsoft.com/rest/api/visualstudio/operations/list#operationproperties) na API REST.

### <a name="groups-folder"></a>pasta grupos
A `groups` pasta contém uma pasta para cada grupo definida na instância de serviço.

* `groups\<group name>\configuration.json`- esta é a configuração para o grupo. Esta é a mesma informação que seria devolvida se chamasse o Get a uma operação específica do [grupo.](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/group/get)
* `groups\<group name>\description.html`- esta é a descrição do grupo e corresponde à `description` propriedade da entidade do [grupo.](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity)

### <a name="policies-folder"></a>pasta políticas
A `policies` pasta contém as declarações políticas para a sua instância de serviço.

* `policies\global.xml`- contém políticas definidas no âmbito global para a sua instância de serviço.
* `policies\apis\<api name>\`- se tiver alguma política definida no âmbito da API, estão contidas nesta pasta.
* `policies\apis\<api name>\<operation name>\`pasta - se tiver alguma política definida no âmbito de funcionamento, elas estão contidas nesta pasta em `<operation name>.xml` ficheiros que mapeiam as declarações políticas de cada operação.
* `policies\products\`- se tiver alguma política definida no âmbito do produto, estão contidas nesta pasta, que contém `<product name>.xml` ficheiros que mapeiam as declarações políticas de cada produto.

### <a name="portalstyles-folder"></a>pasta portalStyles
A `portalStyles` pasta contém configuração e folhas de estilo para personalizações do portal do desenvolvedor para a instância de serviço.

* `portalStyles\configuration.json`- contém os nomes das folhas de estilo utilizadas pelo portal de desenvolvimento
* `portalStyles\<style name>.css`- cada `<style name>.css` ficheiro contém estilos para o portal do desenvolvedor `Preview.css` (e por `Production.css` padrão).

### <a name="products-folder"></a>pasta de produtos
A `products` pasta contém uma pasta para cada produto definida na instância de serviço.

* `products\<product name>\configuration.json`- esta é a configuração do produto. Esta é a mesma informação que seria devolvida se chamasse o Get a uma operação específica do [produto.](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/product/get)
* `products\<product name>\product.description.html`- esta é a descrição do produto e corresponde à `description` propriedade da entidade do [produto](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) na API REST.

### <a name="templates"></a>modelos
A `templates` pasta contém configuração para os modelos de [e-mail](api-management-howto-configure-notifications.md) da instância de serviço.

* `<template name>\configuration.json`- esta é a configuração para o modelo de e-mail.
* `<template name>\body.html`- este é o corpo do modelo de e-mail.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outras formas de gerir a sua instância de serviço, consulte:

* Gerencie a sua instância de serviço utilizando os seguintes cmdlets PowerShell
  * [Referência do cmdlet do Powershell de implementação do serviço](https://docs.microsoft.com/powershell/module/wds)
  * [Referência do cmdlet do Powershell de gestão do serviço](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)
* Gerencie a sua instância de serviço utilizando a API REST
  * [Referência aPI Management REST API](/rest/api/apimanagement/)


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




