---
title: Migrar um nome DNS ativo-serviço de Azure App | Microsoft Docs
description: Saiba como migrar um nome de domínio DNS personalizado que já está atribuído a um site ativo para Azure App serviço sem nenhum tempo de inatividade.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/21/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5f11173c7b7f7396a8cf5cda4b9c8975cd7bb38e
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679798"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrar um nome DNS ativo para Azure App serviço

Este artigo mostra como migrar um nome DNS ativo para [Azure app serviço](../app-service/overview.md) sem nenhum tempo de inatividade.

Quando você migra um site ativo e seu nome de domínio DNS para o serviço de aplicativo, esse nome DNS já está servindo tráfego ao vivo. Você pode evitar o tempo de inatividade na resolução DNS durante a migração ligando o nome DNS ativo ao seu aplicativo do serviço de aplicativo preemptivamente.

Se você não estiver preocupado com o tempo de inatividade na resolução DNS, consulte [mapear um nome DNS personalizado existente para o serviço de Azure app](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este "como":

- [Verifique se o aplicativo do serviço de aplicativo não está na camada gratuita](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Associar o nome de domínio preventivamente

Ao associar um domínio personalizado de forma preventiva, você realiza os dois itens a seguir antes de fazer qualquer alteração em seus registros DNS:

- Verificar a propriedade do domínio
- Habilitar o nome de domínio para seu aplicativo

Quando você finalmente migrar o nome DNS personalizado do site antigo para o aplicativo do serviço de aplicativo, não haverá nenhum tempo de inatividade na resolução DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Criar registro de verificação de domínio

Para verificar a propriedade do domínio, adicione um registro TXT. O registro TXT mapeia de _awverify. &lt;subdomain >_ para _&lt;appname >. azurewebsites. net_. 

O registro TXT necessário depende do registro DNS que você deseja migrar. Para obter exemplos, consulte a tabela a seguir (`@` normalmente representa o domínio raiz):

| Exemplo de registro DNS | Host TXT | Valor TXT |
| - | - | - |
| \@ (raiz) | _awverify_ | _&lt;appname >. azurewebsites. net_ |
| www (sub) | _awverify. www_ | _&lt;appname >. azurewebsites. net_ |
| \* (curinga) | _awverify. \*_ | _&lt;appname >. azurewebsites. net_ |

Na página de registros DNS, observe o tipo de registro do nome DNS que você deseja migrar. O serviço de aplicativo dá suporte A mapeamentos de CNAME e registros A.

> [!NOTE]
> Para determinados provedores, como CloudFlare, `awverify.*` não é um registro válido. Use `*` apenas em vez disso.

> [!NOTE]
> Os registros de `*` curinga não validarão subdomínios com um registro de CNAME existente. Talvez seja necessário criar explicitamente um registro TXT para cada subdomínio.


### <a name="enable-the-domain-for-your-app"></a>Habilitar o domínio para seu aplicativo

No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda da página do aplicativo, selecione **domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **domínios personalizados** , selecione o ícone de **+** ao lado de **adicionar nome de host**.

![Adicionar nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digite o nome de domínio totalmente qualificado para o qual você adicionou o registro TXT, como `www.contoso.com`. Para um domínio curinga (como \*. contoso.com), você pode usar qualquer nome DNS que corresponda ao domínio curinga. 

Selecione **Validar**.

O botão **Adicionar nome de anfitrião** é ativado. 

Verifique se o **tipo de registro de nome de host** está definido como o tipo de registro DNS que você deseja migrar.

Selecione **Adicionar nome de anfitrião**.

![Adicionar o nome DNS à aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Poderá demorar algum tempo até que o nome de anfitrião novo seja refletido na página **Domínios personalizados** da aplicação. Experimente atualizar o browser para atualizar os dados.

![Registo CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Seu nome DNS personalizado agora está habilitado em seu aplicativo do Azure. 

## <a name="remap-the-active-dns-name"></a>Remapear o nome DNS ativo

A única coisa que resta fazer é remapear seu registro DNS ativo para apontar para o serviço de aplicativo. No momento, ele ainda aponta para o seu site antigo.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Copiar o endereço IP do aplicativo (apenas um registro)

Se você estiver remapeando um registro CNAME, ignore esta seção. 

Para remapear um registro A, você precisa do endereço IP externo do aplicativo do serviço de aplicativo, que é mostrado na página **domínios personalizados** .

Feche a página **adicionar nome do host** selecionando **X** no canto superior direito. 

Na página **Domínios personalizados**, copie o endereço IP da aplicação.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Atualizar o registro DNS

De volta à página de registros DNS do seu provedor de domínio, selecione o registro DNS para remapear.

Para o `contoso.com` exemplo de domínio raiz, remapeie o registro a ou CNAME como os exemplos na tabela a seguir: 

| Exemplo de FQDN | Tipo de registo | Host | Valor |
| - | - | - | - |
| contoso.com (raiz) | A | `@` | Endereço IP de [Copiar o endereço IP da aplicação](#info) |
| www \.contoso. com (sub) | CNAME | `www` | _&lt;appname >. azurewebsites. net_ |
| \*. contoso.com (curinga) | CNAME | _\*_ | _&lt;appname >. azurewebsites. net_ |

Salve suas configurações.

As consultas DNS devem começar a resolver o aplicativo do serviço de aplicativo imediatamente após a propagação do DNS.

## <a name="active-domain-in-azure"></a>Domínio ativo no Azure

Você pode migrar um domínio personalizado ativo no Azure, entre assinaturas ou dentro da mesma assinatura. No entanto, essa migração sem tempo de inatividade requer que o aplicativo de origem e o aplicativo de destino recebam o mesmo domínio personalizado em um determinado momento. Portanto, você precisa certificar-se de que os dois aplicativos não sejam implantados na mesma unidade de implantação (internamente conhecida como espaço Web). Um nome de domínio pode ser atribuído a apenas um aplicativo em cada unidade de implantação.

Você pode encontrar a unidade de implantação para seu aplicativo examinando o nome de domínio da URL do FTP/S `<deployment-unit>.ftp.azurewebsites.windows.net`. Verifique e certifique-se de que a unidade de implantação seja diferente entre o aplicativo de origem e o aplicativo de destino. A unidade de implantação de um aplicativo é determinada pelo [plano do serviço de aplicativo](overview-hosting-plans.md) em que ele está. Ele é selecionado aleatoriamente pelo Azure quando você cria o plano e não pode ser alterado. O Azure só garante que dois planos estejam na mesma unidade de implantação quando você [os cria no mesmo grupo de recursos *e* na mesma região](app-service-plan-manage.md#create-an-app-service-plan), mas não tem nenhuma lógica para garantir que os planos estejam em unidades de implantação diferentes. A única maneira de criar um plano em uma unidade de implantação diferente é continuar criando um plano em um novo grupo de recursos ou região até obter uma unidade de implantação diferente.

## <a name="next-steps"></a>Passos seguintes

Saiba como associar um certificado SSL personalizado ao serviço de aplicativo.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure App Service](app-service-web-tutorial-custom-ssl.md) (Vincular um certificado SSL personalizado já existente ao Serviço de Aplicações do Azure)
