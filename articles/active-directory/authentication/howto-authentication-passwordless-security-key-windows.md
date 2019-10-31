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
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5758b1fbb9d311219e3dc4dd483691f6c9d80c1
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172162"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>Habilitar a entrada de chave de segurança sem senha em dispositivos Windows 10 (versão prévia)

Este documento se concentra em habilitar a autenticação sem senha baseada em chave de segurança FIDO2 com dispositivos Windows 10. No final deste artigo, você poderá entrar em aplicativos baseados na Web e no Azure AD que ingressaram em dispositivos Windows 10 com sua conta do Azure AD usando uma chave de segurança FIDO2.

|     |
| --- |
| As chaves de segurança do FIDO2 são um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre visualizações, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Requisitos

| Tipo de dispositivo | Ingressado no Azure AD | Ingressado no Azure AD híbrido |
| --- | --- | --- |
| [Autenticação multifator do Azure](howto-mfa-getstarted.md) | X | X |
| [Visualização do registro de informações de segurança combinadas](concept-registration-mfa-sspr-combined.md) | X | X |
| [Chaves de segurança FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatíveis | X | X |
| Webauthn requer o Windows 10 versão 1809 ou superior | X | X |
| [Dispositivos ingressados no Azure ad](../devices/concept-azure-ad-join.md) requerem o Windows 10 versão 1809 ou superior | X |   |
| [Dispositivos ingressados no Azure ad híbrido](../devices/concept-azure-ad-join-hybrid.md) exigem o Windows 10 Insider Build 18945 ou superior |   | X |
| Controladores de domínio do Windows Server 2016/2019 totalmente corrigidos. |   | X |
| Atualizar para a versão mais recente do [Azure ad Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (opcional) | X | X |
| Pacote de provisionamento (opcional) | X | X |
| Política de Grupo (opcional) |   | X |

### <a name="unsupported-scenarios"></a>Cenários sem suporte

- **Não há suporte para**a implantação do Windows Server Active Directory Domain Services (AD DS) ingressado no domínio (somente dispositivos locais).
- Os cenários RDP, VDI e Citrix **não têm suporte** usando a chave de segurança.
- **Não há suporte para** S/MIME usando a chave de segurança.
- **Não há suporte para** "executar como" usando a chave de segurança.
- **Não há suporte**para fazer logon em um servidor usando a chave de segurança.
- Se você não tiver usado sua chave de segurança para entrar em seu dispositivo enquanto estiver online, você não poderá usá-lo para entrar ou desbloquear offline.

## <a name="prepare-devices-for-preview"></a>Preparar dispositivos para visualização

Os dispositivos ingressados no Azure AD com os quais você fará o piloto devem estar executando o Windows 10 versão 1809 ou superior. A melhor experiência é no Windows 10 versão 1903 ou superior.

Dispositivos ingressados no Azure AD híbridos com os quais você estará realizando o piloto devem estar executando o Windows 10 Insider Build 18945 ou mais recente.

## <a name="enable-security-keys-for-windows-sign-in"></a>Habilitar chaves de segurança para entrada no Windows

As organizações podem optar por usar um ou mais dos métodos a seguir para habilitar o uso de chaves de segurança para entrada do Windows com base nos requisitos de sua organização.

- [Habilitar com o Intune](#enable-with-intune)
   - [Implantação do Intune de destino](#targeted-intune-deployment)
- [Habilitar com um pacote de provisionamento](#enable-with-a-provisioning-package)
- [Habilitar com Política de Grupo (somente dispositivos ingressados no Azure AD híbrido)](#enable-with-group-policy)

> [!IMPORTANT]
> Organizações com **dispositivos ingressados no Azure ad híbrido** **também** devem concluir as etapas no artigo [habilitar a autenticação FIDO2 para recursos locais](howto-authentication-passwordless-security-key-on-premises.md) antes que a autenticação de chave de segurança do Windows 10 FIDO2 funcione.
>
> As organizações com **dispositivos ingressados no Azure ad** devem fazer isso antes que seus dispositivos possam se autenticar em recursos locais com chaves de segurança FIDO2.

### <a name="enable-with-intune"></a>Habilitar com o Intune

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até **Microsoft Intune** > **registro de dispositivo** > **registro do Windows** > **Propriedades** **do Windows Hello para empresas** > .
1. Em **configurações** , defina **usar chaves de segurança para entrar** como **habilitado**.

A configuração de chaves de segurança para entrada não depende da configuração do Windows Hello para empresas.

#### <a name="targeted-intune-deployment"></a>Implantação do Intune de destino

Para direcionar grupos de dispositivos específicos para habilitar o provedor de credenciais, use as seguintes configurações personalizadas por meio do Intune.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até **Microsoft Intune** > **configuração do dispositivo** > **perfis** > **Criar perfil**.
1. Configurar o novo perfil com as seguintes configurações
   1. Nome: chaves de segurança para entrar no Windows
   1. Descrição: permite que as chaves de segurança FIDO sejam usadas durante a entrada do Windows
   1. Plataforma: Windows 10 e posterior
   1. Tipo de perfil: personalizado
   1. Configurações personalizadas de OMA-URI:
      1. Nome: ativar as chaves de segurança do FIDO para entrar no Windows
      1. OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Tipo de dados: inteiro
      1. Valor: 1
1. Essa política pode ser atribuída a usuários, dispositivos ou grupos específicos. Mais informações podem ser encontradas no artigo [atribuir perfis de usuário e de dispositivo no Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Criação de política de configuração de dispositivo personalizada do Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Habilitar com um pacote de provisionamento

Para dispositivos não gerenciados pelo Intune, um pacote de provisionamento pode ser instalado para habilitar a funcionalidade. O aplicativo do Windows Configuration designer pode ser instalado por meio do [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Inicie o designer de configuração do Windows.
1. Selecione **arquivo** > **novo projeto**.
1. Dê um nome ao projeto e anote o caminho em que o projeto é criado.
1. Selecione **Seguinte**.
1. Deixe o **pacote de provisionamento** selecionado como o **fluxo de trabalho do projeto selecionado** e selecione **Avançar**.
1. Selecione **todas as edições da área de trabalho do Windows** em **escolher quais configurações exibir e configurar** e selecione **Avançar**.
1. Selecione **Concluir**.
1. Em seu projeto recém-criado, navegue até **configurações de tempo de execução** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Defina **UseSecurityKeyForSignIn** como **habilitado**.
1. Selecione **exportar** > **pacote de provisionamento**
1. Deixe os padrões na janela de **compilação** em **descrever o pacote de provisionamento** e selecione **Avançar**.
1. Deixe os padrões na janela de **compilação** em **selecionar detalhes de segurança para o pacote de provisionamento** e selecione **Avançar**.
1. Anote ou altere o caminho nas janelas de **Build** em **selecionar onde salvar o pacote de provisionamento** e selecione **Avançar**.
1. Selecione **Compilar** na página **criar o pacote de provisionamento** .
1. Salve os dois arquivos criados (ppkg e Cat) em um local em que você possa aplicá-los aos computadores mais tarde.
1. Siga as orientações no artigo [aplicar um pacote de provisionamento](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)para aplicar o pacote de provisionamento que você criou.

> [!NOTE]
> Os dispositivos que executam o Windows 10 versão 1809 também devem habilitar o modo de computador compartilhado (EnableSharedPCMode). Informações sobre como habilitar esse funtionality podem ser encontradas no artigo [configurar um PC compartilhado ou convidado com o Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Habilitar com Política de Grupo

Para **dispositivos ingressados no Azure ad híbrido** , as organizações podem definir a configuração de política de grupo a seguir para habilitar a entrada de chave de segurança do Fido.

A configuração pode ser encontrada em **configuração do computador** > **Modelos Administrativos** > **sistema** > **logon** > **Ativar entrada de chave de segurança**.

- Definir essa política como **habilitada** permitirá que os usuários entrem com chaves de segurança.
- Definir essa política como **desabilitada** ou **não configurada** interromperá os usuários de entrar com chaves de segurança.

Essa configuração de Política de Grupo requer uma versão atualizada do modelo de Política de Grupo de `credentialprovider.admx`. Esse novo modelo está disponível com a próxima versão do Windows Server e com o Windows 10 20H1. Essa configuração pode ser gerenciada com um dispositivo que executa uma dessas versões mais recentes do Windows ou centralmente seguindo as orientações no tópico de suporte, [como criar e gerenciar o armazenamento central para Política de Grupo modelos administrativos no Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Entrar com a chave de segurança do FIDO2

No exemplo abaixo, um usuário bala Sandhu já provisionou sua chave de segurança do FIDO2 usando as etapas do artigo anterior, [Habilitar entrada de chave de segurança sem senha](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Bala pode escolher o provedor de credenciais de chave de segurança na tela de bloqueio do Windows 10 e inserir a chave de segurança para entrar no Windows.

![Entrada de chave de segurança na tela de bloqueio do Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Gerenciar chave de segurança biométrica, PIN ou redefinição de chaves de segurança

* Windows 10 versão 1903 ou superior
   * Os usuários podem abrir **as configurações do Windows** no dispositivo > **contas** > **chave de segurança**
   * Os usuários podem alterar seu PIN, atualizar a biometria ou redefinir sua chave de segurança

## <a name="troubleshooting-and-feedback"></a>Solução de problemas e comentários

Se você quiser compartilhar comentários ou encontrar problemas ao visualizar esse recurso, compartilhe por meio do aplicativo Hub de comentários do Windows.

1. Inicie o **Hub de comentários** e verifique se você está conectado.
1. Envie comentários sob a seguinte categorização:
   1. Categoria: segurança e privacidade
   1. Subcategoria: FIDO
1. Para capturar logs, use a opção: **recriar meu problema**

## <a name="next-steps"></a>Passos seguintes

[Habilitar o acesso a recursos locais para o Azure AD e dispositivos ingressados no Azure AD híbrido](howto-authentication-passwordless-security-key-on-premises.md)

[Saiba mais sobre o registro de dispositivo](../devices/overview.md)

[Saiba mais sobre a autenticação multifator do Azure](../authentication/howto-mfa-getstarted.md)
