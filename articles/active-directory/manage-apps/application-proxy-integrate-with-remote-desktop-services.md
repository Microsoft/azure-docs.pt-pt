---
title: Publicar Desktop Remoto com App AD AZure Proxy / Microsoft Docs
description: Cobre o básico sobre conectores proxy de aplicação AD Azure.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/23/2019
ms.author: kenwith
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34f3dcd607a7417932912528167a1120dbfd9b4f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764524"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publicar desktop remoto com Proxy de aplicação AD Azure

O Serviço remoto de Desktop e a Aplicação AD Azure trabalham em conjunto para melhorar a produtividade dos trabalhadores que estão longe da rede corporativa. 

O público pretendido para este artigo é:
- Clientes proxy de aplicação atual que pretendam oferecer mais aplicações aos seus utilizadores finais, publicando aplicações no local através de Serviços de Desktop Remoto.
- Clientes de Serviços de Ambiente de Trabalho remoto atuais que pretendam reduzir a superfície de ataque da sua implementação utilizando o Azure AD Application Proxy. Este cenário proporciona um conjunto limitado de controlos de verificação em duas etapas e de acesso condicional ao RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Como o Proxy de Aplicação se enquadra na implementação padrão do RDS

Uma implementação RDS padrão inclui vários serviços de função remote desktop em execução no Windows Server. Olhando para a [arquitetura Remote Desktop Services,](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)existem múltiplas opções de implementação. Ao contrário de outras opções de implementação de [RDS, a implementação de RDS com Azure AD Application Proxy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (mostrada no diagrama seguinte) tem uma ligação de saída permanente a partir do servidor que executa o serviço de conector. Outras implementações deixam as ligações de entrada abertas através de um equilibrador de carga.

![Application Proxy fica entre o RDS VM e a internet pública](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

Numa implementação RDS, o papel da RD Web e o papel RD Gateway funcionam em máquinas viradas para a Internet. Estes pontos finais são expostos pelas seguintes razões:
- A RD Web fornece ao utilizador um ponto final público para iniciar sing e visualizar as várias aplicações e desktops no local a que pode aceder. Ao selecionar um recurso, uma ligação RDP é criada usando a aplicação nativa no SISTEMA.
- RD Gateway entra na imagem assim que um utilizador lança a ligação RDP. O RD Gateway trata do tráfego RDP encriptado que vem através da internet e traduz-o para o servidor no local a que o utilizador está a ligar. Neste cenário, o tráfego que o RD Gateway está a receber provém do Azure AD Application Proxy.

>[!TIP]
>Se ainda não implementou RDS antes, ou quer mais informações antes de começar, aprenda a [implementar o RDS de forma perfeita com o Azure Resource Manager e o Azure Marketplace.](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)

## <a name="requirements"></a>Requirements

- Utilize um cliente que não seja o cliente web remote desktop, uma vez que o cliente web não suporta o Application Proxy.

- Tanto os pontos finais rd Web como RD Gateway devem estar localizados na mesma máquina e com uma raiz comum. RD Web e RD Gateway são publicados como uma única aplicação com Application Proxy para que possa ter uma única experiência de sign-on entre as duas aplicações.

- Já deveria ter [implantado RDS,](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)e [ativado o Proxy de Aplicação](application-proxy-add-on-premises-application.md).

- Este cenário pressupõe que os seus utilizadores finais passam pelo Internet Explorer nos desktops do Windows 7 ou Windows 10 que se conectam através da página Web RD. Se precisar de suportar outros sistemas operativos, consulte [o Suporte para outras configurações do cliente.](#support-for-other-client-configurations)

- Ao publicar a RD Web, recomenda-se a utilização do mesmo FQDN interno e externo. Se as FQDNs internas e externas forem diferentes, então deve desativar a Tradução do Cabeçalho de Pedido para evitar que o cliente receba links inválidos. 

- No Internet Explorer, ative o add-on RDS ActiveX.

- Para o fluxo de pré-autenticação AD Azure, os utilizadores só podem ligar-se aos recursos que lhes são publicados no **painel RemoteApp e Desktops.** Os utilizadores não conseguem ligar-se a um ambiente de trabalho utilizando o **Painel de Ligação a um painel remoto** do PC.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Implementar o cenário conjunto RDS e Application Proxy

Depois de configurar o RDS e o Azure AD Application Proxy para o seu ambiente, siga os passos para combinar as duas soluções. Estes passos passam pela publicação dos dois pontos finais RDS (RD Web e RD Gateway) como aplicações e, em seguida, direcionando o tráfego no seu RDS para passar pelo Application Proxy.

### <a name="publish-the-rd-host-endpoint"></a>Publique o ponto final do anfitrião RD

1. [Publique uma nova aplicação Proxy de aplicação](application-proxy-add-on-premises-application.md) com os seguintes valores:
   - URL interno: `https://\<rdhost\>.com/` , onde está a raiz comum que RD Web e RD Gateway `\<rdhost\>` partilham.
   - URL externo: Este campo é automaticamente povoado com base no nome da aplicação, mas pode modificá-lo. Os seus utilizadores irão a este URL quando acederem a RDS.
   - Método de pré-autorização: Azure Ative Directory
   - Traduzir cabeçalhos URL: Não
2. Atribua os utilizadores à aplicação RD publicada. Certifique-se de que todos têm acesso a RDS, também.
3. Deixe o único método de inscrição para a aplicação como **Azure AD único sinal de insação**. Os seus utilizadores são convidados a autenticar uma vez para Azure AD e uma vez para RD Web, mas têm um único sinal de entrada no RD Gateway.
4. Selecione **Azure Ative Directory**e, em seguida, **Registos de Aplicações**. Escolha a sua aplicação na lista.
5. Under **Manage**, selecione **Branding**.
6. Atualize o campo **URL da página inicial** para apontar para o seu ponto final da Web RD (como `https://\<rdhost\>.com/RDWeb` ).

### <a name="direct-rds-traffic-to-application-proxy"></a>Tráfego RDS direto para Application Proxy

Ligue-se à implementação RDS como administrador e altere o nome do servidor RD Gateway para a implementação. Esta configuração garante que as ligações passam pelo serviço de procuração de aplicações AD Azure.

1. Ligue-se ao servidor RDS que executa a função RD Connection Broker.
2. **Gestor do servidor de lançamento**.
3. Selecione **Serviços de Secretária Remoto** a partir do painel à esquerda.
4. Selecione **Descrição geral**.
5. Na secção 'Vista Geral' de Implementação, selecione o menu suspenso e escolha **as propriedades de implementação editar**.
6. No separador RD Gateway, altere o campo **de nomes do Servidor** para o URL Externo que definiu para o ponto final do anfitrião RD no Application Proxy.
7. Altere o campo **de método de início de** súm em **palavra-passe para autenticação de palavra-passe.**

   ![Ecrã de propriedades de implementação em RDS](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Executar este comando para cada coleção. Substitua *\<yourcollectionname\>* e pela sua própria *\<proxyfrontendurl\>* informação. Este comando permite um único início de sação entre RD Web e RD Gateway e otimiza o desempenho:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Por exemplo:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >O comando acima usa um backtick em "'nrequire".

9. Para verificar a modificação das propriedades RDP personalizadas, bem como ver os conteúdos de ficheiros RDP que serão descarregados a partir de RDWeb para esta coleção, execute o seguinte comando:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Agora que configuraste o Remote Desktop, o Azure AD Application Proxy assumiu como o componente virado para a Internet do RDS. Pode remover os outros pontos finais virados para a Internet nas suas máquinas RD Web e RD Gateway.

## <a name="test-the-scenario"></a>Teste o cenário

Teste o cenário com o Internet Explorer num computador Windows 7 ou 10.

1. Vá ao URL externo que configurar ou encontre a sua aplicação no [painel MyApps](https://myapps.microsoft.com).
2. É-lhe pedido que autente para autenticar no Azure Ative Directory. Utilize uma conta que atribuiu à aplicação.
3. É-lhe pedido que autentica na RD Web.
4. Assim que a autenticação do RDS for bem sucedida, pode selecionar o ambiente de trabalho ou a aplicação que deseja e começar a trabalhar.

## <a name="support-for-other-client-configurations"></a>Suporte para outras configurações de clientes

A configuração descrita neste artigo é para utilizadores no Windows 7 ou 10, com o Internet Explorer mais o add-on RDS ActiveX. No entanto, se precisar, pode suportar outros sistemas operativos ou navegadores. A diferença está no método de autenticação que utiliza.

| Método de autenticação | Configuração de cliente suportado |
| --------------------- | ------------------------------ |
| Pré-autenticação    | Windows 7/10 utilizando o suplemento Do Internet Explorer + RDS ActiveX |
| Passthrough | Qualquer outro sistema operativo que suporte a aplicação Microsoft Remote Desktop |

O fluxo de pré-autenticação oferece mais benefícios de segurança do que o fluxo de passagem. Com a pré-autenticação pode utilizar funcionalidades de autenticação AZure AD como um único sign-on, Acesso Condicional e verificação em duas etapas para os seus recursos no local. Também garante que apenas o tráfego autenticado chega à sua rede.

Para utilizar a autenticação passthrough, existem apenas duas modificações nas etapas enumeradas neste artigo:
1. Em Publicar o ponto [final do anfitrião RD](#publish-the-rd-host-endpoint) passo 1, definir o método de pré-autorização para **Passthrough**.
2. No [tráfego RDS direto para Application Proxy,](#direct-rds-traffic-to-application-proxy)salte completamente o passo 8.

## <a name="next-steps"></a>Próximos passos

[Ativar o acesso remoto ao SharePoint com o Proxy de Aplicações do Azure AD](application-proxy-integrate-with-sharepoint-server.md)  
[Considerações de segurança para aceder remotamente a apps através da utilização de Proxy de Aplicação AD AZure](application-proxy-security.md)
