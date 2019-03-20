---
title: Ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Abrange as noções básicas sobre como integrar um servidor do SharePoint no local com o Proxy de aplicações do Azure AD.
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
ms.date: 12/10/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dc80b78bbba369e0ddb5c2c1e9fd90834dc0148
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120419"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD

Este artigo aborda como integrar um servidor do SharePoint no local com o Proxy de aplicações do Azure Active Directory (Azure AD).

Para ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD, siga as secções neste artigo passo a passo.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já tenha SharePoint 2013 ou mais recente no seu ambiente. Além disso, considere os seguintes pré-requisitos:

* O SharePoint inclui suporte nativo do Kerberos. Por conseguinte, os utilizadores que estão a aceder a sites internos remotamente através do Proxy de aplicações do Azure AD podem assumir que tenham uma experiência de início de sessão único (SSO).

* Este cenário inclui alterações de configuração ao seu servidor do SharePoint. Recomendamos que utilize um ambiente de teste. Dessa forma, pode fazer atualizações ao seu servidor de teste pela primeira vez e, em seguida, facilitam um ciclo de testes antes de entrar em produção.

* É necessário SSL no URL publicado. SSL também é necessário o URL interno para garantir que as ligações são enviados/mapeada corretamente.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Passo 1: Configurar o Kerberos (KCD) de delegação restrita

