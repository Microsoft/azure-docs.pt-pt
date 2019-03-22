---
title: Portal de utilizador do servidor de MFA do Azure - Azure Active Directory
description: Introdução ao Azure MFA e ao portal de utilizador.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbee5316b78838bedc62454e8c1954eb5f9205ff
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317134"
---
# <a name="user-portal-for-the-azure-multi-factor-authentication-server"></a>Portal de utilizador do Servidor Multi-Factor Authentication do Azure

O portal de utilizador é um site do IIS que permite aos utilizadores inscreverem-se no Multi-Factor Authentication do Azure (MFA) e fazerem a manutenção das contas deles. O utilizador pode alterar o número de telefone, alterar o PIN ou optar por ignorar a verificação de dois passos durante o próximo início de sessão.

Os utilizadores iniciam sessão no portal de utilizador com o nome de utilizador e a palavra-passe habituais e, depois, concluem uma chamada de verificação de dois passos ou respondem a perguntas de segurança para terminar a autenticação. Se a inscrição de utilizadores for permitida, os utilizadores configuraram o número de telefone e o PIN na primeira vez que iniciam sessão no portal de utilizador.

Os Administradores do portal de Utilizador podem ser configurados e ser-lhes concedida permissão para adicionar novos utilizadores e atualizar utilizadores existentes.

Dependendo do seu ambiente, poderá implementar o portal de utilizador no mesmo servidor que o Servidor Multi-Factor Authentication ou noutro servidor com acesso à Internet.

![Portal de utilizador do MFA](./media/howto-mfaserver-deploy-userportal/portal.png)

> [!NOTE]
> O portal de utilizador apenas está disponível com o Servidor Multi-Factor Authentication. Se utilizar o Multi-Factor Authentication na cloud, refira o artigo [Set-up your account for two-step verification](../user-help/multi-factor-authentication-end-user-first-time.md) (Configurar a conta para verificação de dois passos) ou [Manage your settings for two-step verification](../user-help/multi-factor-authentication-end-user-manage-settings.md) (Gerir as definições de verificação em dois passos) aos seus utilizadores.

## <a name="install-the-web-service-sdk"></a>Instalar o SDK do serviço Web

Em ambos os cenários, se o SDK do Serviço Web do Multi-Factor Authentication do Azure ainda **não** estiver instalado no Servidor Multi-Factor Authentication (MFA) do Azure, conclua os passos que se seguem.

1. Abra a consola do Servidor Multi-Factor Authentication.
2. Aceda ao **SDK do Serviço Web** e selecione **Instalar SDK do Serviço Web**.
3. Conclua a instalação com as predefinições, a menos que tenha de as alterar por algum motivo.
4. Vincule um Certificado SSL ao site no IIS.

Se tiver dúvidas sobre como configurar um certificado SSL num servidor IIS, veja o artigo [How to Set Up SSL on IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Como realizar uma Cópia de Segurança do SSL no IIS).

O SDK do Serviço Web tem de estar protegido por um certificado SSL. Pode utilizar um certificado autoassinado para este fim. Importe o certificado para o arquivo “Autoridades de Certificação de Raiz Fidedigna” da conta de Computador Local no servidor Web do Portal de Utilizador, para que este confie no certificado quando inicia a ligação SSL.

![SDK do Serviço Web de configuração do Servidor MFA](./media/howto-mfaserver-deploy-userportal/sdk.png)

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Implementar o portal de utilizador no mesmo servidor do Servidor Multi-Factor Authentication do Azure

Os pré-requisitos seguintes são precisos para instalar o portal de utilizador no **mesmo servidor** do Servidor Multi-Factor Authentication do Azure:

* IIS, incluindo compatibilidade base meta para ASP.NET e IIS 6 (para IIS 7 ou posterior)
* Uma conta com direitos de administrador para o computador e Domínio, se aplicável. A conta necessita permissões para criar grupos de segurança do Active Directory.
* Proteja o portal de utilizador com um certificado SSL.
* Proteja o SDK do Serviço Web do Multi-Factor Authentication do Azure com um certificado SSL.

Para implementar o portal de utilizador, siga estes passos:

1. Abra a consola do Servidor Multi-Factor Authentication do Azure, clique no ícone do **Portal de Utilizador**, no menu à esquerda e clique em **Instalar o Portal de Utilizador**.
2. Conclua a instalação com as predefinições, a menos que tenha de as alterar por algum motivo.
3. Vincule um Certificado SSL ao site no IIS

   > [!NOTE]
   > Este certificado SSL é, normalmente, um certificado SSL assinado publicamente.

