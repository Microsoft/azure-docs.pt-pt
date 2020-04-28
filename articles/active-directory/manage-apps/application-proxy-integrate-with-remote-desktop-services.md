---
title: Publique o Ambiente de Trabalho Remoto com a App AD Proxy do Azure AD [ Microsoft Docs
description: Cobre o básico sobre conectores de procuração de aplicação ad azure.
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
ms.date: 05/23/2019
ms.author: mimart
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6ca64e2de5734c567173fc735776074f4c87fbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67108469"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publique o ambiente de trabalho remoto com procuração de aplicação ad azure

O Remote Desktop Service e o Azure AD Application Proxy trabalham em conjunto para melhorar a produtividade dos trabalhadores que estão afastados da rede corporativa. 

O público pretendido para este artigo é:
- Os clientes proxy de aplicação atuais que queiram oferecer mais aplicações aos seus utilizadores finais através da publicação de aplicações no local através de Serviços de Ambiente de Trabalho Remoto.
- Os atuais clientes dos Serviços de Ambiente de Trabalho Remoto que pretendem reduzir a superfície de ataque da sua implantação utilizando o Proxy de Aplicação AD Azure. Este cenário proporciona um conjunto limitado de controlos de verificação em duas etapas e controlos de acesso condicional ao RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Como o Proxy de Aplicação se encaixa na implementação padrão de RDS

Uma implementação padrão de RDS inclui vários serviços de função Remote Desktop em execução no Windows Server. Olhando para a [arquitetura Remote Desktop Services,](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)existem múltiplas opções de implementação. Ao contrário de outras opções de implementação RDS, a [implementação RDS com o Proxy de Aplicação AD Azure](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (mostrada no diagrama seguinte) tem uma ligação de saída permanente do servidor que executa o serviço de conector. Outras implementações deixam as ligações de entrada abertas através de um equilibrador de carga.

![Application Proxy situa-se entre o RDS VM e a internet pública](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

Numa implementação RDS, o papel RD Web e o papel RD Gateway funcionam em máquinas viradas para a Internet. Estes pontos finais são expostos pelas seguintes razões:
- A RD Web fornece ao utilizador um ponto final público para iniciar sessão e visualizar as várias aplicações e desktops no local a que podem aceder. Ao selecionar um recurso, é criada uma ligação RDP utilizando a aplicação nativa no SISTEMA.
- RD Gateway entra na imagem assim que um utilizador lança a ligação RDP. O RD Gateway lida com o tráfego de RDP encriptado que vem pela internet e traduz-o para o servidor no local a que o utilizador está a ligar. Neste cenário, o tráfego que o RD Gateway está a receber vem do Procurador de Aplicação AD Azure.

>[!TIP]
>Se ainda não implementou rds antes, ou quer mais informações antes de começar, aprenda a [implementar RDS sem problemas com o Azure Resource Manager e o Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Requisitos

- Utilize um cliente diferente do cliente web remote Desktop, uma vez que o cliente web não suporta proxy de aplicação.

- Tanto os pontos finais RD Web como RD Gateway devem estar localizados na mesma máquina e com uma raiz comum. RD Web e RD Gateway são publicados como uma única aplicação com Application Proxy para que você possa ter uma única experiência de inscrição entre as duas aplicações.

- Já deve ter [implantado RDS,](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)e [ativado o Proxy de Aplicação.](application-proxy-add-on-premises-application.md)

- Este cenário pressupõe que os utilizadores finais passem pelo Internet Explorer nos desktops do Windows 7 ou Windows 10 que se conectam através da página Web rd. Se precisar de suportar outros sistemas operativos, consulte [suporte para outras configurações do cliente](#support-for-other-client-configurations).

- Ao publicar RD Web, recomenda-se a utilização do mesmo FQDN interno e externo. Se as FQDNs internas e externas forem diferentes, então deve desativar a Tradução do Cabeçalho do Pedido para evitar que o cliente receba links inválidos. 

- No Internet Explorer, ative o addon RDS ActiveX.

- Para o fluxo de pré-autenticação da AD Azure, os utilizadores só podem ligar-se aos recursos que lhes são publicados no painel **RemoteApp e Desktops.** Os utilizadores não podem ligar-se a um ambiente de trabalho utilizando o Connect a um painel **de PC remoto.**

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Implementar o cenário conjunto RDS e Application Proxy

Depois de configurar o PROXY de aplicação RDS e Azure AD para o seu ambiente, siga os passos para combinar as duas soluções. Estes passos passam pela publicação dos dois pontos finais RDS (RD Web e RD Gateway) como aplicações e, em seguida, direcionando o tráfego no seu RDS para passar pelo Proxy de Aplicação.

### <a name="publish-the-rd-host-endpoint"></a>Publique o ponto final do anfitrião RD

1. [Publique uma nova aplicação proxy](application-proxy-add-on-premises-application.md) de aplicação com os seguintes valores:
   - URL `https://\<rdhost\>.com/`interno:, `\<rdhost\>` onde está a raiz comum que rd Web e RD Gateway partilham.
   - URL externo: Este campo é automaticamente povoado com base no nome da aplicação, mas pode modificá-lo. Os seus utilizadores irão a este URL quando acederem ao RDS.
   - Método de pré-autenticação: Diretório Ativo Azure
   - Traduzir cabeçalhos URL: Não
2. Atribuir utilizadores à aplicação RD publicada. Certifique-se de que todos têm acesso a RDS, também.
3. Deixe o método de inscrição único para a aplicação como **Azure AD single sign-on desativado**. Pede-se aos seus utilizadores que autentiquem uma vez ao Azure AD e uma vez à RD Web, mas têm um único sinal para o RD Gateway.
4. Selecione **Diretório Ativo Azure**, e, em seguida, **registos de aplicações.** Escolha a sua aplicação na lista.
5. Em **'Gerir',** selecione **Branding**.
6. Atualize o campo URL da **página inicial** para `https://\<rdhost\>.com/RDWeb`apontar para o seu ponto final RD Web (como ).

### <a name="direct-rds-traffic-to-application-proxy"></a>Tráfego direto RDS para procuração de aplicação

Ligue-se à implementação RDS como administrador e altere o nome do servidor RD Gateway para a implementação. Esta configuração garante que as ligações passam pelo serviço de proxy de aplicação AD Azure.

1. Ligue-se ao servidor RDS executando a função de Corretor de Ligação RD.
2. Gestor **de servidor de**lançamento .
3. Selecione **Serviços de Ambiente** de Trabalho Remoto a partir do painel à esquerda.
4. Selecione **Descrição geral**.
5. Na secção 'Visão Geral de Implementação', selecione o menu suspenso e escolha as propriedades de **implementação editar**.
6. No separador RD Gateway, altere o campo de nome do **Servidor** para o URL Externo que definiu para o ponto final do anfitrião RD no Proxy de Aplicação.
7. Altere o campo do **método De Início** para **Autenticação de Palavras-Passe**.

   ![Ecrã de propriedades de implementação em RDS](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Execute este comando para cada coleção. Substitua * \<o\> seu nome de coleção* e * \<proxyfrontendurl\> * com as suas próprias informações. Este comando permite um único sign-on entre RD Web e RD Gateway, e otimiza o desempenho:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Por exemplo:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >O comando acima usa um backtick em "'nrequire".

9. Para verificar a modificação das propriedades personalizadas do RDP, bem como ver os conteúdos de ficheirordp que serão descarregados do RDWeb para esta recolha, execute o seguinte comando:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Agora que configurao Remote Desktop, o Proxy de Aplicação AD Azure assumiu como o componente virado para a Internet do RDS. Pode remover os outros pontos finais virados para a Internet nas suas máquinas RD Web e RD Gateway.

## <a name="test-the-scenario"></a>Teste o cenário

Teste o cenário com o Internet Explorer num computador Windows 7 ou 10.

1. Vá ao URL externo que configura, ou encontre a sua aplicação no [painel MyApps](https://myapps.microsoft.com).
2. É-lhe pedido que autenticasse o Diretório Ativo Azure. Utilize uma conta que atribuiu ao pedido.
3. É-lhe pedido que autenticar a RD Web.
4. Assim que a autenticação RDS for bem sucedida, pode selecionar o ambiente de trabalho ou a aplicação que deseja e começar a trabalhar.

## <a name="support-for-other-client-configurations"></a>Suporte para outras configurações de clientes

A configuração descrita neste artigo é para utilizadores no Windows 7 ou 10, com o Internet Explorer mais o addon RDS ActiveX. No entanto, se precisar, pode suportar outros sistemas operativos ou navegadores. A diferença está no método de autenticação que utiliza.

| Método de autenticação | Configuração de cliente suportado |
| --------------------- | ------------------------------ |
| Pré-autenticação    | Windows 7/10 usando internet explorer + ADDS ActiveX |
| Passa | Qualquer outro sistema operativo que suporte a aplicação Microsoft Remote Desktop |

O fluxo de pré-autenticação oferece mais benefícios de segurança do que o fluxo de passagem. Com pré-autenticação pode utilizar funcionalidades de autenticação Azure AD como inscrição única, Acesso Condicional e verificação em duas etapas para os seus recursos no local. Também garante que apenas o tráfego autenticado chega à sua rede.

Para utilizar a autenticação passthrough, existem apenas duas alterações aos passos enumerados neste artigo:
1. Em [Publicar o ponto final do rd,](#publish-the-rd-host-endpoint) derpor o método de pré-autenticação para **passar**.
2. No [tráfego direto RDS para Application Proxy,](#direct-rds-traffic-to-application-proxy)salte completamente o passo 8.

## <a name="next-steps"></a>Passos seguintes

[Ativar o acesso remoto ao SharePoint com o Proxy de Aplicações do Azure AD](application-proxy-integrate-with-sharepoint-server.md)  
[Considerações de segurança para aceder remotamente a apps utilizando o Proxy de Aplicação AD Azure](application-proxy-security.md)
