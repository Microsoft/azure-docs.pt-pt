---
title: Domínios personalizados no Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Faça a gestão de domínios personalizados no Proxy de aplicações do Azure AD para que o URL para a aplicação é o mesmo, independentemente de onde os utilizadores aceder a ele.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59663346fce415d73609f09345048ff321f1a234
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359837"
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Trabalhar com domínios personalizados no Proxy de aplicações do Azure AD

Quando publica um aplicativo por meio do Proxy de aplicações do Azure Active Directory, vai criar um URL externo para os seus utilizadores aceder quando trabalham remotamente. Este URL obtém o domínio predefinido *yourtenant.msappproxy.net*. Por exemplo, se publicou uma aplicação com o nome despesas e o inquilino com o nome Contoso, em seguida, o URL externo seria `https://expenses-contoso.msappproxy.net`. Se pretender utilizar o seu próprio nome de domínio, configure um domínio personalizado para a sua aplicação. 

Recomendamos que configure domínios personalizados para as suas aplicações sempre que possível. Alguns dos benefícios de domínios personalizados incluem:

- Os utilizadores acedem à aplicação com o mesmo URL, esteja ele trabalhando dentro ou fora da sua rede.
- Se todas as suas aplicações tem os mesmos URLs internos e externos, em seguida, ligações num aplicativo que apontam para outro continuam a funcionar mesmo fora da rede empresarial. 
- Pode controlar a sua imagem corporativa e criar os URLs que pretende. 


## <a name="configure-a-custom-domain"></a>Configurar um domínio personalizado

### <a name="prerequisites"></a>Pré-requisitos

Antes de configurar um domínio personalizado, certifique-se de que tem os seguintes requisitos preparados: 
- R [adicionado ao Azure Active Directory do domínio verificado](../fundamentals/add-custom-domain.md).
- Um certificado personalizado para o domínio, na forma de um ficheiro PFX. 
- Uma aplicação no local [publicados através do Proxy de aplicações](application-proxy-add-on-premises-application.md).

### <a name="configure-your-custom-domain"></a>Configurar o seu domínio personalizado

Quando tem essas três requisitos prontos, siga estes passos para configurar o seu domínio personalizado:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** e escolha a aplicação que pretende gerir.
3. Selecione **Proxy de aplicações**. 
4. No campo URL externo, utilize a lista pendente para selecionar o seu domínio personalizado. Se não vir o seu domínio na lista, em seguida, ele ainda não foi verificado ainda. 
5. Selecione **guardar**
5. O **certificado** campo foi desabilitado se torna habilitado. Selecione este campo. 

   ![Clique aqui para carregar um certificado](./media/application-proxy-configure-custom-domain/certificate.png)

   Se já carregado um certificado para este domínio, o campo de certificado apresenta as informações do certificado. 

6. Carregue o certificado do PFX e introduza a palavra-passe do certificado. 
7. Selecione **guardar** para guardar as alterações. 
8. Adicionar uma [registo de DNS](../../dns/dns-operations-recordsets-portal.md) que redireciona o novo URL externo para o domínio msappproxy.net.
9. Verifique se o registo DNS está configurado corretamente ao utilizar o [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) comando para ver se o URL externo está acessível e de domínio msapproxy.net aparece como um alias.

>[!TIP] 
>Apenas terá de carregar um certificado por domínio personalizado. Depois de carregar um certificado, pode escolher o domínio personalizado ao publicar uma nova aplicação e não tem de efetuar qualquer configuração adicional, exceto para o registo DNS. 

## <a name="manage-certificates"></a>Gerir certificados

### <a name="certificate-format"></a>Formato do certificado
Não existe nenhuma restrição os métodos de assinatura de certificado. Criptografia de curva elíptica (ECC), nome alternativo do requerente (SAN) e outros tipos de certificado comuns são todos suportados. 

Pode utilizar um certificado de caráter universal, desde que o caráter universal corresponda ao URL externo pretendido. 

### <a name="changing-the-domain"></a>Alterar o domínio
Todos os domínios verificados aparecem na lista pendente de URL externo para a sua aplicação. Para alterar o domínio, basta Atualize esse campo para a aplicação. Se o domínio que pretende não estiver na lista, [adicioná-la como um domínio verificado](../fundamentals/add-custom-domain.md). Se selecionar um domínio que não têm um certificado associado ainda, siga os passos 5 a 7 para adicionar o certificado. Em seguida, certifique-se de que atualizar o registo DNS para redirecionar a partir do URL externo de novo. 

### <a name="certificate-management"></a>Gestão de certificados
Pode utilizar o mesmo certificado para vários aplicativos, a menos que os aplicativos compartilham um anfitrião externo. 

Receberá um aviso quando um certificado expira, solicitando que carregue outro certificado através do portal. Se o certificado é revogado, os seus utilizadores poderão ver um aviso de segurança quando o acesso à aplicação. Não realizar verificações de revogação de certificados.  Para atualizar o certificado para um determinado aplicativo, navegue para a aplicação e siga os passos 5 a 7 para configurar domínios personalizados nos aplicativos publicados para carregar um novo certificado. Se o certificado antigo não está a ser utilizado por outros aplicativos, é eliminado automaticamente. 

Atualmente os toda a gestão de certificados é por meio de páginas de aplicativos individuais, por isso terá de gerir os certificados no contexto das aplicações relevantes. 

## <a name="next-steps"></a>Passos Seguintes
* [Ativar o início de sessão único](application-proxy-configure-single-sign-on-with-kcd.md) às suas aplicações publicadas com autenticação do Azure AD.
* [Ativar o acesso condicional](application-proxy-integrate-with-sharepoint-server.md) às suas aplicações publicadas.
* [Adicionar o seu nome de domínio personalizado para o Azure AD](../fundamentals/add-custom-domain.md)


