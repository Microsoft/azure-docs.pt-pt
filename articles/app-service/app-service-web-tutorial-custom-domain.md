---
title: 'Tutorial: mapear o nome DNS personalizado existente'
description: Saiba como adicionar um nome de domínio DNS personalizado já existente a uma aplicação Web, ao back-end de uma aplicação móvel ou a uma aplicação API no Serviço de Aplicações do Azure.
keywords: serviço de aplicações, serviço de aplicações do azure, mapeamento de domínios, domínio existente, nome de anfitrião
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: 37bb1193056894ea824f4aa1723fb327f54bccae
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672080"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Tutorial: mapear um nome DNS personalizado existente para o serviço Azure App

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra como mapear um nome DNS personalizado existente para Azure App serviço.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Mapear um subdomínio (por exemplo, `www.contoso.com`) através da utilização de um registo CNAME
> * Mapear um domínio raiz (por exemplo, `contoso.com`) através da utilização de um registo A
> * Mapear um domínio com carateres universais (por exemplo, `*.contoso.com`) através da utilização de um registo CNAME
> * Redirecionar o URL predefinido para um diretório personalizado
> * Automatizar o mapeamento de domínios com scripts

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie uma aplicação do Serviço de Aplicações](/azure/app-service/) ou utilize uma aplicação que tenha criado para outro tutorial.
* Compre um nome de domínio e confirme que tem acesso ao registo DNS do seu fornecedor de domínio (como a GoDaddy).

  Por exemplo, para adicionar entradas DNS para `contoso.com` e `www.contoso.com`, tem de ter poder configurar as definições de DNS para o domínio de raiz `contoso.com`.

  > [!NOTE]
  > Se não tiver um nome de domínio já existente, considere [comprar um domínio através do portal do Azure](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Preparar a aplicação

Para mapear um nome DNS personalizado para um aplicativo Web, o plano do [serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/) do aplicativo Web deve ser uma camada paga (**compartilhada**, **básica**, **Standard**, **Premium** ou **consumo** para Azure Functions). Neste passo, vai confirmar que a aplicação do Serviço de Aplicações está no escalão de preço suportado.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="select-the-app-in-the-azure-portal"></a>Selecione o aplicativo no portal do Azure

Pesquise e selecione **serviços de aplicativos**.

![Selecionar serviços de aplicativos](./media/app-service-web-tutorial-custom-domain/app-services.png)

Na página **serviços de aplicativos** , selecione o nome do seu aplicativo do Azure.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Será apresentada a página de gestão da aplicação do Serviço de Aplicações.  

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

No painel de navegação esquerdo da página da aplicação, desloque-se para a secção **Definições** e selecione **Aumentar verticalmente (plano do Serviço de Aplicações)** .

![Menu de aumento vertical](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

O escalão atual da aplicação é realçado com um limite azul. Confirme que a aplicação não está no escalão **F1**. O DNS personalizado não é suportado no escalão **F1**. 

![Verificar o escalão de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Se o plano do Serviço de Aplicações não estiver no escalão **F1**, feche a página **Aumentar verticalmente** e avance para [Mapear um registo CNAME](#cname).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Aumentar verticalmente o plano do Serviço de Aplicações

Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

![Verificar o escalão de preço](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando vir a notificação seguinte, significa que a operação de dimensionamento está completa.

![Confirmação de operação de dimensionamento](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="map-your-domain"></a>Mapear o seu domínio

Para mapear um nome DNS personalizado para o Serviço de Aplicações, pode utilizar tanto um **registo CNAME**, como um **registo A**. Siga os passos correspondentes:

- [Mapear um registo CNAME](#map-a-cname-record)
- [Mapear um registo A](#map-an-a-record)
- [Mapear um domínio de caráter universal (com um registo CNAME)](#map-a-wildcard-domain)

> [!NOTE]
> Deve utilizar os registos CNAME para todos os nomes DNS personalizados, exceto os domínios de raiz (por exemplo, `contoso.com`). Para os domínios de raiz, utilize registos A.

### <a name="map-a-cname-record"></a>Mapear um registo CNAME

No exemplo do tutorial, vai adicionar um registo CNAME ao subdomínio `www` (por exemplo, `www.contoso.com`).

#### <a name="access-dns-records-with-domain-provider"></a>Aceder a registos DNS com o fornecedor de domínios

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Criar o registo CNAME

Adicione um registro CNAME para mapear um subdomínio para o nome de domínio padrão do aplicativo (`<app_name>.azurewebsites.net`, em que `<app_name>` é o nome do seu aplicativo).

No exemplo do domínio `www.contoso.com`, adicione um registo CNAME que mapeie o nome `www` para `<app_name>.azurewebsites.net`.

Depois de adicionar o CNAME, a página de registos DNS terá um aspeto semelhante ao seguinte exemplo:

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Ativar o mapeamento de registos CNAME no Azure

No painel de navegação esquerdo da página da aplicação no portal do Azure, selecione **Domínios personalizados**.

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

N página **Domínios personalizados** da aplicação, adicione o nome DNS personalizado completamente qualificado (`www.contoso.com`) à lista.

Selecione o ícone de **+** ao lado de **Adicionar domínio personalizado**.

![Adicionar nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escreva o nome de domínio completamente qualificado ao qual adicionou um registo CNAME, como `www.contoso.com`.

Selecione **Validar**.

A página **Adicionar domínio personalizado** é mostrada.

Verifique se **tipo de registro de nome de host** está definido como **CNAME (www\.example.com ou qualquer subdomínio)** .

Selecione **Adicionar domínio personalizado**.

![Adicionar o nome DNS à aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **domínios personalizados** do aplicativo. Experimente atualizar o browser para atualizar os dados.

![Registo CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

> [!NOTE]
> Um rótulo **não seguro** para seu domínio personalizado significa que ele ainda não está associado a um certificado SSL, e qualquer solicitação HTTPS de um navegador para seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para adicionar uma associação SSL, consulte [proteger um nome DNS personalizado com uma associação SSL no serviço Azure app](configure-ssl-bindings.md).

Se tiver perdido um passo ou escrito algo mal em algum momento acima, verá um erro de verificação na parte inferior da página.

![Erro de verificação](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Mapear um registo A

No exemplo do tutorial, vai adicionar um registo A ao domínio de raiz (por exemplo, `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Copiar o endereço IP da aplicação

Para mapear um registo A, precisa do endereço IP externo da aplicação. Pode encontrar este endereço IP na página **Domínios personalizados** da aplicação, no portal do Azure.

No painel de navegação esquerdo da página da aplicação no portal do Azure, selecione **Domínios personalizados**.

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **Domínios personalizados**, copie o endereço IP da aplicação.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-domain-provider"></a>Aceder a registos DNS com o fornecedor de domínios

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Criar um registo A

Para mapear um registo A para uma aplicação, o Serviço de Aplicações requer **dois** registos DNS:

- Um registo **A**, para mapear o endereço IP da aplicação.
- Um registro **txt** para mapear para o nome de domínio padrão do aplicativo `<app_name>.azurewebsites.net`. O Serviço de Aplicações utiliza este registo apenas no momento da configuração, para verificar que é o proprietário do domínio personalizado. Após a validação e configuração do domínio personalizado no Serviço de Aplicações, pode eliminar este registo TXT.

No exemplo do domínio `contoso.com`, crie os registos A e TXT de acordo com a tabela seguinte (`@` representa, normalmente, o domínio de raiz).

| Tipo de registo | Host | Valor |
| - | - | - |
| A | `@` | Endereço IP de [Copiar o endereço IP da aplicação](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

> [!NOTE]
> Para adicionar um subdomínio (como `www.contoso.com`) usando um registro A em vez de um [registro CNAME](#map-a-cname-record)recomendado, o registro a e o registro txt devem ser semelhantes à seguinte tabela:
>
> | Tipo de registo | Host | Valor |
> | - | - | - |
> | A | `www` | Endereço IP de [Copiar o endereço IP da aplicação](#info) |
> | TXT | `www` | `<app_name>.azurewebsites.net` |
>

Quando os registos estiverem adicionados, a página de registos DNS terá um aspeto semelhante ao seguinte exemplo:

![Página de registos DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Ativar o mapeamento de registos A na aplicação

Novamente na página **Domínios personalizados** da aplicação, no portal do Azure, adicione o nome DNS personalizado completamente qualificado (por exemplo, `contoso.com`) à lista.

Selecione o ícone de **+** ao lado de **Adicionar domínio personalizado**.

![Adicionar nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escreva o nome de domínio completamente qualificado para o qual configurou o registo A, como `contoso.com`.

Selecione **Validar**.

A página **Adicionar domínio personalizado** é mostrada.

Confirme que o **Tipo de registo de nome de anfitrião** está definido como **Registo A (www.example.com)** .

Selecione **Adicionar domínio personalizado**.

![Adicionar o nome DNS à aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **domínios personalizados** do aplicativo. Experimente atualizar o browser para atualizar os dados.

![Registo A adicionado](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

> [!NOTE]
> Um rótulo **não seguro** para seu domínio personalizado significa que ele ainda não está associado a um certificado SSL, e qualquer solicitação HTTPS de um navegador para seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para adicionar uma associação SSL, consulte [proteger um nome DNS personalizado com uma associação SSL no serviço Azure app](configure-ssl-bindings.md).

Se tiver perdido um passo ou escrito algo mal em algum momento acima, verá um erro de verificação na parte inferior da página.

![Erro de verificação](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mapear um domínio com caráteres universais

No exemplo do tutorial, vai mapear um [nome DNS com carateres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por exemplo, `*.contoso.com`) para a aplicação do Serviço de Aplicações ao adicionar um registo CNAME.

#### <a name="access-dns-records-with-domain-provider"></a>Aceder a registos DNS com o fornecedor de domínios

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Criar o registo CNAME

Adicione um registro CNAME para mapear um nome de curinga para o nome de domínio padrão do aplicativo (`<app_name>.azurewebsites.net`).

No domínio de exemplo `*.contoso.com`, o registo CNAME vai mapear o nome `*` para `<app_name>.azurewebsites.net`.

Quando o CNAME estiver adicionado, a página de registos DNS terá um aspeto semelhante ao seguinte exemplo:

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Ativar o mapeamento de registos CNAME na aplicação

Agora, pode adicionar qualquer subdomínio que corresponda ao nome com caráteres universais à aplicação (por exemplo, `sub1.contoso.com` e `sub2.contoso.com` correspondem a `*.contoso.com`).

No painel de navegação esquerdo da página da aplicação no portal do Azure, selecione **Domínios personalizados**.

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Selecione o ícone de **+** ao lado de **Adicionar domínio personalizado**.

![Adicionar nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escreva um nome de domínio completamente qualificado que corresponda ao domínio de caráter universal (por exemplo, `sub1.contoso.com`) e, em seguida, selecione **Validar**.

O botão **Adicionar domínio personalizado** é ativado.

Verifique se o **tipo de registro hostname** está definido como **registro CNAME (www\.example.com ou qualquer subdomínio)** .

Selecione **Adicionar domínio personalizado**.

![Adicionar o nome DNS à aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **domínios personalizados** do aplicativo. Experimente atualizar o browser para atualizar os dados.

Selecione o ícone de **+** novamente para adicionar outro domínio personalizado que corresponda ao domínio curinga. Por exemplo, adicione `sub2.contoso.com`.

![Registo CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

> [!NOTE]
> Um rótulo **seguro de observação** para seu domínio personalizado significa que ele ainda não está associado a um certificado SSL, e qualquer solicitação HTTPS de um navegador para seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para adicionar uma associação SSL, consulte [proteger um nome DNS personalizado com uma associação SSL no serviço Azure app](configure-ssl-bindings.md).

## <a name="test-in-browser"></a>Testar no browser

Navegue para o nome ou nomes DNS que configurou anteriormente (por exemplo, `contoso.com`, `www.contoso.com`, `sub1.contoso.com` e `sub2.contoso.com`).

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Resolver 404 "Não Encontrado"

Se receber o erro de HTTP 404 (Não Encontrado) ao navegar para o URL do seu domínio personalizado, utilize <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a> para confirmar que o domínio é resolvido para o endereço IP da sua aplicação. Se não for, essa situação poderá dever-se a um dos motivos seguintes:

- Falta um registo A e/ou um registo CNAME ao domínio personalizado configurado.
- O cliente do browser colocou em cache o endereço IP antigo do seu domínio. Limpe a cache e volte a testar a resolução de DNS. Num computador Windows, limpe a cache com `ipconfig /flushdns`.

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>Migrar um domínio ativo

Para migrar um site em direto e o respetivo nome de domínio DNS para o Serviço de Aplicações, consulte [Migrar um nome DNS ativo para o Serviço de Aplicações do Azure](manage-custom-dns-migrate-domain.md).

## <a name="redirect-to-a-custom-directory"></a>Redirecionar para um diretório personalizado

Por predefinição, o Serviço de Aplicações direciona os pedidos Web para o diretório de raiz do código da sua aplicação. No entanto, determinadas estruturas da Web não são iniciadas no diretório de raiz. Por exemplo, o [Laravel](https://laravel.com/) é iniciado no subdiretório `public`. Para continuar com o exemplo de DNS `contoso.com`, essa aplicação seria acessível em `http://contoso.com/public`, mas o que realmente quereria era direcionar `http://contoso.com` para o diretório `public`. Este passo não envolve a resolução de DNS, mas sim a personalização do diretório virtual.

Para tal, selecione **Definições da Aplicação** no painel de navegação esquerdo da página da aplicação Web. 

Na parte inferior da página, o diretório de raiz virtual `/` aponta para `site\wwwroot` por predefinição, que é o diretório de raiz do código da aplicação. Altere-o para apontar para `site\wwwroot\public`, por exemplo, e guarde as alterações.

![Personalizar o diretório virtual](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Depois que a operação for concluída, seu aplicativo deverá retornar a página correta no caminho raiz (por exemplo, `http://contoso.com`).

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a gestão de domínios personalizados com scripts através da [CLI do Azure](/cli/azure/install-azure-cli) ou do [Azure PowerShell](/powershell/azure/overview). 

### <a name="azure-cli"></a>CLI do Azure 

O comando seguinte adiciona um nome DNS personalizado configurado a uma aplicação do Serviço de Aplicações. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Para obter mais informações, veja [Map a custom domain to a web app](scripts/cli-configure-custom-domain.md) (Mapear um domínio personalizado a uma aplicação Web).

### <a name="azure-powershell"></a>Azure PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O comando seguinte adiciona um nome DNS personalizado configurado a uma aplicação do Serviço de Aplicações.

```powershell  
Set-AzWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net")
```

Para obter mais informações, veja [Assign a custom domain to a web app](scripts/powershell-configure-custom-domain.md) (Atribuir um domínio personalizado a uma aplicação Web).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Mapear um subdomínio com um registo CNAME
> * Mapear um domínio de raiz com um registo A
> * Mapear um domínio de caráter universal com um registo CNAME
> * Redirecionar o URL predefinido para um diretório personalizado
> * Automatizar o mapeamento de domínios com scripts

Avance para o tutorial seguinte para aprender a vincular um certificado SSL personalizado a uma aplicação Web.

> [!div class="nextstepaction"]
> [Proteger um nome DNS personalizado com um enlace SSL no Serviço de Aplicações do Azure](configure-ssl-bindings.md)
