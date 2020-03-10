---
title: Use restrições de inquilinos para gerir acesso a apps SaaS - Azure AD
description: Como utilizar as restrições dos inquilinos para gerir quais os utilizadores que podem aceder a aplicações com base no seu inquilino Azure AD.
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
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70cdb4b42e835a9bfa03f4551ba25088ef8c5226
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942859"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Use restrições de inquilinos para gerir o acesso a aplicações em nuvem SaaS

Organizações de grandes porte que enfatizam a segurança pretendem mover para serviços como o Office 365 cloud, mas precisa de saber que os utilizadores só podem aceder a aprovados recursos. Tradicionalmente, empresas restringem os nomes de domínio ou endereços IP, quando desejam gerir o acesso. Esta abordagem falha num mundo onde as aplicações de software como serviço (ou SaaS) são alojadas numa nuvem pública, funcionando em nomes de domínio partilhadocomo [outlook.office.com](https://outlook.office.com/) e [login.microsoftonline.com](https://login.microsoftonline.com/). Bloquear estes endereços seria manter os utilizadores acedam ao Outlook na web totalmente, em vez de simplesmente restringi-los a identidades aprovadas e recursos.

A solução Azure Ative Directory (Azure AD) para este desafio é uma funcionalidade chamada restrições ao arrendatário. Com restrições aos inquilinos, as organizações podem controlar o acesso às aplicações em nuvem SaaS, com base no inquilino da AD Azure que as aplicações usam para um único sign-on. Por exemplo, pode querer permitir o acesso a aplicações do Office 365 da sua organização, impedindo o acesso a instâncias de outras organizações dessas mesmas aplicações.  

Com restrições aos inquilinos, as organizações podem especificar a lista de inquilinos a que os seus utilizadores estão autorizados a aceder. O Azure AD, em seguida, só concede acesso a estes permitido inquilinos.

Este artigo centra-se nas restrições de inquilinos para o Office 365, mas a funcionalidade deve funcionar com qualquer aplicação em nuvem SaaS que utilize protocolos de autenticação modernos com a AD Azure para um único sinal. Se utilizar aplicações com um diferente do Azure AD de inquilino do inquilino utilizado pelo Office 365 de SaaS, certifique-se de que todas as necessárias de inquilinos são permitidos. Para mais informações sobre as aplicações em nuvem SaaS, consulte o [Ative Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Como funciona

A solução geral inclui os seguintes componentes:

1. **Azure AD**: Se o `Restrict-Access-To-Tenants: <permitted tenant list>` estiver presente, a Azure AD apenas emite fichas de segurança para os inquilinos permitidos.

2. **Infraestrutura de servidor proxy no local**: Esta infraestrutura é um dispositivo proxy capaz de inspeção Secure Sockets Layer (SSL). Deve configurar o representante para inserir o cabeçalho que contém a lista de inquilinos autorizados no tráfego destinado ao Azure AD.

3. **Software**do cliente : Para suportar as restrições de inquilinos, o software do cliente deve solicitar fichas diretamente da Azure AD, para que a infraestrutura proxy possa intercetar o tráfego. As aplicações do Office 365 baseadas no navegador suportam atualmente restrições de inquilinos, assim como os clientes do Office que usam a autenticação moderna (como o OAuth 2.0).

4. **Autenticação Moderna**: Os serviços em nuvem devem usar a autenticação moderna para usar restrições de inquilinos e bloquear o acesso a todos os inquilinos não permitidos. Deve configurar os serviços em nuvem do Office 365 para utilizar protocolos modernos de autenticação por padrão. Para obter as informações mais recentes sobre o suporte do Office 365 para a autenticação moderna, leia a [autenticação moderna do Gabinete Atualizado 365.](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)

O diagrama seguinte ilustra o fluxo de tráfego de alto nível. As restrições aos inquilinos exigem uma inspeção SSL apenas no tráfego para a Azure AD, e não para os serviços de nuvem do Office 365. Esta distinção é importante, porque o volume de tráfego para autenticação para a AD Azure é tipicamente muito inferior ao volume de tráfego para aplicações SaaS como Exchange Online e SharePoint Online.

![O fluxo de tráfego do inquilino restrições - diagrama](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Criar restrições aos inquilinos

Há dois passos para começar com restrições de inquilinos. Primeiro, certifique-se de que os seus clientes podem ligar-se aos endereços certos. Segundo, configure a sua infraestrutura de procuração.

### <a name="urls-and-ip-addresses"></a>URLs e endereços IP

Para utilizar as restrições de inquilinos, os seus clientes devem poder ligar-se aos seguintes URLs Azure AD para autenticar: [login.microsoftonline.com,](https://login.microsoftonline.com/) [login.microsoft.com](https://login.microsoft.com/)e [login.windows.net](https://login.windows.net/). Além disso, para aceder ao Office 365, os seus clientes também devem poder ligar-se aos nomes de domínio totalmente qualificados (FQDNs), URLs e endereços IP definidos nos intervalos de [endereços do Office 365 URLs e IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Configuração de proxy e de requisitos

A seguinte configuração é necessária para permitir restrições de inquilino através da sua infraestrutura de procuração. Esta orientação é genérica, portanto, deve consultar a documentação do seu fornecedor de proxy para obter os passos de implementação específica.

#### <a name="prerequisites"></a>Pré-requisitos

- O proxy deve ser capaz de realizar a interceptação de SSL, inserção de cabeçalho HTTP e filtrar destinos utilizando FQDNs/URLs.

- Os clientes têm de confiar a cadeia de certificados apresentada pelo proxy para comunicações SSL. Por exemplo, se forem utilizados certificados de uma [infraestrutura de chave pública interna (PKI),](/windows/desktop/seccertenroll/public-key-infrastructure) deve ser confiado o certificado interno de autoridade do certificado de raiz.

- Esta funcionalidade está incluída nas subscrições do Office 365, mas se quiser usar restrições de inquilinos para controlar o acesso a outras aplicações do SaaS, então são necessárias licenças Azure AD Premium 1.

#### <a name="configuration"></a>Configuração

Para cada pedido de entrada para login.microsoftonline.com, login.microsoft.com e login.windows.net, insira dois cabeçalhos HTTP: *Restringir-Acesso aos Inquilinos* e *Restringir-Acesso-Contexto*.

Os cabeçalhos devem incluir os seguintes elementos:

- Para *restringir o acesso aos inquilinos,* utilize um valor de \<lista de inquilinos permitida\>, que é uma lista separada de inquilinos que pretende permitir o acesso dos utilizadores. Qualquer domínio que está registado com um inquilino pode ser utilizado para identificar o inquilino nesta lista. Por exemplo, para permitir o acesso aos inquilinos Contoso e Fabrikam, o nome/par de valor parece: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Para *restringir o contexto de acesso,* utilize um valor de um único ID de diretório, declarando qual o inquilino que está a definir as restrições ao arrendatário. Por exemplo, para declarar Contoso como o inquilino que definiu a política de restrições de inquilinos, o nome/par de valor parece: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Pode encontrar o seu ID de diretório no [portal azure Ative Directy](https://aad.portal.azure.com/). Inscreva-se como administrador, selecione **Azure Ative Directory**e, em seguida, selecione **Propriedades**.

Para evitar que os utilizadores insiram o seu próprio cabeçalho HTTP com inquilinos não aprovados, o representante tem de substituir o cabeçalho *de Acesso restrito aos inquilinos* se já estiver presente no pedido de entrada.

Os clientes devem ser forçados a utilizar o proxy para todos os pedidos para login.microsoftonline.com login.microsoft.com e login.windows.net. Por exemplo, se os ficheiros PAC forem utilizados para direcionar os clientes para a utilização do proxy, os utilizadores finais não devem ser capazes de editar ou desativar os ficheiros PAC.

## <a name="the-user-experience"></a>A experiência do usuário

Esta secção descreve a experiência tanto para utilizadores finais como para administradores.

### <a name="end-user-experience"></a>Experiência do utilizador final

Um utilizador de exemplo é na rede da Contoso, mas está a tentar aceder à instância da Fabrikam de SaaS aplicação partilhada, como o Outlook online. Se Fabrikam é um inquilino não autorizado para a instância Contoso, o utilizador vê uma mensagem de negação de acesso, que diz que está a tentar aceder a um recurso que pertence a uma organização não aprovada pelo seu departamento de TI.

### <a name="admin-experience"></a>Experiência de administrador

Enquanto a configuração das restrições de inquilinos é feita na infraestrutura de procuração corporativa, os administradores podem aceder diretamente aos relatórios de restrições de inquilinos no portal Azure. Para ver os relatórios:

1. Inscreva-se no [portal de Diretório Ativo Azure.](https://aad.portal.azure.com/) O painel de instrumentos de **administração do Azure Ative Directory** aparece.

2. No painel esquerdo, selecione **Azure Active Directory**. Aparece a página de visão geral do Diretório Ativo Azure.

3. Na rubrica **Outras capacidades,** selecione **restrições de Inquilino.**

O administrador do inquilino especificado como inquilino de acesso restrito pode usar este relatório para ver os inscrições bloqueados devido à política de restrições de inquilinos, incluindo a identidade utilizada e o ID do diretório alvo. Inícios de sessão estão incluídos, se a definição de restrição de inquilino é o inquilino do utilizador ou um inquilino de recursos para o início de sessão.

> [!NOTE]
> O relatório pode conter informações limitadas, tais como id de directório-alvo, quando um utilizador que esteja num inquilino que não o inquilino restrito-acesso-contexto assina. Neste caso, as informações identificáveis do utilizador, como o nome e o nome principal do utilizador, são mascaradas para proteger os dados dos utilizadores noutros inquilinos.

Como outros relatórios no portal do Azure, pode utilizar filtros para especificar o âmbito do seu relatório. Pode filtrar num intervalo de tempo específico, utilizador, aplicação, cliente ou estado. Se selecionar o botão **Colunas,** pode optar por apresentar dados com qualquer combinação dos seguintes campos:

- **Utilizador**
- **Candidatura**
- **Estado**
- **Data**
- **Data (UTC)** (onde utc é tempo universal coordenado)
- **Método MFA Auth** (método de autenticação multifactor)
- **Detalhe MFA Auth** (detalhe de autenticação multifactor)
- **Resultado do MFA**
- **Endereço IP**
- **Cliente**
- **Nome de Utilizador**
- **Localização**
- **ID do inquilino alvo**

## <a name="office-365-support"></a>Suporte do Office 365

Os pedidos do Office 365 devem satisfazer dois critérios para apoiar plenamente as restrições aos inquilinos:

1. O cliente usou suportes de autenticação moderna.
2. Autenticação moderna está ativada como o protocolo de autenticação predefinido para o serviço cloud.

Consulte a [autenticação moderna do Gabinete Atualizado 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) para obter as informações mais recentes sobre quais os clientes do Office que atualmente suportam a autenticação moderna. Essa página também inclui ligações para instruções para habilitar a autenticação moderna no específicos Exchange Online e Skype para empresas Online inquilinos. O SharePoint Online já permite a autenticação moderna por defeito.

As aplicações baseadas no office 365 (portal do escritório, Yammer, sites SharePoint, Outlook on the Web e muito mais) suportam atualmente as restrições dos inquilinos. Clientes grossos (Outlook, Skype for Business, Word, Excel, PowerPoint e muito mais) só podem impor restrições aos inquilinos quando utilizam a autenticação moderna.  

O Outlook e o Skype para clientes empresariais que suportem a autenticação moderna podem ainda utilizar protocolos antigos contra inquilinos onde a autenticação moderna não está ativada, contornando efetivamente as restrições dos inquilinos. As restrições aos inquilinos podem bloquear aplicações que utilizem protocolos antigos se contactarem login.microsoftonline.com, login.microsoft.com ou login.windows.net durante a autenticação.

Para o Outlook no Windows, os clientes podem optar por implementar restrições de impedir que os utilizadores finais adicionando contas de correio não aprovados a seus perfis. Por exemplo, consulte a definição de política do grupo de contas de [troca não predefinida.](https://gpsearch.azurewebsites.net/default.aspx?ref=1)

## <a name="testing"></a>Testes

Se quiser experimentar as restrições de inquilinos antes de implementá-la para toda a sua organização, tem duas opções: uma abordagem baseada em hospedeiros usando uma ferramenta como o Fiddler, ou um lançamento encenado de configurações de procuração.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler para uma abordagem baseada no anfitrião

Fiddler é um proxy que pode ser usado para capturar e modificar o tráfego HTTP/HTTPS, incluindo a inserção de cabeçalhos HTTP de depuração de web gratuito. Para configurar o Fiddler para testar as restrições dos inquilinos, execute os seguintes passos:

1. [Descarregue e instale o Fiddler.](https://www.telerik.com/fiddler)

2. Configure o Fiddler para desencriptar o tráfego HTTPS, de acordo com a [documentação de ajuda do Fiddler.](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)

3. Configure o Violinista para inserir os *cabeçalhos de restrição de acesso aos inquilinos* e os cabeçalhos *de restrição-acesso-contexto* usando regras personalizadas:

   1. Na ferramenta Fiddler Web Debugger, selecione o menu **Regras** e selecione **Regras Personalizadas...** Para abrir o ficheiro de CustomRules.

   2. Adicione as seguintes linhas no início da função `OnBeforeRequest`. Substitua \<domínio do inquilino\> por um domínio registado pelo seu inquilino (por exemplo, `contoso.onmicrosoft.com`). Substitua \<\> de id de diretório com o identificador Azure AD GUID do seu inquilino.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Se tiver de permitir que vários inquilinos, utilize uma vírgula para separar os nomes de inquilino. Por exemplo:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Guarde e feche o ficheiro de CustomRules.

Depois de configurar o Fiddler, pode capturar o tráfego indo ao menu **Ficheiro** e selecionando **o Tráfego de Captura**.

### <a name="staged-rollout-of-proxy-settings"></a>Distribuição em etapas das definições de proxy

Consoante as capacidades da sua infraestrutura de proxy, poderá testar a implementação das definições para os seus utilizadores. Aqui estão algumas opções de alto nível para consideração:

1. Utilize ficheiros PAC para apontar os utilizadores de teste para uma infraestrutura de proxy de teste, embora usuários normais continuam a utilizar a infraestrutura do proxy de produção.
2. Alguns servidores de proxy deve dar suporte a utilização de grupos de configurações diferentes.

Para mais detalhes, consulte a documentação do seu servidor proxy.

## <a name="next-steps"></a>Passos Seguintes

- Ler sobre [a autenticação moderna do Escritório 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Reveja as gamas de [endereços office 365 URLs e IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
