---
title: 'Tutorial: Mapeie o nome DNS personalizado existente'
description: Saiba como adicionar um nome de domínio DNS personalizado já existente a uma aplicação Web, ao back-end de uma aplicação móvel ou a uma aplicação API no Serviço de Aplicações do Azure.
keywords: serviço de aplicações, serviço de aplicações do azure, mapeamento de domínios, domínio existente, nome de anfitrião
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: mvc, seodec18
ms.openlocfilehash: 46c27f18f8f16f783248790f03364654d0b3c2fe
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986835"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Tutorial: Mapeie um nome DNS personalizado existente para o Azure App Service

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra-lhe como mapear um nome DNS personalizado existente para o Azure App Service.

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

Para mapear um nome DNS personalizado para uma aplicação web, o plano de Serviço de [Aplicações](https://azure.microsoft.com/pricing/details/app-service/) da web deve ser um nível pago **(Compartilhado,** **Básico,** **Padrão,** **Premium** ou **Consumo** para Funções Azure). Neste passo, vai confirmar que a aplicação do Serviço de Aplicações está no escalão de preço suportado.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="select-the-app-in-the-azure-portal"></a>Selecione a aplicação no portal Azure

Procure e selecione **Serviços de Aplicações.**

![Selecione Serviços de Aplicações](./media/app-service-web-tutorial-custom-domain/app-services.png)

Na página **Serviços de Aplicações,** selecione o nome da sua aplicação Azure.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Será apresentada a página de gestão da aplicação do Serviço de Aplicações.  

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

No painel de navegação esquerdo da página da aplicação, desloque-se para a secção **Definições** e selecione **Aumentar verticalmente (plano do Serviço de Aplicações)**.

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

## <a name="get-domain-verification-id"></a>Obtenha iD de verificação de domínio

Para adicionar um domínio personalizado à sua aplicação, tem de verificar a sua propriedade do domínio adicionando um ID de verificação como registo TXT com o seu fornecedor de domínio. Na navegação à esquerda da sua página de aplicações, clique em **Domínios Personalizados** em **Definições**. Valor de cópia do ID de Verificação de Domínio Personalizado a partir daqui. Precisa desta identificação de verificação para o próximo passo.

## <a name="map-your-domain"></a>Mapear o seu domínio

Para mapear um nome DNS personalizado para o Serviço de Aplicações, pode utilizar tanto um **registo CNAME**, como um **registo A**. Siga os passos correspondentes:

- [Mapear um registo CNAME](#map-a-cname-record)
- [Mapear um registo A](#map-an-a-record)
- [Mapear um domínio de caráter universal (com um registo CNAME)](#map-a-wildcard-domain)

> [!NOTE]
> Deve utilizar os registos CNAME para todos os nomes DNS personalizados, exceto os domínios de raiz (por exemplo, `contoso.com`). Para os domínios de raiz, utilize registos A.

### <a name="map-a-cname-record"></a>Mapear um registo CNAME

No exemplo do tutorial, vai adicionar um registo CNAME ao subdomínio `www` (por exemplo, `www.contoso.com`).

Se tiver um subdomínio `www` diferente, `www` substitua-o pelo seu subdomínio (por exemplo, se `sub` o seu domínio personalizado `sub.constoso.com` for).

#### <a name="access-dns-records-with-domain-provider"></a>Aceder a registos DNS com o fornecedor de domínios

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Criar o registo CNAME

Mapeie um subdomínio para o nome de domínio padrão da aplicação `<app_name>.azurewebsites.net` (, onde `<app_name>` está o nome da sua aplicação). Para criar um mapeamento CNAME para o `www` subdomínio, crie dois registos:

| Tipo de registo | Anfitrião | Valor | Comentários |
| - | - | - |
| CNAME | `www` | `<app_name>.azurewebsites.net` | O próprio mapeamento de domínio. |
| TXT | `asuid.www` | [A verificação que recebeu mais cedo](#get-domain-verification-id) | O Serviço de Aplicações acede ao `asuid.<subdomain>` registo TXT para verificar a sua propriedade do domínio personalizado. |

Depois de adicionar os registos CNAME e TXT, a página de registos DNS parece ser o seguinte exemplo:

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Ativar o mapeamento de registos CNAME no Azure

No painel de navegação esquerdo da página da aplicação no portal do Azure, selecione **Domínios personalizados**.

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

N página **Domínios personalizados** da aplicação, adicione o nome DNS personalizado completamente qualificado (`www.contoso.com`) à lista.

Selecione o **+** ícone ao lado do domínio **personalizado**.

![Adicionar nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escreva o nome de domínio completamente qualificado ao qual adicionou um registo CNAME, como `www.contoso.com`.

Selecione **Validar**.

É mostrada a página **de domínio do Add custom.**

Certifique-se de que **o tipo de registo hostname** está definido para **CNAME (www \. example.com ou qualquer subdomínio)**.

**Selecione Adicionar domínio personalizado**.

![Adicionar o nome DNS à aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Pode levar algum tempo para que o novo domínio personalizado seja refletido na página de **domínios personalizados** da aplicação. Experimente atualizar o browser para atualizar os dados.

![Registo CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

> [!NOTE]
> Uma etiqueta **Não Segura** para o seu domínio personalizado significa que ainda não está vinculado a um certificado TLS/SSL, e qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá e erro ou aviso, dependendo do navegador. Para adicionar uma ligação TLS, consulte [Secure um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md).

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

Para mapear um registo A para uma aplicação, normalmente para o domínio raiz, crie dois registos:

| Tipo de registo | Anfitrião | Valor | Comentários |
| - | - | - |
| A | `@` | Endereço IP de [Copiar o endereço IP da aplicação](#info) | O próprio mapeamento de domínio `@` (normalmente representa o domínio raiz). |
| TXT | `asuid` | [A verificação que recebeu mais cedo](#get-domain-verification-id) | O Serviço de Aplicações acede ao `asuid.<subdomain>` registo TXT para verificar a sua propriedade do domínio personalizado. Para o domínio raiz, utilize `asuid` . |

> [!NOTE]
> Para adicionar um subdomínio (como `www.contoso.com` ) utilizando um registo A em vez de um registo [CNAME](#map-a-cname-record)recomendado, o seu registo A e o registo TXT devem parecer-se com a seguinte tabela:
>
> | Tipo de registo | Anfitrião | Valor |
> | - | - | - |
> | A | `www` | Endereço IP de [Copiar o endereço IP da aplicação](#info) |
> | TXT | `asuid.www` | `<app_name>.azurewebsites.net` |
>

Quando os registos estiverem adicionados, a página de registos DNS terá um aspeto semelhante ao seguinte exemplo:

![Página de registos DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Ativar o mapeamento de registos A na aplicação

Novamente na página **Domínios personalizados** da aplicação, no portal do Azure, adicione o nome DNS personalizado completamente qualificado (por exemplo, `contoso.com`) à lista.

Selecione o **+** ícone ao lado do domínio **personalizado**.

![Adicionar nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escreva o nome de domínio completamente qualificado para o qual configurou o registo A, como `contoso.com`.

Selecione **Validar**.

É mostrada a página **de domínio do Add custom.**

Confirme que o **Tipo de registo de nome de anfitrião** está definido como **Registo A (www.example.com)**.

**Selecione Adicionar domínio personalizado**.

![Adicionar o nome DNS à aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Pode levar algum tempo para que o novo domínio personalizado seja refletido na página de **domínios personalizados** da aplicação. Experimente atualizar o browser para atualizar os dados.

![Registo A adicionado](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

> [!NOTE]
> Uma etiqueta **Não Segura** para o seu domínio personalizado significa que ainda não está vinculado a um certificado TLS/SSL, e qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá e erro ou aviso, dependendo do navegador. Para adicionar uma ligação TLS, consulte [Secure um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md).

Se tiver perdido um passo ou escrito algo mal em algum momento acima, verá um erro de verificação na parte inferior da página.

![Erro de verificação](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mapear um domínio com caráteres universais

No exemplo do tutorial, vai mapear um [nome DNS com carateres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por exemplo, `*.contoso.com`) para a aplicação do Serviço de Aplicações ao adicionar um registo CNAME.

#### <a name="access-dns-records-with-domain-provider"></a>Aceder a registos DNS com o fornecedor de domínios

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Criar o registo CNAME

Adicione um registo CNAME para mapear um nome wildcard ao nome de domínio padrão da aplicação ( `<app_name>.azurewebsites.net` ).

No domínio de exemplo `*.contoso.com`, o registo CNAME vai mapear o nome `*` para `<app_name>.azurewebsites.net`.

Quando o CNAME estiver adicionado, a página de registos DNS terá um aspeto semelhante ao seguinte exemplo:

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Ativar o mapeamento de registos CNAME na aplicação

Agora, pode adicionar qualquer subdomínio que corresponda ao nome com caráteres universais à aplicação (por exemplo, `sub1.contoso.com` e `sub2.contoso.com` correspondem a `*.contoso.com`).

No painel de navegação esquerdo da página da aplicação no portal do Azure, selecione **Domínios personalizados**.

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Selecione o **+** ícone ao lado do domínio **personalizado**.

![Adicionar nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escreva um nome de domínio completamente qualificado que corresponda ao domínio de caráter universal (por exemplo, `sub1.contoso.com`) e, em seguida, selecione **Validar**.

O botão **de domínio personalizado Add** está ativado.

Certifique-se de que **o tipo de registo do nome anfitrião** está definido para o registo **CNAME (www \. example.com ou qualquer subdomínio)**.

**Selecione Adicionar domínio personalizado**.

![Adicionar o nome DNS à aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Pode levar algum tempo para que o novo domínio personalizado seja refletido na página de **domínios personalizados** da aplicação. Experimente atualizar o browser para atualizar os dados.

Selecione o **+** ícone novamente para adicionar outro domínio personalizado que corresponda ao domínio wildcard. Por exemplo, adicione `sub2.contoso.com`.

![Registo CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

> [!NOTE]
> Uma etiqueta **Note Secure** para o seu domínio personalizado significa que ainda não está vinculado a um certificado TLS/SSL, e qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá e erro ou aviso, dependendo do navegador. Para adicionar uma ligação TLS, consulte [Secure um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md).

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

Uma vez concluída a operação, a sua aplicação deverá devolver a página certa no caminho raiz (por exemplo, `http://contoso.com` ).

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

Avance para o próximo tutorial para aprender a ligar um certificado TLS/SSL personalizado a uma aplicação web.

> [!div class="nextstepaction"]
> [Proteja um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md)
