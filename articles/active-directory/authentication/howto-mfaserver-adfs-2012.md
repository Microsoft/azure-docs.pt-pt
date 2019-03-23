---
title: Servidor MFA do Azure com o AD FS no Windows Server – o Azure Active Directory
description: Este artigo descreve como começar a utilizar o Multi-Factor Authentication do Azure e o AD FS no Windows Server 2012 R2 e 2016.
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
ms.openlocfilehash: 970e570d9ad27da2690cd38fe480823128322db0
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370708"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-in-windows-server"></a>Configurar o Servidor Multi-Factor Authentication do Azure para trabalhar com o AD FS no Windows Server

Se utilizar os Serviços de Federação do Active Directory (AD FS) e pretender proteger os recursos na nuvem ou no local, poderá configurar o Servidor Multi-Factor Authentication do Azure para trabalhar com o AD FS. Esta configuração aciona a verificação de dois passos para pontos finais de alto valor.

Neste artigo, discutimos a utilização do Servidor Multi-Factor Authentication do Azure com o AD FS no Windows Server 2012 R2 ou no Windows Server 2016. Para obter mais informações, leia sobre como [proteger recursos na nuvem e no local através do Servidor Multi-Factor Authentication do Azure com o AD FS 2.0](howto-mfaserver-adfs-2.md).

## <a name="secure-windows-server-ad-fs-with-azure-multi-factor-authentication-server"></a>Proteger o Windows Server AD FS com o Servidor Multi-Factor Authentication do Azure

Quando instala o Servidor Multi-Factor Authentication do Azure, tem as seguintes opções:

* Instalar o Servidor Multi-Factor Authentication do Azure localmente no mesmo servidor do AD FS
* Instalar o adaptador do Multi-Factor Authentication do Azure localmente no Servidor do AD FS e instalar o Servidor Multi-Factor Authentication num computador diferente

Antes de começar, tenha em atenção as seguintes informações:

* Não é necessário instalar o Servidor Multi-Factor Authentication do Azure no seu servidor AD FS. No entanto, tem de instalar o adaptador do Multi-Factor Authentication para AD FS num Windows Server 2012 R2 ou Windows Server 2016 que esteja a executar o AD FS. Pode instalar o servidor num computador diferente se instalar o adaptador AD FS separadamente no seu servidor de federação do AD FS. Consulte os seguintes procedimentos para saber como instalar o adaptador em separado.
* Se a sua organização utilizar os métodos de verificação por mensagem de texto ou por aplicação móvel, as cadeias definidas nas Definições da Empresa incluirão um marcador de posição <$*application_name*$>. No servidor do MFA v7.1, pode fornecer um nome de aplicação que substitui este marcador de posição. Na versão v7.0 e anteriores, este marcador de posição não é substituído automaticamente quando utiliza o adaptador AD FS. Nestas versões mais antigas, remova o marcador de posição das cadeias adequadas quando proteger o AD FS.
* A conta que utiliza para iniciar sessão tem de ter direitos de utilizador para criar grupos de segurança no seu serviço do Active Directory.
* O assistente de instalação do adaptador AD FS do Multi-Factor Authentication cria um grupo de segurança chamado PhoneFactor Admins na sua instância do Active Directory. Este adiciona, em seguida, a conta de serviço do AD FS do seu serviço de federação a este grupo. Verifique se o grupo PhoneFactor Admins foi criado no controlador de domínio e se a conta de serviço do AD FS faz parte deste grupo. Se necessário, adicione manualmente a conta de serviço do AD FS ao grupo PhoneFactor Admins no seu controlador de domínio.
* Para obter informações sobre como instalar o SDK do Serviço Web com o portal de utilizador, veja [implementar o portal de utilizador do Servidor Multi-Factor Authentication do Azure.](howto-mfaserver-deploy-userportal.md)

### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Instalar o Servidor Multi-Factor Authentication do Azure localmente no servidor AD FS

