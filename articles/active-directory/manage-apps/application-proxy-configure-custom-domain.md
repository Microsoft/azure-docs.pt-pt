---
title: Domínios personalizados no Azure Proxy de Aplicativo do AD | Microsoft Docs
description: Configurar e gerenciar domínios personalizados no Azure Proxy de Aplicativo do AD.
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
ms.openlocfilehash: 189b8666adde0eedcb451655657a4a82dc5e4fec
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062536"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Configurar domínios personalizados com o Azure Proxy de Aplicativo do AD

Ao publicar um aplicativo por meio do Proxy de Aplicativo do Azure Active Directory, você cria uma URL externa para seus usuários. Esta URL Obtém o domínio padrão *yourtenant.msappproxy.net*. Por exemplo, se você publicar um aplicativo chamado *despesas* em seu locatário chamado *contoso*, a URL externa será *https: \//Expenses-contoso.msappproxy.net*. Se você quiser usar seu próprio nome de domínio em vez de *msappproxy.net*, poderá configurar um domínio personalizado para seu aplicativo. 

## <a name="benefits-of-custom-domains"></a>Benefícios de domínios personalizados

É uma boa ideia configurar domínios personalizados para seus aplicativos sempre que possível. Alguns motivos para usar domínios personalizados incluem:

- Os links entre aplicativos funcionam mesmo fora da rede corporativa. Sem um domínio personalizado, se seu aplicativo tiver links internos embutidos em código para destinos fora do proxy de aplicativo e os links não forem resolvidos externamente, eles serão interrompidos. Quando suas URLs internas e externas são as mesmas, você evita esse problema. Se não for possível usar domínios personalizados, consulte [redirecionar links codificados para aplicativos publicados com o Azure proxy de aplicativo do AD](../application-proxy-link-translation.md) para outras maneiras de resolver esse problema. 
  
- Os usuários terão uma experiência mais fácil, pois podem acessar o aplicativo com a mesma URL de dentro ou fora de sua rede. Eles não precisam aprender diferentes URLs internas e externas, ou controlar seu local atual. 

- Você pode controlar sua identidade visual e criar as URLs desejadas. Um domínio personalizado pode ajudar a criar a confiança dos usuários, pois os usuários veem e usam um nome familiar em vez de *msappproxy.net*.

- Algumas configurações só funcionarão com domínios personalizados. Por exemplo, você precisa de domínios personalizados para aplicativos que usam Security Assertion Markup Language (SAML), como quando você está usando Serviços de Federação do Active Directory (AD FS) (AD FS), mas não é possível usar o WS-Federation. Para obter mais informações, consulte [trabalhar com aplicativos com reconhecimento de declaração no proxy de aplicativo](application-proxy-configure-for-claims-aware-applications.md). 

Se não for possível fazer as URLs internas e externas corresponderem, não será tão importante usar domínios personalizados, mas você ainda poderá aproveitar os outros benefícios. 

## <a name="dns-configuration-options"></a>Opções de configuração de DNS

Há várias opções para configurar sua configuração de DNS, dependendo de seus requisitos:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Mesma URL interna e externa, diferente comportamento interno e externo 

Se você não quiser que os usuários internos sejam direcionados por meio do proxy de aplicativo, poderá configurar um *DNS de divisão-Brain*. Uma infraestrutura de DNS de divisão direciona hosts internos para um servidor de nome de domínio interno e hosts externos para um servidor de nome de domínio externo, para resolução de nomes. 

