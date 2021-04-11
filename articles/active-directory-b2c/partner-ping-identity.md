---
title: Tutorial para configurar Azure Ative Directy B2C com identidade ping
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com identidade ping
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 066fd6f91b19da211a73ac12fb6dca94085399ac
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256638"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Tutorial: Configurar a identidade do ping com o Azure Ative Directory B2C para acesso híbrido seguro

Neste tutorial de amostra, aprenda a estender o Azure Ative Directory (AD) B2C com  [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) e [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) para permitir um acesso híbrido seguro.

Muitas propriedades web existentes, tais como sites de eCommerce e aplicações web que estão expostas à internet são implementadas por trás de um sistema de procuração, às vezes referido como um sistema de procuração inversa. Estes sistemas de procuração fornecem várias funções, incluindo pré-autenticação, aplicação de políticas e encaminhamento de tráfego. Os casos de uso de exemplo incluem proteger a aplicação web do tráfego web de entrada e fornecer uma gestão uniforme da sessão através de implementações distribuídas do servidor.

Na maioria dos casos, esta configuração inclui uma camada de tradução de autenticação que externaliza a autenticação a partir da aplicação web. Os proxies invertidos, por sua vez, fornecem o contexto autenticado dos utilizadores às aplicações web, de uma forma mais simples, como um valor de cabeçalho na forma clara ou digestiva. Nesta configuração, as aplicações não estão a utilizar quaisquer fichas padrão da indústria, tais como Linguagem de Marcação de Afirmação de Segurança (SAML), OAuth ou Open ID Connect (OIDC), em vez de dependerem do proxy para fornecer o contexto de autenticação e manter a sessão com o agente utilizador final, como o navegador ou a aplicação nativa. Como um serviço que funciona num "homem-no-meio", os proxies podem fornecer o controlo final da sessão. Isto também significa que o serviço de procuração deve ser altamente eficiente e escalável, não para se tornar um estrangulamento ou um único ponto de falha para as aplicações por trás do serviço de procuração. O diagrama é uma representação de uma implementação e fluxo típico de procuração inversa das comunicações.

![imagem mostra a implementação de procuração inversa](./media/partner-ping/reverse-proxy.png)

Se estiver numa situação em que pretende modernizar a plataforma de identidade nestas configurações, são levantadas as preocupações.

- Como pode o esforço de modernização das aplicações ser dissociado da modernização da plataforma de identidade?

- Como se pode estabelecer um ambiente de coexistência com a autenticação moderna e legado, consumindo do prestador de serviços de identidade modernizado?

  a. Como impulsionar a consistência da experiência do utilizador final?

  b. Como proporcionar uma única experiência de inscrição em todas as aplicações coexistindo?

