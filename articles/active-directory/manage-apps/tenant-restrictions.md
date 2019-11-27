---
title: Usar restrições de locatário para gerenciar o acesso a aplicativos SaaS-Azure AD
description: Como usar restrições de locatário para gerenciar quais usuários podem acessar aplicativos com base em seu locatário do Azure AD.
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
ms.openlocfilehash: 64f73dd8dbef3f08cd4ea5841e4ec21bac2f55bf
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276511"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Usar restrições de locatário para gerenciar o acesso a aplicativos de nuvem SaaS

Organizações de grandes porte que enfatizam a segurança pretendem mover para serviços como o Office 365 cloud, mas precisa de saber que os utilizadores só podem aceder a aprovados recursos. Tradicionalmente, empresas restringem os nomes de domínio ou endereços IP, quando desejam gerir o acesso. Essa abordagem falha em um mundo em que aplicativos de software como serviço (ou SaaS) são hospedados em uma nuvem pública, sendo executados em nomes de domínio compartilhados como [Outlook.Office.com](https://outlook.office.com/) e [login.microsoftonline.com](https://login.microsoftonline.com/). Bloquear estes endereços seria manter os utilizadores acedam ao Outlook na web totalmente, em vez de simplesmente restringi-los a identidades aprovadas e recursos.

A solução Azure Active Directory (Azure AD) para esse desafio é um recurso chamado restrições de locatário. Com as restrições de locatário, as organizações podem controlar o acesso a aplicativos de nuvem SaaS, com base no locatário do Azure AD que os aplicativos usam para logon único. Por exemplo, pode querer permitir o acesso a aplicações do Office 365 da sua organização, impedindo o acesso a instâncias de outras organizações dessas mesmas aplicações.  

Com as restrições de locatário, as organizações podem especificar a lista de locatários que seus usuários têm permissão para acessar. O Azure AD, em seguida, só concede acesso a estes permitido inquilinos.

Este artigo se concentra em restrições de locatário para o Office 365, mas o recurso deve funcionar com qualquer aplicativo de nuvem SaaS que usa protocolos de autenticação modernos com o Azure AD para logon único. Se utilizar aplicações com um diferente do Azure AD de inquilino do inquilino utilizado pelo Office 365 de SaaS, certifique-se de que todas as necessárias de inquilinos são permitidos. Para obter mais informações sobre aplicativos de nuvem SaaS, consulte o [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Como funciona

A solução geral inclui os seguintes componentes:

1. **Azure ad**: se o `Restrict-Access-To-Tenants: <permitted tenant list>` estiver presente, o Azure ad só emite tokens de segurança para os locatários permitidos.

2. **Infraestrutura de servidor proxy local**: Essa infraestrutura é um dispositivo proxy compatível com a inspeção protocolo SSL (SSL). Você deve configurar o proxy para inserir o cabeçalho que contém a lista de locatários permitidos no tráfego destinado ao Azure AD.

3. **Software cliente**: para dar suporte a restrições de locatário, o software cliente deve solicitar tokens diretamente do Azure AD, para que a infraestrutura de proxy possa interceptar o tráfego. Atualmente, os aplicativos do Office 365 baseados em navegador dão suporte a restrições de locatário, como clientes do Office que usam autenticação moderna (como o OAuth 2,0).

4. **Autenticação moderna**: os serviços de nuvem devem usar a autenticação moderna para usar restrições de locatário e bloquear o acesso a todos os locatários não permitidos. Você deve configurar os serviços de nuvem do Office 365 para usar protocolos de autenticação modernos por padrão. Para obter as informações mais recentes sobre o suporte do Office 365 para autenticação moderna, leia [atualização da autenticação moderna do office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

O diagrama seguinte ilustra o fluxo de tráfego de alto nível. As restrições de locatário exigem a inspeção SSL somente no tráfego para o Azure AD, não nos serviços de nuvem do Office 365. Essa distinção é importante, pois o volume de tráfego para autenticação no Azure AD normalmente é muito menor do que o volume de tráfego para aplicativos SaaS, como o Exchange Online e o SharePoint Online.

![Fluxo de tráfego de restrições de locatário-diagrama](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurar restrições de locatário

Há duas etapas para começar a usar as restrições de locatário. Primeiro, verifique se os clientes podem se conectar aos endereços corretos. Em segundo lugar, configure sua infraestrutura de proxy.

### <a name="urls-and-ip-addresses"></a>URLs e endereços IP

Para usar restrições de locatário, os clientes devem ser capazes de se conectar às seguintes URLs do Azure AD para autenticar: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.Microsoft.com](https://login.microsoft.com/)e [login.Windows.net](https://login.windows.net/). Além disso, para acessar o Office 365, os clientes também devem ser capazes de se conectar aos FQDNs (nomes de domínio totalmente qualificados), URLs e endereços IP definidos nas [URLs do Office 365 e nos intervalos de endereços IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Configuração de proxy e de requisitos

A configuração a seguir é necessária para habilitar restrições de locatário por meio da sua infraestrutura de proxy. Esta orientação é genérica, portanto, deve consultar a documentação do seu fornecedor de proxy para obter os passos de implementação específica.

#### <a name="prerequisites"></a>Pré-requisitos

- O proxy deve ser capaz de realizar a interceptação de SSL, inserção de cabeçalho HTTP e filtrar destinos utilizando FQDNs/URLs.

- Os clientes têm de confiar a cadeia de certificados apresentada pelo proxy para comunicações SSL. Por exemplo, se forem usados certificados de uma [PKI (infraestrutura de chave pública)](/windows/desktop/seccertenroll/public-key-infrastructure) interna, o certificado de autoridade de certificação raiz de emissão interno deverá ser confiável.

- Esse recurso está incluído nas assinaturas do Office 365, mas se você quiser usar restrições de locatário para controlar o acesso a outros aplicativos SaaS, Azure AD Premium 1 licenças serão necessárias.

#### <a name="configuration"></a>Configuração

Para cada solicitação de entrada para login.microsoftonline.com, login.microsoft.com e login.windows.net, insira dois cabeçalhos HTTP: *restrict-Access-to-locatários* e *restrict-Access-Context*.

Os cabeçalhos devem incluir os seguintes elementos:

- Para *restringir o acesso-para-locatários*, use um valor de \<lista de locatários permitidos\>, que é uma lista separada por vírgulas de locatários que você deseja permitir que os usuários acessem. Qualquer domínio que está registado com um inquilino pode ser utilizado para identificar o inquilino nesta lista. Por exemplo, para permitir o acesso aos locatários contoso e Fabrikam, o par nome/valor é semelhante a: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Para o *contexto de acesso restrito*, use um valor de uma ID de diretório único, declarando qual locatário está definindo as restrições de locatário. Por exemplo, para declarar contoso como o locatário que define a política de restrições de locatário, o par nome/valor é semelhante a: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Você pode encontrar a ID do diretório no [portal de Azure Active Directory](https://aad.portal.azure.com/). Entre como administrador, selecione **Azure Active Directory**e, em seguida, selecione **Propriedades**.

Para impedir que os usuários insiram seu próprio cabeçalho HTTP com locatários não aprovados, o proxy precisa substituir o cabeçalho *restrict-Access-to-locatários* se ele já estiver presente na solicitação de entrada.

Os clientes devem ser forçados a utilizar o proxy para todos os pedidos para login.microsoftonline.com login.microsoft.com e login.windows.net. Por exemplo, se os arquivos PAC forem usados para direcionar os clientes para usarem o proxy, os usuários finais não poderão editar ou desabilitar os arquivos PAC.

## <a name="the-user-experience"></a>A experiência do usuário

Esta seção descreve a experiência para usuários finais e administradores.

### <a name="end-user-experience"></a>Experiência do utilizador final

Um utilizador de exemplo é na rede da Contoso, mas está a tentar aceder à instância da Fabrikam de SaaS aplicação partilhada, como o Outlook online. Se a Fabrikam for um locatário não permitido para a instância da Contoso, o usuário verá uma mensagem de negação de acesso, que diz que você está tentando acessar um recurso que pertence a uma organização não aprovada pelo departamento de ti.

### <a name="admin-experience"></a>Experiência de administrador

Embora a configuração de restrições de locatário seja feita na infraestrutura de proxy corporativo, os administradores podem acessar os relatórios de restrições de locatário no portal do Azure diretamente. Para exibir os relatórios:

1. Entre no portal de [Azure Active Directory](https://aad.portal.azure.com/). O painel do **centro de administração do Azure Active Directory** é exibido.

2. No painel esquerdo, selecione **Azure Active Directory**. A página Visão geral do Azure Active Directory é exibida.

3. No cabeçalho **outros recursos** , selecione **restrições de locatário**.

O administrador do locatário especificado como o locatário de contexto de acesso restrito pode usar esse relatório para ver as entradas bloqueadas devido à política de restrições de locatário, incluindo a identidade usada e a ID do diretório de destino. Inícios de sessão estão incluídos, se a definição de restrição de inquilino é o inquilino do utilizador ou um inquilino de recursos para o início de sessão.

Como outros relatórios no portal do Azure, pode utilizar filtros para especificar o âmbito do seu relatório. Você pode filtrar em um intervalo de tempo, usuário, aplicativo, cliente ou status específico. Se você selecionar o botão **colunas** , poderá optar por exibir os dados com qualquer combinação dos seguintes campos:

- **Usuário**
- **Aplicativo**
- **Estado**
- **Date**
- **Data (UTC)** (em que UTC é o tempo Universal Coordenado)
- **Método** de autenticação de MFA (método de pseudo-autenticação multifator)
- **Detalhe** de autenticação de MFA (detalhe de pseudo-autenticação de multifator)
- **Resultado da MFA**
- **Endereço IP**
- **Cliente**
- **Nome de Utilizador**
- **Localização**
- **ID do locatário de destino**

## <a name="office-365-support"></a>Suporte do Office 365

Os aplicativos do Office 365 devem atender a dois critérios para oferecer suporte total às restrições de locatário:

1. O cliente usado dá suporte à autenticação moderna.
2. Autenticação moderna está ativada como o protocolo de autenticação predefinido para o serviço cloud.

Consulte [autenticação moderna do office 365 atualizada](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) para obter as informações mais recentes sobre quais clientes do Office atualmente dão suporte à autenticação moderna. Essa página também inclui ligações para instruções para habilitar a autenticação moderna no específicos Exchange Online e Skype para empresas Online inquilinos. O SharePoint Online já habilita a autenticação moderna por padrão.

Os aplicativos baseados em navegador do Office 365 (o portal do Office, Yammer, sites do SharePoint, Outlook na Web e mais) atualmente dão suporte a restrições de locatário. Clientes densos (Outlook, Skype for Business, Word, Excel, PowerPoint e mais) podem impor restrições de locatário somente ao usar a autenticação moderna.  

Clientes do Outlook e Skype for Business que dão suporte à autenticação moderna ainda podem usar protocolos herdados em locatários em que a autenticação moderna não está habilitada, ignorando efetivamente as restrições de locatário. Restrições de locatário podem bloquear aplicativos que usam protocolos herdados se entrarem em contato com login.microsoftonline.com, login.microsoft.com ou login.windows.net durante a autenticação.

Para o Outlook no Windows, os clientes podem optar por implementar restrições de impedir que os utilizadores finais adicionando contas de correio não aprovados a seus perfis. Por exemplo, consulte a configuração da política de grupo [impedir a adição de contas do Exchange não padrão](https://gpsearch.azurewebsites.net/default.aspx?ref=1) .

## <a name="testing"></a>Testar

Se você quiser experimentar restrições de locatário antes de implementá-lo para toda a organização, terá duas opções: uma abordagem baseada em host usando uma ferramenta como o Fiddler ou uma distribuição em etapas de configurações de proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler para uma abordagem baseada no anfitrião

Fiddler é um proxy que pode ser usado para capturar e modificar o tráfego HTTP/HTTPS, incluindo a inserção de cabeçalhos HTTP de depuração de web gratuito. Para configurar o Fiddler para testar restrições de locatário, execute as seguintes etapas:

1. [Baixe e instale o Fiddler](https://www.telerik.com/fiddler).

2. Configure o Fiddler para descriptografar o tráfego HTTPS, por [documentação de ajuda do Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configure o Fiddler para inserir os cabeçalhos *restrict-Access-to-Tenants* e *restrict-Access-Context* usando regras personalizadas:

   1. Na ferramenta depurador da Web do Fiddler, selecione o menu **regras** e selecione **Personalizar regras...** Para abrir o ficheiro de CustomRules.

   2. Adicione as linhas a seguir no início da função `OnBeforeRequest`. Substitua \<\> de domínio do locatário por um domínio registrado com seu locatário (por exemplo, `contoso.onmicrosoft.com`). Substitua \<ID de diretório\> pelo identificador de GUID do Azure AD do locatário.

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

Depois de configurar o Fiddler, você pode capturar o tráfego acessando o menu **arquivo** e selecionando **capturar tráfego**.

### <a name="staged-rollout-of-proxy-settings"></a>Distribuição em etapas das definições de proxy

Consoante as capacidades da sua infraestrutura de proxy, poderá testar a implementação das definições para os seus utilizadores. Aqui estão algumas opções de alto nível para consideração:

1. Utilize ficheiros PAC para apontar os utilizadores de teste para uma infraestrutura de proxy de teste, embora usuários normais continuam a utilizar a infraestrutura do proxy de produção.
2. Alguns servidores de proxy deve dar suporte a utilização de grupos de configurações diferentes.

Para obter detalhes específicos, consulte a documentação do servidor proxy.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre a [autenticação moderna do Office 365 atualizada](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Examinar as [URLs do Office 365 e os intervalos de endereços IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
