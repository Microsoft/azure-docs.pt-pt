---
title: Configurar o Azure Active Directory passe de início de sessão (pré-visualização)
description: Ativar o início de sessão para o Azure AD através de chaves de segurança de FIDO2 ou a aplicação Microsoft Authenticator (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ba2545467ebfbd032408aeee25b82b92a628f2a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712075"
---
# <a name="enable-passwordless-sign-in-for-azure-ad-preview"></a>Ativar o início de sessão para o Azure AD (pré-visualização)

## <a name="requirements"></a>Requisitos

* Multi-Factor Authentication do Azure
* Combinados pré-visualização do registo
* Pré-visualização de principais de segurança do FIDO2 requer chaves de segurança de FIDO2 compatíveis
* WebAuthN requer o Microsoft Edge no Windows 10 versão 1809 ou superior
* FIDO2 baseada em Windows início de sessão requer o Azure AD associado ao Windows 10 versão 1809 ou superior

## <a name="prepare-devices-for-preview"></a>Preparar os dispositivos para pré-visualização

Dispositivos que irá ser criar um piloto com tem de ter o Windows 10 versão 1809 ou superior. É a melhor experiência no Windows 10 versão 1903 ou superior.

## <a name="enable-security-keys-for-windows-sign-in"></a>Ativar chaves de segurança para início de sessão do Windows

As organizações podem optar por utilizar um ou mais dos métodos seguintes para ativar a utilização de chaves de segurança para o Windows iniciar sessão.

### <a name="enable-credential-provider-via-intune"></a>Ativar o provedor de credenciais através do Intune

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até **Microsoft Intune** > **inscrição de dispositivos** > **inscrição Windows** > **Hello do Windows para empresas** > **propriedades**.
1. Sob **configurações** definir **utilizar chaves de segurança para iniciar sessão** para **ativado**.

Configuração de chaves de segurança para início de sessão, não é dependente sobre como configurar o Windows Hello para empresas.

#### <a name="enable-targeted-intune-deployment"></a>Ativar a implementação do Intune visada

Para filtrar grupos de dispositivos específicos para permitir que o provedor de credenciais, utilize as seguintes definições personalizadas através do Intune. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até **Microsoft Intune** > **configuração do dispositivo** > **perfis** > **criar perfil**.
1. Configurar o novo perfil com as seguintes definições
   1. Nome: Chaves de segurança para o início de sessão do Windows
   1. Descrição: Permite que as chaves de segurança FIDO a serem usados durante o início de sessão no Windows
   1. Plataforma: Windows 10 e posterior
   1. Tipo de plataforma: Personalizar
   1. Definições de OMA-URI personalizadas:
      1. Nome: Ative as chaves de segurança FIDO para início de sessão no Windows
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Tipo de dados: Número inteiro
      1. Valor: 1 
1. Esta política pode ser atribuída a utilizadores específicos, dispositivos ou grupos. Mais informações podem ser encontradas no artigo [atribuir perfis de utilizador e dispositivo no Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Criação de política de configuração de dispositivo personalizado do Intune](./media/howto-authentication-passwordless-enable/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Ativar o provedor de credenciais através do pacote de aprovisionamento

Para dispositivos não geridos pelo Intune, pode ser instalado um pacote de aprovisionamento para ativar a funcionalidade. A aplicação Windows Configuration Designer pode ser instalada a partir da [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Inicie o estruturador de configuração do Windows.
1. Selecione **arquivo** > **novo projeto**.
1. Dê um nome ao seu projeto e anote o caminho onde o projeto ser criado.
1. Selecione **Seguinte**.
1. Deixe **pacote de aprovisionamento** selecionado como o **fluxo de trabalho de projeto de Selected** e selecione **seguinte**.
1. Selecione **edições de área de trabalho de todos os Windows** sob **escolhem quais as definições para ver e configurar** e selecione **seguinte**.
1. Selecione **Concluir**.
1. No projeto criado recentemente, navegue para **definições de tempo de execução** > **WindowsHelloForBusiness** > **SecurityKeys**  >  **UseSecurityKeyForSignIn**.
1. Definir **UseSecurityKeyForSignIn** ao **ativada**.
1. Selecione **exportar** > **pacote de aprovisionamento**
1. Deixe as predefinições no **crie** janela sob **descrevem o pacote de aprovisionamento** e selecione **seguinte**.
1. Deixe as predefinições no **crie** janela sob **selecionar os detalhes de segurança para o pacote de aprovisionamento** e selecione **seguinte**.
1. Tome nota do ou altera o caminho da **crie** windows sob **selecione onde pretende guardar o pacote de aprovisionamento** e selecione **seguinte**.
1. Selecione **crie** sobre o **criar o pacote de aprovisionamento** página.
1. Guarde os dois arquivos criados (ppkg e cat) para uma localização onde pode aplicá-las para máquinas mais tarde.
1. Siga as instruções no artigo [aplicam-se de um pacote de aprovisionamento](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)para aplicar o pacote de aprovisionamento que criou.

## <a name="obtain-fido2-security-keys"></a>Obter chaves de segurança de FIDO2

Consulte a secção chaves de segurança de FIDO2, no artigo [o que é o início?](concept-authentication-passwordless.md) para obter mais informações sobre chaves suportadas e fabricantes.

> [!NOTE]
> Se adquirir e planear a utilização de chaves de segurança de NFC com base terá um leitor de NFC suportado.

## <a name="enable-passwordless-authentication-methods"></a>Ativar métodos de autenticação de início

### <a name="enable-the-combined-registration-experience"></a>Ativar a experiência de registo combinado

Funcionalidades de registo para chaves de segurança de FIDO2 contam com a pré-visualização do registo combinada. Siga os passos abaixo para ativar a pré-visualização de registo combinada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Navegue até **do Azure Active Directory** > **as definições de utilizador**
   1. Clique em **gerir as definições para as funcionalidades de pré-visualização do painel de acesso**
   1. Sob **os utilizadores podem utilizar funcionalidades de pré-visualização para registar e gerir informações de segurança - aprimorada**.
      1. Escolher **selecionados** e escolha um grupo de utilizadores que irá participar na pré-visualização.
      1. Ou escolha **todos os** para ativar para todos os utilizadores no seu diretório.
1. Clique em **Guardar**

### <a name="enable-new-passwordless-authentication-methods"></a>Ativar os novos métodos de autenticação de início

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Navegue até **do Azure Active Directory** > **métodos de autenticação** > **política de método de autenticação (pré-visualização)**
1. Em cada **método**, selecione as seguintes opções
   1. **Ativar** - Sim ou não
   1. **Destino** -todos os utilizadores ou selecionar utilizadores
1. **Guardar** cada método

> [!WARNING]
> O FIDO2 «Políticas de restrição de chave» não funcionam ainda. Esta funcionalidade estará disponível antes da disponibilidade geral, não altere estas políticas da predefinição.

> [!NOTE]
> Não precisa de optar ativamente por participar em ambos os métodos de início (se quiser apenas um método de início de pré-visualização, pode ativar apenas esse método). É recomendável que experimente a ambos os métodos, uma vez que ambos têm seus próprios benefícios.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registo de utilizador e a gestão de chaves de segurança de FIDO2

1. Navegue para [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Inicie sessão em se não o tiver feito
1. Clique em **informações de segurança**
   1. Se o utilizador já tem pelo menos um método de multi-factor Authentication registado, imediatamente eles podem registrar uma chave de segurança FIDO2.
   1. Se não tiver pelo menos um método de multi-factor Authentication registado, tem de adicionar um.
1. Adicionar uma chave de segurança de FIDO2 clicando **adicione o método** e escolher **a chave de segurança**
1. Escolher **dispositivo USB** ou **dispositivo NFC**
1. Tem a chave está preparado e escolha **seguinte**
1. Uma caixa de irá aparecer e pedir-lhe criar/introduzir um PIN para a sua chave de segurança, em seguida, criar o gesto necessário para a sua chave biométrica ou touch.
1. Será retornado para a experiência de registo combinado e solicitado a fornecer um nome significativo para o seu token, para que possa identificar qual delas se tiver vários. Clique em **Seguinte**.
1. Clique em **feito** para concluir o processo

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Gerir a chave de segurança biométrica, PIN, ou a repor a chave de segurança

* Windows 10 versão 1809
   * É necessário o software complementar do fornecedor de chave de segurança
* Windows 10 versão 1903 ou superior
   * Os utilizadores podem abrir **definições do Windows** no respetivo dispositivo > **contas** > **a chave de segurança**
   * Os utilizadores podem alterar o PIN, atualizar biometria ou repor as chaves de segurança

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registo de utilizador e a gestão da aplicação Microsoft Authenticator

Para configurar a aplicação Microsoft Authenticator para início de sessão de telefone, siga as instruções no artigo [iniciar sessão nas suas contas com a aplicação Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

## <a name="sign-in-with-passwordless-credentials"></a>Inicie sessão com credenciais de início

### <a name="sign-in-at-the-lock-screen"></a>Inicie sessão no ecrã de bloqueio

No exemplo abaixo de um utilizador Bala Sandhu já aprovisionou a sua chave de segurança FIDO2. Bala pode escolher o fornecedor de credencial de chave de segurança a partir do ecrã de bloqueio do Windows 10 e inserir a chave de segurança para iniciar sessão no Windows.

![A chave de segurança, inicie sessão no ecrã de bloqueio do Windows 10](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Inicie sessão na web

No exemplo abaixo de um utilizador já tiver aprovisionado seus FIDO2 a chave de segurança. O utilizador pode optar por iniciar sessão na web com a sua chave de segurança FIDO2 dentro do browser Microsoft Edge no Windows 10 versão 1809 ou superior.

![Sessão de principais de segurança no Microsoft Edge](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-edge-sign-in.png)

Para obter informações sobre como iniciar sessão utilizando a aplicação Microsoft Authenticator, consulte o artigo [iniciar sessão nas suas contas com a aplicação Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

## <a name="known-issues"></a>Problemas conhecidos

### <a name="fido2-security-keys"></a>Chaves de segurança de FIDO2

#### <a name="security-key-provisioning"></a>Provisionamento de principais de segurança

Administrador de provisionamento e desprovisionamento de chaves de segurança não está disponível na pré-visualização pública.

#### <a name="hybrid-azure-ad-join"></a>Associação ao Azure AD Híbrido

Os utilizadores da entidade confiadora WIA SSO que utilizam credenciais geridas como chaves de segurança de FIDO2 ou passe inicie a sessão com a aplicação Microsoft Authenticator tem híbrida Junte-se no Windows 10 para obter os benefícios do SSO. No entanto, a segurança chaves só funcionam para máquinas associadas do Azure Active Directory por agora. Recomendamos que experimente apenas FIDO2 chaves de segurança para o ecrã de bloqueio do Windows em máquinas do Azure Active Directory associados a um puros. Esta limitação não se aplica para a web.

#### <a name="upn-changes"></a>Alterações UPN

Estamos a trabalhar no suporte a um recurso que permite a alteração de UPN híbrida AADJ e dispositivos AADJ. Se alterar o UPN de um utilizador, já não é possível modificar FIDO2 chaves de segurança para levar em conta que. Portanto, a única abordagem é para repor o dispositivo e o utilizador tem de voltar a registar.

### <a name="authenticator-app"></a>Aplicação de autenticador

#### <a name="ad-fs-integration"></a>Integração do AD FS

Quando um utilizador tiver ativado a credencial de passe do Microsoft Authenticator, a autenticação para que o usuário sempre será predefinido para enviar uma notificação para aprovação. Essa lógica impede que os utilizadores num inquilino híbrido sejam direcionadas para AD FS para verificação de início de sessão sem que o usuário tirar uma etapa adicional de clicar em "Use a palavra-passe em vez disso,". Este processo também irá ignorar quaisquer políticas de acesso condicional no local e fluxos de autenticação pass-through. A exceção a este processo é se um login_hint for especificado, um utilizador serão autoforwarded para o AD FS e ignorar a opção para utilizar as credenciais de início.

#### <a name="azure-mfa-server"></a>Servidor MFA do Azure

Os utilizadores finais que estão ativados para a MFA através do servidor de MFA do Azure no local de uma organização pode ainda criar e utilizar um início de sessão de telefone de início na credencial. Se o utilizador tenta atualizar várias instalações (5 +) do Microsoft Authenticator com a credencial, esta alteração poderá resultar num erro.  

#### <a name="device-registration"></a>Registo de dispositivo

Um dos pré-requisitos para criar esta credencial novas, forte, é que o dispositivo em que reside está registrado no inquilino do Azure AD, para um utilizador individual. Devido a restrições de registo do dispositivo, um dispositivo só pode ser registrado num único inquilino. Este limite significa que apenas uma conta profissional ou escolar na aplicação Microsoft Authenticator pode ser ativada para início de sessão de telefone.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre o registo de dispositivos](../devices/overview.md)

[Saiba mais sobre o multi-factor Authentication](../authentication/howto-mfa-getstarted.md)