Para aplicações no local que utilizam a autenticação do Windows, pode obter início de sessão único (SSO) com o protocolo de autenticação Kerberos e um recurso chamado delegação restringida de Kerberos (KCD). KCD, quando configurado, permite que o conector de Proxy de aplicações obter um token do Windows para um utilizador, mesmo que o utilizador não tiver sessão iniciada Windows diretamente. Para saber mais sobre o KCD, veja [descrição geral de delegação restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Para configurar o KCD para um servidor do SharePoint, utilize os procedimentos nas seguintes secções seqüenciais:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Certifique-se de que o aplicativo web do SharePoint está em execução sob uma conta de domínio

Em primeiro lugar, certifique-se de que o aplicativo web do SharePoint está em execução sob uma conta de domínio – um sistema não local, serviço local ou serviço de rede. Opte por fazê-lo para que pode anexar os nomes de principal de serviço (SPNs) para esta conta. SPNs são a forma como o protocolo Kerberos identifica os diferentes serviços. E terá da conta mais tarde para configurar o KCD.

> [!NOTE]
> Tem de ter um criado anteriormente conta do Azure AD para o serviço. Sugerimos que permite que uma alteração de palavra-passe automática. Para obter mais informações sobre o conjunto completo de passos e resolução de problemas, consulte [configurar a alteração de palavra-passe automático no SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

Para garantir que seus sites estão em execução sob uma conta de serviço definido, execute os seguintes passos:

1. Abra o **Administração Central do SharePoint** site.
2. Aceda a **Security** e selecione **configurar contas de serviço**.
3. Selecione **Web Pool de aplicativos - SharePoint – 80**. As opções podem ser ligeiramente diferentes com base no nome do seu conjunto de web, ou se o conjunto de web utiliza SSL por padrão.

   ![Opções para configurar uma conta de serviço](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Se **Selecione uma conta para este componente** campo é definido como **Serviço Local** ou **serviço de rede**, tem de criar uma conta. Se não tiver terminado e pode avançar para a secção seguinte.
5. Selecione **Registre-se conta gerida novo**. Depois de criar a sua conta, tem de definir **Pool de aplicativos Web** antes de poder utilizar a conta.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Defina um nome de principal de serviço para a conta de serviço do SharePoint

Antes de configurar o KCD, terá de:

* Identifica a conta de domínio que executa a aplicação web do SharePoint que o Proxy do Azure AD irá expor.
* Escolha um URL interno que serão configurados no Proxy do Azure AD e no SharePoint. Este URL interno já não pode ser utilizado na aplicação web e nunca aparecerá no navegador da web.

Supondo que o URL interno escolhido é <https://sharepoint>, em seguida, o SPN é:

```
HTTP/SharePoint
```

> [!NOTE]
> O utilizador deverá respeite as seguintes recomendações para o URL interno:
> * Utilizar HTTPS
> * Não utilize portas personalizadas
> * No DNS, criar um anfitrião (A) a ponto para o SharePoint WFE (ou Balanceador de carga) e não um Alias (CName)

Para registar este SPN, execute o seguinte comando na linha de comandos como administrador do domínio:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Este comando define o SPN _HTTP/SharePoint_ para a conta de pool de aplicativos do SharePoint _demo\spAppPoolAccount_.

Substitua _HTTP/SharePoint_ com o SPN para o URL interno e _demo\spAppPoolAccount_ com a conta do conjunto aplicacional no seu ambiente. O comando Setspn procura o SPN antes de adicioná-lo. Em já existir, verá uma **valor de SPN duplicado** erro. Neste caso, considere remover o SPN existente se não for definida com a conta do conjunto de aplicação correta.

Pode verificar se o SPN foi adicionado ao executar o comando Setspn com a opção – L. Para saber mais sobre este comando, consulte [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Certifique-se de que o conector é fidedigno para delegação para o SPN adicionado para a conta de pool de aplicativos do SharePoint

Configure o KCD para que o serviço de Proxy de aplicações do Azure AD pode delegar as identidades de utilizador para a conta de pool de aplicativos do SharePoint. Configure o KCD ao ativar o conector do Proxy de aplicações para obter permissões de Kerberos para seus usuários que tiverem sido autenticados no Azure AD. Em seguida, esse servidor passa o contexto para a aplicação de destino ou o SharePoint neste caso.

Para configurar o KCD, repita os passos seguintes para cada computador do conector:

1. Iniciar sessão como um administrador de domínio para um controlador de domínio e, em seguida, abra **Active Directory Users and Computers**.
2. Encontre o que o conector está em execução no computador. Neste exemplo, é o mesmo servidor do SharePoint.
3. Clique duas vezes o computador e, em seguida, clique nas **delegação** separador.
4. Certifique-se de que as definições de delegação estiverem definidas como **confiar no computador para delegação apenas as serviços especificados**. Em seguida, selecione **utilizar qualquer protocolo de autenticação**.
5. Clique nas **Add** botão, clique em **usuários ou computadores**e localize a conta do conjunto aplicacional do SharePoint, por exemplo _demo\spAppPoolAccount_.
6. Na lista de SPNs, selecione aquela que criou anteriormente para a conta de serviço.
7. Clique em **OK**. Clique em **OK** novamente para guardar as alterações.
  
   ![Definições de delegação](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Passo 2: Configurar o Proxy do AD do Azure

Agora que configurou o KCD, está pronto para configurar o Proxy de aplicações do Azure AD.

1. Publica o seu site do SharePoint com as seguintes definições. Para obter instruções passo a passo, consulte [publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-publish-azure-portal.md).
   * **URL interno**: URL interno do SharePoint que foi escolhido anteriormente, como **<https://SharePoint/>**.
   * **Método de pré-autenticação**: Azure Active Directory
   * **Traduzir URL nos cabeçalhos**: NO

   >[!TIP]
   >O SharePoint usa a _cabeçalho de anfitrião_ valor para procurar o site. Também gera links com base nesse valor. O efeito líquido é que qualquer ligação que gera o SharePoint é um URL publicado que está corretamente definido para utilizar o URL externo. Definindo o valor como **Sim** também permite que o conector encaminhar a solicitação para a aplicação de back-end. No entanto, definindo o valor como **não** significa que o conector não enviará o nome de anfitrião interno. Em vez disso, o conector envia o cabeçalho de anfitrião como o URL publicado para a aplicação de back-end.

   ![Publicar o SharePoint como aplicação](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. Assim que a sua aplicação for publicada, configure as definições de início de sessão únicas com os seguintes passos:

   1. Na página da aplicação no portal, selecione **início de sessão único**.
   2. Para o início de sessão em modo único, selecione **a autenticação integrada do Windows**.
   3. Definir o SPN da aplicação interna para o valor que definiu anteriormente. Neste exemplo, isso seria **HTTP/SharePoint**.
   4. No "Delegado início de sessão Identity", selecione **nome da conta SAM no local**.

   ![Configurar a autenticação integrada do Windows para SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Para concluir a configuração a sua aplicação, vá para o **utilizadores e grupos** secção e atribuir utilizadores a aceder a esta aplicação. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Passo 3: Configurar o SharePoint para utilizar o Kerberos e URLs de Proxy do Azure AD

Próxima etapa é estender o aplicativo de web do SharePoint para uma nova zona, configurada com o Kerberos e o mapeamento de acesso alternativo apropriado para permitir que o SharePoint para processar pedidos de entrada enviados para o URL interno, e responder com ligações criadas para o URL externo.

1. Iniciar o **Shell de gestão do SharePoint**.
2. Execute o seguinte script para expandir a sua aplicação web para a zona da Extranet e ativar a autenticação Kerberos:

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

3. Abra o **Administração Central do SharePoint** site.
4. Sob **definições do sistema**, selecione **configurar mapeamentos de acesso alternativo**. Abre a caixa de mapeamentos de acesso alternativo.
5. Selecione o seu site, por exemplo **SharePoint – 80**. Por enquanto, o zona Extranet não tem o URL interno definido corretamente ainda:

   ![Caixa de mapeamentos de acesso alternativa](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

6. Clique em **adicionar URLs internos**.
7. Na **URL protocolo, porta e anfitrião** caixa de texto, tipo a **URL interno** configurado no proxy do AD do Azure, por exemplo <https://SharePoint/>.
8. Selecione zona **Extranet** na lista pendente.
9. Clique em **Guardar**.
10. Os mapeamentos de acesso alternativo deve agora ter um aspeto semelhante a esta:

    ![Corrigir os mapeamentos de acesso alternativo](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Passo 4: Certifique-se de que um certificado HTTPS está configurado para o site do IIS da zona da Extranet

Configuração do SharePoint é agora concluído, mas uma vez que é o URL interno da zona Extranet <https://SharePoint/>, um certificado tem de ser definido para este site.

1. Abra a consola do Windows PowerShell.
2. Execute o seguinte script para gerar um certificado autoassinado e adicioná-lo para o computador meu arquivo:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > Certificados autoassinados são adequados apenas para fins de teste. Em ambientes de produção, recomenda-se vivamente a utilização de certificados emitidos por uma autoridade de certificado em vez disso.

3. Abra a consola "Gestor de serviços de informação de Internet".
4. Expanda o servidor na vista de árvore, expanda "Sites", selecione o site "SharePoint – AAD Proxy" e clique em **enlaces**.
5. Selecione o enlace https e clique em **editar...** .
6. No campo de certificado SSL, escolha **SharePoint** certificado e clique em OK.

Agora pode acessar o site do SharePoint externamente através do Proxy de aplicações do Azure AD.

## <a name="next-steps"></a>Passos Seguintes

* [Trabalhar com domínios personalizados no Proxy de aplicações do Azure AD](application-proxy-configure-custom-domain.md)
* [Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-connectors.md)
