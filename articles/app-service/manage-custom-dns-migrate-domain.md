---
title: Migrar um nome DNS do Active Directory - App Service do Azure | Documentos da Microsoft
description: Saiba como migrar um nome de domínio DNS personalizado já está atribuído a um site em direto para o serviço de aplicações do Azure sem qualquer período de inatividade.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6215230a52bcb5c44f54747b447dc5f64e6af650
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999096"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrar um nome DNS do Active Directory para o serviço de aplicações do Azure

Este artigo mostra como migrar um nome DNS do Active Directory para [App Service do Azure](../app-service/overview.md) sem qualquer período de inatividade.

Quando migra um site em direto e o respetivo nome de domínio DNS para o serviço de aplicações, esse nome DNS já está a enviar a tráfego em direto. Pode evitar o tempo de inatividade na resolução DNS durante a migração ao vincular o nome DNS do Active Directory para a sua aplicação de serviço de aplicações preventivamente.

Se não estiver preocupado com tempo de inatividade na resolução DNS, consulte [mapear um nome DNS existente personalizado para o serviço de aplicações do Azure](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este procedimentos:

- [Certifique-se de que o seu serviço de aplicações não está no escalão gratuito](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Vincular o nome de domínio preventivamente

Quando se associar um domínio personalizado preventivamente, realizar ambos os procedimentos seguintes antes de fazer alterações aos seus registos DNS:

- Verificar a propriedade do domínio
- Ativar o nome de domínio para a sua aplicação

Quando finalmente migrar seu nome DNS personalizado do site antigo para a aplicação de serviço de aplicações, isso significa que haverá sem períodos de indisponibilidade na resolução de DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Criar registo de verificação de domínio

Para verificar a propriedade do domínio, adicione um registo TXT. O registo TXT mapeia desde _awverify.&lt; subdomínio >_ para  _&lt;appname >. azurewebsites_. 

O registo TXT que precisa depende o registo DNS que pretende migrar. Para obter exemplos, consulte a tabela seguinte (`@` normalmente representa o domínio de raiz):

| Exemplo de registo DNS | TXT Host | Valor TXT |
| - | - | - |
| \@ (root) | _awverify_ | _&lt;appname>.azurewebsites.net_ |
| www (sub) | _awverify.www_ | _&lt;appname>.azurewebsites.net_ |
| \* (com carateres universais) | _awverify.\*_ | _&lt;appname>.azurewebsites.net_ |

Na sua página de registos DNS, tenha em atenção o tipo de registo do nome DNS que pretende migrar. Serviço de aplicações suporta mapeamentos de CNAME e registos.

> [!NOTE]
> Para alguns fornecedores, como CloudFlare, `awverify.*` não é um registo válido. Utilize `*` apenas em vez disso.

> [!NOTE]
> Caráter universal `*` registos não será validado subdomínios com registo de um CNAME existente. Terá de criar explicitamente um registo TXT para cada subdomínio.


### <a name="enable-the-domain-for-your-app"></a>Ativar o domínio para a sua aplicação

Na [portal do Azure](https://portal.azure.com), na navegação da esquerda da página da aplicação, selecione **domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na **domínios personalizados** página, selecione a **+** ícone junto a **Adicionar nome de anfitrião**.

![Adicionar nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escreva o nome de domínio completamente qualificado que adicionou o registo TXT, tais como `www.contoso.com`. Para um domínio de caráter universal (como \*. contoso.com), pode utilizar qualquer nome DNS que corresponde ao domínio de caráter universal. 

Selecione **Validar**.

O botão **Adicionar nome de anfitrião** é ativado. 

Certifique-se de que **tipo de registo de nome de anfitrião** está definido como o tipo de registo DNS que pretende migrar.

Selecione **Adicionar nome de anfitrião**.

![Adicionar o nome DNS à aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Poderá demorar algum tempo até que o nome de anfitrião novo seja refletido na página **Domínios personalizados** da aplicação. Experimente atualizar o browser para atualizar os dados.

![Registo CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

O nome DNS personalizado está agora ativado na sua aplicação do Azure. 

## <a name="remap-the-active-dns-name"></a>Remapear o nome DNS do Active Directory

A única falta fazer é o remapeamento o Active Directory registo DNS para apontar para o serviço de aplicações. Direita agora, ele ainda aponta para o seu site antigo.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Copie o endereço IP da aplicação (apenas para um registo)

Se o remapeamento de um registo CNAME, ignore esta secção. 

Para remapear um registo, terá de endereço IP externo da aplicação de serviço de aplicações, que é mostrado na **domínios personalizados** página.

Fechar o **Adicionar nome de anfitrião** página ao selecionar **X** no canto superior direito. 

Na página **Domínios personalizados**, copie o endereço IP da aplicação.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Atualize o registo DNS

Na página de registos de DNS do seu fornecedor de domínios, selecione o registo DNS para mapear novamente.

Para o `contoso.com` exemplo do domínio de raiz, Remapeie o registo A ou CNAME como nos exemplos na tabela a seguir: 

| Exemplo de FQDN | Tipo de registo | Anfitrião | Value |
| - | - | - | - |
| contoso.com (root) | A | `@` | Endereço IP de [Copiar o endereço IP da aplicação](#info) |
| www\.contoso.com (sub) | CNAME | `www` | _&lt;appname>.azurewebsites.net_ |
| \*. contoso.com (com carateres universais) | CNAME | _\*_ | _&lt;appname>.azurewebsites.net_ |

Guarde as suas definições.

Consultas DNS devem começar a resolver para o seu serviço de aplicações imediatamente após a propagação de DNS acontece.

## <a name="next-steps"></a>Passos Seguintes

Saiba como vincular um certificado SSL personalizado no serviço de aplicações.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure App Service](app-service-web-tutorial-custom-ssl.md) (Vincular um certificado SSL personalizado já existente ao Serviço de Aplicações do Azure)
