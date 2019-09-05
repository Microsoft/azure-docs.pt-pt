---
title: Habilitar entrada de chave de segurança sem senha para o Azure AD (versão prévia)-Azure Active Directory
description: Habilitar a entrada de chave de segurança sem senha no Azure AD usando as chaves de segurança do FIDO2 (versão prévia)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 316a523a6216354ae5b6166be55e183a4e050766
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305079"
---
# <a name="enable-passwordless-security-key-sign-in-for-azure-ad-preview"></a>Habilitar entrada de chave de segurança sem senha para o Azure AD (versão prévia)

## <a name="requirements"></a>Requisitos

* Multi-Factor Authentication do Azure
* Visualização de registro combinado com usuários habilitados para SSPR
* A visualização da chave de segurança do FIDO2 requer chaves de segurança FIDO2 compatíveis
* Webauthn requer o Microsoft Edge no Windows 10 versão 1809 ou superior
* O logon do Windows baseado em FIDO2 requer o Azure AD ingressado no Windows 10 versão 1809 ou superior

## <a name="prepare-devices-for-preview"></a>Preparar dispositivos para visualização

Os dispositivos com os quais você executará o piloto devem estar executando o Windows 10 versão 1809 ou superior. A melhor experiência é no Windows 10 versão 1903 ou superior.

## <a name="enable-security-keys-for-windows-sign-in"></a>Habilitar chaves de segurança para entrada no Windows

As organizações podem optar por usar um ou mais dos métodos a seguir para habilitar o uso de chaves de segurança para entrada no Windows.

### <a name="enable-credential-provider-via-intune"></a>Habilitar provedor de credenciais via Intune

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até **Microsoft Intune** > registro de**dispositivo** > inscrição do**Windows** > **Windows Hello para empresas** > **Propriedades**.
1. Em **configurações** , defina **usar chaves de segurança para entrar** como **habilitado**.

A configuração de chaves de segurança para entrar, não depende da configuração do Windows Hello para empresas.

#### <a name="enable-targeted-intune-deployment"></a>Habilitar implantação de destino do Intune

Para direcionar grupos de dispositivos específicos para habilitar o provedor de credenciais, use as seguintes configurações personalizadas por meio do Intune. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até **Microsoft Intune** >  > **perfis**de > configuração de dispositivo**Criar perfil**.
1. Configurar o novo perfil com as seguintes configurações
   1. Nome: Chaves de segurança para entrada no Windows
   1. Descrição: Permite que as chaves de segurança FIDO sejam usadas durante a entrada do Windows
   1. Plataforma: Windows 10 e posterior
   1. Tipo de perfil: Personalizar
   1. Configurações personalizadas de OMA-URI:
      1. Nome: Ativar as chaves de segurança do FIDO para entrar no Windows
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Tipo de dados: Integer
      1. Valor 1 
