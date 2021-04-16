---
title: Use restrições de inquilinos para gerir o acesso a apps SaaS - Azure AD
description: Como usar as restrições do inquilino para gerir quais os utilizadores que podem aceder a apps com base no seu inquilino AZure AD.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 4/6/2021
ms.author: iangithinji
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 941bf61f3387abf19be58bdd4d8861ab123e244f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376584"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Use restrições de inquilinos para gerir o acesso a aplicações em nuvem SaaS

Grandes organizações que enfatizam a segurança querem mudar-se para serviços na nuvem como o Microsoft 365, mas precisam de saber que os seus utilizadores apenas podem aceder a recursos aprovados. Tradicionalmente, as empresas restringem nomes de domínio ou endereços IP quando querem gerir o acesso. Esta abordagem falha num mundo em que as aplicações de software como um serviço (ou SaaS) são hospedadas numa nuvem pública, executando nomes de domínio partilhado como [outlook.office.com](https://outlook.office.com/) e [login.microsoftonline.com](https://login.microsoftonline.com/). Bloquear estes endereços impediria os utilizadores de acederem inteiramente ao Outlook na web, em vez de apenas os restringirem a identidades e recursos aprovados.

A solução Azure Ative Directory (Azure AD) para este desafio é uma característica chamada restrições de inquilinos. Com as restrições de inquilinos, as organizações podem controlar o acesso a aplicações em nuvem SaaS, com base no inquilino Azure AD que as aplicações usam para uma única inscrição. Por exemplo, pode querer permitir o acesso às aplicações Microsoft 365 da sua organização, ao mesmo tempo que impede o acesso a instâncias de outras organizações destas mesmas aplicações.  

Com as restrições ao arrendamento, as organizações podem especificar a lista de inquilinos a que os seus utilizadores estão autorizados a aceder. A Azure AD só concede acesso a estes inquilinos autorizados.

Este artigo centra-se nas restrições de inquilinos para o Microsoft 365, mas a funcionalidade protege todas as aplicações que enviam o utilizador para Azure AD para um único sign-on. Se utilizar aplicativos SaaS com um inquilino AD AZure diferente do inquilino utilizado pelo seu Microsoft 365, certifique-se de que todos os inquilinos necessários são permitidos (por exemplo, em cenários de colaboração B2B). Para obter mais informações sobre as aplicações em nuvem SaaS, consulte o [Ative Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

Além disso, a funcionalidade de restrições ao arrendatário suporta agora [o bloqueio ao uso de todas as aplicações de consumidores da Microsoft](#blocking-consumer-applications-public-preview) (apps MSA) como OneDrive, Hotmail e Xbox.com.  Isto usa um cabeçalho separado para o `login.live.com` ponto final, e é detalhado no final do documento.

## <a name="how-it-works"></a>Como funciona

A solução global compreende os seguintes componentes:

1. **Azure AD**: Se o `Restrict-Access-To-Tenants: <permitted tenant list>` cabeçalho estiver presente, a Azure AD apenas emite fichas de segurança para os inquilinos autorizados.

2. **Infraestrutura de servidor de procuração no local**: Esta infraestrutura é um dispositivo proxy capaz de inspeção de Segurança da Camada de Transporte (TLS). Você deve configurar o representante para inserir o cabeçalho contendo a lista de inquilinos permitidos no tráfego destinado a Azure AD.

3. **Software do cliente**: Para suportar as restrições dos inquilinos, o software do cliente deve solicitar fichas diretamente da Azure AD, para que a infraestrutura proxy possa intercetar tráfego. As aplicações microsoft 365 baseadas no navegador suportam atualmente as restrições dos inquilinos, assim como os clientes do Office que usam a autenticação moderna (como o OAuth 2.0).

4. **Autenticação Moderna**: Os serviços em nuvem devem utilizar a autenticação moderna para usar as restrições do arrendatário e bloquear o acesso a todos os inquilinos não permitidos. Tem de configurar os serviços em nuvem microsoft 365 para utilizar por padrão protocolos de autenticação modernos. Para obter as informações mais recentes sobre o suporte da Microsoft 365 para a autenticação moderna, leia [a autenticação moderna do Office 365.](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)

O diagrama que se segue ilustra o fluxo de tráfego de alto nível. As restrições ao arrendamento requerem a inspeção TLS apenas no tráfego para Azure AD, e não para os serviços de cloud Microsoft 365. Esta distinção é importante, porque o volume de tráfego para autenticação para Azure AD é tipicamente muito inferior ao volume de tráfego para aplicações SaaS como Exchange Online e SharePoint Online.

![Restrições de inquilino fluxo de tráfego - diagrama](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurar restrições de inquilinos

Há dois passos para começar com restrições de inquilinos. Primeiro, certifique-se de que os seus clientes podem ligar-se aos endereços certos. Segundo,confige a sua infraestrutura de procuração.

### <a name="urls-and-ip-addresses"></a>UrLs e endereços IP

Para utilizar as restrições de inquilinos, os seus clientes devem poder ligar-se aos seguintes URLs AD Azure para autenticar: [login.microsoftonline.com,](https://login.microsoftonline.com/) [login.microsoft.com](https://login.microsoft.com/)e [login.windows.net](https://login.windows.net/). Além disso, para aceder ao Office 365, os seus clientes também devem poder ligar-se aos nomes de domínio totalmente qualificados (FQDNs), URLs e endereços IP definidos nos [intervalos de endereços URLs e IP do Office 365.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 

### <a name="proxy-configuration-and-requirements"></a>Configuração e requisitos de procuração

A configuração seguinte é necessária para permitir restrições de inquilino através da sua infraestrutura de procuração. Esta orientação é genérica, por isso deve consultar a documentação do seu fornecedor de procuração para etapas de implementação específicas.

#### <a name="prerequisites"></a>Pré-requisitos

- O proxy deve ser capaz de realizar interceção de TLS, inserção de cabeçalho HTTP e destinos de filtragem utilizando FQDNs/URLs.

- Os clientes devem confiar na cadeia de certificados apresentada pelo representante para as comunicações TLS. Por exemplo, se forem utilizados certificados de uma [infraestrutura de chave pública interna (PKI),](/windows/desktop/seccertenroll/public-key-infrastructure) deve ser confiado o certificado interno de autoridade do certificado de emissão de certificados de origem.

- As licenças Azure AD Premium 1 são necessárias para o uso de Restrições de Inquilino.

#### <a name="configuration"></a>Configuração

Para cada pedido de saída para login.microsoftonline.com, login.microsoft.com e login.windows.net, insira dois cabeçalhos HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

> [!NOTE]
> Não inclua subdomínios `*.login.microsoftonline.com` na sua configuração de procuração. Ao fazê-lo, incluirá device.login.microsoftonline.com e interferirá com a autenticação do Certificado de Cliente, que é utilizado em cenários de Registo de Dispositivos e Acesso Condicional baseados no Dispositivo. Configure o seu servidor proxy para excluir device.login.microsoftonline.com da injeção de break-and-inspect e da injeção de cabeçalho TLS.

Os cabeçalhos devem incluir os seguintes elementos:

- Para *os inquilinos de acesso restrito,* utilize um valor \<permitted tenant list\> de, que é uma lista separada de vírgulas de inquilinos que pretende permitir o acesso dos utilizadores. Qualquer domínio registado com um inquilino pode ser usado para identificar o inquilino nesta lista, bem como o próprio diretório. Para um exemplo de todas as três formas de descrever um inquilino, o par de nomes/valores para permitir Contoso, Fabrikam e Microsoft parece: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- Para *restringir o contexto de acesso,* use o valor de um único iD de diretório, declarando qual o inquilino que está estabelecendo as restrições do inquilino. Por exemplo, para declarar Contoso como o inquilino que definiu a política de restrições do arrendatário, o par de nome/valor parece: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` .  Você **deve** usar seu próprio iD de diretório neste local para obter registos para estas autenticações.

> [!TIP]
> Pode encontrar o seu ID de diretório no [portal Azure Ative Directory](https://aad.portal.azure.com/). Inscreva-se como administrador, selecione **Azure Ative Directory** e, em seguida, selecione **Propriedades**. 
>
> Para validar que um iD de diretório ou nome de domínio se referem ao mesmo inquilino, utilize esse ID ou domínio no lugar <tenant> deste URL: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` .  Se os resultados com o domínio e o ID forem os mesmos, referem-se ao mesmo inquilino. 

Para evitar que os utilizadores insiram o seu próprio cabeçalho HTTP por inquilinos não aprovados, o representante precisa de substituir o cabeçalho *de Acesso Restrito-Acesso-A-Inquilinos* se já estiver presente no pedido de entrada.

Os clientes devem ser obrigados a usar o representante para todos os pedidos para login.microsoftonline.com, login.microsoft.com e login.windows.net. Por exemplo, se os ficheiros PAC forem utilizados para direcionar os clientes a utilizar o proxy, os utilizadores finais não devem ser capazes de editar ou desativar os ficheiros PAC.

## <a name="the-user-experience"></a>A experiência do utilizador

Esta secção descreve a experiência tanto para os utilizadores finais como para os administradores.

### <a name="end-user-experience"></a>Experiência do utilizador final

Um utilizador exemplo está na rede Contoso, mas está a tentar aceder à instância Fabrikam de uma aplicação SaaS partilhada como o Outlook online. Se Fabrikam é um inquilino não permitido para a instância Contoso, o utilizador vê uma mensagem de negação de acesso, que diz que está a tentar aceder a um recurso que pertence a uma organização não aprovada pelo seu departamento de TI.

![Mensagem de erro de restrições de inquilinos, a partir de abril de 2021](./media/tenant-restrictions/error-message.png)

### <a name="admin-experience"></a>Experiência de administração

Enquanto a configuração das restrições de inquilinos é feita na infraestrutura de procuração corporativa, os administradores podem aceder diretamente aos relatórios de restrições do arrendatário no portal Azure. Para ver os relatórios:

1. Inscreva-se no [portal Azure Ative Directory](https://aad.portal.azure.com/). Aparece o painel de administração **do Azure Ative Directory.**

2. No painel esquerdo, selecione **Azure Active Directory**. Aparece a página geral do Diretório Ativo Azure.

3. Na página 'Visão Geral', **selecione restrições de inquilinos.**

O administrador para o inquilino especificado como o inquilino restrito-contexto de acesso pode usar este relatório para ver as inscrições bloqueadas devido à política de restrições do arrendatário, incluindo a identidade utilizada e o iD do directório-alvo. As inscrições estão incluídas se o arrendatário que estabelece a restrição for o inquilino do utilizador ou o inquilino de recursos para a inscrição.

O relatório pode conter informações limitadas, tais como identificação de diretório-alvo, quando um utilizador que esteja num inquilino que não seja o inquilino de contexto restrito assina. Neste caso, as informações identificáveis do utilizador, tais como nome e nome principal do utilizador, são mascaradas para proteger os dados dos utilizadores em outros inquilinos ("{PII @domain.com Removed}" ou 0000000-0000-0000-0000-0000000000000000, em vez de nomes de utilizador e IDs de objeto, conforme apropriado). 

À semelhança de outros relatórios no portal Azure, pode utilizar filtros para especificar o âmbito do seu relatório. Pode filtrar num intervalo de tempo específico, utilizador, aplicação, cliente ou estado. Se selecionar o botão **Colunas,** pode optar por exibir dados com qualquer combinação dos seguintes campos:

- **Utilizador** - este campo pode ter dados pessoais removidos, onde serão definidos para `00000000-0000-0000-0000-000000000000` . 
- **Aplicação**
- **Estado**
- **Data**
- **Data (UTC)** - onde UTC é Tempo Universal Coordenado
- **Endereço IP**
- **Cliente**
- **Nome de utilizador** - este campo pode ter dados pessoais removidos, onde será definido para `{PII Removed}@domain.com`
- **Localização**
- **ID do inquilino alvo**

## <a name="microsoft-365-support"></a>Suporte do Microsoft 365

As aplicações da Microsoft 365 devem cumprir dois critérios para suportar totalmente as restrições dos inquilinos:

1. O cliente usado suporta a autenticação moderna.
2. A autenticação moderna é ativada como o protocolo de autenticação predefinido para o serviço de nuvem.

Consulte a [autenticação moderna do Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) atualizada para obter as informações mais recentes sobre as quais os clientes do Office suportam atualmente a autenticação moderna. Essa página também inclui links para instruções para permitir a autenticação moderna em Exchange Online e Skype específico para inquilinos Business Online. O SharePoint Online já permite a autenticação moderna por padrão.

As aplicações baseadas no navegador Microsoft 365 (o Portal do Office, Yammer, sites SharePoint, Outlook na Web e muito mais) suportam atualmente as restrições dos inquilinos. Os clientes grossos (Outlook, Skype for Business, Word, Excel, PowerPoint, entre outros) só podem impor restrições ao arrendatário quando utilizam a autenticação moderna.  

Outlook e Skype para clientes empresariais que suportem a autenticação moderna podem ainda poder usar protocolos antigos contra inquilinos onde a autenticação moderna não está ativada, efetivamente contornando as restrições dos inquilinos. As restrições ao arrendatário podem bloquear aplicações que utilizem protocolos antigos se contactarem login.microsoftonline.com, login.microsoft.com ou login.windows.net durante a autenticação.

No caso do Outlook on Windows, os clientes podem optar por implementar restrições que impeçam os utilizadores finais de adicionar contas de correio não aprovadas aos seus perfis. Por exemplo, consulte a definição de política de grupo [de contas de câmbio não padrão.](https://gpsearch.azurewebsites.net/default.aspx?ref=1)

## <a name="testing"></a>Testar

Se você quiser experimentar as restrições do inquilino antes de implementá-lo para toda a sua organização, você tem duas opções: uma abordagem baseada em hospedeiro usando uma ferramenta como Fiddler, ou um lançamento encenado de configurações de procuração.

### <a name="fiddler-for-a-host-based-approach"></a>Violinista para uma abordagem baseada em hospedeiro

O Fiddler é um proxy de depurador de web gratuito que pode ser usado para capturar e modificar o tráfego HTTP/HTTPS, incluindo inserir cabeçalhos HTTP. Para configurar o Fiddler para testar as restrições do inquilino, execute os seguintes passos:

1. [Descarregue e instale o Violinista.](https://www.telerik.com/fiddler)

2. Configure o violinista para desencriptar o tráfego HTTPS, por [documentação de ajuda do Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configure o Violinista para inserir os *cabeçalhos de acesso restrito-acesso-a-inquilinos* e *de contexto de acesso restrito* usando regras personalizadas:

   1. Na ferramenta Fiddler Web Debugger, selecione o menu **Regras** e **selecione Personalize Regras...** para abrir o ficheiro CustomRules.

   2. Adicione as seguintes linhas no início da `OnBeforeRequest` função. Substitua \<List of tenant identifiers\> por um domínio registado pelo seu inquilino (por exemplo, `contoso.onmicrosoft.com` ). Substitua \<directory ID\> o identificador Azure AD GUID do seu inquilino.  **Deve** incluir o identificador GUID correto para que os registos apareçam no seu inquilino. 

   ```JScript.NET
    // Allows access to the listed tenants.
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<List of tenant identifiers>";
          oSession.oRequest["Restrict-Access-Context"] = "<Your directory ID>";
      }

    // Blocks access to consumer apps
      if (
          oSession.HostnameIs("login.live.com")
      )
      {
          oSession.oRequest["sec-Restrict-Tenant-Access-Policy"] = "restrict-msa";
      }
   ```

   Se precisar de permitir vários inquilinos, use uma vírgula para separar os nomes dos inquilinos. Por exemplo:

   `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Guarde e feche o ficheiro CustomRules.

Depois de configurar o Violinista, pode capturar o tráfego indo ao menu **Ficheiro** e selecionando **o Tráfego de Captura.**

### <a name="staged-rollout-of-proxy-settings"></a>Lançamento encenado de configurações de procuração

Dependendo das capacidades da sua infraestrutura proxy, poderá ser capaz de configurar o lançamento de definições para os seus utilizadores. Aqui estão algumas opções de alto nível para consideração:

1. Utilize ficheiros PAC para apontar os utilizadores de teste a uma infraestrutura de procuração de teste, enquanto os utilizadores normais continuam a utilizar a infraestrutura de procuração de produção.
2. Alguns servidores proxy podem suportar diferentes configurações usando grupos.

Para obter detalhes específicos, consulte a documentação do seu servidor proxy.

## <a name="blocking-consumer-applications-public-preview"></a>Bloqueio das aplicações dos consumidores (pré-visualização pública)

As aplicações da Microsoft que suportam contas de consumidores e contas organizacionais, como [o OneDrive](https://onedrive.live.com/) ou o [Microsoft Learn,](/learn/)podem por vezes ser hospedadas no mesmo URL.  Isto significa que os utilizadores que devem aceder a esse URL para fins de trabalho também têm acesso ao mesmo para uso pessoal, o que pode não ser permitido de acordo com as suas diretrizes operacionais.

Algumas organizações tentam corrigir isso bloqueando `login.live.com` para impedir que as contas pessoais autensem.  Isto tem várias desvantagens:

1. O bloqueio `login.live.com` bloqueia o uso de contas pessoais em cenários de hóspedes B2B, o que pode intrometer-se nos visitantes e na colaboração.
1. [Piloto automático requer `login.live.com` a utilização de](/mem/autopilot/networking-requirements) a fim de implantar. Os cenários intune e autopiloto podem falhar quando `login.live.com` estão bloqueados.
1. As atualizações de telemetria organizacional e Windows que dependem do serviço login.live.com para iDs do dispositivo [deixarão de funcionar](/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are).

### <a name="configuration-for-consumer-apps"></a>Configuração para aplicativos de consumo

Embora o `Restrict-Access-To-Tenants` cabeçalho funcione como uma lista de admissões, o bloco da conta Microsoft (MSA) funciona como um sinal de negação, dizendo à plataforma de conta da Microsoft para não permitir que os utilizadores inscrevam-se nas aplicações dos consumidores. Para enviar este sinal, o `sec-Restrict-Tenant-Access-Policy` cabeçalho é injetado no tráfego visitando `login.live.com` usando o mesmo representante corporativo ou firewall como [acima](#proxy-configuration-and-requirements). O valor do cabeçalho deve ser `restrict-msa` . Quando o cabeçalho está presente e uma aplicação de consumo está a tentar assinar diretamente num utilizador, esse sinal será bloqueado.

Neste momento, a autenticação para aplicações de consumidores não aparece nos registos de [administração](#admin-experience), uma vez login.live.com é hospedado separadamente do Azure AD.

### <a name="what-the-header-does-and-does-not-block"></a>O que o cabeçalho faz e não bloqueia

A `restrict-msa` política bloqueia a utilização de aplicações de consumidores, mas permite através de vários outros tipos de tráfego e autenticação:

1. Tráfego sem utilizador para dispositivos.  Isto inclui tráfego para Autopilot, Windows Update e telemetria organizacional.
1. Autenticação B2B das contas de consumo. Os utilizadores com contas microsoft que são [convidados a colaborar com um inquilino](../external-identities/redemption-experience.md#invitation-redemption-flow) autenticam para login.live.com para aceder a um inquilino de recursos.
    1. Este acesso é controlado utilizando o `Restrict-Access-To-Tenants` cabeçalho para permitir ou negar o acesso a esse inquilino de recurso.
1. A autenticação "Passthrough", utilizada por muitas aplicações Azure, bem como Office.com, onde as aplicações utilizam a Azure AD para assinar nos utilizadores do consumidor num contexto de consumo.
    1. Este acesso também é controlado utilizando o `Restrict-Access-To-Tenants` cabeçalho para permitir ou negar o acesso ao inquilino especial "passthrough" `f8cdef31-a31e-4b4a-93e4-5f571e91255a` ().  Se este inquilino não aparecer na sua `Restrict-Access-To-Tenants` lista de domínios permitidos, as contas de consumo serão bloqueadas pela Azure AD de se inscrever nestas aplicações.

## <a name="next-steps"></a>Passos seguintes

- Ler sobre [a autenticação moderna do Office 365](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Rever os [intervalos de endereços URLs e IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
