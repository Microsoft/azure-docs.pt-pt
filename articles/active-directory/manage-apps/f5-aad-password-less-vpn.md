---
title: Azure AD garantir acesso híbrido com F5 VPN| Microsoft Docs
description: Tutorial para integração única de sign-on (SSO) do Azure Ative Directory (SSO) com F5 BIG-IP para VPN sem palavra-passe
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e177f1ce55d803f54bb2553078441557e5c191
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730892"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>Tutorial para integração única de sign-on do Azure Ative Directory com F5 BIG-IP para VPN sem palavra-passe

Neste tutorial, aprenda a integrar a solução de camada privada virtual (SSL-VPN) baseada em BIG-IP da F5 com o Azure Ative Directory (AD) para acesso híbrido seguro (SHA).

A integração de um BIG-IP SSL-VPN com AD Azure proporciona [muitos benefícios fundamentais,](f5-aad-integration.md)incluindo:

- Melhoria da governação da confiança Zero através da [pré-autenticação e autorização da AZure AD](../../app-service/overview-authentication-authorization.md)

- [Autenticação sem palavra-passe para o serviço VPN](https://www.microsoft.com/security/business/identity/passwordless)

- Gerir identidades e aceder a partir de um único plano de controlo - O [portal Azure](https://portal.azure.com/#home)

Apesar destes grandes acrescentações de valor, a VPN clássica continua, no entanto, baseada na noção de um perímetro de rede, onde a confiança está no interior e não fidedigno do exterior. Este modelo já não é eficaz na concretização de uma verdadeira postura do Zero Trust, uma vez que os ativos corporativos já não estão confinados às paredes de um centro de dados empresarial, mas sim em ambientes multi-nuvens sem limites fixos. Por esta razão, encorajamos os nossos clientes a considerar em mudar para uma abordagem mais orientada para a identidade na gestão [do acesso por aplicação.](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="scenario-description"></a>Descrição do cenário

Neste cenário, a instância APM BIG-IP do serviço SSL-VPN será configurada como Um Prestador de Serviços SAML (SP) e Azure AD torna-se o IDP SAML confiável, fornecendo pré-autenticação. O sign-on único (SSO) da Azure AD é então fornecido através da autenticação baseada em sinistros para o BIG-IP APM, proporcionando uma experiência de acesso VPN sem costura.

![Imagem mostra arquitetura ssl-vpn](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>Todas as cordas ou valores de exemplo referenciados ao longo deste guia devem ser substituídos por estes para o seu ambiente real.

## <a name="prerequisites"></a>Pré-requisitos

Experiência prévia ou conhecimento de F5 BIG-IP não é necessário, no entanto, você precisará:

- Uma [subscrição gratuita](https://azure.microsoft.com/trial/get-started-active-directory/) da AZure AD ou superior

- As identidades dos utilizadores devem ser [sincronizadas do seu diretório no local](../hybrid/how-to-connect-sync-whatis.md) para a Azure AD.

- Uma conta com [permissões](../roles/permissions-reference.md#application-administrator) de administração de aplicações AD da Azure

- Uma infraestrutura BIG-IP existente com encaminhamento do tráfego de clientes de e para o BIG-IP ou [implementar uma Edição Virtual BIG-IP em Azure](f5-bigip-deployment-guide.md).

- Um registo para o serviço VPN publicado pela BIG-IP terá de existir em DNS públicos, ou no ficheiro local de um cliente de teste durante os testes

- O BIG-IP deve ser a provisionado com os certificados SSL necessários para serviços de publicação em HTTPS.

Familiarizar-se com [a terminologia F5 BIG-IP](https://www.f5.com/services/resources/glossary) também ajudará a compreender os vários componentes que são referenciados ao longo do tutorial.

>[!NOTE]
>O Azure está em constante evolução, por isso não se surpreenda se encontrar alguma nuance entre as instruções deste guia e o que vê no portal Azure. As imagens são de BIG-IP v15, no entanto, permanecem relativamente semelhantes a v13.1.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Adicione F5 BIG-IP da galeria AD AZure

A criação de um fundo de federação SAML entre o BIG-IP permite ao Azure AD BIG-IP entregar a pré-autenticação e [o Acesso Condicional](../conditional-access/overview.md) ao AD Azure, antes de conceder acesso ao serviço VPN publicado.

1. Inscreva-se no portal AD AZure usando uma conta com direitos de administração de aplicações

2. A partir do painel de navegação à esquerda, selecione o **serviço Azure Ative Directory**

3. Aceda às **Aplicações empresariais** e a partir da fita superior selecione **Nova aplicação**.

4. Procure F5 na galeria e selecione **F5 BIG-IP APM AD integração**.

5. Forneça um nome para a aplicação, seguido por **Add/Create** para que seja adicionado ao seu inquilino. O utilizador pode ver o nome como um ícone nos portais de aplicação Azure e Office 365. O nome deve refletir esse serviço específico. Por exemplo, VPN.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

1. Com as suas novas propriedades de aplicação F5 à vista, vá para **Gerir**  >  **o sign-on single**

2. Na página de método **de inscrição** única, selecione **SAML**. Salte o pedido para guardar as definições de inscrição única selecionando **Não, vou guardar mais tarde**.

3. No **único sinal de configuração com** o menu SAML, selecione o ícone de caneta para **configuração SAML básica** para fornecer os seguintes detalhes:

   - Substitua o **URL de identificação** pré-definido pelo URL para o seu serviço publicado BIG-IP. Por exemplo, `https://ssl-vpn.contoso.com`

   - Faça o mesmo com a caixa de texto **URL de resposta,** incluindo a rota do ponto final SAML. Por exemplo, `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - Só nesta configuração, a aplicação funcionaria num modo iniciado pelo IDP, onde o Azure AD emite ao utilizador uma afirmação SAML antes de redirecionar para o serviço SAML BIG-IP. Para aplicações que não suportam o modo iniciado pelo IDP, especifique o **URL de inscrição** para o serviço SAML BIG-IP. Por exemplo, `https://ssl-vpn.contoso.com`.

   - Para o url logout, introduza o ponto final de logótipo único (SLO) pré-pendido pelo cabeçalho do anfitrião do serviço que está a ser publicado. Por exemplo, `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   O fornecimento de um URL SLO garante que uma sessão de utilizador é terminada em ambas as extremidades, o BIG-IP e o AD Azure, após a indicação do utilizador. O BIG-IP APM também oferece uma [opção](https://support.f5.com/csp/article/K12056) para terminar todas as sessões ao chamar um URL de aplicação específico.

![A imagem mostra a configuração básica do saml](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>De TMOS v16 o ponto final DA SLO SAML mudou para /saml/sp/profile/redirect/slo

4. **Selecione Guardar** antes de sair do menu de configuração SAML e saltar a indicação de teste SSO.

Observe as propriedades da secção **Atributos & Do Utilizador,** uma vez que a Azure AD as emitirá aos utilizadores para a autenticação APM BIG-IP.

![A imagem mostra as reclamações de atributos do utilizador](media/f5-sso-vpn/user-attributes-claims.png)

Sinta-se livre para adicionar quaisquer outras reclamações específicas que o seu serviço publicado BIG-IP possa esperar, embora notando que quaisquer reclamações definidas para além do conjunto padrão só serão emitidas se existirem em Azure AD, como atributos povoados. Da mesma forma, [as funções](../hybrid/how-to-connect-fed-group-claims.md) de diretório ou os membros do grupo também precisam de ser definidos contra um objeto de utilizador em Azure AD antes de poderem ser emitidos como uma reivindicação.

![Imagem mostra link de descarregamento de metadados da federação](media/f5-sso-vpn/saml-signing-certificate.png)

Os certificados de assinatura SAML criados pela Azure AD têm uma vida útil de três anos, pelo que precisarão de ser geridos através da orientação publicada pela Azure AD.

### <a name="azure-ad-authorization"></a>Autorização AZure AD

Por padrão, o Azure AD apenas emitirá fichas aos utilizadores a quem tenha sido concedido acesso a um serviço.

1. Ainda na visão de configuração da aplicação, selecione **Utilizadores e grupos**

2. Selecione **+ Adicionar utilizador** e no menu 'Adicionar Atribuição' selecione **Utilizadores e grupos**

3. No diálogo **de Utilizadores e grupos,** adicione os grupos de utilizadores autorizados a aceder à VPN, seguidos por **Select**  >  **Assign**

![A imagem mostra a adição de ligação de utilizador ](media/f5-sso-vpn/add-user-link.png)

4. Isto completa a parte AD Azure do fundo da federação SAML. O BIG-IP APM pode agora ser configurado para publicar o serviço SSL-VPN e configurado com um conjunto de propriedades correspondente para completar o fundo, para pré-autenticação SAML.

## <a name="big-ip-apm-configuration"></a>Configuração de APM BIG-IP

### <a name="saml-federation"></a>Federação SAML

A secção seguinte cria o prestador de serviços SAML BIG-IP e os correspondentes objetos DE IDP SAML necessários para completar o serviço VPN com Azure AD.

1. Aceda aos **serviços**  >  locais do prestador de serviços da **Federação** de Acesso  >  **SAML** e  >   selecione **Criar**

![Imagem mostra configuração SAML BIG-IP](media/f5-sso-vpn/bigip-saml-configuration.png)

2. Introduza um **Nome** e o mesmo valor **de ID da Entidade** que definiu no AD Azure anteriormente, e o Anfitrião FQDN que será usado para ligar à aplicação

![Imagem mostra criação de novo serviço SAML SP](media/f5-sso-vpn/create-new-saml-sp.png)

   As definições **de nome** SP só são necessárias se o ID da entidade não for uma correspondência exata da parte do nome anfitrião do URL publicado, ou se não estiver no formato URL baseado em nome anfitrião regular. Fornecer o esquema externo e o nome de anfitrião do pedido que está a ser publicado se a identificação da entidade for `urn:ssl-vpn:contosoonline` .

3. Desloque-se para baixo para selecionar o novo **objeto SAML SP** e selecione **Conectores Bind/UnBind IDP**.

![Imagem mostra criar federação com serviço SP local](media/f5-sso-vpn/federation-local-sp-service.png)

4. Selecione **Criar novo conector IDP** e a partir do menu suspenso selecione **A partir de metadados**

![Os programas de imagem criam novo conector IDP](media/f5-sso-vpn/create-new-idp-connector.png)

5. Navegue no ficheiro XML dos metadados da federação que descarregou anteriormente e forneça um **Nome de Fornecedor de Identidade** para o objeto APM que representará o IDP SAML externo

6. **Selecione Adicionar New Row** para selecionar o novo conector IDP externo Azure AD.

![Imagem mostra conector iDP externo](media/f5-sso-vpn/external-idp-connector.png)

7. Selecione **Update** para ligar o objeto SAML SP ao objeto SAML IDP e, em seguida, selecione **OK**.

![Imagem mostra SAML IDP usando SP](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Configuração do Webtop

Os seguintes passos permitem que o SSL-VPN seja oferecido aos utilizadores através do portal web proprietário da BIG-IP.

1. Aceda a  >  **listas**  >  **webtops de** acesso e selecione **Criar**.

2. Dê ao portal um nome e desemote o tipo para **Full**. Por exemplo, `Contoso_webtop`.

3. Ajuste as restantes preferências e, em seguida, **selecione Terminado**.

![Imagem mostra configuração do webtop](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>Configuração de VPN

A capacidade VPN é composta por vários elementos, cada um controlando um aspeto diferente do serviço geral.

1. Aceda a conectividade **de**  >  **acesso/acesso**  >  **à rede VPN (VPN)**  >  **IPV4 Lease Pools** e selecione **Create**.

2. Fornecer um nome para o conjunto de endereços IP que estão sendo atribuídos a clientes VPN. Por exemplo, Contoso_vpn_pool

3. Definir tipo para **gama de endereços IP** e fornecer um IP de início e fim, seguido por **Add** and **Finished**.

![Imagem mostra configuração vpn](media/f5-sso-vpn/vpn-configuration.png)

Uma lista de acesso à Rede fornece o serviço com definições IP e DNS a partir do pool VPN, permissões de encaminhamento de utilizadores, e também pode lançar aplicações se necessário.

1. Ir para **Access**  >  **Connectivity/VPN: Network Access (VPN)**  >  **Network Access Lists** e selecionar **Criar**.

2. Fornecer um nome para a lista de acesso VPN e legenda, por exemplo, Contoso-VPN seguido por **Terminado**.

![Imagem mostra configuração VPN na lista de acesso à rede](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. A partir da fita superior, selecione **Definições de Rede** e adicione as definições abaixo:

   • **Versão IP suportada**: IPV4

   • **Piscina de Arrendamento IPV4**: Selecione a piscina VPN criada anteriormente, por exemplo, Contoso_vpn_pool

![Imagem mostra piscina contoso vpn](media/f5-sso-vpn/contoso-vpn-pool.png)

   As opções de Definições de Cliente podem ser usadas para impor restrições à forma como o tráfego do cliente é encaminhado quando uma VPN é estabelecida.

4. Selecione **Terminado** e vá ao separador DNS/Anfitriões para adicionar as definições:

   • **IpV4 Primary Name Server**: IP do servidor DNS do seu ambiente

   • **Sufixo de domínio padrão DNS**: O sufixo de domínio para esta ligação VPN específica. Por exemplo, contoso.com

![A imagem mostra sufixo de domínio predefinido](media/f5-sso-vpn/domain-suffix.png)

[O artigo F5](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) fornece detalhes sobre o ajuste das definições restantes de acordo com a sua preferência.

Um perfil de ligação BIG-IP é agora necessário para configurar as definições para cada um dos tipos de clientes VPN que o serviço VPN precisa de suportar. Por exemplo, Windows, OSX e Android.

1. Aceda aos perfis  >  **de conectividade de acesso/VPN**  >    >   e selecione **Add.**

2. Forneça um nome de perfil e desa um perfil dos pais para **/Common/conectividade,** por exemplo, Contoso_VPN_Profile.

![Os programas de imagem criam um novo perfil de conectividade](media/f5-sso-vpn/create-connectivity-profile.png)

A [documentação](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) da F5 fornece mais detalhes sobre o apoio ao cliente.

## <a name="access-profile-configuration"></a>Configuração de perfil de acesso

Com os objetos VPN configurados, é necessária uma política de acesso para ativar o serviço de autenticação SAML.

1. Aceda a  >  **perfis de acesso/políticas**  >  **Perfis de acesso (políticas por sessão)** e selecione **Criar**

2. Forneça um nome de perfil e para o tipo de perfil **selecione All**, por exemplo, Contoso_network_access

3. Percorra para baixo para adicionar pelo menos um idioma à lista **de línguas aceites** e selecione **Terminado**

![A imagem mostra propriedades gerais](media/f5-sso-vpn/general-properties.png)

4. **Selecione Editar** no campo Per-Session Política do novo perfil de acesso, para que o editor de política visual seja lançado num separado separado do separador de navegador.

![A imagem mostra a política por sessão](media/f5-sso-vpn/per-session-policy.png)

5. Selecione o **+** sinal e no pop-up **selecione Autenticação**  >  **SAML Auth**  >  **Add Item**.

6. Na configuração DE AUTENTICAção SP de autenticação SAML, selecione o objeto VPN SAML SP que criou anteriormente, seguido de **Save**.

![Imagem mostra autenticação saml](media/f5-sso-vpn/saml-authentication.png)

7. Selecione **+** para o ramo de sucesso da auth SAML.

8. A partir do separador Atribuição, selecione **Advanced Resource Assign** seguido de Add **Item**

![A imagem mostra a atribuição de recursos avançados](media/f5-sso-vpn/advance-resource-assign.png)

9. No pop-up, selecione **New Entry** e, em seguida, **Adicione/Apague**.

10. Na janela da criança, selecione **o Acesso à Rede** e, em seguida, selecione o perfil de Acesso à Rede criado anteriormente

![Imagem mostra adicionar nova entrada de acesso à rede](media/f5-sso-vpn/add-new-entry.png)

11. Mude para o **separador Webtop** e adicione o objeto Webtop criado anteriormente.

![Imagem mostra adicionar objeto webtop](media/f5-sso-vpn/add-webtop-object.png)

12. Selecione **Update** seguido de **Save**.

13. Selecione o link na caixa de Deny superior para alterar o ramo de sucesso para **permitir** e depois **guardar**.

![Imagem mostra novo editor de política visual](media/f5-sso-vpn/vizual-policy-editor.png)

14. Empenhar essas definições selecionando a Política de **Acesso de Aplicar** e fechar o separador de editor de política visual.

![Imagem mostra novo gestor de políticas de acesso](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>Publicar o serviço VPN

Com todas as definições no lugar, o APM agora requer um servidor virtual frontal para ouvir os clientes que se conectam à VPN.

1. Selecione **a** lista de  >  **servidores virtuais de tráfego** local e  >   selecione **Criar**.

2. Fornecer um **Nome** para o servidor virtual VPN, por exemplo, **VPN_Listener**.

3. Forneça ao servidor virtual um **endereço** de destino IP não uused que tenha encaminhamento no local para receber o tráfego do cliente

4. Desagure a porta de serviço para **443 HTTPS** e certifique-se de que os programas estatais **estão ativados**

![Imagem mostra novo servidor virtual](media/f5-sso-vpn/new-virtual-server.png)

5. Desa esta medida o **perfil HTTP** para http e adicione o Perfil SSL (Cliente) para o certificado SSL público que adere ele como parte dos requisitos prévios.

![Imagem mostra perfil ssl](media/f5-sso-vpn/ssl-profile.png)

6. No âmbito da Política de Acesso, desa estale o **Perfil de Acesso** e o Perfil de **Conectividade** para utilizar os objetos VPN criados.

![Imagem mostra política de acesso](media/f5-sso-vpn/access-policy.png)

7. Selecione **Terminado** quando terminar.

8.  O seu serviço SSL-VPN já foi publicado e acessível através da SHA, quer diretamente através do seu URL, quer através dos portais de aplicação da Microsoft.

## <a name="additional-resources"></a>Recursos adicionais

- [O fim das senhas, ir sem palavras-passe](https://www.microsoft.com/security/business/identity/passwordless)

- [O que é o Acesso Condicional?](../conditional-access/overview.md)

- [Quadro do Microsoft Zero Trust para permitir o trabalho remoto](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Cinco passos para integração completa da aplicação com a Azure AD](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>Passos seguintes

Abra um browser num cliente remoto do Windows e navegue para o url do **serviço BIG-IP VPN**. Depois de autenticar a Azure AD, verá o portal de webtop BIG-IP e o lançador VPN.

![Imagem mostra lançador VPN](media/f5-sso-vpn/vpn-launcher.png)

A seleção do azulejo VPN instalará o cliente BIG-IP Edge e estabelecerá uma ligação VPN configurada para SHA.
A aplicação F5 VPN também deve ser visível como um recurso-alvo no Acesso Condicionado AD Azure. Consulte a nossa [orientação](../conditional-access/concept-conditional-access-policies.md) para a construção de políticas de acesso condicional e também para permitir aos utilizadores a [autenticação sem senha](https://www.microsoft.com/security/business/identity/passwordless)Azure.