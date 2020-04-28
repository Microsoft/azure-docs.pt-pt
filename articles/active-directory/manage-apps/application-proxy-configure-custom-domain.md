---
title: Domínios personalizados em Procuração de Aplicação AD Azure [ Microsoft Docs
description: Configure e gerencie domínios personalizados em Procuração de Aplicação AD Azure.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f35658a75adb4d4c6c279e45087e741b8117e65
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481386"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Configure domínios personalizados com procuração de aplicação ad azure

Ao publicar uma aplicação através do Azure Ative Directory Application Proxy, cria um URL externo para os seus utilizadores. Este URL obtém o domínio padrão *yourtenant.msappproxy.net*. Por exemplo, se publicar uma aplicação chamada *Despesas* do seu inquilino chamada *Contoso,* o URL externo é *\/https: /expenses-contoso.msappproxy.net*. Se quiser usar o seu próprio nome de domínio em vez de *msappproxy.net,* pode configurar um domínio personalizado para a sua aplicação. 

## <a name="benefits-of-custom-domains"></a>Benefícios dos domínios personalizados

É uma boa ideia configurar domínios personalizados para as suas apps sempre que possível. Algumas razões para usar domínios personalizados incluem:

- As ligações entre apps funcionam mesmo fora da rede corporativa. Sem um domínio personalizado, se a sua aplicação tiver ligações internas codificadas para alvos fora do Proxy de Aplicação, e os links não forem resolúveis externamente, quebrarão. Quando os seus URLs internos e externos são os mesmos, evita este problema. Se não conseguir utilizar domínios personalizados, consulte [links codificados redirecionamento para aplicações publicadas com o Procurador de Aplicação AD Azure](../application-proxy-link-translation.md) para outras formas de resolver este problema. 
  
- Os seus utilizadores terão uma experiência mais fácil, pois podem chegar à aplicação com o mesmo URL de dentro ou fora da sua rede. Não precisam de aprender diferentes URLs internos e externos, ou rastrear a sua localização atual. 

- Pode controlar a sua marca e criar os URLs que deseja. Um domínio personalizado pode ajudar a construir a confiança dos seus utilizadores, porque os utilizadores vêem e usam um nome familiar em vez de *msappproxy.net*.

- Algumas configurações só funcionarão com domínios personalizados. Por exemplo, você precisa de domínios personalizados para apps que usam linguagem de marcação de afirmação de segurança (SAML), como quando você está usando Serviços da Federação de Diretório Ativo (AD FS), mas não é capaz de usar WS-Federation. Para mais informações, consulte [Trabalhar com aplicações conscientes de sinistros no Proxy](application-proxy-configure-for-claims-aware-applications.md)de Aplicação . 

Se não conseguir fazer com que os URLs internos e externos correspondam, não é tão importante usar domínios personalizados, mas ainda pode tirar partido dos outros benefícios. 

## <a name="dns-configuration-options"></a>Opções de configuração DNS

Existem várias opções para configurar a sua configuração DNS, dependendo dos seus requisitos:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Mesmo URL interno e externo, comportamento interno e externo diferente 

Se não quiser que os seus utilizadores internos sejam direcionados através do Proxy de Aplicação, pode configurar um *DNS de cérebro dividido*. Uma infraestrutura DNS dividida direciona os anfitriões internos para um servidor de nome de domínio interno, e anfitriões externos para um servidor de nome de domínio externo, para resolução de nomes. 

![DNS "Split-brain"](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>URLs internos e externos diferentes 

Se os URLs internos e externos forem diferentes, não é necessário configurar o comportamento do cérebro dividido, porque o encaminhamento do utilizador é determinado pelo URL. Neste caso, altera apenas o DNS externo e encaminha o URL externo para o ponto final do Proxy de Aplicação. 

Quando seleciona um domínio personalizado para um URL externo, uma barra de informação mostra a entrada CNAME que precisa adicionar ao fornecedor externo de DNS. Pode sempre ver esta informação indo para a página de procuração da **aplicação** da aplicação.

## <a name="set-up-and-use-custom-domains"></a>Configurar e usar domínios personalizados

Para configurar uma aplicação no local para utilizar um domínio personalizado, precisa de um domínio personalizado de Diretório Ativo Azure verificado, um certificado PFX para o domínio personalizado e uma aplicação no local para configurar. 

### <a name="create-and-verify-a-custom-domain"></a>Criar e verificar um domínio personalizado

Para criar e verificar um domínio personalizado:

1. No Diretório Ativo Azure, selecione nomes de **domínio personalizados** na navegação à esquerda e, em seguida, selecione **Adicionar domínio personalizado**. 
1. Introduza o nome de domínio personalizado e selecione **Adicionar Domínio**. 
1. Na página de domínio, copie as informações de registo TXT para o seu domínio. 
1. Vá ao seu registo de domínio e crie um novo disco TXT para o seu domínio, com base nas suas informações de DNS copiadas.
1. Depois de registar o domínio, na página do domínio no Diretório Ativo Azure, selecione **Verificar**. Uma vez **verificado**o estado de domínio, pode utilizar o domínio em todas as configurações do Anúncio Azure, incluindo o Proxy de Aplicação. 

Para obter instruções mais detalhadas, consulte Adicionar o seu nome de [domínio personalizado utilizando o portal de diretório Ativo Azure](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Configure uma aplicação para usar um domínio personalizado

Para publicar a sua aplicação através do Application Proxy com um domínio personalizado:

1. Para uma nova aplicação, no Azure Ative Directory, selecione **aplicações da Enterprise** na navegação à esquerda. Selecione **Nova aplicação**. Na secção de **aplicações No local,** selecione **Adicionar uma aplicação no local**. 
   
   Para uma aplicação já em **aplicações da Enterprise,** selecione-a a partir da lista e, em seguida, selecione **proxy de Aplicação** na navegação esquerda. 

2. Na página de definições de Procuração de Aplicação, introduza um **Nome** se estiver a adicionar a sua própria aplicação no local.

3.  No campo **Url Interno,** introduza o URL interno para a sua aplicação.
   
4. No campo **Url Externo,** deixe cair a lista e selecione o domínio personalizado que pretende utilizar.
   
5. Selecione **Adicionar**.
   
   ![Selecione domínio personalizado](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Se o domínio já tiver um certificado, o campo **certificado** apresenta as informações do certificado. Caso contrário, selecione o campo **Certificado.** 
   
   ![Clique para carregar um certificado](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Na página de **certificadoS SSL,** navegue e selecione o seu ficheiro de certificado PFX. Introduza a palavra-passe para o certificado e **selecione'Sou Certificado de Upload**. Para obter mais informações sobre certificados, consulte a secção [Certificados para domínios personalizados.](#certificates-for-custom-domains)
   
   ![Certificado de upload](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Um domínio personalizado só precisa do seu certificado carregado uma vez. Depois disso, o certificado carregado é aplicado automaticamente quando utiliza o domínio personalizado para outras aplicações.
   
8. Se tiver adicionado um certificado, na página proxy da **Aplicação,** selecione **Guardar**. 
   
9. Na barra de informações na página proxy da **Aplicação,** note a entrada CNAME que precisa adicionar à sua zona DNS. 
   
   ![Adicionar entrada CNAME DNS](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Siga as instruções em [Registos DNS de Gestão e recordes utilizando o portal Azure](../../dns/dns-operations-recordsets-portal.md) para adicionar um registo DNS que redireciona o novo URL externo para o domínio *msappproxy.net.*
   
11. Para verificar se o registo DNS está configurado corretamente, utilize o comando [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) para confirmar que o seu URL externo é acessível e que o domínio *msapproxy.net* aparece como um pseudónimo.

A sua aplicação está agora configurada para utilizar o domínio personalizado. Certifique-se de atribuir utilizadores à sua aplicação antes de testá-la ou libertá-la. 

Para alterar o domínio de uma aplicação, selecione um domínio diferente da lista de **dropdown** no URL Externo na página de proxy da **aplicação.** Faça o upload de um certificado para o domínio atualizado, se necessário, e atualize o registo DNS. Se não vir o domínio personalizado que deseja na lista de dropdown em **URL Externo,** pode não ser verificado.

Para obter instruções mais detalhadas para o Proxy da Aplicação, consulte [Tutorial: Adicione um pedido no local para acesso remoto através de Application Proxy no Diretório Ativo Azure](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certificados para domínios personalizados

Um certificado cria a ligação TLS segura para o seu domínio personalizado. 

### <a name="certificate-formats"></a>Formatos de certificados

Deve utilizar um certificado PFX, para garantir que todos os certificados intermédios necessários estão incluídos. O certificado deve incluir a chave privada.

Não há restrições nos métodos de assinatura de certificado. Criptografia da Curva Elíptica (ECC), Nome Alternativo sujeito (SAN) e outros tipos de certificados comuns são suportados. 

Pode utilizar certificados wildcard desde que o wildcard corresponda ao URL externo. Deve utilizar certificados wildcard para [aplicações wildcard](application-proxy-wildcard.md). Se pretender utilizar o certificado para aceder também a subdomínios, deve adicionar os wildcards de subdomínio como nomes alternativos sujeitos no mesmo certificado. Por exemplo, um certificado para * \*.adventure-works.com* não funcionará para * \*.apps.adventure-works.com* a menos que adicione * \*.apps.adventure-works.com* como um nome alternativo. 

Pode utilizar certificados emitidos pela sua própria infraestrutura de chaves públicas (PKI) se a cadeia de certificados estiver instalada nos seus dispositivos clientes. Intune pode implementar estes certificados para dispositivos geridos. Para dispositivos não geridos, tem de instalar manualmente estes certificados. 

Não recomendamos a utilização de um CA de raiz privada, uma vez que a CA de raiz privada também teria de ser empurrada para as máquinas de clientes, o que pode introduzir muitos desafios.

### <a name="certificate-management"></a>Gestão de certificados

Toda a gestão de certificados é através das páginas de candidatura individual. Aceda à página de procuração de pedidos de **aplicação** do pedido para aceder ao campo **Certificado.**

Pode utilizar o mesmo certificado para várias aplicações. Se um certificado carregado funcionar com outra aplicação, será aplicado automaticamente. Não será solicitado que o carregue novamente quando adicionar ou configurar a aplicação. 

Quando um certificado expira, recebe um aviso a dizer-lhe para fazer o upload de outro certificado. Se o certificado for revogado, os seus utilizadores poderão ver um aviso de segurança ao aceder à aplicação. Para atualizar o certificado para uma aplicação, navegue para a página proxy da **Aplicação** para a aplicação, selecione **Certificado**, e faça upload de um novo certificado. Se o certificado antigo não estiver a ser utilizado por outras aplicações, é apagado automaticamente. 

## <a name="next-steps"></a>Passos seguintes
* Ative um [único início de sessão](application-proxy-configure-single-sign-on-with-kcd.md) nas suas aplicações publicadas com autenticação Azure AD.
* [Ative o Acesso Condicional](../conditional-access/overview.md) às suas aplicações publicadas.

