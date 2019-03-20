---
title: Publicar o ambiente de trabalho remoto com o Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Abrange as noções básicas sobre os conectores de Proxy de aplicações do Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: celested
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 295422e0f456c4dfd4166911ef8150e8a896ba1a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58111111"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publicar o ambiente de trabalho remoto com o Proxy de aplicações do Azure AD

Serviço de ambiente de trabalho remoto e o Proxy de aplicações do Azure AD funcionam em conjunto para aumentar a produtividade de trabalhadores que estão longe da rede corporativa. 

O público-alvo para este artigo é:
- Os clientes atuais do Proxy de aplicações que pretendem oferecer mais aplicativos aos usuários finais ao publicar aplicações no local através dos serviços de ambiente de trabalho remoto.
- Os clientes atuais dos serviços de ambiente de trabalho remoto que pretendem reduzem a superfície de ataque da sua implementação utilizando o Proxy de aplicações do Azure AD. Este cenário dá um conjunto limitado de verificação de dois passos e controlos de acesso condicional para RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Como o Proxy de aplicações se encaixa na implementação de RDS padrão

Uma implementação de RDS padrão inclui vários serviços de função de ambiente de trabalho remoto em execução no Windows Server. Olhando para o [arquitetura de serviços de ambiente de trabalho remoto](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture), existem várias opções de implementação. Ao contrário de outras opções de implementação de RDS, o [implementação de RDS com o Proxy de aplicações do Azure AD](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (mostrado no diagrama seguinte) tem uma ligação de saída permanente de servidor que executa o serviço de conectores. Outras implementações de deixam a abrir ligações de entrada através de um balanceador de carga.

![Proxy da aplicação encontra-se entre a VM de RDS e a internet pública](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

Numa implementação de RDS, a função da Web de RD e a função do Gateway de RD são executados em máquinas de acesso à Internet. Estes pontos finais são expostos pelos seguintes motivos:
- Web de área de trabalho remota fornece ao utilizador um ponto final público para iniciar sessão e ver as várias aplicações no local e ambientes de trabalho podem aceder. Depois de selecionar um recurso, uma ligação RDP é criada utilizando a aplicação nativa no sistema operacional.
- Gateway de RD, entra em cena, assim que um utilizador inicia a ligação de RDP. O Gateway de RD processa o tráfego RDP encriptado através da internet e o converte para o servidor no local que o utilizador está a ligar a. Neste cenário, o tráfego que do Gateway de RD está a receber é proveniente do Proxy de aplicações do Azure AD.

>[!TIP]
>Se não tiver implementado o RDS antes, ou se pretende obter mais informações antes de começar, saiba como [facilmente implementar RDS com o Azure Resource Manager e o Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Requisitos

- Utilize um cliente que não seja o cliente de web do ambiente de trabalho remoto, uma vez que o cliente web não suporta o Proxy de aplicações.

- Pontos de extremidade da Web de RD e o Gateway de RD têm de estar localizados na mesma máquina e com uma raiz comuns. Web de RD e Gateway de RD são publicados como um único aplicativo com o Proxy de aplicações para que possa ter uma única experiência de logon entre as duas aplicações.

- Já deverá ter [implementado RDS](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure), e [ativado a Proxy da aplicação](application-proxy-add-on-premises-application.md).

- Este cenário pressupõe que os utilizadores finais passem por meio do Internet Explorer no Windows 7 ou Windows 10 áreas de trabalho que se ligam através da página da Web de RD. Se for necessário oferecer suporte a outros sistemas operativos, consulte [suporte para outras configurações de cliente](#support-for-other-client-configurations).

- Durante a publicação de Web de RD, recomenda-se para utilizar o mesmo FQDN interno e externo. Se os FQDNs internos e externos são diferentes, em seguida, deve desabilitar tradução de cabeçalho do pedido para evitar que o cliente receber ligações inválidas. 

- No Internet Explorer, ative o suplemento do ActiveX de RDS.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Implementar o cenário conjunto de RDS e o Proxy de aplicações

Depois de configurar RDS e o Proxy de aplicações do Azure AD para o seu ambiente, siga os passos para combinar as duas soluções. Estes passos irão guiá publicar os dois com acesso à web RDS pontos de extremidade (Web de RD e Gateway de RD) como aplicações e, em seguida, direcionar o tráfego em seu RDS passar pelo Proxy de aplicações.

### <a name="publish-the-rd-host-endpoint"></a>Publique o ponto final de anfitrião de área de trabalho remota

1. [Publicar uma nova aplicação de Proxy de aplicações](application-proxy-add-on-premises-application.md) com os seguintes valores:
   - URL interno: `https://\<rdhost\>.com/`, onde `\<rdhost\>` é a raiz comuns que partilham Web de RD e Gateway de RD.
   - URL externo: Este campo é preenchido automaticamente com base no nome do aplicativo, mas pode modificá-la. Os utilizadores passa a este URL quando acede a RDS.
   - Método de pré-autenticação: Azure Active Directory
   - Traduza os cabeçalhos de URL: Não
2. Atribua utilizadores para a aplicação publicada de área de trabalho remota. Certifique-se de que todos eles têm acesso ao RDS, demasiado.
3. Deixe o método único início de sessão para a aplicação como **do Azure AD início de sessão único desativado**. Os utilizadores são-lhe pedidos para autenticar uma vez para o Azure AD e outra para a Web da área de trabalho remota, mas tem início de sessão único para o Gateway de RD.
4. Aceda a **do Azure Active Directory** > **registos das aplicações** > *seu aplicativo* > **definições**.
5. Selecione **propriedades** e atualizar a **URL da Home page** campo para apontar para o ponto final de Web de área de trabalho remota (como `https://\<rdhost\>.com/RDWeb`).

### <a name="direct-rds-traffic-to-application-proxy"></a>Direcionar o tráfego RDS para Proxy de aplicações

Ligue-se para a implementação de RDS como administrador e alterar o nome do servidor de Gateway de RD para a implementação. Esta configuração garante que as ligações passam pelo serviço do Proxy de aplicações do Azure AD.

1. Ligar ao servidor RDS a executar a função de Mediador de ligações de RD.
2. Inicie **Gestor de servidor**.
3. Selecione **Remote Desktop Services** partir do painel à esquerda.
4. Selecione **Descrição geral**.
5. Na secção Descrição geral da implementação, selecione o menu pendente e escolha **editar as propriedades de implementação**.
6. No separador de Gateway de RD, alterar os **nome do servidor** campo para o URL externo que definiu para o ponto de final de anfitrião de área de trabalho remota no Proxy de aplicações.
7. Alteração da **método de início de sessão** campo **autenticação de palavra-passe**.

   ![Ecrã de propriedades de implementação no RDS](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Execute este comando para cada coleção. Substitua *\<yourcollectionname\>* e *\<proxyfrontendurl\>* pelas suas informações. Este comando permite início de sessão único entre Web de RD e Gateway de RD e otimiza o desempenho:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Por exemplo:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >O comando acima utiliza um acento grave em "'nrequire".

9. Para verificar a modificação das propriedades personalizadas do RDP, bem como ver o conteúdo do ficheiro RDP que será transferido da RDWeb para esta coleção, execute o seguinte comando:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Agora que configurou o ambiente de trabalho remoto, o Proxy de aplicações do Azure AD tem colocado como o componente de acesso à internet do RDS. Pode remover os outros com acesso à internet pontos finais públicos nas suas máquinas de Web de RD e Gateway de RD.

## <a name="test-the-scenario"></a>O cenário de teste

Teste o cenário com o Internet Explorer num Windows 7 ou 10 do computador.

1. Vá para o URL externo que configurou ou encontrar a sua aplicação na [MyApps painel](https://myapps.microsoft.com).
2. É-lhe perguntado para autenticar para o Azure Active Directory. Utilize uma conta que atribuiu à aplicação.
3. É-lhe perguntado para autenticar para a Web da área de trabalho remota.
4. Assim que a autenticação de RDS for bem-sucedida, pode selecionar a área de trabalho ou aplicação que pretende e começar a trabalhar.

## <a name="support-for-other-client-configurations"></a>Suporte para outras configurações de cliente

A configuração descrita neste artigo é para os utilizadores no Windows 7 ou 10, com o suplemento do ActiveX de RDS e o Internet Explorer. Se for necessário, no entanto, pode suportar outros sistemas operativos ou browsers. A diferença está no método de autenticação que utiliza.

| Método de autenticação | Configuração de cliente suportados |
| --------------------- | ------------------------------ |
| Pré-autenticação    | O Windows 7/10 através do Internet Explorer + suplemento ActiveX de RDS |
| Passthrough | Qualquer outro sistema operativo que suporte a aplicação de ambiente de trabalho remoto |

O fluxo de pré-autenticação oferece mais benefícios de segurança do que o fluxo de pass-through. Com a pré-autenticação pode utilizar funcionalidades de autenticação do Azure AD como o início de sessão único, acesso condicional e verificação de dois passos para os seus recursos no local. Também garantir que apenas o tráfego autorizado alcance sua rede.

Para utilizar autenticação pass-through, existem apenas duas modificações para os passos apresentados neste artigo:
1. Na [publique o ponto final de anfitrião de área de trabalho remota](#publish-the-rd-host-endpoint) passo 1, definir o método de pré-autenticação **pass-through**.
2. Na [tráfego de RDS direto para o Proxy de aplicações](#direct-rds-traffic-to-application-proxy), ignore o passo 8 inteiramente.

## <a name="next-steps"></a>Passos Seguintes

[Ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD](application-proxy-integrate-with-sharepoint-server.md)  
[Considerações de segurança para aceder a aplicações remotamente utilizando o Proxy de aplicações do Azure AD](application-proxy-security.md)
