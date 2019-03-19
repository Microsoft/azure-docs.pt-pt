---
title: Configurar o seu serviço de gestão de API com o Git - Azure | Documentos da Microsoft
description: Saiba como guardar e configurar a sua configuração do serviço de gestão de API com o Git.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: 36b60b3784739a884b887a29f3dd53c61c44cd6f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57851351"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Para guardar e configurar a sua configuração do serviço de gestão de API com o Git

Cada instância de serviço de gestão de API mantém um banco de dados de configuração que contém informações sobre a configuração e os metadados para a instância do serviço. Podem ser feitas alterações para a instância do serviço ao alterar uma definição no portal do Azure, utilizando um cmdlet do PowerShell ou fazer uma chamada de REST API. Para além destes métodos, também pode gerir sua configuração de instância de serviço com o Git, permitindo cenários de gestão de serviço, tais como:

* Controlo de versões de configuração - transferir e armazenar versões diferentes da sua configuração de serviço
* Em massa as alterações de configuração - fazer alterações em várias partes da sua configuração de serviço no seu repositório local e integrar as alterações de volta para o servidor com uma única operação
* Coleção de ferramentas familiar de Git e o fluxo de trabalho - utilize o Git ferramentas e os fluxos de trabalho que já estiver familiarizado com

O diagrama seguinte mostra uma descrição geral das diferentes maneiras de configurar a sua instância do serviço de gestão de API.

![Configurar o Git][api-management-git-configure]

Quando fizer alterações ao seu serviço com o portal do Azure, cmdlets do PowerShell ou a API REST, que está a gerir a configuração de serviço base de dados com o `https://{name}.management.azure-api.net` ponto de extremidade, conforme mostrado no lado direito do diagrama. O lado esquerdo do diagrama ilustra como pode gerir a configuração de serviço com o Git e o repositório de Git para o seu serviço localizado em `https://{name}.scm.azure-api.net`.

Os passos seguintes fornecem uma visão geral do gerenciamento de sua instância do serviço de gestão de API com o Git.

1. Configuração do Git no seu serviço de acesso
2. Guardar a sua base de dados de configuração de serviço para o seu repositório de Git
3. Clone o repositório de Git no seu computador local
4. Extrair o repositório do mais recente para baixo para o seu computador local e alterações de consolidação e push de volta ao seu repositório
5. Implementar as alterações do seu repositório na sua base de dados de configuração de serviço

Este artigo descreve como ativar e utilizar o Git para gerir a sua configuração de serviço e fornece uma referência para os ficheiros e pastas no repositório Git.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>Configuração do Git no seu serviço de acesso

Para ver e configurar as definições de configuração do Git, pode clicar a **Security** menu e navegue para o **repositório de configuração** separador.

![Permitem que o GIT][api-management-enable-git]

> [!IMPORTANT]
> Segredos que não estão definidos como valores com o nome serão armazenados no repositório e permanecerão no respetivo histórico de até desativar e reativar o acesso ao Git. Valores nomeados fornecem um local seguro para gerir os valores de cadeia de caracteres constante, incluindo segredos, em todas as configurações de API e as políticas, para que não tenha armazená-las diretamente em suas instruções de política. Para obter mais informações, consulte [como utilizar os valores com o nome nas políticas de gestão de API do Azure](api-management-howto-properties.md).
>
>