Discutimos uma abordagem para resolver tais preocupações integrando o Azure AD B2C com [tecnologias PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) e [PingFederate.](https://www.pingidentity.com/en/software/pingfederate.html)

## <a name="coexistence-environment"></a>Ambiente de coexistência

Uma solução simples tecnicamente viável que também é rentável é configurar o sistema de procuração inversa para usar o sistema de identidade modernizado, delegando a autenticação.  
Neste caso, os proxies irão suportar os protocolos de autenticação modernos e utilizar a autenticação baseada no redirecionamento (passivo) que enviará o utilizador para o novo fornecedor de Identidade (IdP).

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C como fornecedor de identidade

O Azure AD B2C tem a capacidade de definir **políticas** que impulsionam diferentes experiências e comportamentos do utilizador que também são **chamados de viagens** de utilizador orquestradas a partir do final do servidor. Cada política deste tipo expõe um ponto final de protocolo que pode realizar a autenticação como se fosse um IdP. Não é necessário um tratamento especial do lado da aplicação de políticas específicas. A aplicação simplesmente faz um pedido de autenticação padrão do setor para o ponto final de autenticação específico do protocolo exposto pela política de interesse.  
O Azure AD B2C pode ser configurado para partilhar o mesmo emitente através de múltiplas políticas ou emitente único para cada política. Cada aplicação pode apontar para uma ou muitas destas políticas, fazendo um pedido de autenticação nativa protocolar e impulsionando os comportamentos desejados do utilizador, tais como edições de início de sessão, inscrição e perfis. O diagrama mostra fluxos de trabalho de aplicações OIDC e SAML.

![imagem mostra a implementação do OIDC e DOL](./media/partner-ping/azure-ad-identity-provider.png)

Embora o cenário mencionado funcione bem para aplicações modernizadas, pode ser um desafio para as aplicações antigas redirecionar adequadamente o utilizador, uma vez que o pedido de acesso às aplicações pode não incluir o contexto para a experiência do utilizador. Na maioria dos casos, a camada de procuração ou um agente integrado na aplicação web interceta o pedido de acesso.

### <a name="pingaccess-as-a-reverse-proxy"></a>PingAccess como um proxy invertido

Muitos clientes têm implementado o PingAccess como o representante inverso para desempenhar um ou muitos papéis, como notado anteriormente neste artigo. O PingAccess pode intercetar um pedido direto por meio ou como um redirecionamento que vem de um agente que está a funcionar no servidor de aplicações web.

O PingAccess pode ser configurado com OIDC, OAuth2 ou SAML para realizar a autenticação contra um fornecedor de autenticação a montante. Um único IdP a montante pode ser configurado para este fim no servidor PingAccess. O diagrama seguinte mostra esta configuração.

![imagem mostra o PingAccess com implementação OIDC](./media/partner-ping/authorization-flow.png)

Numa implantação típica do Azure AD B2C, onde várias políticas estão a expor vários **IDPs,** representa um desafio. Uma vez que o PingAccess só pode ser configurado com um único IdP a montante.  

### <a name="pingfederate-as-a-federation-proxy"></a>PingFederate como representante da federação

PingFederate é uma ponte de identidade da empresa que pode ser totalmente configurada como um fornecedor de autenticação ou um representante para outros IdPs a montante múltiplos, se necessário. O diagrama que se segue mostra esta capacidade.

![imagem mostra a implementação pingFederate](./media/partner-ping/pingfederate.png)

Esta capacidade pode ser usada para alternar contextualmente/dinamicamente ou declarativamente um pedido de entrada para uma política específica do Azure AD B2C. Segue-se uma representação do fluxo de sequência de protocolo para esta configuração.

![imagem mostra o fluxo de trabalho PingAccess e PingFederate](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma subscrição do Azure. Se não tiver uma, obtenha uma [conta gratuita.](https://azure.microsoft.com/free/)

- Um [inquilino Azure AD B2C](./tutorial-create-tenant.md) que está ligado à sua assinatura Azure.

- PingAccess e PingFederate implantados em contentores Docker ou diretamente em VMs Azure.

## <a name="connectivity"></a>Conectividade

Verifique se o seguinte está ligado.

- **Servidor PingAccess** – Capaz de comunicar com o servidor PingFederate, browser cliente, OIDC, OAuth bem conhecido e descoberta de chaves publicada pelo serviço Azure AD B2C e servidor PingFederate.

- **PingFederate servidor** – Capaz de comunicar com o servidor PingAccess, browser cliente, OIDC, OAuth bem conhecido e descoberta de chaves publicada pelo serviço Azure AD B2C.

- **Aplicação AuthN baseada em legado ou cabeçalho** – Capaz de comunicar de e para o servidor PingAccess.

- **Saml contando com a aplicação do partido** – Capaz de alcançar o tráfego do navegador a partir do cliente. Capaz de aceder aos metadados da federação SAML publicados pelo serviço Azure AD B2C.

- **Aplicação moderna** – Capaz de alcançar o tráfego do navegador a partir do cliente. Capaz de aceder ao OIDC, OAuth bem conhecido, e descoberta de chaves publicada pelo serviço Azure AD B2C.

- **REST API** – Capaz de chegar ao tráfego de um cliente nativo ou web. Capaz de aceder ao OIDC, OAuth bem conhecido, e descoberta de chaves publicada pelo serviço Azure AD B2C.

## <a name="configure-azure-ad-b2c"></a>Configurar Azure Ad B2C

Pode utilizar os fluxos básicos de utilizador ou políticas avançadas de enquadramento empresarial de identidade (IEF) para o efeito. O PingAccess gera o ponto final dos metadados com base no valor **do Emitente** utilizando a convenção de descoberta baseada na [WebFinger.](https://tools.ietf.org/html/rfc7033)
Para acompanhar esta convenção, atualize a atualização do emitente Azure AD B2C utilizando as propriedades da política nos fluxos de utilizador.

![imagem mostra as definições simbólicas](./media/partner-ping/token-setting.png)

Nas políticas avançadas, isto pode ser configurado utilizando o elemento metadados **IssuanceClaimPattern** ao valor **da AuthorityWithTfp** no perfil técnico do [emissor JWT](./jwt-issuer-technical-profile.md).

## <a name="configure-pingaccesspingfederate"></a>Configure PingAccess/PingFederate

A secção seguinte cobre a configuração necessária.
O diagrama ilustra o fluxo geral do utilizador para a integração.

![imagem mostra a integração PingAccess e PingFederate](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>Configure PingFederate como provedor de fichas

Para configurar o PingFederate como fornecedor de token para o PingAccess, garantir que a conectividade do PingFederate ao PingAccess é estabelecida seguida pela conectividade do PingAccess ao PingFederate.  
Consulte [este artigo](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) para ver os passos de configuração.

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>Configurar um pedido de PingAccess para autenticação baseada em cabeçalhos

Deve ser criada uma aplicação PingAccess para a aplicação web-alvo para autenticação baseada em cabeçalhos. Siga estes passos.

#### <a name="step-1--create-a-virtual-host"></a>Passo 1 - Criar um anfitrião virtual

>[!IMPORTANT]
>Para configurar esta solução, o hospedeiro virtual precisa de ser criado para cada aplicação. Para obter mais informações sobre considerações de configuração e seus impactos, consulte [considerações fundamentais](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html).

Siga estes passos para criar um hospedeiro virtual:

1. Ir a **Configurações**  >  **Aceder**  >  **a Anfitriões Virtuais**

2. Selecione **Adicionar Anfitrião Virtual**

3. No campo Anfitrião, insira a parte FQDN do URL de aplicação

4. No campo do Porto, insira **o 443**

5. Selecione **Guardar**

#### <a name="step-2--create-a-web-session"></a>Passo 2 - Criar uma sessão web

Siga estes passos para criar uma sessão web:

1. Navegar para **definições**  >  **aceder**  >  **a sessões web**

2. Selecione **Adicionar Sessão Web**

3. Forneça um **nome** para a sessão web.

4. Selecione o **Tipo de Cookie**, assinado **JWT** ou **JWT encriptado**

5. Proporcionar um valor único para o **Público**

6. No campo **ID** do Cliente, insira o **ID da Aplicação AZure**

7. No campo **Cliente Secreto,** insira a **Chave** que gerou para a aplicação em Azure AD.

8. Opcional - Pode criar e utilizar reclamações personalizadas com a API do Gráfico microsoft. Se optar por fazê-lo, selecione **Advanced** e desmarca as opções **de Perfil de Pedido** e **Atributos do Utilizador de Atualização.** Para obter mais informações sobre a utilização de reclamações personalizadas, consulte [uma reclamação personalizada.](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-headers.md)

9. Selecione **Guardar**

#### <a name="step-3--create-identity-mapping"></a>Passo 3 - Criar mapeamento de identidade

>[!NOTE]
>O mapeamento de identidade pode ser usado com mais de uma aplicação se mais de uma aplicação estiver à espera dos mesmos dados no cabeçalho.

Siga estes passos para criar mapeamento de identidade:

1. Ir a **Definições**  >  **Aceder a**  >  **Mapeamentos de Identidade**

2. Selecione **adicionar mapeamento de identidade**

3. Especificar um **Nome**

4. Selecione o tipo de mapeamento de identidade **de cabeçalho**

5. Na tabela **Atributo-Mapeamento,** especifique os mapeamentos necessários. Por exemplo,

   Nome do atributo | Nome do cabeçalho |
   |-------|--------|
   |upn | x-userprinciplename |
   |e-mail   |    x-e-mail  |
   |oid   | x-oid  |
   |scp   |     x-âmbito |
   |amr    |    x-amr    |

6. Selecione **Guardar**

#### <a name="step-4--create-a-site"></a>Passo 4 - Criar um site

>[!NOTE]
>Em algumas configurações, é possível que um site possa conter mais do que uma aplicação. Um site pode ser usado com mais de uma aplicação, se for caso disso.

Siga estes passos para criar um site:

1. Ir aos  >  **principais sites**

2. Selecione **Adicionar Site**

3. Especificar um **Nome** para o site

4. Insira o site **Target**. O alvo é o nome anfitrião:par de porta para o servidor que hospeda a aplicação. Não entre no caminho para a aplicação neste campo. Por exemplo, uma aplicação terá https://mysite:9999/AppName um valor-alvo do mysite: 9999

5. Indique se o alvo espera ou não ligações seguras.

6. Se o alvo estiver à espera de ligações seguras, desconfiem do Grupo de Certificados Fidedignos para **Fidedignidade Qualquer**.

7. Selecione **Guardar**

#### <a name="step-5--create-an-application"></a>Passo 5 - Criar uma aplicação

Siga estes passos para criar uma aplicação no PingAccess para cada aplicação em Azure que pretende proteger.

1. Ir para **as**  >  **Principais Aplicações**

2. Selecione **adicionar aplicação**

3. Especificar um **Nome** para a aplicação

4. Opcionalmente, introduza uma **Descrição** para a aplicação

5. Especifique a **raiz de contexto** para a aplicação. Por exemplo, uma aplicação terá https://mysite:9999/AppName uma raiz de contexto de /AppName. A raiz de contexto deve começar com um corte (/), não deve terminar com um corte (/), e pode ter mais de uma camada de profundidade, por exemplo, /Apps/MyApp.

6. Selecione o **anfitrião virtual** que criou

   >[!NOTE]
   >A combinação de hospedeiro virtual e raiz de contexto deve ser única no PingAccess.

7. Selecione a **sessão web** que criou

8. Selecione o **Site** que criou que contém a aplicação

9. Selecione o **Mapeamento de Identidade** que criou

10. Selecione **Ativado** para ativar o site quando guardar

11. Selecione **Guardar**

### <a name="configure-the-pingfederate-authentication-policy"></a>Configure a política de autenticação PingFederate

Configure a política de autenticação PingFederate para federar aos múltiplos IDPs fornecidos pelos inquilinos Azure AD B2C

1. Criar um contrato para fazer a ponte entre os IdPs e o SP. Para mais informações, consulte [o centro da Federação e os contratos de política de autenticação.](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html) É provável que precise de apenas um contrato, a menos que o SP exija um conjunto diferente de atributos de cada IdP.

2. Para cada IdP, crie uma ligação IdP entre o IdP e o PingFederate, o centro da federação como o SP.

3. Na janela **Target Session Mapping,** adicione os contratos de política de autenticação aplicáveis à ligação IdP.

4. Na janela **Selectors,** configuure um seletor de autenticação. Por exemplo, consulte uma instância do **Primeiro Adaptador de Identificação para mapear** cada IdP para a correspondente ligação IdP numa política de autenticação.

5. Criar uma ligação SP entre o PingFederate, o centro da federação como o IdP, e o SP.

6. Adicione o contrato de política de autenticação correspondente à ligação SP na janela **de Mapeamento de Fonte de Autenticação.**

7. Trabalhe com cada IdP para ligar ao PingFederate, o centro da federação como o SP.

8. Trabalhe com o SP para ligar ao PingFederate, o centro da federação como idP.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
