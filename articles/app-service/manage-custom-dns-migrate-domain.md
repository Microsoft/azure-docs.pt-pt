---
title: Migrar um nome DNS ativo
description: Saiba como migrar um nome de domínio DNS personalizado que já está atribuído a um site ao vivo para o Azure App Service sem qualquer tempo de inatividade.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: e1b50675bef0f883ff617b3098a742d3491b3c13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484308"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrar um nome DNS ativo para o Azure App Service

Este artigo mostra-lhe como migrar um nome DNS ativo para [o Azure App Service](../app-service/overview.md) sem qualquer tempo de inatividade.

Quando migra um site ao vivo e o seu nome de domínio DNS para o Serviço de Aplicações, esse nome DNS já está a servir tráfego ao vivo. Pode evitar tempo de inatividade na resolução do DNS durante a migração, vinculando o nome DNS ativo à sua aplicação App Service preventivamente.

Se não estiver preocupado com o tempo de inatividade na resolução dns, consulte [mapear um nome DNS personalizado existente para o Serviço de Aplicações Azure](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar este como::

- [Certifique-se de que a sua aplicação de Serviço de Aplicações não está no nível FREE](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Ligue o nome de domínio preventivamente

Quando liga um domínio personalizado preventivamente, realiza ambos os seguintes antes de efetivo quaisquer alterações nos registos DNS existentes:

- Verificar a propriedade do domínio
- Ativar o nome de domínio da sua aplicação

Quando, finalmente, migrar o nome DNS personalizado do antigo site para a aplicação Do Serviço de Aplicações, não haverá tempo de inatividade na resolução do DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="get-domain-verification-id"></a>Obtenha iD de verificação de domínio

Obtenha o ID de verificação de domínio para a sua aplicação seguindo os passos no [Get domain Verification ID](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id).

### <a name="create-domain-verification-record"></a>Criar registo de verificação de domínio

Para verificar a propriedade do domínio, adicione um registo TXT para verificação de domínio. O nome de anfitrião para o registo TXT depende do tipo de registo DNS que pretende mapear. Consulte a tabela seguinte `@` (normalmente representa o domínio raiz):

| DnS exemplo de registo | Anfitrião TXT | Valor TXT |
| - | - | - |
| \@ (raiz) | _asuid_ | [ID de verificação de domínio para a sua aplicação](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| www(sub) | _asuid.www_ | [ID de verificação de domínio para a sua aplicação](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| \* (wildcard) | _asuid_ | [ID de verificação de domínio para a sua aplicação](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |

Na sua página de registos DNS, note o tipo de registo do nome DNS que pretende migrar. O Serviço de Aplicações suporta mapeamentos de registos CNAME e A.

> [!NOTE]
> `*`Os registos wildcard não validam subdomínios com um registo cname existente. Pode ser necessário criar explicitamente um registo TXT para cada subdomínio.

### <a name="enable-the-domain-for-your-app"></a>Ativar o domínio da sua aplicação

1. No [portal Azure,](https://portal.azure.com)na navegação à esquerda da página da aplicação, selecione **domínios personalizados**. 

    ![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na página **de domínios personalizados,** selecione **Adicionar domínio personalizado**.

    ![Adicionar nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Digite o nome de domínio totalmente qualificado que pretende migrar, que corresponda ao registo TXT que cria, como `contoso.com` `www.contoso.com` , ou `*.contoso.com` . Selecione **Validar**.

    O botão **de domínio personalizado Add** está ativado. 

1. Certifique-se de que **o tipo de gravação do Hostname** está definido para o tipo de registo DNS que pretende migrar. Selecione **Adicionar nome de anfitrião**.

    ![Adicionar o nome DNS à aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Poderá demorar algum tempo até que o nome de anfitrião novo seja refletido na página **Domínios personalizados** da aplicação. Experimente atualizar o browser para atualizar os dados.

    ![Registo CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    O seu nome DNS personalizado está agora ativado na sua aplicação Azure. 

## <a name="remap-the-active-dns-name"></a>Remapia o nome DNS ativo

A única coisa que resta a fazer é remapping seu registo de DNS ativo para apontar para o Serviço de Aplicações. Neste momento, ainda aponta para o teu antigo site.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Copie o endereço IP da aplicação (apenas registo)

Se estiver a remexeparar um registo CNAME, ignore esta secção. 

Para remaptar um registo A, precisa do endereço IP externo da aplicação app App Service, que é mostrado na página **de domínios personalizados.**

Na página **Domínios personalizados**, copie o endereço IP da aplicação.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Atualize o registo do DNS

De volta à página de registos DNS do seu fornecedor de domínio, selecione o registo DNS para remapar.

Para o exemplo do `contoso.com` domínio raiz, remape o registo A ou CNAME como os exemplos no quadro seguinte: 

| Exemplo FQDN | Tipo de registo | Anfitrião | Valor |
| - | - | - | - |
| contoso.com (raiz) | A | `@` | Endereço IP de [Copiar o endereço IP da aplicação](#info) |
| www \. contoso.com (sub) | CNAME | `www` | _&lt;appname>.azurewebsites.net_ |
| \*.contoso.com (wildcard) | CNAME | _\*_ | _&lt;appname>.azurewebsites.net_ |

Guarde as suas definições.

As consultas de DNS devem começar a ser resolvidas para a sua aplicação de Serviço de Aplicações imediatamente após a propagação do DNS acontecer.

## <a name="migrate-domain-from-another-app"></a>Migrar domínio de outra app

Pode migrar um domínio personalizado ativo em Azure, entre subscrições ou dentro da mesma subscrição. No entanto, tal migração sem tempo de inatividade requer que a app de origem e a aplicação-alvo sejam atribuídas ao mesmo domínio personalizado num determinado momento. Por isso, é necessário certificar-se de que as duas aplicações não são implantadas na mesma unidade de implementação (internamente conhecida como webspace). Um nome de domínio pode ser atribuído a apenas uma aplicação em cada unidade de implantação.

Pode encontrar a unidade de implantação da sua aplicação olhando para o nome de domínio do URL FTP/S `<deployment-unit>.ftp.azurewebsites.windows.net` . Verifique e certifique-se de que a unidade de implementação é diferente entre a aplicação de origem e a aplicação-alvo. A unidade de implantação de uma aplicação é determinada pelo plano de Serviço de [Aplicações](overview-hosting-plans.md) em que está. É selecionado aleatoriamente pelo Azure quando se cria o plano e não se pode mudar. O Azure apenas garante que dois planos estão na mesma unidade de implantação quando [os cria no mesmo grupo de recursos *e* na mesma região](app-service-plan-manage.md#create-an-app-service-plan), mas não tem qualquer lógica para garantir que os planos estão em diferentes unidades de implantação. A única maneira de criar um plano numa unidade de implantação diferente é continuar a criar um plano num novo grupo de recursos ou região até obter uma unidade de implantação diferente.

## <a name="next-steps"></a>Passos seguintes

Saiba como ligar um certificado TLS/SSL personalizado ao Serviço de Aplicações.

> [!div class="nextstepaction"]
> [Proteja um nome DNS personalizado com uma ligação TLS no Azure App Service](configure-ssl-bindings.md)
