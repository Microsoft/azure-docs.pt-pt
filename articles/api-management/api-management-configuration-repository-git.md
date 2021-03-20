---
title: Configurar o seu serviço de Gestão API utilizando o Git - Azure | Microsoft Docs
description: Saiba como guardar e configurar a configuração do serviço de Gestão API utilizando o Git.
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
ms.openlocfilehash: 18cc42c3447de733447c27db52a9a6d664539464
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89400365"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Como guardar e configurar a sua configuração do serviço de Gestão de API com Git

Cada instância de serviço de Gestão API mantém uma base de dados de configuração que contém informações sobre a configuração e metadados para a instância de serviço. Podem ser efetuadas alterações na instância de serviço alterando uma definição no portal Azure, utilizando um cmdlet PowerShell ou fazendo uma chamada de API REST. Além destes métodos, também pode gerir a configuração da sua instância de serviço utilizando o Git, permitindo cenários de gestão de serviços como:

* Versão de configuração - descarregue e guarde diferentes versões da configuração do seu serviço
* Alterações na configuração a granel - faça alterações em várias partes da sua configuração de serviço no seu repositório local e integre as alterações de volta ao servidor com uma única operação
* Familiar Git toolchain e workflow - use a ferramenta Git e fluxos de trabalho que você já está familiarizado com

O diagrama seguinte mostra uma visão geral das diferentes formas de configurar a sua instância de serviço de Gestão API.

![Configuração de Git][api-management-git-configure]

Quando escoda alterações ao seu serviço utilizando o portal Azure, os cmdlets PowerShell ou a API REST, está a gerir a sua base de dados de configuração de serviço utilizando o `https://{name}.management.azure-api.net` ponto final, como mostrado no lado direito do diagrama. O lado esquerdo do diagrama ilustra como pode gerir a sua configuração de serviço utilizando o repositório Git e Git para o seu serviço localizado em `https://{name}.scm.azure-api.net` .

Os seguintes passos fornecem uma visão geral da gestão da sua instância de serviço de Gestão API usando o Git.

1. Aceda à configuração do Git no seu serviço
2. Guarde a sua base de dados de configuração de serviço para o seu repositório Git
3. Clone o git repo para a sua máquina local
4. Puxe a última repo para baixo para a sua máquina local, e comprometa e empurre as mudanças de volta para o seu repo
5. Implemente as alterações do seu repo na base de dados de configuração de serviço

Este artigo descreve como ativar e utilizar o Git para gerir a sua configuração de serviço e fornece uma referência para os ficheiros e pastas no repositório git.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Aceda à configuração do Git no seu serviço

Para visualizar e configurar as definições de configuração do Git, pode clicar no menu **de Implementação e infraestrutura** e navegar no **separador Repositório.**

![Ativar GIT][api-management-enable-git]

> [!IMPORTANT]
> Quaisquer segredos que não sejam definidos como Valores Nomeados serão armazenados no repositório e permanecerão na sua história até que você desative e reativa o acesso ao Git. Os Valores Nomeados fornecem um local seguro para gerir valores de cordas constantes, incluindo segredos, em todas as configurações e políticas da API, para que não tenha de os armazenar diretamente nas suas declarações políticas. Para obter mais informações, consulte [Como utilizar Valores Nomeados nas políticas de Gestão API da Azure.](api-management-howto-properties.md)
>
>

