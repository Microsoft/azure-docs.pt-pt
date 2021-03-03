---
title: 'Tutorial: Mapeie o nome DNS personalizado existente'
description: Saiba como adicionar um nome de domínio dNS personalizado existente (domínio de vaidade) a uma aplicação web, aplicativo móvel ou app API no Azure App Service.
keywords: serviço de aplicações, serviço de aplicações do azure, mapeamento de domínios, domínio existente, nome de anfitrião
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: b3ff1b344852d57f0effbd978c06aa617682ea4f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720332"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Tutorial: Mapeie um nome DNS personalizado existente para o Azure App Service

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra-lhe como mapear um nome personalizado do Sistema de Nome de Domínio (DNS) existente para o Serviço de Aplicações.

![Screenshot que mostra a navegação do portal Azure para uma aplicação Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Mapear um subdomínio (por exemplo, `www.contoso.com` ) utilizando um registo CNAME.
> * Mapear um domínio de raiz (por exemplo, `contoso.com` ) utilizando um registo A.
> * Mapear um domínio wildcard (por exemplo, `*.contoso.com` ) utilizando um registo CNAME.
> * Redirecione o URL padrão para um diretório personalizado.
> * Automatize o mapeamento de domínio com scripts.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie uma aplicação do Serviço de Aplicações](./index.yml) ou utilize uma aplicação que tenha criado para outro tutorial.
* Compre um nome de domínio e certifique-se de que tem acesso ao registo DNS para o seu fornecedor de domínio (como o GoDaddy).

  Por exemplo, para adicionar entradas DNS para `contoso.com` e `www.contoso.com`, tem de ter poder configurar as definições de DNS para o domínio de raiz `contoso.com`.

  > [!NOTE]
  > Se não tiver um nome de domínio existente, considere [comprar um domínio utilizando o portal Azure](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Preparar a aplicação

Para mapear um nome DNS personalizado para uma aplicação web, o plano de Serviço de [Aplicações](https://azure.microsoft.com/pricing/details/app-service/) da web deve ser um nível pago (Compartilhado, Básico, Padrão, Premium ou Consumo para Funções Azure). Neste passo, vai confirmar que a aplicação do Serviço de Aplicações está no escalão de preço suportado.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra o [portal Azure](https://portal.azure.com)e inscreva-se na sua conta Azure.

### <a name="select-the-app-in-the-azure-portal"></a>Selecione a aplicação no portal Azure

1. Procure e selecione **Serviços de Aplicações.**

   ![Screenshot que mostra a seleção de Serviços de Aplicações.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Na página **Serviços de Aplicações,** selecione o nome da sua aplicação Azure.

   ![Screenshot mostrando navegação de portal para uma aplicação Azure.](./media/app-service-web-tutorial-custom-domain/select-app.png)

Será apresentada a página de gestão da aplicação do Serviço de Aplicações.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

1. No painel esquerdo da página da aplicação, percorra a secção **Definições** e selecione **Scale up (Plano de Serviço de Aplicações)**.

   ![Screenshot que mostra o menu Scale up (App Service plan).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. O escalão atual da aplicação é realçado com um limite azul. Verifique se a aplicação não está no nível **de F1.** O DNS personalizado não é suportado no nível **de F1.**

   ![Screenshot que mostra os níveis de preços recomendados.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Se o plano de Serviço de Aplicações não estiver no nível **F1,** feche a página **Scale up** e salte para [mapear um registo CNAME](#map-a-cname-record).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Aumentar verticalmente o plano do Serviço de Aplicações

1. Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para opções adicionais, **selecione Ver opções adicionais**.

1. Selecione **Aplicar**.

   ![Screenshot que mostra a verificação do nível de preços.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Quando vir a notificação seguinte, significa que a operação de dimensionamento está completa.

   ![Screenshot que mostra a confirmação da operação de escala.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>Obtenha uma ID de verificação de domínio

Para adicionar um domínio personalizado à sua aplicação, tem de verificar a sua propriedade do domínio adicionando um ID de verificação como registo TXT com o seu fornecedor de domínio. No painel esquerdo da sua página de aplicações, selecione **domínios personalizados**. Copie o ID na caixa de identificação de **verificação de domínio personalizado** na página de **Domínios Personalizados** para o passo seguinte.

![Screenshot que mostra o ID na caixa de identificação de verificação de domínio personalizado.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> Adicionar IDs de verificação de domínio ao seu domínio personalizado pode impedir entradas de DNS pendentes e ajudar a evitar aquisições de subdomínios. Para domínios personalizados que previamente configurado sem este ID de verificação, deverá protegê-los do mesmo risco adicionando o ID de verificação ao seu registo DNS. Para obter mais informações sobre esta ameaça comum de alta gravidade, consulte [a aquisição de Subdomínio.](../security/fundamentals/subdomain-takeover.md)

## <a name="map-your-domain"></a>Mapear o seu domínio

Para mapear um nome DNS personalizado para o Serviço de Aplicações, pode utilizar tanto um registo CNAME, como um registo A. Siga os passos correspondentes:

- [Mapear um registo CNAME](#map-a-cname-record)
- [Mapear um registo A](#map-an-a-record)
- [Mapear um domínio de caráter universal (com um registo CNAME)](#map-a-wildcard-domain)

> [!NOTE]
> Deve utilizar os registos CNAME para todos os nomes DNS personalizados, exceto os domínios de raiz (por exemplo, `contoso.com`). Para os domínios de raiz, utilize registos A.

### <a name="map-a-cname-record"></a>Mapear um registo CNAME

No exemplo do tutorial, vai adicionar um registo CNAME ao subdomínio `www` (por exemplo, `www.contoso.com`).

Se tiver um subdomínio `www` diferente, `www` substitua-o pelo seu subdomínio (por exemplo, se `sub` o seu domínio personalizado `sub.constoso.com` for).

#### <a name="access-dns-records-with-a-domain-provider"></a>Aceder a registos DNS com um fornecedor de domínio

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Criar o registo CNAME

Mapeie um subdomínio para o nome de domínio padrão da aplicação `<app-name>.azurewebsites.net` (, onde `<app-name>` está o nome da sua aplicação). Para criar um mapeamento CNAME para o `www` subdomínio, crie dois registos:

| Tipo de registo | Anfitrião | Valor | Comentários |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | O próprio mapeamento de domínio. |
| TXT | `asuid.www` | [A verificação que recebeu mais cedo](#get-a-domain-verification-id) | O Serviço de Aplicações acede ao `asuid.<subdomain>` registo TXT para verificar a sua propriedade do domínio personalizado. |

Depois de adicionar os registos CNAME e TXT, a página de registos DNS parece ser o seguinte exemplo:

![Screenshot que mostra a navegação do portal para uma aplicação Azure.](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Ativar o mapeamento de registos CNAME no Azure

1. No painel esquerdo da página da aplicação no portal Azure, selecione **domínios personalizados**.

    ![Screenshot que mostra o menu de domínios personalizados.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na página de **domínios personalizados** da aplicação, adicione o nome DNS personalizado totalmente qualificado `www.contoso.com` à lista.

1. **Selecione Adicionar domínio personalizado**.

    ![Screenshot que mostra o item do nome do anfitrião Adicionar.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Escreva o nome de domínio completamente qualificado ao qual adicionou um registo CNAME, como `www.contoso.com`.

1. Selecione **Validar**. Aparece a página **de domínio de adicionar personalizado.**

1. Certifique-se de que **o tipo de registo hostname** está definido para **CNAME (www \. example.com ou qualquer subdomínio)**. **Selecione Adicionar domínio personalizado**.

    ![Screenshot que mostra o botão de domínio personalizado Adicionar.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **de Domínios Personalizados** da aplicação. Atualize o navegador para atualizar os dados.

    ![Screenshot que mostra a adição do disco CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Um rótulo de aviso para o seu domínio personalizado significa que ainda não está ligado a um certificado TLS/SSL. Qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para adicionar uma ligação TLS, consulte [Secure um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md).

    Se perdeu um passo ou fez um erro de erro algures mais cedo, um erro de verificação aparece na parte inferior da página.

    ![Screenshot que mostra um erro de verificação.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Mapear um registo A

No exemplo do tutorial, vai adicionar um registo A ao domínio de raiz (por exemplo, `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Copiar o endereço IP da aplicação

Para mapear um registo A, precisa do endereço IP externo da aplicação. Pode encontrar este endereço IP na página **de Domínios Personalizados** da aplicação no portal Azure.

1. No painel esquerdo da página da aplicação no portal Azure, selecione **domínios personalizados**.

   ![Screenshot que mostra o menu de domínios personalizados.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na página **De Domínios Personalizados,** copie o endereço IP da aplicação.

   ![Screenshot que mostra a navegação do portal para uma aplicação Azure.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>Aceder a registos DNS com um fornecedor de domínio

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Criar um registo A

Para mapear um registo A para uma aplicação, normalmente para o domínio raiz, crie dois registos:

| Tipo de registo | Anfitrião | Valor | Comentários |
| - | - | - |
| A | `@` | Endereço IP de [Copiar o endereço IP da aplicação](#info) | O próprio mapeamento de domínio `@` (normalmente representa o domínio raiz). |
| TXT | `asuid` | [A verificação que recebeu mais cedo](#get-a-domain-verification-id) | O Serviço de Aplicações acede ao `asuid.<subdomain>` registo TXT para verificar a sua propriedade do domínio personalizado. Para o domínio raiz, utilize `asuid` . |

> [!NOTE]
> Para adicionar um subdomínio `www.contoso.com` (como) utilizando um registo A em vez de um registo [CNAME](#map-a-cname-record)recomendado, o seu registo A e OTXT devem parecer-se com a seguinte tabela:
>
> | Tipo de registo | Anfitrião | Valor |
> | - | - | - |
> | A | `www` | Endereço IP de [Copiar o endereço IP da aplicação](#info) |
> | TXT | `asuid.www` | [A verificação que recebeu mais cedo](#get-a-domain-verification-id) |
>

Após a adição dos registos, a página de registos DNS parece ser o seguinte exemplo:

![Screenshot que mostra uma página de registos DNS.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Ativar o mapeamento de registos A na aplicação

De volta à página **de Domínios Personalizados** da aplicação no portal Azure, adicione o nome DNS personalizado totalmente qualificado (por exemplo, `contoso.com` ) à lista.

1. **Selecione Adicionar domínio personalizado**.

    ![Screenshot que mostra adicionar um nome de hospedeiro.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Escreva o nome de domínio completamente qualificado para o qual configurou o registo A, como `contoso.com`. 

1. Selecione **Validar**. É mostrada a página **de domínio do Add custom.**

1. Confirme que o **Tipo de registo de nome de anfitrião** está definido como **Registo A (www.example.com)**. **Selecione Adicionar domínio personalizado**.

    ![Screenshot que mostra adicionar um nome DNS à aplicação.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **de Domínios Personalizados** da aplicação. Atualize o navegador para atualizar os dados.

    ![Screenshot que mostra a adição de um disco A.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Um rótulo de aviso para o seu domínio personalizado significa que ainda não está ligado a um certificado TLS/SSL. Qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para adicionar uma ligação TLS, consulte [Secure um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md).
    
    Se perdeu um passo ou fez um erro de erro algures mais cedo, um erro de verificação aparece na parte inferior da página.
    
    ![Screenshot mostrando um erro de verificação.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mapear um domínio com caráteres universais

No exemplo do tutorial, vai mapear um [nome DNS com carateres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por exemplo, `*.contoso.com`) para a aplicação do Serviço de Aplicações ao adicionar um registo CNAME.

#### <a name="access-dns-records-with-a-domain-provider"></a>Aceder a registos DNS com um fornecedor de domínio

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Criar o registo CNAME

Mapeie um nome wildcard `*` para o nome de domínio padrão da aplicação `<app-name>.azurewebsites.net` (, onde está o nome da `<app-name>` sua aplicação). Para mapear o nome wildcard, crie dois registos:

| Tipo de registo | Anfitrião | Valor | Comentários |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | O próprio mapeamento de domínio. |
| TXT | `asuid` | [A verificação que recebeu mais cedo](#get-a-domain-verification-id) | O Serviço de Aplicações acede ao `asuid` registo TXT para verificar a sua propriedade do domínio personalizado. |

No domínio de exemplo `*.contoso.com`, o registo CNAME vai mapear o nome `*` para `<app-name>.azurewebsites.net`.

Quando o CNAME estiver adicionado, a página de registos DNS terá um aspeto semelhante ao seguinte exemplo:

![Screenshot que mostra a navegação para uma aplicação Azure.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Ativar o mapeamento de registos CNAME na aplicação

Agora pode adicionar qualquer subdomínio que corresponda ao nome wildcard da aplicação (por exemplo, `sub1.contoso.com` `sub2.contoso.com` e ambos `*.contoso.com` `*.contoso.com` correspondem).

1. No painel esquerdo da página da aplicação no portal Azure, selecione **domínios personalizados**.

    ![Screenshot que mostra o menu de domínios personalizados.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **Selecione Adicionar domínio personalizado**.

    ![Screenshot que mostra adicionar um nome de hospedeiro.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Escreva um nome de domínio completamente qualificado que corresponda ao domínio de caráter universal (por exemplo, `sub1.contoso.com`) e, em seguida, selecione **Validar**.

    O botão **de domínio personalizado Add** está ativado.

1. Certifique-se de que **o tipo de registo do nome anfitrião** está definido para o registo **CNAME (www \. example.com ou qualquer subdomínio)**. **Selecione Adicionar domínio personalizado**.

    ![Screenshot que mostra a adição de um nome DNS à aplicação.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **de Domínios Personalizados** da aplicação. Atualize o navegador para atualizar os dados.

1. Selecione o **+** ícone novamente para adicionar outro domínio personalizado que corresponda ao domínio wildcard. Por exemplo, adicione `sub2.contoso.com`.

    ![Screenshot que mostra a adição de um disco CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > Um rótulo de aviso para o seu domínio personalizado significa que ainda não está ligado a um certificado TLS/SSL. Qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para adicionar uma ligação TLS, consulte [Secure um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md).
    
## <a name="test-in-a-browser"></a>Teste num browser

Navegue pelos nomes DNS que configuraste anteriormente (por exemplo, `contoso.com` `www.contoso.com` , , e `sub1.contoso.com` `sub2.contoso.com` ).

![Screenshot que mostra navegação para uma aplicação Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Resolver 404 "Não Encontrado"

Se receber um erro HTTP 404 (Não Encontrado) quando navegar no URL do seu domínio personalizado, verifique se o seu domínio se resolve com o endereço IP da sua aplicação utilizando <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>. Caso contrário, pode ser por causa de uma das seguintes razões:

- O domínio personalizado configurado está a faltar um registo A ou um registo CNAME.
- O cliente do browser colocou em cache o endereço IP antigo do seu domínio. Limpe a cache e volte a testar a resolução do DNS. Num computador Windows, limpe a cache com `ipconfig /flushdns`.

## <a name="migrate-an-active-domain"></a>Migrar um domínio ativo

Para migrar um site em direto e o respetivo nome de domínio DNS para o Serviço de Aplicações, consulte [Migrar um nome DNS ativo para o Serviço de Aplicações do Azure](manage-custom-dns-migrate-domain.md).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Redirecionar para um diretório personalizado

Por predefinição, o Serviço de Aplicações direciona os pedidos Web para o diretório de raiz do código da sua aplicação. Mas certas estruturas web não começam no diretório de raiz. Por exemplo, o [Laravel](https://laravel.com/) é iniciado no subdiretório `public`. Para continuar o `contoso.com` exemplo do DNS, esta aplicação é acessível em `http://contoso.com/public` , mas você pretende `http://contoso.com` dirigir-se ao `public` diretório. Este passo não envolve a resolução do DNS, mas trata-se de personalizar o diretório virtual.

Para personalizar um diretório virtual para aplicações do Windows, selecione definições de **aplicação** no painel esquerdo da sua página de aplicações web. 

> [!NOTE]
> As aplicações linux não têm esta página. Para alterar a raiz do site para aplicações Linux, consulte os guias de configuração específicos da linguagem[(PHP,](configure-language-php.md?pivots=platform-linux#change-site-root)por exemplo).

Na parte inferior da página, o diretório de raiz virtual `/` aponta para `site\wwwroot` por predefinição, que é o diretório de raiz do código da aplicação. Altere-o para apontar para `site\wwwroot\public`, por exemplo, e guarde as alterações.

![Screenshot que mostra personalizar um diretório virtual.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Após o fim da operação, a sua aplicação deve devolver a página certa no caminho raiz (por exemplo, `http://contoso.com` ).

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a gestão de domínios personalizados com scripts utilizando o [Azure CLI](/cli/azure/install-azure-cli) ou [o Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>CLI do Azure

O comando seguinte adiciona um nome DNS personalizado configurado a uma aplicação do Serviço de Aplicações.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Para obter mais informações, veja [Map a custom domain to a web app](scripts/cli-configure-custom-domain.md) (Mapear um domínio personalizado a uma aplicação Web).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O comando seguinte adiciona um nome DNS personalizado configurado a uma aplicação do Serviço de Aplicações.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Para obter mais informações, veja [Assign a custom domain to a web app](scripts/powershell-configure-custom-domain.md) (Atribuir um domínio personalizado a uma aplicação Web).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Mapear um subdomínio usando um registo CNAME.
> * Mapear um domínio de raiz usando um registo A.
> * Mapear um domínio wildcard usando um registo CNAME.
> * Redirecione o URL padrão para um diretório personalizado.
> * Automatize o mapeamento de domínio com scripts.

Continue até ao próximo tutorial para aprender a ligar um certificado TLS/SSL personalizado a uma aplicação web.

> [!div class="nextstepaction"]
> [Proteja um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md)
