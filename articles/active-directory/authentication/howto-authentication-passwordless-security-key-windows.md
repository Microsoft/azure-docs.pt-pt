---
title: Sinal de sinal de chave de segurança sem palavras-passe Windows - Diretório Ativo Azure
description: Saiba como ativar o acesso à chave de segurança sem palavras-passe no Azure Ative Directory utilizando teclas de segurança FIDO2 (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd3ebb40ff101f4d2e3fecd05afed834a9ddc688
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505687"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Ative o acesso à chave de segurança sem palavras-passe para dispositivos Windows 10 com Diretório Ativo Azure (pré-visualização)

Este documento centra-se em permitir a autenticação baseada na chave de segurança FIDO2 com dispositivos Windows 10. No final deste artigo, poderá iniciar sessão no Seu Azure AD e o Híbrido Azure AD juntou-se aos dispositivos Windows 10 com a sua conta Azure AD utilizando uma chave de segurança FIDO2.

|     |
| --- |
| As chaves de segurança FIDO2 são uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para mais informações sobre pré-visualizações, consulte [Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) do Microsoft Azure|
|     |

## <a name="requirements"></a>Requisitos

| Device Type | Ingressado no Azure AD | Ingressado no Azure AD híbrido |
| --- | --- | --- |
| [Autenticação azure multi-factor](howto-mfa-getstarted.md) | X | X |
| [Pré-visualização combinada de registo de informações de segurança](concept-registration-mfa-sspr-combined.md) | X | X |
| Chaves de [segurança FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatíveis | X | X |
| WebAuthN requer versão 1809 do Windows 1809 ou superior | X | X |
| [Dispositivos unidos do Azure AD](../devices/concept-azure-ad-join.md) requerem versão 1809 do Windows 1809 ou superior | X |   |
| [Dispositivos aderes híbridos Azure](../devices/concept-azure-ad-join-hybrid.md) requerem Windows 10 Insider Build 18945 ou superior |   | X |
| Controladores de domínio do Windows Server 2016/2019 totalmente remendados. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) versão 1.4.32.0 ou posterior |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (Opcional) | X | X |
| Pacote de provisionamento (Opcional) | X | X |
| Política de Grupo (Opcional) |   | X |

### <a name="unsupported-scenarios"></a>Cenários não apoiados

Os seguintes cenários não são suportados:

- Os Serviços de Domínio ativo do Windows Server (AD DS) uniram a implementação (apenas dispositivos no local).
- Cenários RDP, VDI e Citrix usando uma chave de segurança.
- S/MIME usando uma chave de segurança.
- "Corra como" usando uma chave de segurança.
- Inicie sessão num servidor utilizando uma chave de segurança.
- Se não usou a sua chave de segurança para iniciar sessão no seu dispositivo enquanto estiver online, não pode usá-la para iniciar sessão ou desbloquear offline.
- Inserição ou desbloqueio de um dispositivo Windows 10 com uma chave de segurança contendo várias contas Azure AD. Este cenário utiliza a última conta adicionada à chave de segurança. O WebAuthN permite que os utilizadores escolham a conta que desejam utilizar.

## <a name="prepare-devices-for-preview"></a>Prepare os dispositivos para pré-visualização

O Azure AD juntou-se a dispositivos que está a pilotar durante a pré-visualização da funcionalidade com a versão 1809 do Windows 10 ou superior. A melhor experiência está na versão 1903 do Windows 10 ou superior.

Os dispositivos híbridos Azure AD devem executar o Windows 10 Insider Build 18945 ou mais recentes.

## <a name="enable-security-keys-for-windows-sign-in"></a>Ativar as chaves de segurança para iniciar sessão do Windows

As organizações podem optar por utilizar um ou mais dos seguintes métodos para permitir a utilização de chaves de segurança para o windows de acesso com base nos requisitos da sua organização:

- [Ativar com Intune](#enable-with-intune)
- [Implantação intune direcionada](#targeted-intune-deployment)
- [Ativar com um pacote de provisionamento](#enable-with-a-provisioning-package)
- [Ativar com política de grupo (Hybrid Azure AD apenas dispositivos)](#enable-with-group-policy)

> [!IMPORTANT]
> As organizações com **dispositivos adessimados híbridos Azure AD** **também** devem completar as etapas do artigo, permitir a [autenticação FIDO2 aos recursos no local](howto-authentication-passwordless-security-key-on-premises.md) antes de a autenticação da chave de segurança DOI10 2.
>
> As organizações com **dispositivos adessimados do Azure AD** devem fazê-lo antes que os seus dispositivos possam autenticar recursos no local com chaves de segurança FIDO2.

### <a name="enable-with-intune"></a>Ativar com Intune

Para permitir a utilização de chaves de segurança utilizando o Intune, complete os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Navegue para a **microsoft Intune** > **Device > inscrição** **do Windows** > Windows Hello **for Business** > **Properties**.
1. Em **definições,** coloque **as teclas de segurança para iniciar** **sessão ativada**.

A configuração das chaves de segurança para iniciar sessão não depende da configuração do Windows Hello for Business.

### <a name="targeted-intune-deployment"></a>Implantação intune direcionada

Para direcionar grupos específicos de dispositivos para ativar o prestador de credenciais, utilize as seguintes definições personalizadas via Intune:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Navegue na ** > ** **configuração** do dispositivo > **microsoft** > **Criar perfil**.
1. Configure o novo perfil com as seguintes definições:
   - Nome: Chaves de segurança para Windows Iniciar sessão
   - Descrição: Permite que as chaves de segurança FIDO sejam utilizadas durante o Windows Sign In
   - Plataforma: Windows 10 e mais tarde
   - Tipo de perfil: Personalizado
   - Definições oma-URI personalizadas:
      - Nome: Ligue as chaves de segurança fido para o Windows Iniciar sessão
      - OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Tipo de dados: Inteiro
      - Valor: 1
1. Esta política pode ser atribuída a utilizadores, dispositivos ou grupos específicos. Para mais informações, consulte os perfis de [utilizador e dispositivo no Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Criação de política de configuração de dispositivos personalizados intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Ativar com um pacote de provisionamento

Para dispositivos não geridos pela Intune, pode ser instalado um pacote de fornecimento para ativar a funcionalidade. A aplicação Windows Configuration Designer pode ser instalada a partir da [Microsoft Store](https://www.microsoft.com/en-us/p/windows-configuration-designer/9nblggh4tx22). Complete os seguintes passos para criar um pacote de provisionamento:

1. Lance o Designer de Configuração do Windows.
1. Selecione **File** > **Novo projeto**.
1. Dê um nome ao seu projeto e tome nota do caminho onde o seu projeto é criado e, em seguida, selecione **Next**.
1. Deixe o *pacote de provisionamento* selecionado como o fluxo de trabalho do **projeto Selecionado** e selecione **Next**.
1. Selecione *todas as edições do Windows* em ambientes de trabalho, Escolha quais as **definições a visualizar e configurar**e, em seguida, selecione **Next**.
1. Selecione **Concluir**.
1. No seu projeto recém-criado, navegue nas **definições do Runtime** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Desloque **o UseSecurityKeyForSignIn** para *ativado*.
1. Selecione pacote de **prestação de** > **de exportação**
1. Deixe as predefinições na janela **'Construir'** sob **descrever o pacote**de fornecimento e, em seguida, selecione **Next**.
1. Deixe as predefinições na janela **'Construir'** sob **os detalhes de segurança Do Select para o pacote** de fornecimento e selecione **Next**.
1. Tome nota ou altere o caminho nas janelas **Construir** em Select onde guardar o pacote de **provisionamento** e selecione **Seguinte**.
1. Selecione **Construir** na página de **pacote** seletiva.
1. Guarde os dois ficheiros criados *(ppkg* e *gato*) para um local onde pode aplicá-los mais tarde às máquinas.
1. Para aplicar o pacote de provisionamento que criou, consulte Aplicar um pacote de [provisionamento](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> Os dispositivos que executam o Windows 10 Version 1809 também devem ativar o modo PC partilhado *(EnableSharedPCMode).* Para mais informações sobre a ativação desta funcionalidade, consulte [Configurar um PC partilhado ou convidado com o Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Ativar com a Política de Grupo

Para **dispositivos híbridos Azure AD,** as organizações podem configurar a seguinte definição de Política de Grupo para permitir o início da chave de segurança FIDO. A definição pode ser encontrada em **configuração** de computador > **modelos administrativos** > **Sistema** > **Logon** > **ligar o sinal de chave de segurança:**

- A definição desta política para **ativado** permite que os utilizadores assinem com chaves de segurança.
- A definição desta política para **Desativado** ou **Não Configurado** impede os utilizadores de iniciarem sessão com chaves de segurança.

Esta definição de Política de Grupo requer uma versão atualizada do modelo de política de grupo `credentialprovider.admx`. Este novo modelo está disponível com a próxima versão do Windows Server e com o Windows 10 20H1. Esta definição pode ser gerida com um dispositivo que executa uma destas versões mais recentes do Windows ou centralmente seguindo a orientação no tópico de suporte, [Como criar e gerir a Loja Central para Modelos Administrativos](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)de Política de Grupo no Windows .

## <a name="sign-in-with-fido2-security-key"></a>Inscreva-se na chave de segurança FIDO2

No exemplo abaixo, um utilizador chamado Bala Sandhu já disponibilizou a sua chave de segurança FIDO2 utilizando as etapas do artigo anterior, [Enable passwords security sign in](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Para dispositivos híbridos Azure AD, certifique-se de que também ativou o acesso à chave de [segurança sem palavras-passe para os recursos no local](howto-authentication-passwordless-security-key-on-premises.md). Bala pode escolher o fornecedor de credenciais de segurança do ecrã de bloqueio Windows 10 e inserir a chave de segurança para assinar no Windows.

![Sessão de chave de segurança no ecrã de bloqueio do Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Gerir a chave de segurança biométrica, PIN ou redefinir a chave de segurança

* Windows 10 versão 1903 ou superior
   * Os utilizadores podem abrir **as Definições do Windows** no seu dispositivo > **Contas** > Chave de **Segurança**
   * Os utilizadores podem alterar o seu PIN, atualizar biometria ou redefinir a sua chave de segurança

## <a name="troubleshooting-and-feedback"></a>Resolução de problemas e feedback

Se quiser partilhar problemas de feedback ou de encontro durante a pré-visualização desta funcionalidade, partilhe através da aplicação Windows Feedback Hub utilizando os seguintes passos:

1. Inicie o **Feedback Hub** e certifique-se de que está inscrito.
1. Envie feedback sob a seguinte categorização:
   - Categoria: Segurança e Privacidade
   - Subcategoria: FIDO
1. Para capturar registos, use a opção para **recriar** o meu Problema

## <a name="next-steps"></a>Passos seguintes

[Permitir o acesso aos recursos no local para dispositivos aderes à AD Azure e híbridos Azure](howto-authentication-passwordless-security-key-on-premises.md)

[Saiba mais sobre o registo do dispositivo](../devices/overview.md)

[Saiba mais sobre a autenticação de multifactor azure](../authentication/howto-mfa-getstarted.md)