4. Abra um browser a partir de qualquer computador e navegue para o URL em que o portal de utilizador foi instalado (Exemplo: https://mfa.contoso.com/MultiFactorAuth). Certifique-se de que não são apresentados erros ou avisos de certificado.

![Instalação do Portal de Utilizador do Servidor MFA](./media/howto-mfaserver-deploy-userportal/install.png)

Se tiver dúvidas sobre como configurar um certificado SSL num servidor IIS, veja o artigo [How to Set Up SSL on IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Como realizar uma Cópia de Segurança do SSL no IIS).

## <a name="deploy-the-user-portal-on-a-separate-server"></a>Implementar no portal do utilizador num servidor separado

Se o servidor onde está a executar o Servidor Multi-Factor Authentication do Azure não tem acesso à Internet, deve instalar o portal do utilizador num **servidor separado com acesso à Internet**.

Se a sua organização utiliza a aplicação Microsoft Authenticator como um dos métodos de verificação e pretender implementar o portal de utilizador no próprio servidor, execute os seguintes requisitos:

* Utilize a v6.0 ou superior do Servidor Multi-Factor Authentication do Azure.
* Instale o portal de utilizador num servidor Web com acesso à Internet com Serviços de Informação Internet (IIS) da Microsoft 6.x ou superior.
* Ao utilizar o IIS 6.x, certifique-se de que ASP.NET v2.0.50727 está instalado, registado e definido como **Permitido**.
* Ao utilizar o IIS 7.x ou superior, IIS, incluindo compatibilidade base meta para Autenticação Básica, ASP.NET e IIS 6.
* Proteja o portal de utilizador com um certificado SSL.
* Proteja o SDK do Serviço Web do Multi-Factor Authentication do Azure com um certificado SSL.
* Certifique-se de que o portal de utilizador consegue ligar-se ao SDK do Serviço Web do Multi-Factor Authentication do Azure através de SSL.
* Certifique-se de que o portal de utilizador consegue comunicar com o SDK do Serviço Web do Multi-Factor Authentication do Azure com as credenciais de uma conta de serviço no grupo de segurança “PhoneFactor Admins”. Esta conta de serviço e o grupo devem existir no Active Directory se o Servidor Multi-Factor Authentication do Azure estiver em execução num servidor associado a um domínio. Esta conta de serviço e o grupo existem localmente no Servidor Multi-Factor Authentication do Azure se não estiver associado a um domínio.

Instalar o portal de utilizador num servidor diferente do Servidor Multi-Factor Authentication do Azure requer os seguintes passos:

1. **No servidor MFA**, navegue para o caminho de instalação (exemplo: C:\Programas\Microsoft files\servidor multi-Factor Authentication Server) e copie o ficheiro **MultiFactorAuthenticationUserPortalSetup64** num local acessível para o servidor de acesso à internet, onde irá instalá-lo.
2. **No servidor Web com acesso à Internet**, execute o ficheiro de instalação MultiFactorAuthenticationUserPortalSetup64 como administrador, altere o Site e o diretório Virtual para um nome abreviado, se o pretender.
3. Vincule um Certificado SSL ao site no IIS.

   > [!NOTE]
   > Este certificado SSL é, normalmente, um certificado SSL assinado publicamente.

4. Navegue até **C:\inetpub\wwwroot\MultiFactorAuth**
5. Edite o ficheiro Web.Config no Bloco de notas

    * Encontre a chave **"USE_WEB_SERVICE_SDK"** e altere **value="false"** para **value="true"**
    * Encontre a chave **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** e altere **value=""** para **value="DOMAIN\User"**, onde DOMAIN\User é uma Conta de Serviço que pertence ao Grupo "PhoneFactor Admins".
    * Encontre a chave **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** e altere **value=""** para **value="Password"**, onde a Palavra-passe é a palavra-passe da Conta de Serviço introduzida na linha anterior.
    * Procure o valor **https://www.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx** e altere este URL de marcador de posição para o URL do SDK do Serviço Web que instalámos no passo 2.
    * Guarde o ficheiro Web.Config e feche o Bloco de notas.

6. Abra um browser a partir de qualquer computador e navegue para o URL em que o portal de utilizador foi instalado (Exemplo: https://mfa.contoso.com/MultiFactorAuth). Certifique-se de que não são apresentados erros ou avisos de certificado.

Se tiver dúvidas sobre como configurar um certificado SSL num servidor IIS, veja o artigo [How to Set Up SSL on IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) (Como realizar uma Cópia de Segurança do SSL no IIS).

## <a name="configure-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar as definições do portal de utilizador no Servidor Multi-Factor Authentication do Azure

Agora que o portal de utilizador está instalado, tem de configurar o Servidor Multi-Factor Authentication do Azure para trabalhar com o mesmo.

1. Na consola do Servidor Multi-Factor Authentication do Azure, clique no ícone **Portal de Utilizador**. No separador Definições, introduza o URL do portal de utilizador na caixa de texto **URL do Portal de Utilizador**. Se a funcionalidade de e-mail tiver sido ativada, este URL vai ser incluído nos e-mails que são enviados aos utilizadores quando forem importados para o Servidor Multi-Factor Authentication do Azure.
2. Escolha as definições que pretende utilizar no Portal de Utilizador. Por exemplo, se os utilizadores tiverem permissão para escolher os métodos de autenticação deles, certifique-se de que **Permitir que os utilizadores selecionem o método** está marcado, juntamente com os métodos que podem escolher.
3. Defina quem deverão ser os Administradores no separador **Administradores**. Pode criar permissões administrativas granulares com as caixas de verificação e as listas pendentes nas caixas Adicionar/Editar.

Configuração opcional:
- **Perguntas de Segurança** - defina perguntas de segurança aprovadas para o seu ambiente e o idioma em que aparecem.
- **Sessões Passadas** - configure a integração do portal de utilizador com um site baseado em formulários através do MFA.
- **IPs Fidedignos** - permita que os utilizadores ignorem o MFA ao autenticarem uma lista de IPs fidedignos ou intervalos.

![Configuração do Portal de Utilizador do Servidor MFA](./media/howto-mfaserver-deploy-userportal/config.png)

O Servidor Multi-Factor Authentication do Azure fornece várias opções para o portal de utilizador. A tabela seguinte disponibiliza uma lista destas opções e uma explicação das respetivas utilizações.

| Definições do Portal de Utilizador | Descrição |
|:--- |:--- |
| URL do Portal de Utilizador | Introduzir o URL no qual o portal está alojado. |
| Autenticação primária | Especifique o tipo de autenticação a utilizar quando iniciar sessão no portal. Autenticação do Windows, Radius ou LDAP. |
| Permitir que os utilizadores iniciem sessão | Permitir aos utilizadores introduzir um nome de utilizador e palavra-passe na página de início de sessão do portal de utilizador. Se esta opção não estiver selecionada, as caixas estarão esbatidas. |
| Permitir a inscrição de utilizador | Permitir ao utilizador inscrever-se na Multi-Factor Authentication, ao redirecioná-lo para um ecrã de configuração que lhe pede informações adicionais, como o número de telefone. O pedido do telefone alternativo permite aos utilizadores especificarem um número de telefone secundário. Pedir o token OATH de terceiros permite aos utilizadores especificar um token OATH de terceiros. |
| Permitir que os utilizadores iniciem a Omissão de Uso Individual | Permitir que os utilizadores iniciem uma omissão de uso individual. Se um utilizador definir esta opção, a mesma terá efeito da próxima vez que o utilizador iniciar sessão. O pedido de segundos de omissão fornece uma caixa ao utilizador, para que este possa alterar a predefinição de 300 segundos. Caso contrário, a omissão de uso individual só é adequada para 300 segundos. |
| Permitir que os utilizadores selecionem o método | Permitir aos utilizadores especificar o método de contacto principal. Este método pode ser chamada telefónica, mensagem de texto, aplicação móvel ou token OATH. |
| Permitir que os utilizadores selecionem o idioma | Permitir ao utilizador alterar o idioma que é utilizado para a chamada telefónica, mensagem de texto, aplicação móvel ou token OATH. |
| Permitir que os utilizadores ativem a aplicação móvel | Permitir aos utilizadores gerar um código de ativação para concluir o processo de ativação da aplicação móvel que é utilizada com o servidor.  Também pode definir o número de dispositivos em que os utilizadores podem ativar a aplicação, entre um e dez. |
| Utilizar perguntas de segurança para contingência | Permitir perguntas de segurança no caso de falha da verificação em dois passos. Pode especificar o número de perguntas de segurança que têm de ser respondidas com êxito. |
| Permitir aos utilizadores associar token OATH de terceiros | Permitir aos utilizadores especificar um token OATH de terceiros. |
| Utilizar token OATH para contingência | Permitir a utilização de um token OATH caso a verificação de dois passos falhe. Também pode especificar o tempo limite de sessão em minutos. |
| Ativar registo | Ativar o registo no portal de utilizador. Os ficheiros de registo estão localizados em: C:\Programas\Microsoft files\servidor multi-Factor authentication\registos. |

Estas definições ficam visíveis para o utilizador no portal assim que forem ativadas e iniciarem sessão no portal de utilizador.

![Definições do portal de utilizador](./media/howto-mfaserver-deploy-userportal/portalsettings.png)

### <a name="self-service-user-enrollment"></a>Inscrição de utilizador self-service

Se pretende que os utilizadores iniciem sessão e se inscrevam, tem de selecionar as opções **Permitir que os utilizadores iniciem sessão** e **Permitir a inscrição de utilizador**, no separador Definições. Lembre-se de que as definições que selecionar afetam a experiência de início de sessão do utilizador.

Por exemplo, quando um utilizador inicia sessão no portal de utilizador pela primeira vez, é direcionado para a página de Configuração de Utilizador do Multi-Factor Authentication do Azure. Dependendo de como tiver configurado o Multi-Factor Authentication do Azure, o utilizador poderá conseguir selecionar o método de autenticação.

Se o utilizador selecionar o método de autenticação Chamada de Voz ou tiver sido pré-configurado para utilizar este método, a página pede-lhe para introduzir o respetivo número de telefone principal e uma extensão, se aplicável. Também poderá ser permitida a introdução de um número de telefone de cópia de segurança.

![Registar números de telefone primários e de cópia de segurança](./media/howto-mfaserver-deploy-userportal/backupphone.png)

Se for pedido ao utilizador para utilizar um PIN quando fizer a autenticação, a página pede-lhe que crie um PIN. Após introduzir o número ou números de telefone e o PIN (se aplicável), o utilizador clica no botão **Telefonar-me Agora para Autenticação**. O Multi-Factor Authentication do Azure realiza uma chamada telefónica de autenticação para o número de telefone principal do utilizador. O utilizador tem de atender a chamada e introduzir o PIN (se aplicável), e premir # para avançar para o próximo passo do processo de autoinscrição.

Se o utilizador selecionar o método de autenticação por Mensagem SMS ou tiver sido pré-configurado para utilizar este método, a página pede-lhe o respetivo número de telemóvel. Se for pedido ao utilizador para utilizar um PIN quando fizer a autenticação, a página também lhe pede para introduzir um PIN.  Após introduzir o número de telefone e o PIN (se aplicável), o utilizador clica no botão **Enviar-me SMS Agora para Autenticação**. O Multi-Factor Authentication do Azure realiza uma verificação por SMS para o telemóvel do utilizador. O utilizador recebe a mensagem de texto com um código de acesso de uso individual (OTP) e, em seguida, responde à mensagem com esse OTP e o respetivo PIN (se aplicável).

![SMS do portal de utilizador](./media/howto-mfaserver-deploy-userportal/text.png)

Se o utilizador selecionar o método de verificação de Aplicação Móvel, a página pede-lhe que instale a aplicação Microsoft Authenticator no dispositivo e que gere um código de ativação. Depois de instalar a aplicação, o utilizador clica no botão Gerar Código de Ativação.

> [!NOTE]
> Para utilizar a aplicação Microsoft Authenticator, o utilizador tem de ativar as notificações push no dispositivo.

A página apresenta então um código de ativação e um URL, juntamente com uma imagem de código de barras. Se for pedido ao utilizador para utilizar um PIN quando fizer a autenticação, a página também lhe pede para introduzir um PIN. O utilizador introduz o código de ativação e o URL na aplicação Microsoft Authentication ou utiliza o leitor de código de barras para ler a imagem de código de barras e clica no botão Ativar.

Após a ativação estar concluída, o utilizador clica no botão **Autenticar-me Agora**. O Multi-Factor Authentication do Azure realiza uma verificação para a aplicação móvel do utilizador. O utilizador tem de introduzir o PIN (se aplicável), e premir o botão Autenticar na aplicação móvel para avançar para o próximo passo do processo de autoinscrição.

Se os administradores tiverem configurado o Servidor Multi-Factor Authentication do Azure para recolher perguntas e respostas de segurança, o utilizador é então direcionado para a página Perguntas de Segurança. O utilizador tem de selecionar quatro perguntas de segurança e fornecer respostas às perguntas selecionadas.

![Perguntas de segurança do portal de utilizador](./media/howto-mfaserver-deploy-userportal/secq.png)

A autoinscrição do utilizador está agora concluída e o utilizador tem sessão iniciada no portal de utilizador. Os utilizadores podem voltar a iniciar sessão no portal de utilizador em qualquer altura no futuro para alterar os números de telefone, PINs, métodos de autenticação e perguntas de segurança, se for permitido alterar os seus métodos pelos administradores deles.

## <a name="next-steps"></a>Passos Seguintes

- [Implemente o Serviço Web de Aplicações Móveis do Servidor Multi-Factor Authentication do Azure](howto-mfaserver-deploy-mobileapp.md)
