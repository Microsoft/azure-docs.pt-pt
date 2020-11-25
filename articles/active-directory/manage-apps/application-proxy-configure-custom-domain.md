---
title: Domínios personalizados no Azure AD Application Proxy
description: Configure e gere domínios personalizados no Azure AD Application Proxy.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: bef120e754c84798b2d1b48f4f00fbb8f5fb3c1d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997618"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Configurar domínios personalizados com o Proxy de Aplicações do AAD

Ao publicar uma aplicação através do Azure Ative Directory Application Proxy, cria um URL externo para os seus utilizadores. Este URL obtém o domínio predefinido *yourtenant.msappproxy.net*. Por exemplo, se publicar uma aplicação denominada *Despesas* no seu inquilino chamada *Contoso,* o URL externo é *https: \/ /expenses-contoso.msappproxy.net*. Se quiser utilizar o seu próprio nome de domínio em vez de *msappproxy.net,* pode configurar um domínio personalizado para a sua aplicação. 

## <a name="benefits-of-custom-domains"></a>Benefícios dos domínios personalizados

É uma boa ideia configurar domínios personalizados para as suas apps sempre que possível. Algumas razões para usar domínios personalizados incluem:

- As ligações entre apps funcionam mesmo fora da rede corporativa. Sem um domínio personalizado, se a sua aplicação tiver ligações internas codificadas com códigos rígidos para alvos fora do Application Proxy, e as ligações não forem externamente resolúveis, serão dissolvadas. Quando os seus URLs internos e externos são os mesmos, evita este problema. Se não conseguir utilizar domínios personalizados, consulte [redirecionar links codificados para aplicações publicadas com O Azure AD Application Proxy](./application-proxy-configure-hard-coded-link-translation.md) para outras formas de resolver este problema. 
  
- Os seus utilizadores terão uma experiência mais fácil, pois podem chegar à aplicação com o mesmo URL de dentro ou de fora da sua rede. Não precisam de aprender urls internos e externos diferentes, ou rastrear a sua localização atual. 

- Pode controlar a sua marca e criar os URLs que quiser. Um domínio personalizado pode ajudar a criar a confiança dos seus utilizadores, porque os utilizadores vêem e usam um nome familiar em vez de *msappproxy.net*.

- Algumas configurações só funcionarão com domínios personalizados. Por exemplo, você precisa de domínios personalizados para apps que usam Linguagem de Marcação de Afirmação de Segurança (SAML), como quando você está usando Ative Directory Federation Services (AD FS) mas não é capaz de usar WS-Federation. Para obter mais informações, consulte [Trabalhar com aplicações conscientes de sinistros no Application Proxy.](application-proxy-configure-for-claims-aware-applications.md) 

Se não for capaz de fazer os URLs internos e externos coincidirem, não é tão importante usar domínios personalizados, mas ainda pode aproveitar os outros benefícios. 

## <a name="dns-configuration-options"></a>Opções de configuração de DNS

Existem várias opções para configurar a sua configuração DE DNS, dependendo dos seus requisitos:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>O mesmo URL interno e externo, comportamento interno e externo diferente 

Se não quiser que os seus utilizadores internos sejam direcionados através do Application Proxy, pode configurar um *DNS de cérebro dividido*. Uma infraestrutura de DNS dividida direciona os anfitriões internos para um servidor de nome de domínio interno, e anfitriões externos para um servidor de nome de domínio externo, para resolução de nomes. 