1. Transfira e instale o Servidor Multi-Factor Authentication do Azure no seu servidor AD FS. Para obter informações de instalação, consulte a [introdução ao Servidor Multi-Factor Authentication do Azure](howto-mfaserver-deploy.md).
2. Na consola de gestão do Servidor Multi-Factor Authentication do Azure, clique no ícone **AD FS**. Selecione as opções **Permitir inscrição de utilizadores** e **Permitir que os utilizadores selecionem o método**.
3. Selecione outras opções adicionais que pretende especificar para a sua organização.
4. Clique em **Instalar Adaptador AD FS**.

   ![Instalar o adaptador ADFS a partir da consola do servidor MFA](./media/howto-mfaserver-adfs-2012/server.png)

5. Se for apresentada a janela do Active Directory, poderá significar duas coisas. O seu computador está associado a um domínio e a configuração do Active Directory para proteger a comunicação entre o adaptador AD FS e o serviço Multi-Factor Authentication está incompleta. Clique em **Seguinte** para concluir esta configuração automaticamente ou selecione a caixa de verificação **Ignorar configuração automática do Active Directory e configurar as definições manualmente**. Clique em **Seguinte**.
6. Se for apresentada a janela do Grupo Local, poderá significar duas coisas. O seu computador não está associado a um domínio e a configuração do grupo local para proteger a comunicação entre o adaptador AD FS e o serviço Multi-Factor Authentication está incompleta. Clique em **Seguinte** para concluir esta configuração automaticamente ou selecione a caixa de verificação **Ignorar configuração automática do Grupo Local e configurar as definições manualmente**. Clique em **Seguinte**.
7. No assistente de instalação, clique em **Seguinte**. O Servidor Multi-Factor Authentication do Azure cria o grupo PhoneFactor Admins e adiciona a conta de serviço do AD FS ao mesmo.
8. Na página **Iniciar o Instalador**, clique em **Seguinte**.
9. No instalador do adaptador AD FS do Multi-Factor Authentication, clique em **Seguinte**.
10. Clique em **Fechar** quando a instalação estiver terminada.
11. Quando o adaptador estiver instalado, precisa de o registar no AD FS. Abra o Windows PowerShell e execute o seguinte comando:

    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`

12. Para utilizar o adaptador recém-registado, edite a política de autenticação global do AD FS. Na consola de gestão do AD FS, aceda ao nó **Políticas de Autenticação**. Na secção **Multi-Factor Authentication**, clique na ligação **Editar** junto à secção **Definições Globais**. Na janela **Editar Política de Autenticação Global**, selecione **Multi-Factor Authentication** como método de autenticação adicional e, em seguida, clique em **OK**. O adaptador está registado como WindowsAzureMultiFactorAuthentication. Reinicie o serviço AD FS para o registo ser aplicado.

![Editar política de autenticação global](./media/howto-mfaserver-adfs-2012/global.png)

Neste momento, o Servidor Multi-Factor Authentication está configurado para ser um fornecedor de autenticação adicional para utilização com o AD FS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Instalar uma instância autónoma do adaptador AD FS com o SDK do Serviço Web

1. Instale o SDK do Serviço Web no servidor que está a executar o Servidor Multi-Factor Authentication.
2. Copie os seguintes ficheiros a partir do diretório do Servidor \Program Files\Multi-Factor Authentication para o servidor no qual pretende instalar o adaptador AD FS:
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. Execute o ficheiro de instalação MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. No instalador do adaptador AD FS do Multi-Factor Authentication, clique em **Seguinte** para iniciar a instalação.
5. Clique em **Fechar** quando a instalação estiver terminada.

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Editar o ficheiro MultiFactorAuthenticationAdfsAdapter.con fig

Siga estes passos para editar o ficheiro MultiFactorAuthenticationAdfsAdapter.config:

1. Defina o nó **UseWebServiceSdk** como **true**.  
2. Defina o valor de **WebServiceSdkUrl** para o URL do SDK do Serviço Web do Multi-Factor Authentication. Por exemplo: *<https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx>*, em que *certificatename* é o nome do seu certificado.  
3. Edite o ficheiro Register-MultiFactorAuthenticationAdfsAdapter.ps1 ao adicionar `-ConfigurationFilePath &lt;path&gt;` ao final do `Register-AdfsAuthenticationProvider` comando, em que *&lt;path&gt;* é o caminho completo para o ficheiro MultiFactorAuthenticationAdfsAdapter.config.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Configurar o SDK do Serviço Web com um nome de utilizador e uma palavra-passe

Existem duas opções para configurar o SDK do serviço Web. A primeira consiste em utilizar um nome de utilizador e uma palavra-passe e a segunda um certificado de cliente. Siga estes passos para a primeira opção ou ignore-os para ir para a segunda.  

1. Defina o valor de **WebServiceSdkUsername** para uma conta que faça parte do grupo de segurança PhoneFactor Admins. Utilize o formato &lt;domínio&gt;&#92;&lt;nome de utilizador&gt;.  
2. Defina o valor de **WebServiceSdkPassword** para a palavra-passe da conta adequada.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Configurar o SDK do Serviço Web com um certificado de cliente

Se não quiser utilizar um nome de utilizador e uma palavra-passe, siga estes passos para configurar o SDK do Serviço Web com um certificado de cliente.

1. Obtenha um certificado de cliente de uma autoridade de certificação para o servidor que está a executar o SDK do Serviço Web. Saiba como [obter certificados de cliente](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importe o certificado de cliente para o arquivo de certificados pessoais do computador local no servidor que está a executar o SDK do Serviço Web. Verifique que o certificado público da autoridade de certificação está no arquivo de Certificados de Raiz Fidedigna.  
3. Exporte as chaves públicas e privadas do certificado de cliente para um ficheiro .pfx.  
4. Exporte a chave pública no formato Base64 para um ficheiro .cer.  
5. No Gestor de Servidores, certifique-se de que a funcionalidade Servidor Web (IIS)\Servidor Web\Segurança\Autenticação de Mapeamento de Certificados de Cliente IIS está instalada. Se não estiver instalada, selecione **Adicionar Funções e Funcionalidades** para adicionar esta funcionalidade.  
6. No Gestor do IIS, faça duplo clique em **Editor de Configuração** no site que contém o diretório virtual do SDK do Serviço Web. É importante selecionar o Web site, e não o diretório virtual.  
7. Aceda à secção **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.  
8. Defina enabled como **true**.  
9. Defina oneToOneCertificateMappingsEnabled como **true**.  
10. Clique no botão **…** junto a oneToOneMappings e, em seguida, clique na ligação **Adicionar**.  
11. Abra o ficheiro .cer de Base64 que exportou anteriormente. Remova *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----* e quaisquer quebras de linha. Copie a cadeia resultante.  
12. Defina o certificado para a cadeia que copiou no passo anterior.  
13. Defina enabled como **true**.  
14. Defina userName para uma conta que seja membro do grupo de segurança PhoneFactor Admins. Utilize o formato &lt;domínio&gt;&#92;&lt;nome de utilizador&gt;.  
15. Defina a palavra-passe para a palavra-passe da conta adequada e, em seguida, feche o Editor de Configuração.  
16. Clique na ligação **Aplicar**.  
17. No diretório virtual do SDK do Serviço Web, faça duplo clique em **Autenticação**.  
18. Verifique se a Representação do ASP.NET e a Autenticação Básica estão definidas como **Ativadas** e todos os outros itens estão **Desativados**.  
19. No diretório virtual do SDK do Serviço Web, faça duplo clique em **Definições de SSL**.  
20. Defina os Certificados de Cliente para **Aceitar** e, em seguida, clique em **Aplicar**.  
21. Copie o ficheiro .pfx que exportou anteriormente para o servidor que está a executar o adaptador AD FS.  
22. Importe o ficheiro .pfx para o arquivo de certificados pessoais do computador local.  
23. Clique com o botão direito e selecione **Gerir Chaves Privadas**. Em seguida, conceda acesso de leitura à conta que utilizou para iniciar sessão no serviço do AD FS.  
24. Abra o certificado de cliente e copie o thumbprint a partir do separador **Detalhes**.  
25. No ficheiro MultiFactorAuthenticationAdfsAdapter.config, defina **WebServiceSdkCertificateThumbprint** para a cadeia que copiou no passo anterior.  

Por fim, para registar o adaptador, execute o script \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 no PowerShell. O adaptador está registado como WindowsAzureMultiFactorAuthentication. Reinicie o serviço AD FS para o registo ser aplicado.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteger recursos do Azure AD com o AD FS

Para proteger o recurso da cloud, configure uma regra de afirmações para que os Serviços de Federação do Active Directory emitam a afirmação de autenticação múltipla quando um utilizador executar uma verificação de dois passos com êxito. Esta afirmação é transmitida ao Azure AD. Siga este procedimento para percorrer os passos:

1. Abra a Gestão do AD FS.
2. À esquerda, selecione **Confianças de Entidades Confiadoras**.
3. Clique com o botão direito do rato na **Plataforma de Identidade do Microsoft Office 365** e selecione **Editar Regras de Afirmação...**

   ![Editar regras de afirmação na consola do AD FS](./media/howto-mfaserver-adfs-2012/trustedip1.png)

4. Em Regras de Transformação da Emissão, clique em **Adicionar Regra.**

   ![Editar regras de transformação na consola do AD FS](./media/howto-mfaserver-adfs-2012/trustedip2.png)

5. No Assistente para Adicionar Regra de Afirmação de Transformação, selecione **Passar ou Filtrar uma Afirmação de Entrada** no menu pendente e clique em **Seguinte**.

   ![Adicionar Assistente de regra de afirmação de transformação](./media/howto-mfaserver-adfs-2012/trustedip3.png)

6. Dê um nome à sua regra.
7. Selecione **Referências de Métodos de Autenticação** como o tipo de Afirmação de entrada.
8. Selecione **Passar todos os valores de afirmação**.

    ![Adicionar Assistente de regra de afirmação de transformação](./media/howto-mfaserver-adfs-2012/configurewizard.png)

9. Clique em **Concluir**. Feche a consola de Gestão do AD FS.

## <a name="troubleshooting-logs"></a>Registos de resolução de problemas

Para ajudar na resolução de problemas do Adaptador AD FS do Servidor MFA, utilize os passos que se seguem para ativar o registo adicional.

1. Na interface do Servidor MFA, abra a secção AD FS e selecione a caixa de verificação **Ativar registo**.
2. Em cada servidor AD FS, utilize o **regedit.exe** para criar a chave do Registo do valor da cadeia `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Positive Networks\PhoneFactor\InstallPath` com o valor `C:\Program Files\Multi-Factor Authentication Server\` (ou outro diretório da sua preferência).  **Tenha em atenção que a barra invertida à direita é importante.**
3. Crie o diretório `C:\Program Files\Multi-Factor Authentication Server\Logs` (ou outro diretório conforme referenciado no **Passo 2**).
4. Conceda o direito de modificação no diretório de Registos à conta do serviço AD FS.
5. Reinicie o serviço AD FS.
6. Certifique-se de que o ficheiro `MultiFactorAuthAdfsAdapter.log` foi criado no diretório de Registos.

## <a name="related-topics"></a>Tópicos relacionados

Para obter ajuda para resolução de problemas, veja [Azure Multi-Factor Authentication FAQs (FAQ do Multi-Factor Authentication do Azure)](multi-factor-authentication-faq.md)