Para informações sobre como ativar ou desativar o acesso ao Git com a API REST, consulte [ativar ou desativar o acesso ao Git com a API REST](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Para guardar a configuração de serviço para o repositório de Git

A primeira etapa antes de clonar o repositório é salvar o estado atual da configuração do serviço para o repositório. Clique em **guardar no repositório**.

Introduzir quaisquer alterações pretendidas no ecrã de confirmação e clique em **Ok** para guardar.

Após alguns instantes é guardada a configuração e o estado de configuração do repositório é apresentado, incluindo a data e hora da última alteração de configuração e a última sincronização entre a configuração do serviço e o repositório.

Depois da configuração é guardada no repositório, pode ser clonado.

Para informações sobre como efetuar esta operação através da API REST, consulte [com a API REST do instantâneo de configuração de consolidação](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Para clonar o repositório no seu computador local

Para clonar um repositório, terá do URL para o repositório, um nome de utilizador e uma palavra-passe. Para obter o nome de utilizador e outras credenciais, clique em **aceder a credenciais** junto à parte superior da página.

Para gerar uma palavra-passe, primeiro certifique-se de que o **expiração** é definida como a data de expiração desejada e a hora e, em seguida, clique em **gerar**.

> [!IMPORTANT]
> Tome nota desta palavra-passe. Depois de sair desta página a senha não será exibida novamente.
>

Os exemplos seguintes utilizam a ferramenta a partir do Git Bash [Git para Windows](https://www.git-scm.com/downloads) mas pode utilizar qualquer ferramenta de Git que esteja familiarizado com.

Abra a sua ferramenta de Git na pasta pretendida e execute o seguinte comando para clonar o repositório de git no seu computador local, utilizando o comando fornecido pelo portal do Azure.

```
git clone https://{name}.scm.azure-api.net/
```

Forneça o nome de utilizador e palavra-passe quando lhe for pedido.

Se receber algum erro, tente modificar seu `git clone` comando para incluir o nome de utilizador e palavra-passe, conforme mostrado no exemplo a seguir.

```
git clone https://username:password@{name}.scm.azure-api.net/
```

Se esta opção fornece um erro, tente a parte de palavra-passe do comando de codificação do URL. Uma forma rápida de fazer isso é abrir o Visual Studio e emita o seguinte comando no **janela imediata**. Para abrir o **janela imediata**, abra qualquer solução ou projeto no Visual Studio (ou crie uma nova aplicação de consola vazia) e escolha **Windows**, **Immediate** das **Depurar** menu.

```
?System.NetWebUtility.UrlEncode("password from the Azure portal")
```

Utilize a palavra-passe codificada, juntamente com a sua localização de nome e o repositório de utilizador para construir o comando do git.

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

Assim que o repositório é clonado, pode ver e trabalhar com eles no sistema de arquivos local. Para obter mais informações, consulte [referência de repositório de Git local de estrutura de arquivo e pasta](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Para atualizar o seu repositório local com a configuração de instância de serviço mais recente

Se efetuar alterações à sua instância do serviço de gestão de API no portal do Azure ou através da API REST, tem de guardar estas alterações para o repositório antes de poder atualizar o seu repositório local com as alterações mais recentes. Para tal, clique em **Guardar configuração para o repositório** sobre o **repositório de configuração** separador no portal do Azure e, em seguida, emita o comando seguinte no seu repositório local.

```
git pull
```

Antes de executar `git pull` Certifique-se de que está na pasta para o repositório local. Se acabou de concluir o `git clone` de comando, em seguida, tem de alterar o diretório ao seu repositório ao executar um comando semelhante ao seguinte.

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Para aplicar as alterações do seu repositório local para o repositório de servidor
Para aplicar as alterações do seu repositório local para o repositório de servidor, tem de consolidar as alterações e, em seguida, enviá-las para o repositório de servidor. Para consolidar as alterações, abra a sua ferramenta de comando do Git, mude para o diretório do repositório local e emita os seguintes comandos.

```
git add --all
git commit -m "Description of your changes"
```

Para enviar todas as confirmações para o servidor, execute o seguinte comando.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Para implementar alterações de configuração de serviço para a instância do serviço de gestão de API

Depois das alterações de locais são aplicadas e enviados por push para o repositório de servidor, pode implementá-las à sua instância do serviço de gestão de API.

Para informações sobre como efetuar esta operação através da API REST, consulte [Git implementar muda para o banco de dados de configuração com a API REST](https://docs.microsoft.com/rest/api/apimanagement/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Referência de estrutura de ficheiros e pastas do repositório de Git local

Os ficheiros e pastas do repositório de local git contém as informações de configuração sobre a instância de serviço.

| Item | Descrição |
| --- | --- |
| pasta raiz de gestão de api |Contém configuração de nível superior para a instância do serviço |
| pasta de APIs |Contém a configuração para as apis na instância do serviço |
| pasta de grupos |Contém a configuração para os grupos na instância do serviço |
| pasta de políticas |Contém as políticas na instância do serviço |
| pasta de portalStyles |Contém a configuração para as personalizações do portal de programador numa instância de serviço |
| pasta de produtos |Contém a configuração para os produtos numa instância de serviço |
| pasta de modelos |Contém a configuração para os modelos de e-mail na instância do serviço |

Cada pasta pode conter um ou mais ficheiros e, em alguns casos, um ou mais pastas, por exemplo, uma pasta para cada API, produtos ou grupo. Os ficheiros em cada pasta são específicos para o tipo de entidade descrito pelo nome da pasta.

| Tipo de ficheiro | Objetivo |
| --- | --- |
| json |Informações de configuração sobre a entidade respectiva |
| html |Descrições sobre a entidade, muitas vezes, é apresentado no portal do Programador |
| xml |Instruções de política |
| css |Folhas de estilo para personalização do portal de programador |

Estes ficheiros podem ser criados, eliminados, editados e geridos no seu sistema de arquivos local e as alterações de implementado novamente para a instância de serviço de gestão de API.

> [!NOTE]
> As seguintes entidades não estão contidas no repositório do Git e não podem ser configuradas com o Git.
>
> * [Utilizadores](https://docs.microsoft.com/en-us/rest/api/apimanagement/user)
> * [Subscrições](https://docs.microsoft.com/en-us/rest/api/apimanagement/subscription)
> * [Valores nomeados](https://docs.microsoft.com/en-us/rest/api/apimanagement/property)
> * Entidades de portais de programador que não seja estilos
>

### <a name="root-api-management-folder"></a>pasta raiz de gestão de api
A raiz `api-management` pasta contém um `configuration.json` ficheiro que contém informações de nível superior sobre a instância de serviço no seguinte formato.

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

As quatro primeiras definições (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, e `UserRegistrationTermsConsentRequired`) mapear para as seguintes definições no **identidades** separador o **segurança** secção.

| Definição de identidade | É mapeado para |
| --- | --- |
| RegistrationEnabled |Presença **nome de utilizador e palavra-passe** fornecedor de identidade |
| UserRegistrationTerms |**Termos de utilização na inscrição do utilizador** caixa de texto |
| UserRegistrationTermsEnabled |**Mostrar termos de utilização na página de inscrição** caixa de verificação |
| UserRegistrationTermsConsentRequired |**Exigir consentimento** caixa de verificação |
| RequireUserSigninEnabled |**Redirecionar utilizadores anónimos para a página de início de sessão** caixa de verificação |

As próximas quatro definições (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, e `DelegationValidationKey`) mapear para as seguintes definições no **delegação** separador o **segurança** secção.

| Configuração de delegação | É mapeado para |
| --- | --- |
| DelegationEnabled |**Delegado de início de sessão e inscrição** caixa de verificação |
| DelegationUrl |**URL de ponto final de delegação** caixa de texto |
| DelegatedSubscriptionEnabled |**Delegar subscrição do produto** caixa de verificação |
| DelegationValidationKey |**Delegar a chave de validação** caixa de texto |

A definição final, `$ref-policy`, mapeia para o ficheiro de instruções de política global para a instância do serviço.

### <a name="apis-folder"></a>pasta de APIs
O `apis` pasta contém uma pasta para cada API numa instância de serviço, que contém os seguintes itens.

* `apis\<api name>\configuration.json` -Esta é a configuração para a API e contém informações sobre o URL do serviço de back-end e as operações. Isso é que as mesmas informações que seriam devolvidas se chamar [obter uma API específica](https://docs.microsoft.com/rest/api/apimanagement/apis/get) com `export=true` no `application/json` formato.
* `apis\<api name>\api.description.html` -Esta é a descrição da API e corresponde à `description` propriedade o [entidade de API](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._entity_property).
* `apis\<api name>\operations\` -Esta pasta contém `<operation name>.description.html` arquivos que mapeiam para as operações na API. Cada ficheiro contém a descrição de uma única operação na API, que mapeia para o `description` propriedade o [entidade de operação](https://docs.microsoft.com/rest/api/visualstudio/operations/list#operationproperties) na REST API.

### <a name="groups-folder"></a>pasta de grupos
O `groups` pasta contém uma pasta para cada grupo definido na instância do serviço.

* `groups\<group name>\configuration.json` -Esta é a configuração para o grupo. Isso é que as mesmas informações que seriam devolvidas se chamar o [obter um grupo específico](https://docs.microsoft.com/rest/api/apimanagement/group/get) operação.
* `groups\<group name>\description.html` -Esta é a descrição do grupo e corresponde à `description` propriedade o [entidade de grupo](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity).

### <a name="policies-folder"></a>pasta de políticas
O `policies` pasta contém as instruções de políticas para a sua instância de serviço.

* `policies\global.xml` -contém as políticas definidas no âmbito global da sua instância de serviço.
* `policies\apis\<api name>\` – Se tiver quaisquer políticas definidas no âmbito da API, estão contidos nesta pasta.
* `policies\apis\<api name>\<operation name>\` pasta - se tiver quaisquer políticas definidas no âmbito da operação, estão contidos nesta pasta no `<operation name>.xml` arquivos que mapeiam para as instruções de políticas para cada operação.
* `policies\products\` – Se tiver quaisquer políticas definidas no âmbito do produto, eles estão contidos nesta pasta, que contém `<product name>.xml` arquivos que mapeiam para as instruções de políticas para cada produto.

### <a name="portalstyles-folder"></a>pasta de portalStyles
O `portalStyles` pasta contém folhas de estilo e de configuração para personalizações do portal de programador para a instância de serviço.

* `portalStyles\configuration.json` -contém os nomes de folhas de estilo utilizados pelo portal do Programador
* `portalStyles\<style name>.css` -cada `<style name>.css` arquivo contém estilos para o portal do programador (`Preview.css` e `Production.css` por predefinição).

### <a name="products-folder"></a>pasta de produtos
O `products` pasta contém uma pasta para cada produto definido na instância do serviço.

* `products\<product name>\configuration.json` -Esta é a configuração para o produto. Isso é que as mesmas informações que seriam devolvidas se chamar o [obter um produto específico](https://docs.microsoft.com/rest/api/apimanagement/product/get) operação.
* `products\<product name>\product.description.html` -Esta é a descrição do produto e que corresponde à `description` propriedade do [entidade product](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity) na REST API.

### <a name="templates"></a>modelos
O `templates` pasta contém a configuração para o [modelos de e-mail](api-management-howto-configure-notifications.md) a instância de serviço.

* `<template name>\configuration.json` -Esta é a configuração para o modelo de e-mail.
* `<template name>\body.html` -Este é o corpo do modelo de e-mail.

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre outras formas de gerir a sua instância de serviço, consulte:

* Gerir a sua instância de serviço com os seguintes cmdlets do PowerShell
  * [Referência do cmdlet do Powershell de implementação do serviço](https://docs.microsoft.com/powershell/module/wds)
  * [Referência de cmdlets do PowerShell da gestão de serviço](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)
* Gerir a sua instância de serviço com a API REST
  * [Referência da API de REST de gestão de API](https://msdn.microsoft.com/library/azure/dn776326.aspx)


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