![DNS "Split-brain"](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>URLs internos e externos diferentes 

Se os URLs internos e externos forem diferentes, não é necessário configurar o comportamento split-brain, porque o encaminhamento do utilizador é determinado pelo URL. Neste caso, você muda apenas o DNS externo, e encaminha o URL externo para o ponto final de Procuração de Aplicação. 

Ao selecionar um domínio personalizado para um URL externo, uma barra de informação mostra a entrada CNAME que precisa de adicionar ao fornecedor externo de DNS. Pode sempre ver esta informação indo para a página de procuração da **aplicação.**

## <a name="set-up-and-use-custom-domains"></a>Configurar e utilizar domínios personalizados

Para configurar uma aplicação no local para utilizar um domínio personalizado, precisa de um domínio personalizado do Azure Ative Directory, um certificado PFX para o domínio personalizado e uma aplicação no local para configurar. 

### <a name="create-and-verify-a-custom-domain"></a>Criar e verificar um domínio personalizado

Para criar e verificar um domínio personalizado:

1. No Azure Ative Directory, selecione **nomes de domínio personalizados** na navegação à esquerda e, em seguida, selecione **Adicionar domínio personalizado**. 
1. Insira o seu nome de domínio personalizado e **selecione Add Domain**. 
1. Na página de domínio, copie as informações de registo TXT para o seu domínio. 
1. Vá ao seu registo de domínio e crie um novo registo TXT para o seu domínio, com base nas informações de DNS copiadas.
1. Depois de registar o domínio, na página do domínio no Diretório Ativo Azure, selecione **Verificar**. Uma vez verificado o estado de **domínio,** pode utilizar o domínio em todas as configurações AD do Azure, incluindo o Application Proxy. 

Para obter instruções mais detalhadas, consulte [Adicionar o seu nome de domínio personalizado utilizando o portal Azure Ative Directory](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Configure uma app para usar um domínio personalizado

Para publicar a sua aplicação através do Application Proxy com um domínio personalizado:

1. Para uma nova aplicação, no Azure Ative Directory, selecione **aplicações Enterprise** na navegação à esquerda. Selecione **Nova aplicação**. Na secção **de aplicações no local,** selecione **Adicionar uma aplicação no local**. 
   
   Para uma aplicação já em **aplicações Enterprise,** selecione-a da lista e, em seguida, selecione **Application proxy** na navegação à esquerda. 

2. Na página de definições de Procuração de Aplicação, insira um **Nome** se estiver a adicionar a sua própria aplicação no local.

3.  No campo **Url Interno,** insira o URL interno para a sua aplicação.
   
4. No campo **Url Externo,** desça a lista e selecione o domínio personalizado que pretende utilizar.
   
5. Selecione **Adicionar**.
   
   ![Selecione domínio personalizado](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Se o domínio já tiver um certificado, o campo **Certificado** apresenta as informações do certificado. Caso contrário, selecione o campo **Certificado.**
   
   ![Clique para carregar um certificado](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Na página de **certificado SSL,** navegue e selecione o seu ficheiro de certificado PFX. Introduza a palavra-passe para o certificado e selecione **o Certificado de Upload**. Para obter mais informações sobre certificados, consulte os Certificados para a secção [de domínios personalizados.](#certificates-for-custom-domains) Se o certificado não for válido ou houver um problema com a palavra-passe, verá uma mensagem de erro. O [Proxy FAQ de aplicação](application-proxy-faq.md#application-configuration) contém alguns passos de resolução de problemas que pode experimentar.
   
   ![Certificado de upload](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Um domínio personalizado só precisa do seu certificado carregado uma vez. Depois disso, o certificado carregado é aplicado automaticamente quando utiliza o domínio personalizado para outras aplicações.
   
8. Se adicionar um certificado, na página **de procuração de aplicação,** selecione **Save**. 
   
9. Na barra de informações na página **de procuração de aplicação,** note a entrada CNAME que precisa de adicionar à sua zona DE DNS. 
   
   ![Adicionar entrada DE DNS CNAME](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Siga as instruções na [Manage DNS records and record sets utilizando o portal Azure](../../dns/dns-operations-recordsets-portal.md) para adicionar um registo DNS que redireciona o novo URL externo para o domínio *msappproxy.net.*
   
11. Para verificar se o registo DNS está configurado corretamente, utilize o comando [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) para confirmar que o seu URL externo está acessível e que o domínio *msapproxy.net* aparece como um pseudónimo.

A sua aplicação está agora configurada para utilizar o domínio personalizado. Certifique-se de atribuir os utilizadores à sua aplicação antes de testá-la ou lançá-la. 

Para alterar o domínio para uma aplicação, selecione um domínio diferente da lista de dropdown em **URL Externo** na página de procuração de aplicação da **aplicação.** Faça o upload de um certificado para o domínio atualizado, se necessário, e atualize o registo dns. Se não vir o domínio personalizado que pretende na lista de dropdown em **URL Externo,** poderá não ser verificado.

Para obter instruções mais detalhadas para o Application Proxy, consulte [Tutorial: Adicione um pedido de acesso remoto no local através do Application Proxy in Azure Ative Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certificados para domínios personalizados

Um certificado cria a ligação TLS segura para o seu domínio personalizado. 

### <a name="certificate-formats"></a>Formatos de certificado

Deve utilizar um certificado PFX para garantir que todos os certificados intermédios necessários estão incluídos. O certificado deve incluir a chave privada.

Os métodos de assinatura de certificado mais comuns são suportados, tais como Nome Alternativo Sujeito (SAN). 

Pode utilizar certificados wildcard desde que o wildcard corresponda ao URL externo. Você deve usar certificados wildcard para [aplicações wildcard](application-proxy-wildcard.md). Se pretender utilizar o certificado para também aceder a subdomínios, tem de adicionar os wildcards do subdomínio como nomes alternativos sujeitos no mesmo certificado. Por exemplo, um certificado para *\* .adventure-works.com* não funcionará para *\* .apps.adventure-works.com* a menos que adicione *\* .apps.adventure-works.com* como nome alternativo ao assunto. 

Pode utilizar certificados emitidos pela sua própria infraestrutura de chaves públicas (PKI) se a cadeia de certificados estiver instalada nos dispositivos do seu cliente. A Intune pode implementar estes certificados para dispositivos geridos. Para dispositivos não geridos, deve instalar manualmente estes certificados. 

Não recomendamos a utilização de uma CA de raiz privada, uma vez que a ca de raiz privada também teria de ser empurrada para as máquinas clientes, o que pode introduzir muitos desafios.

### <a name="certificate-management"></a>Gestão de certificados

Toda a gestão de certificados é através das páginas de candidatura individuais. Aceda à página **de procuração** de aplicação da aplicação para aceder ao campo **Certificado.**

Uma vez que um certificado é carregado para uma aplicação, também será automaticamente aplicado a **novas** aplicações configuradas que usam o mesmo certificado. Terá de reessaltar o certificado para aplicações existentes no seu inquilino.

Quando um certificado expira, recebe um aviso a dizer-lhe para enviar outro certificado. Se o certificado for revogado, os seus utilizadores poderão ver um aviso de segurança ao aceder à aplicação. Para atualizar o certificado para uma aplicação, navegue na página **de procuração** de aplicação para a aplicação, selecione **Certificate** e faça upload de um novo certificado. Se o certificado antigo não estiver a ser usado por outras aplicações, é apagado automaticamente. 

## <a name="next-steps"></a>Passos seguintes

* [Ative o único sign-on das](application-proxy-configure-single-sign-on-with-kcd.md) suas aplicações publicadas com autenticação AZure AD.
* [Acesso condicional](../conditional-access/concept-conditional-access-cloud-apps.md) para as suas aplicações publicadas na nuvem.