1. Essa política pode ser atribuída a usuários, dispositivos ou grupos específicos. Mais informações podem ser encontradas no artigo [atribuir perfis de usuário e de dispositivo no Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Criação de política de configuração de dispositivo personalizada do Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Habilitar o provedor de credenciais por meio do pacote de provisionamento

Para dispositivos não gerenciados pelo Intune, um pacote de provisionamento pode ser instalado para habilitar a funcionalidade. O aplicativo do Windows Configuration designer pode ser instalado por meio do [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Inicie o designer de configuração do Windows.
1. Selecione **arquivo** > **novo projeto**.
1. Dê um nome ao projeto e anote o caminho em que o projeto é criado.
1. Selecione **Seguinte**.
1. Deixe o **pacote de provisionamento** selecionado como o **fluxo de trabalho do projeto selecionado** e selecione **Avançar**.
1. Selecione **todas as edições da área de trabalho do Windows** em **escolher quais configurações exibir e configurar** e selecione **Avançar**.
1. Selecione **Concluir**.
1. Em seu projeto recém-criado, navegue até **configurações** > de tempo de execução**WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Defina **UseSecurityKeyForSignIn** como **habilitado**.
1. Selecione **Exportar** > **pacote de provisionamento**
1. Deixe os padrões na janela de **compilação** em **descrever o pacote de provisionamento** e selecione **Avançar**.
1. Deixe os padrões na janela de **compilação** em **selecionar detalhes de segurança para o pacote de provisionamento** e selecione **Avançar**.
1. Anote ou altere o caminho nas janelas de **Build** em **selecionar onde salvar o pacote de provisionamento** e selecione **Avançar**.
1. Selecione **Compilar** na página **criar o pacote de provisionamento** .
1. Salve os dois arquivos criados (ppkg e Cat) em um local em que você possa aplicá-los aos computadores mais tarde.
1. Siga as orientações no artigo [aplicar um pacote de provisionamento](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)para aplicar o pacote de provisionamento que você criou.

## <a name="obtain-fido2-security-keys"></a>Obter chaves de segurança do FIDO2

Consulte a seção FIDO2 Security Keys, no artigo [o que é sem senha?](concept-authentication-passwordless.md) para obter mais informações sobre as chaves e os fabricantes com suporte.

> [!NOTE]
> Se você comprar e planejar usar as chaves de segurança com base em NFC, precisará de um leitor NFC com suporte.

## <a name="enable-passwordless-authentication-method"></a>Habilitar método de autenticação com senha

### <a name="enable-the-combined-registration-experience"></a>Habilitar a experiência de registro combinado

Os recursos de registro para métodos de autenticação com senha contam com a visualização de registro combinada. Siga as etapas no artigo [habilitar o registro de informações de segurança combinadas (versão prévia)](howto-registration-mfa-sspr-combined.md)para habilitar a visualização de registro combinado.

### <a name="enable-new-passwordless-authentication-method"></a>Habilitar novo método de autenticação com senha

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Navegue até **Azure Active Directory** > política de método de autenticação de**métodos** > de autenticação de**segurança** >  **(versão prévia)**
1. Em cada **método**, escolha as seguintes opções
   1. **Habilitar** -Sim ou não
   1. **Destino** -todos os usuários ou Selecionar usuários
1. **Salvar** cada método

> [!WARNING]
> As "políticas de restrição de chave" do FIDO2 ainda não funcionam. Essa funcionalidade estará disponível antes da disponibilidade geral, não altere essas políticas por padrão.

> [!NOTE]
> Você não precisa aceitar os dois métodos sem senha (se quiser Visualizar apenas um método sem senha, você pode habilitar apenas esse método). Incentivamos você a experimentar os dois métodos, pois ambos têm seus próprios benefícios.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registro de usuário e gerenciamento de chaves de segurança FIDO2

1. Navegue até[https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Entrar se ainda não estiver
1. Clique em **informações de segurança**
   1. Se o usuário já tiver pelo menos um método de autenticação multifator do Azure registrado, ele poderá registrar imediatamente uma chave de segurança FIDO2.
   1. Se eles não tiverem pelo menos um método de autenticação multifator do Azure registrado, eles deverão adicionar um.
1. Adicione uma chave de segurança FIDO2 clicando em **Adicionar método** e escolhendo **chave de segurança**
1. Escolher **dispositivo USB** ou **dispositivo NFC**
1. Prepare sua chave e escolha **Avançar**
1. Uma caixa será exibida e solicitará que você crie/Insira um PIN para sua chave de segurança e, em seguida, execute o gesto necessário para sua chave biométrica ou toque.
1. Você será retornado para a experiência de registro combinada e deverá fornecer um nome significativo para o token para que você possa identificar qual deles se tiver vários. Clique em **Seguinte**.
1. Clique em **concluído** para concluir o processo

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Gerenciar chave de segurança biométrica, PIN ou redefinição de chaves de segurança

* Windows 10 versão 1809
   * O software complementar do fornecedor da chave de segurança é necessário
* Windows 10 versão 1903 ou superior
   * Os usuários podem abrir **as configurações do Windows** em seu dispositivo >**chave de segurança** de **contas** > 
   * Os usuários podem alterar seu PIN, atualizar a biometria ou redefinir sua chave de segurança

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registro de usuário e gerenciamento do aplicativo Microsoft Authenticator

Para configurar o aplicativo Microsoft Authenticator para entrada no telefone, siga as orientações no artigo [entrar em suas contas usando o aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

## <a name="sign-in-with-passwordless-credential"></a>Entrar com credencial sem senha

### <a name="sign-in-at-the-lock-screen"></a>Entrar na tela de bloqueio

No exemplo abaixo, um usuário bala Sandhu já provisionou sua chave de segurança FIDO2. Bala pode escolher o provedor de credenciais de chave de segurança na tela de bloqueio do Windows 10 e inserir a chave de segurança para entrar no Windows.

![Entrada de chave de segurança na tela de bloqueio do Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Entrar na Web

No exemplo abaixo, um usuário já provisionou sua chave de segurança FIDO2. O usuário pode optar por entrar na Web com sua chave de segurança FIDO2 dentro do navegador Microsoft Edge no Windows 10 versão 1809 ou superior.

![Entrada de chave de segurança no Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="security-key-provisioning"></a>Provisionamento de chave de segurança

O provisionamento e desprovisionamento de chaves de segurança do administrador não estão disponíveis na visualização pública.

### <a name="hybrid-azure-ad-join"></a>Associação ao Azure AD Híbrido

Os usuários que dependem do SSO do WIA que usam credenciais gerenciadas como chaves de segurança FIDO2 ou logon sem senha com Microsoft Authenticator aplicativo precisam ingressar no Windows 10 para obter os benefícios do SSO. No entanto, as chaves de segurança só funcionam para computadores Azure Active Directory associados por enquanto. Recomendamos que você experimente apenas as chaves de segurança do FIDO2 para a tela de bloqueio do Windows em computadores adicionados com Azure Active Directory pura. Essa limitação não se aplica à Web.

### <a name="upn-changes"></a>Alterações de UPN

Estamos trabalhando para dar suporte a um recurso que permita a alteração de UPN em dispositivos AADJ e AADJ híbridos. Se o UPN de um usuário for alterado, você não poderá mais modificar as chaves de segurança FIDO2 para considerar isso. Portanto, a única abordagem é redefinir o dispositivo e o usuário precisa se registrar novamente.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre o registro de dispositivo](../devices/overview.md)

[Saiba mais sobre a autenticação multifator do Azure](../authentication/howto-mfa-getstarted.md)
