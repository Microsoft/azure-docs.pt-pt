---
title: Migrar um nome dNS ativo
description: Saiba como migrar um nome de domínio DNS personalizado que já está atribuído a um site ao vivo para o Azure App Service sem qualquer tempo de inatividade.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 79bd0a19a9bd8ebd100ed80ca0206656d73ef76c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672364"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrar um nome dNS ativo para o Serviço de Aplicações Azure

Este artigo mostra-lhe como migrar um nome DNS ativo para [o Azure App Service](../app-service/overview.md) sem qualquer tempo de inatividade.

Quando migra um site ao vivo e o seu nome de domínio DNS para o App Service, esse nome DNS já está a servir tráfego ao vivo. Pode evitar o tempo de inatividade na resolução do DNS durante a migração, ligando preventivamente o nome DNS ativo à sua app App Service.

Se não está preocupado com o tempo de inatividade na resolução do DNS, consulte [mapeie um nome dNS personalizado existente para o Serviço de Aplicações Azure](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar este how-to:

- [Certifique-se de que a sua aplicação App Service não está em nível FREE](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Ligue o nome de domínio preventivamente

Quando liga um domínio personalizado preventivamente, realiza ambos os seguintes antes de efazer quaisquer alterações nos seus registos DNS:

- Verificar a propriedade do domínio
- Ativar o nome de domínio para a sua aplicação

Quando finalmente migrar o seu nome DNS personalizado do antigo site para a aplicação App Service, não haverá tempo de inatividade na resolução do DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Criar registo de verificação de domínio

Para verificar a propriedade do domínio, adicione um registo TXT. Os mapas de registo tXT de _awcheck.&lt; subdomínio>_ para _ &lt;o nome de aplicação>.azurewebsites.net_. 

O registo do TXT de que precisas depende do registo do DNS que queres migrar. Por exemplo, consulte a`@` tabela seguinte (normalmente representa o domínio raiz):

| Exemplo de registo dNS | Hospedeiro TXT | Valor TXT |
| - | - | - |
| \@(raiz) | _awverificar_ | _&lt;nome de aplicação>.azurewebsites.net_ |
| www (sub) | _awcheck.www_ | _&lt;nome de aplicação>.azurewebsites.net_ |
| \*(wildcard) | _awcheck.\*_ | _&lt;nome de aplicação>.azurewebsites.net_ |

Na sua página de registos dNS, note o tipo de registo do nome DNS que pretende migrar. O Serviço de Aplicações suporta mapeamentos de registos CNAME e A.

> [!NOTE]
> Para certos fornecedores, como `awverify.*` o CloudFlare, não é um registo válido. Use `*` apenas em vez disso.

> [!NOTE]
> Os `*` registos wildcard não validarão subdomínios com um registo cname existente. Pode ser necessário criar explicitamente um disco TXT para cada subdomínio.


### <a name="enable-the-domain-for-your-app"></a>Ativar o domínio para a sua aplicação

No [portal Azure,](https://portal.azure.com)na navegação à esquerda da página da aplicação, selecione **domínios Personalizados.** 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **de domínios Personalizados,** selecione o **+** ícone ao lado de Adicionar nome de **anfitrião**.

![Adicionar nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digite o nome de domínio totalmente qualificado para o `www.contoso.com`que adicionou o registo TXT, como . Para um domínio \*wildcard (como .contoso.com), pode utilizar qualquer nome DNS que corresponda ao domínio wildcard. 

Selecione **Validar**.

O botão **Adicionar nome de anfitrião** é ativado. 

Certifique-se de que o tipo de **registo hostname** está definido para o tipo de registo DNS que pretende migrar.

Selecione **Adicionar nome de anfitrião**.

![Adicionar o nome DNS à aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Poderá demorar algum tempo até que o nome de anfitrião novo seja refletido na página **Domínios personalizados** da aplicação. Experimente atualizar o browser para atualizar os dados.

![Registo CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

O seu nome DNS personalizado está agora ativado na sua aplicação Azure. 

## <a name="remap-the-active-dns-name"></a>Remapear o nome DNS ativo

A única coisa que resta fazer é remapear o seu registo dNS ativo para apontar para o Serviço de Aplicações. Neste momento, ainda aponta para o seu antigo site.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Copiar o endereço IP da aplicação (apenas um registo)

Se estiver a mapear um disco CNAME, ignore esta secção. 

Para remapear um registo A, precisa do endereço IP externo da aplicação app Service, que é mostrado na página de **domínios Personalizados.**

Feche a página **Add hostname** selecionando **X** no canto superior direito. 

Na página **Domínios personalizados**, copie o endereço IP da aplicação.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Atualizar o registo dNS

De volta à página de registos dNS do seu fornecedor de domínio, selecione o registo DNS para remapear.

Para `contoso.com` o exemplo do domínio radicular, remape o registo A ou CNAME como os exemplos na tabela a seguir: 

| Exemplo fQDN | Tipo de registo | Anfitrião | Valor |
| - | - | - | - |
| contoso.com (raiz) | A | `@` | Endereço IP de [Copiar o endereço IP da aplicação](#info) |
| www\.contoso.com (sub) | CNAME | `www` | _&lt;nome de aplicação>.azurewebsites.net_ |
| \*.contoso.com (wildcard) | CNAME | _\*_ | _&lt;nome de aplicação>.azurewebsites.net_ |

Guarde as suas definições.

As consultas de DNS devem começar a resolver a sua aplicação de Serviço de Aplicações imediatamente após a propagação do DNS.

## <a name="active-domain-in-azure"></a>Domínio ativo em Azure

Pode migrar um domínio personalizado ativo em Azure, entre subscrições ou dentro da mesma subscrição. No entanto, tal migração sem tempo de inatividade requer a aplicação de origem e a aplicação alvo é atribuída ao mesmo domínio personalizado em determinado momento. Por isso, é necessário certificar-se de que as duas aplicações não são implantadas na mesma unidade de implementação (internamente conhecida como webspace). Um nome de domínio pode ser atribuído a apenas uma aplicação em cada unidade de implementação.

Pode encontrar a unidade de implantação da sua aplicação olhando `<deployment-unit>.ftp.azurewebsites.windows.net`para o nome de domínio do URL FTP/S . Verifique e certifique-se de que a unidade de implementação é diferente entre a aplicação de origem e a aplicação alvo. A unidade de implementação de uma aplicação é determinada pelo plano de Serviço de [Aplicações](overview-hosting-plans.md) em que se encontra. É selecionado aleatoriamente pelo Azure quando crias o plano e não podes ser alterado. O Azure apenas garante que dois planos estão na mesma unidade de implantação quando os cria no mesmo grupo de [recursos *e* na mesma região](app-service-plan-manage.md#create-an-app-service-plan), mas não tem qualquer lógica para garantir que os planos estão em diferentes unidades de implantação. A única maneira de criar um plano numa unidade de implantação diferente é continuar a criar um plano num novo grupo de recursos ou região até obter uma unidade de implantação diferente.

## <a name="next-steps"></a>Passos seguintes

Saiba como ligar um certificado SSL personalizado ao Serviço de Aplicações.

> [!div class="nextstepaction"]
> [Ligue um certificado SSL ao Serviço de Aplicações Azure](configure-ssl-bindings.md)