Para obter informações sobre como ativar ou desativar o acesso do Git utilizando a API REST, consulte [ativar ou desativar o acesso do Git utilizando a API REST](/rest/api/apimanagement/2019-12-01/tenantaccess?EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Para guardar a configuração de serviço para o repositório git

O primeiro passo antes da clonagem do repositório é salvar o estado atual da configuração do serviço para o repositório. Clique **em Guardar para repositório**.

Faça quaisquer alterações desejadas no ecrã de confirmação e clique em **Guardar** para guardar.

Após alguns momentos, a configuração é guardada e o estado de configuração do repositório é apresentado, incluindo a data e hora da última alteração de configuração e a última sincronização entre a configuração de serviço e o repositório.

Uma vez guardada a configuração no repositório, pode ser clonada.

Para obter informações sobre a realização desta operação utilizando a API REST, consulte [a imagem de configuração do REST utilizando a API REST](/rest/api/apimanagement/2019-12-01/tenantaccess?CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Para clonar o repositório para a sua máquina local

Para clonar um repositório, precisa do URL para o seu repositório, um nome de utilizador e uma senha. Para obter o nome do utilizador e outras credenciais, clique nas **credenciais de Acesso** perto do topo da página.

Para gerar uma palavra-passe, certifique-se primeiro de que a **Expiração** está definida para a data e hora de validade desejadas e, em seguida, clique em **Gerar**.

> [!IMPORTANT]
> Tome nota desta senha. Assim que sair desta página, a palavra-passe não será novamente exibida.
>

Os exemplos a seguir utilizam a ferramenta Git Bash do [Git para Windows,](https://www.git-scm.com/downloads) mas pode utilizar qualquer ferramenta Git que conheça.

Abra a sua ferramenta Git na pasta desejada e executar o seguinte comando para clonar o repositório Git para a sua máquina local, utilizando o comando fornecido pelo portal Azure.

```
git clone https://{name}.scm.azure-api.net/
```

Forneça o nome de utilizador e a palavra-passe quando solicitado.

Se receber algum erro, tente modificar o seu `git clone` comando para incluir o nome de utilizador e a palavra-passe, como mostra o exemplo a seguir.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Se isto fornecer um erro, tente URL codificando a parte da palavra-passe do comando. Uma maneira rápida de o fazer é abrir o Visual Studio e emitir o seguinte comando na **Janela Imediata.** Para abrir a **Janela Imediata,** abra qualquer solução ou projeto no Estúdio Visual (ou crie uma nova aplicação de consola vazia) e escolha **o Windows**, **Imediato** do menu **Debug.**

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

Utilize a palavra-passe codificada juntamente com o seu nome de utilizador e localização do repositório para construir o comando git.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

Uma vez clonado o repositório, pode ver e trabalhar com ele no seu sistema de ficheiros local. Para obter mais informações, consulte [a referência de estrutura de arquivo e pasta do repositório local de Git](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Para atualizar o seu repositório local com a configuração de instância de serviço mais atual

Se escamar alterações na sua instância de serviço de Gestão API no portal Azure ou utilizar a API REST, tem de guardar estas alterações no repositório antes de poder atualizar o seu repositório local com as alterações mais recentes. Para isso, clique em Guardar para o **repositório** no **separador Repositório** no portal Azure e, em seguida, emite o seguinte comando no seu repositório local.

```
git pull
```

Antes de `git pull` correr, certifique-se de que está na pasta para o seu repositório local. Se acabou de completar o `git clone` comando, então deve mudar o diretório para o seu repo, executando um comando como o seguinte.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Para empurrar as mudanças do seu repo local para o repo servidor
Para empurrar as alterações do seu repositório local para o repositório do servidor, tem de cometer as alterações e, em seguida, pressioná-las para o repositório do servidor. Para cometer as suas alterações, abra a sua ferramenta de comando Git, mude para o diretório do seu repositório local e emita os seguintes comandos.

```
git add --all
git commit -m "Description of your changes"
```

Para empurrar todos os compromissos para o servidor, executar o seguinte comando.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Para implementar quaisquer alterações de configuração de serviço na instância do serviço de Gestão API

Uma vez que as alterações locais são comprometidas e empurradas para o repositório do servidor, pode implantá-las na sua instância de serviço de Gestão API.

Para obter informações sobre a realização desta operação utilizando a API REST, consulte [alterações do Git de implementação na base de dados de configuração utilizando a API REST](/rest/api/apimanagement/2019-12-01/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Referência da estrutura de arquivo e pasta do repositório local de Git

Os ficheiros e pastas do repositório local do Git contêm as informações de configuração sobre a instância de serviço.

| Item | Descrição |
| --- | --- |
| pasta de gestão de api raiz |Contém uma configuração de nível superior para a instância de serviço |
| pasta apis |Contém a configuração para a apis na instância de serviço |
| pasta grupos |Contém a configuração para os grupos na instância de serviço |
| pasta de políticas |Contém as políticas na instância de serviço |
| portalStyles pasta |Contém a configuração para as personalizações do portal do desenvolvedor na instância de serviço |
| pasta de produtos |Contém a configuração dos produtos na instância de serviço |
| pasta de modelos |Contém a configuração para os modelos de e-mail na instância de serviço |

Cada pasta pode conter um ou mais ficheiros e, em alguns casos, uma ou mais pastas, por exemplo, uma pasta para cada API, produto ou grupo. Os ficheiros dentro de cada pasta são específicos para o tipo de entidade descrito pelo nome da pasta.

| Tipo de arquivo | Objetivo |
| --- | --- |
| json |Informação de configuração sobre a respetiva entidade |
| html |Descrições sobre a entidade, muitas vezes exibidas no portal do desenvolvedor |
| xml |Instruções de política |
| css |Folhas de estilo para personalização do portal do desenvolvedor |

Estes ficheiros podem ser criados, eliminados, editados e geridos no seu sistema de ficheiros local, e as alterações implementadas de volta à sua instância de serviço de Gestão API.

> [!NOTE]
> As seguintes entidades não estão contidas no repositório de Git e não podem ser configuradas usando Git.
>
> * [Utilizadores](/rest/api/apimanagement/2019-12-01/user)
> * [Subscrições](/rest/api/apimanagement/2019-12-01/subscription)
> * Valores Nomeados
> * Entidades do portal do desenvolvedor que não estilos
>

### <a name="root-api-management-folder"></a>Pasta de gestão de api raiz
A pasta raiz `api-management` contém um ficheiro que contém `configuration.json` informações de alto nível sobre a instância de serviço no seguinte formato.

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

As primeiras quatro definições `RegistrationEnabled` (, `UserRegistrationTerms` , , e `UserRegistrationTermsEnabled` ) `UserRegistrationTermsConsentRequired` mapeam para as seguintes definições no **separador Identidades** na secção **portal do Desenvolvedor.**

| Definição de identidade | Mapas para |
| --- | --- |
| RegistrationEnabled |Presença de Nome de Utilizador e fornecedor de identidade **de senha** |
| UserRegistrationTerms |**Termos de utilização na** caixa de texto de inscrição do utilizador |
| UserRegistrationTermsEnabled |**Mostrar termos de utilização na caixa de verificação da página de inscrição** |
| UserRegistrationTermsConsentRequired |**Requerer caixa de** verificação de consentimento |
| RequeruserSigninEnabled |**Redirecione os utilizadores anónimos para iniciar sing-in na caixa de** verificação da página |

As próximas quatro configurações `DelegationEnabled` (, `DelegationUrl` , e ) `DelegatedSubscriptionEnabled` `DelegationValidationKey` mapeiam para as seguintes definições no **separador Delegação** na secção **portal do Desenvolvedor.**

| Definição de delegação | Mapas para |
| --- | --- |
| DelegaçãoEnabled |**Delegado dedesiná-in &** caixa de verificação de inscrição |
| DelegaçãoUr |Caixa de texto **URL de ponto final da delegação** |
| DelegadoSubscriçãoEnabled |**Caixa de verificação de assinatura de produto delegado** |
| DelegaçãoValidationKey |Caixa de texto **chave de validação de delegados** |

A definição final, `$ref-policy` , mapas para o arquivo de declarações de política global para a instância de serviço.

### <a name="apis-folder"></a>pasta apis
A `apis` pasta contém uma pasta para cada API na placa de serviço, que contém os seguintes itens.

* `apis\<api name>\configuration.json` - esta é a configuração para a API e contém informações sobre o URL de serviço de backend e as operações. Esta é a mesma informação que seria devolvida se ligasse para [obter uma API específica](/rest/api/apimanagement/2019-12-01/apis/get) em `export=true` `application/json` formato.
* `apis\<api name>\api.description.html`- esta é a descrição da API e corresponde à `description` propriedade da [entidade API.](/java/api/com.microsoft.azure.storage.table.entityproperty)
* `apis\<api name>\operations\` - esta pasta contém `<operation name>.description.html` ficheiros que mapeiam as operações na API. Cada ficheiro contém a descrição de uma única operação na API, que mapeia para a `description` propriedade da entidade de [operação](/rest/api/visualstudio/operations/list#operationproperties) na API REST.

### <a name="groups-folder"></a>pasta grupos
A `groups` pasta contém uma pasta para cada grupo definido na instância de serviço.

* `groups\<group name>\configuration.json` - esta é a configuração para o grupo. Esta é a mesma informação que seria devolvida se ligasse para a Get a uma operação [específica de grupo.](/rest/api/apimanagement/2019-12-01/group/get)
* `groups\<group name>\description.html`- esta é a descrição do grupo e corresponde à `description` propriedade da entidade do [grupo.](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity)

### <a name="policies-folder"></a>pasta de políticas
A `policies` pasta contém as declarações de política para a sua instância de serviço.

* `policies\global.xml` - contém políticas definidas no âmbito global para a sua instância de serviço.
* `policies\apis\<api name>\` - se tiver alguma política definida no âmbito da API, estas estão contidas nesta pasta.
* `policies\apis\<api name>\<operation name>\` pasta - se tiver alguma política definida no âmbito de funcionamento, estas estão contidas nesta pasta em `<operation name>.xml` ficheiros que mapeiam as declarações de política para cada operação.
* `policies\products\` - se tiver alguma política definida no âmbito do produto, estas estão contidas nesta pasta, que contém `<product name>.xml` ficheiros que mapeiam as declarações políticas de cada produto.

### <a name="portalstyles-folder"></a>portalStyles pasta
A `portalStyles` pasta contém folhas de configuração e estilo para personalização do portal do desenvolvedor para a instância de serviço.

* `portalStyles\configuration.json` - contém os nomes das folhas de estilo utilizadas pelo portal do desenvolvedor
* `portalStyles\<style name>.css` - cada `<style name>.css` ficheiro contém estilos para o portal do desenvolvedor `Preview.css` (e por `Production.css` defeito).

### <a name="products-folder"></a>pasta de produtos
A `products` pasta contém uma pasta para cada produto definido na instância de serviço.

* `products\<product name>\configuration.json` - esta é a configuração do produto. Esta é a mesma informação que seria devolvida se ligasse para a Get a uma operação [específica do produto.](/rest/api/apimanagement/2019-12-01/product/get)
* `products\<product name>\product.description.html` - esta é a descrição do produto e corresponde à `description` propriedade da entidade do [produto](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) na API REST.

### <a name="templates"></a>modelos
A `templates` pasta contém configuração para os modelos de [e-mail](api-management-howto-configure-notifications.md) da instância de serviço.

* `<template name>\configuration.json` - esta é a configuração para o modelo de e-mail.
* `<template name>\body.html` - este é o corpo do modelo de e-mail.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outras formas de gerir a sua instância de serviço, consulte:

* Gerencie a sua instância de serviço utilizando os seguintes cmdlets PowerShell
  * [Referência do cmdlet do Powershell de implementação do serviço](/powershell/module/wds)
  * [Referência do cmdlet do Powershell de gestão do serviço](/powershell/azure/servicemanagement/overview)
* Gerencie a sua instância de serviço utilizando a API REST
  * [Referência API Management REST API](/rest/api/apimanagement/)


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
