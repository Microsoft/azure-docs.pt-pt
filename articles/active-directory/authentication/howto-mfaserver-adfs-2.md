---
title: Use o servidor Azure MFA com AD FS 2.0 - Diretório Ativo Azure
description: Esta é a página do Multi-Factor Authentication do Azure que descreve como começar a utilizar o MFA do Azure e o AD FS 2.0.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13cfed6bf378ff9dacc4b6446cdfc246a87949bb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994235"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>Configurar o Servidor Multi-Factor Authentication do Azure para trabalhar com o AD FS 2.0

Este artigo destina-se a organizações federadas com o Azure Active Directory e que pretendem proteger recursos que estão no local ou na nuvem. Proteja os seus recursos através do Servidor Multi-Factor Authentication do Azure e configure-o para funcionar com o AD FS, para que a verificação em dois passos seja acionada para pontos finais de elevado valor.

Esta documentação abrange a utilização do Servidor Multi-Factor Authentication do Azure com o AD FS 2.0. Para mais informações sobre o AD FS, consulte [Securing cloud and on-premises resources using Azure Multi-Factor Authentication Server with Windows Server 2012 R2 AD FS (Proteger recursos na cloud e no local através do Servidor Multi-Factor Authentication do Azure com o AD FS no Windows Server 2012 R2)](howto-mfaserver-adfs-2012.md).

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft já não oferece O MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores (MFA) durante os eventos de entrada devem utilizar a autenticação multi-factor Azure AD baseada na nuvem.
>
> Para começar com mFA baseado na nuvem, consulte [Tutorial: Secure user in events with Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Se utilizar MFA baseado na nuvem, consulte [a Garantia de recursos em nuvem com autenticação multi-factor AD Azure e FS AD](howto-mfa-adfs.md).
>
> Os clientes existentes que ativaram o MFA Server antes de 1 de julho de 2019 podem descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

## <a name="secure-ad-fs-20-with-a-proxy"></a>Proteger o AD FS 2.0 com um proxy

Para proteger o AD FS 2.0 com um proxy, instale o Servidor Multi-Factor Authentication do Azure no servidor proxy do AD FS.

### <a name="configure-iis-authentication"></a>Configurar a autenticação do IIS

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone **Autenticação do IIS** no menu da esquerda.
2. Clique no separador **Baseado em Formulários**.
3. Clique em **Adicionar**.

   ![Janela de autenticação do servidor do MFA IIS](./media/howto-mfaserver-adfs-2/setup1.png)

4. Para detetar automaticamente o nome de utilizador, palavra-passe e variáveis de domínio, introduza o URL de login `https://sso.contoso.com/adfs/ls` (como) dentro da caixa de diálogo do site Form-Based Configurar automaticamente e clique **em OK**.
5. Selecione a caixa de correspondência de utilizador **Exigir autenticação Azure Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e forem sujeitos à verificação em dois passos. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor e/ou forem excluídos da verificação em dois passos, deixe a caixa desmarcada.
6. Se as variáveis da página não puderem ser detetadas automaticamente, clique no botão **Especificar Manualmente...** na caixa de diálogo Autoconfigurar Web site Baseado em Formulários.
7. Na caixa de diálogo do Site Add Form-Based, introduza o URL na página de login AD FS no campo URL de submissão `https://sso.contoso.com/adfs/ls` (como) e introduza um nome de Aplicação (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel.
8. Defina o formato do Pedido como **POST ou GET**.
9. Introduza a variável de Nome de Utilizador (ctl00$ContentPlaceHolder1$UsernameTextBox) e a variável de Palavra-passe (ctl00$ContentPlaceHolder1$PasswordTextBox). Se a página de início de sessão baseada em formulários apresentar uma caixa de texto do domínio, introduza também a variável de Domínio. para localizar os nomes das caixas de entrada na página de início de sessão num browser; clique com o botão direito do rato na página e selecione **Ver Origem** .
10. Selecione a caixa de correspondência de utilizador **Exigir autenticação Azure Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e forem sujeitos à verificação em dois passos. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor e/ou forem excluídos da verificação em dois passos, deixe a caixa desmarcada.

    ![Adicionar website baseado em formulários ao MFA Server](./media/howto-mfaserver-adfs-2/manual.png)

11. Clique em **Avançado...** para rever as definições avançadas. As definições que pode configurar incluem:

    - Selecione um ficheiro de página de rejeição personalizado
    - Coloque em cache as autenticações bem-sucedidas para o Web site através de cookies
    - Selecione como pretende autenticar as credenciais primárias

12. Uma vez que é provável que o servidor proxy do AD FS não esteja associado ao domínio, pode utilizar o LDAP para ligar ao seu controlador de domínio para importação e pré-autenticação de utilizadores. Na caixa de diálogo Web site Baseado em Formulários Avançado, clique no separador **Autenticação Primária** e selecione **Enlace de LDAP** para o tipo de autenticação Pré-autenticação.
13. Quando terminar, clique em **OK** para regressar à caixa de diálogo Adicionar Web site Baseado em Formulários.
14. Clique em **OK** para fechar a caixa de diálogo.
15. Assim que as variáveis de URL e página forem detetadas ou introduzidas, os dados do site são apresentados no painel Baseado em Formulários.
16. Clique no **separador Módulo Nativo** e selecione o servidor, o website em que o proxy AD FS está a ser gerido (como "Site Padrão"), ou a aplicação de procuração AD FS (como "ls" em "adfs") para permitir o plug-in IIS ao nível pretendido.
17. Clique na caixa **Ativar autenticação do IIS** na parte superior do ecrã.

A autenticação do IIS está agora ativada.

### <a name="configure-directory-integration"></a>Configurar a integração de diretórios

Ativou a autenticação do IIS, mas para executar a pré-autenticação para o Active Directory (AD) através de LDAP, tem de configurar a ligação LDAP para o controlador de domínio.

1. Clique no ícone **Integração de Diretórios**.
2. No separador Definições, selecione o botão de opção **Utilizar configuração de LDAP específica**.

   ![Configurar definições LDAP para definições específicas de LDAP](./media/howto-mfaserver-adfs-2/ldap1.png)

3. Clique em **Editar**.
4. Na caixa de diálogo Editar Configuração de LDAP, preencha os campos com as informações necessárias para ligar ao controlador de domínio do AD. O ficheiro de ajuda do Servidor Multi-Factor Authentication do Azure inclui descrições dos campos.
5. Teste a ligação LDAP, clicando no botão **Testar**.

   ![Teste configuração LDAP no servidor MFA](./media/howto-mfaserver-adfs-2/ldap2.png)

6. Se o teste de ligação LDAP for concluído com êxito, clique em **OK**.

### <a name="configure-company-settings"></a>Configurar definições da empresa

1. Em seguida, clique no ícone **Definições da Empresa** e selecione o separador **Resolução de Nomes de Utilizador**.
2. Selecione o botão de opção **Utilizar o atributo de identificador exclusivo de LDAP para nomes de utilizador correspondentes**.
3. Se os utilizadores introduzirem o seu nome de utilizador no formato "domínio\username", o Servidor tem de ser capaz de retirar o domínio do nome de utilizador quando criar a consulta LDAP. Isso pode ser feito através de uma definição de registo.
4. Abra o editor de registo e aceda a HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor num servidor de 64 bits. Se num servidor de 32 bits, tire o "Wow6432Node" do caminho. Crie uma chave de registo DWORD chamada "UsernameCxz_stripPrefixDomain" e desa um valor para 1. O Multi-Factor Authentication do Azure está agora a proteger o proxy do AD FS.

Certifique-se de que os utilizadores foram importados do Active Directory para o Servidor. Consulte a [secção IPs Fidedigna](#trusted-ips) se pretende permitir endereços IP internos para que não seja necessária uma verificação em duas etapas ao iniciar sessão no site a partir dessas localizações.

![Editor de registo para configurar as configurações da empresa](./media/howto-mfaserver-adfs-2/reg.png)

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 Direct sem proxy

Pode proteger o AD FS quando o proxy do AD FS não é utilizado. Instale o Servidor Multi-Factor Authentication do Azure no servidor do AD FS e configure o Servidor de acordo com os seguintes passos:

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone **Autenticação do IIS** no menu da esquerda.
2. Clique no separador **HTTP**.
3. Clique em **Adicionar**.
4. Na caixa de diálogo URL De Base Adicionar, insira o URL para o website AD FS onde a autenticação HTTP é realizada (como `https://sso.domain.com/adfs/ls/auth/integrated` ) no campo URL base. Em seguida, introduza um Nome da aplicação (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel.
5. Se pretender, ajuste o Tempo limite de inatividade e os Tempos máximos de sessão.
6. Selecione a caixa de correspondência de utilizador **Exigir autenticação Azure Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e forem sujeitos à verificação em dois passos. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor e/ou forem excluídos da verificação em dois passos, deixe a caixa desmarcada.
7. Selecione a caixa da cache de cookies, se pretender.

   ![AD FS 2.0 Direct sem proxy](./media/howto-mfaserver-adfs-2/noproxy.png)

8. Clique em **OK**.
9. Clique no **separador Módulo Nativo** e selecione o servidor, o website (como "Web Site padrão"), ou a aplicação AD FS (como "ls" em "adfs") para ativar o plug-in IIS ao nível pretendido.
10. Clique na caixa **Ativar autenticação do IIS** na parte superior do ecrã.

O Multi-Factor Authentication do Azure está agora a proteger o AD FS.

Certifique-se de que os utilizadores foram importados do Active Directory para o Servidor. Consulte a secção IPs Fidedigna se pretende permitir endereços IP internos para que não seja necessária uma verificação em duas etapas ao iniciar sessão no site a partir dessas localizações.

## <a name="trusted-ips"></a>IPs Fidedignos

Os IPs Fidedignos permitem aos utilizadores ignorar o Multi-Factor Authentication do Azure para pedidos de sites com origem em sub-redes ou endereços IP específicos. Por exemplo, pode querer excluir utilizadores da verificação em dois passos quando iniciam sessão a partir do escritório. Para tal, especifique a sub-rede do escritório como uma entrada de IPs Fidedignos.

### <a name="to-configure-trusted-ips"></a>Para configurar IPs fidedignos

1. Na secção Autenticação do IIS, clique no separador **IPs Fidedignos**.
2. Clique no botão **Adicionar…** Adicionar...
3. Quando for apresentada a caixa de diálogo Adicionar IPs Fidedignos, selecione o botão de opção **IP único**, **Intervalo de IPs** ou **Sub-rede**.
4. Introduza o endereço IP, intervalo de endereços IP ou sub-rede que deve ser permitida. Se introduzir uma sub-rede, selecione a Máscara de rede adequada e clique no botão **OK**.

![Configure iPs fidedignos ao Servidor MFA](./media/howto-mfaserver-adfs-2/trusted.png)