![DNS de divisão-Brain](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Diferentes URLs internas e externas 

Se as URLs internas e externas forem diferentes, você não precisará configurar o comportamento de divisão-Brain, pois o roteamento do usuário é determinado pela URL. Nesse caso, você altera apenas o DNS externo e roteia a URL externa para o ponto de extremidade do proxy de aplicativo. 

Quando você seleciona um domínio personalizado para uma URL externa, uma barra de informações mostra a entrada CNAME que você precisa adicionar ao provedor DNS externo. Você sempre pode ver essas informações indo para a página proxy de **aplicativo** do aplicativo.

## <a name="set-up-and-use-custom-domains"></a>Configurar e usar domínios personalizados

Para configurar um aplicativo local para usar um domínio personalizado, você precisa de um Azure Active Directory domínio personalizado verificado, um certificado PFX para o domínio personalizado e um aplicativo local para configurar. 

### <a name="create-and-verify-a-custom-domain"></a>Criar e verificar um domínio personalizado

Para criar e verificar um domínio personalizado:

1. Em Azure Active Directory, selecione **nomes de domínio personalizados** no painel de navegação esquerdo e, em seguida, selecione **Adicionar domínio personalizado**. 
1. Insira seu nome de domínio personalizado e selecione **Adicionar domínio**. 
1. Na página domínio, copie as informações de registro TXT para seu domínio. 
1. Acesse seu registrador de domínio e crie um novo registro TXT para seu domínio, com base nas informações de DNS copiadas.
1. Depois de registrar o domínio, na página do domínio em Azure Active Directory, selecione **verificar**. Depois que o status de domínio for **verificado**, você poderá usar o domínio em todas as suas configurações do Azure AD, incluindo o proxy de aplicativo. 

Para obter instruções mais detalhadas, consulte [Adicionar seu nome de domínio personalizado usando o portal de Azure Active Directory](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Configurar um aplicativo para usar um domínio personalizado

Para publicar seu aplicativo por meio do proxy de aplicativo com um domínio personalizado:

1. Para um novo aplicativo, em Azure Active Directory, selecione **aplicativos empresariais** no painel de navegação esquerdo. Selecione **Nova aplicação**. Na seção **aplicativos locais** , selecione **Adicionar um aplicativo local**. 
   
   Para um aplicativo que já está em **aplicativos corporativos**, selecione-o na lista e, em seguida, selecione **proxy de aplicativo** no painel de navegação esquerdo. 

2. Na página Configurações de proxy de aplicativo, insira um **nome** se você estiver adicionando seu próprio aplicativo local.

3.  No campo **URL interna** , insira a URL interna para seu aplicativo.
   
4. No campo **URL externa** , clique na lista suspensa e selecione o domínio personalizado que você deseja usar.
   
5. Selecione **Adicionar**.
   
   ![Selecionar domínio personalizado](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Se o domínio já tiver um certificado, o campo **certificado** exibirá as informações do certificado. Caso contrário, selecione o campo **certificado** . 
   
   ![Clique para carregar um certificado](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Na página **certificado SSL** , navegue até e selecione o arquivo de certificado PFX. Insira a senha para o certificado e selecione **carregar certificado**. Para obter mais informações sobre certificados, consulte a seção [certificados para domínios personalizados](#certificates-for-custom-domains) .
   
   ![Carregar certificado](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Um domínio personalizado precisa apenas de seu certificado carregado uma vez. Depois disso, o certificado carregado é aplicado automaticamente quando você usa o domínio personalizado para outros aplicativos.
   
8. Se você adicionou um certificado, na página **proxy de aplicativo** , selecione **salvar**. 
   
9. Na barra de informações na página **proxy de aplicativo** , observe a entrada CNAME que você precisa adicionar à zona DNS. 
   
   ![Adicionar entrada DNS CNAME](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Siga as instruções em [gerenciar registros DNS e conjuntos de registros usando o portal do Azure](../../dns/dns-operations-recordsets-portal.md) para adicionar um registro DNS que redireciona a nova URL externa para o domínio *msappproxy.net* .
   
11. Para verificar se o registro DNS está configurado corretamente, use o comando [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) para confirmar se a URL externa está acessível e se o domínio *msapproxy.net* aparece como um alias.

Seu aplicativo agora está configurado para usar o domínio personalizado. Certifique-se de atribuir usuários ao seu aplicativo antes de testá-lo ou liberá-lo. 

Para alterar o domínio de um aplicativo, selecione um domínio diferente na lista suspensa na **URL externa** na página **proxy de aplicativo** do aplicativo. Carregue um certificado para o domínio atualizado, se necessário, e atualize o registro DNS. Se você não vir o domínio personalizado que deseja na lista suspensa na **URL externa**, talvez ele não seja verificado.

Para obter instruções mais detalhadas sobre o proxy de aplicativo, consulte [tutorial: adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certificados para domínios personalizados

Um certificado cria a conexão SSL segura para seu domínio personalizado. 

### <a name="certificate-formats"></a>Formatos de certificado

Você deve usar um certificado PFX para garantir que todos os certificados intermediários necessários estejam incluídos. O certificado deve incluir a chave privada.

Não há restrição para os métodos de assinatura de certificado. O ECC (criptografia de curva elíptica), o SAN (nome alternativo da entidade) e outros tipos de certificado comuns têm suporte. 

Você pode usar certificados curinga, desde que o curinga corresponda à URL externa. Você deve usar certificados curinga para [aplicativos curinga](application-proxy-wildcard.md). Se você quiser usar o certificado para também acessar subdomínios, deverá adicionar os curingas de subdomínio como nomes alternativos da entidade no mesmo certificado. Por exemplo, um certificado para *\*. Adventure-Works.com* não funcionará para *\*. Apps.Adventure-Works.com* , a menos que você adicione *\*. Apps.Adventure-Works.com* como um nome alternativo da entidade. 

Você pode usar certificados emitidos por sua própria PKI (infraestrutura de chave pública) se a cadeia de certificados estiver instalada em seus dispositivos cliente. O Intune pode implantar esses certificados em dispositivos gerenciados. Para dispositivos não gerenciados, você deve instalar manualmente esses certificados.

Não é uma boa ideia usar uma autoridade de certificação raiz privada. A AC raiz privada também precisaria ser enviada por push para computadores cliente, o que apresenta muitos desafios. 

### <a name="certificate-management"></a>Gestão de certificados

Todo o gerenciamento de certificados é por meio das páginas individuais do aplicativo. Acesse a página **proxy de aplicativo** do aplicativo para acessar o campo **certificado** .

Você pode usar o mesmo certificado para vários aplicativos. Se um certificado carregado funcionar com outro aplicativo, ele será aplicado automaticamente. Você não será solicitado a carregá-lo novamente quando adicionar ou configurar o aplicativo. 

Quando um certificado expirar, você receberá um aviso informando que você deseja carregar outro certificado. Se o certificado for revogado, os usuários poderão ver um aviso de segurança ao acessar o aplicativo. Para atualizar o certificado para um aplicativo, navegue até a página **proxy de aplicativo** do aplicativo, selecione **certificado**e carregue um novo certificado. Se o certificado antigo não estiver sendo usado por outros aplicativos, ele será excluído automaticamente. 

## <a name="next-steps"></a>Passos seguintes
* [Habilite o logon único](application-proxy-configure-single-sign-on-with-kcd.md) para seus aplicativos publicados com a autenticação do Azure AD.
* [Habilite o acesso condicional](../conditional-access/technical-reference.md#cloud-apps-assignments) para seus aplicativos publicados.

