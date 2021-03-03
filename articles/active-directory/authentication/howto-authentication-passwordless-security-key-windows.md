---
title: Sinal de chave de segurança sem palavra-passe Windows - Azure Ative Directory
description: Saiba como ativar o acesso à chave de segurança sem palavras-passe no Azure Ative Directory utilizando as teclas de segurança FIDO2
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 190e9c857f1ec9d19eb89493dc4b4a9fb68fac87
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653512"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory"></a>Ativar o acesso à chave de segurança sem palavras-passe para dispositivos Windows 10 com Diretório Ativo Azure 

Este documento centra-se em permitir a autenticação sem palavras-passe baseada em chaves de segurança FIDO2 com dispositivos Windows 10. No final deste artigo, poderás iniciar súb na sua AD Azure e o híbrido Azure AD juntou-se aos dispositivos Do Windows 10 com a sua conta AZure AD utilizando uma chave de segurança FIDO2.

## <a name="requirements"></a>Requisitos

| Tipo de Dispositivo | Azure AD associado | associado ao Azure AD Híbrido |
| --- | --- | --- |
| [Multi-Factor Authentication do Azure AD](howto-mfa-getstarted.md) | X | X |
| [Registo combinado de informações de segurança](concept-registration-mfa-sspr-combined.md) | X | X |
| Chaves de [segurança FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatíveis | X | X |
| WebAuthN requer versão 10 do Windows 1903 ou superior | X | X |
| [Dispositivos aderidos a AD AZure](../devices/concept-azure-ad-join.md) requerem versão 1909 ou superior ao Windows 10 | X |   |
| [Dispositivos híbridos Azure AD aderidos](../devices/concept-azure-ad-join-hybrid.md) requerem versão 10 ou superior do Windows 10 |   | X |
| Controladores de domínio totalmente corrigidos do Windows Server 2016/2019. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) versão 1.4.32.0 ou mais tarde |   | X |
| [Microsoft Intune](/intune/fundamentals/what-is-intune) (Opcional) | X | X |
| Pacote de provisionamento (Opcional) | X | X |
| Política de Grupo (Opcional) |   | X |

### <a name="unsupported-scenarios"></a>Cenários não suportados

Os seguintes cenários não são suportados:

- Serviços de domínio ativos do Windows Server (AD DS) unidos ao domínio (apenas dispositivos no local).
- Cenários RDP, VDI e Citrix usando uma chave de segurança.
- S/MIME utilizando uma chave de segurança.
- "Corra como" usando uma chave de segurança.
- Faça login num servidor usando uma chave de segurança.
- Se não usou a sua chave de segurança para iniciar sôm no seu dispositivo enquanto está online, não pode usá-la para iniciar sôm ou desbloquear offline.
- Iniciar ou desbloquear um dispositivo Windows 10 com uma chave de segurança contendo várias contas AD Azure. Este cenário utiliza a última conta adicionada à chave de segurança. A WebAuthN permite que os utilizadores escolham a conta que pretendem utilizar.
- Desbloqueie um dispositivo que executa a versão 1809 do Windows 10. Para uma melhor experiência, utilize o Windows 10 versão 1903 ou superior.

## <a name="prepare-devices"></a>Preparar dispositivos

Os dispositivos aderidos a AD AD devem executar a versão 1909 ou superior do Windows 10.

Os dispositivos híbridos Azure AD devem executar a versão 2004 ou mais recente do Windows 10.

## <a name="enable-security-keys-for-windows-sign-in"></a>Ativar as chaves de segurança para o s-in do Windows

As organizações podem optar por utilizar um ou mais dos seguintes métodos para permitir a utilização de chaves de segurança para o s-in do Windows com base nos requisitos da sua organização:

- [Ativar com Intune](#enable-with-intune)
- [Implantação direcionada intune](#targeted-intune-deployment)
- [Ativar com um pacote de provisionamento](#enable-with-a-provisioning-package)
- [Ativar com a Política de Grupo (apenas dispositivos aderidos híbridos Azure)](#enable-with-group-policy)

> [!IMPORTANT]
> As organizações com **dispositivos híbridos Azure AD aderidos** **também** devem completar os passos no artigo, [permitir a autenticação FIDO2 para recursos no local](howto-authentication-passwordless-security-key-on-premises.md) antes do Funcionamento da autenticação da chave de segurança FIDO2 do Windows 10.
>
> As organizações com **dispositivos aderidos a Azure AD** devem fazê-lo antes que os seus dispositivos possam autenticar recursos no local com chaves de segurança FIDO2.

### <a name="enable-with-intune"></a>Ativar com Intune

Para ativar a utilização de chaves de segurança utilizando o Intune, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o **Microsoft Intune**  >  **Device matriculando** Windows  >  **Hello** for  >  **Business**  >  **Properties**.
1. Em **Definições**, defina **As teclas de segurança para iniciar sômposições** **ativadas**.

A configuração das chaves de segurança para o sing-in não depende da configuração do Windows Hello for Business.

### <a name="targeted-intune-deployment"></a>Implantação direcionada intune

Para direcionar grupos de dispositivos específicos para permitir o fornecedor de credenciais, utilize as seguintes definições personalizadas via Intune:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para perfis de configuração do dispositivo **Microsoft Intune**  >    >    >  **Crie o perfil**.
1. Configure o novo perfil com as seguintes definições:
   - Nome: Chaves de segurança para Sign-In do Windows
   - Descrição: Permite que as chaves de segurança DO FIDO sejam utilizadas durante o S.A. do Windows
   - Plataforma: Windows 10 e mais tarde
   - Tipo de perfil: Personalizado
   - Definições personalizadas de OMA-URI:
      - Nome: Ligue as chaves de segurança do FIDO para Sign-In do Windows
      - OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Tipo de dados: Inteiro
      - Valor: 1
1. Esta política pode ser atribuída a utilizadores, dispositivos ou grupos específicos. Para obter mais informações, consulte [atribuir perfis de utilizador e dispositivo no Microsoft Intune](/intune/device-profile-assign).

![Criação de política de configuração de dispositivo personalizado intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Ativar com um pacote de provisionamento

Para dispositivos não geridos pelo Intune, pode ser instalado um pacote de provisionamento para permitir a funcionalidade. A aplicação Do Designer de Configuração do Windows pode ser instalada a partir da [Microsoft Store.](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22) Preencha as seguintes etapas para criar um pacote de provisionamento:

1. Lançar o Designer de Configuração do Windows.
1. Selecione o novo projeto **de**  >  **arquivo.**
1. Dê um nome ao seu projeto e tome nota do caminho onde o seu projeto é criado e, em seguida, selecione **Next**.
1. Deixe *o pacote de provisionamento* selecionado como o fluxo de trabalho do projeto **selecionado** e selecione **Seguinte**.
1. Selecione *todas as edições para desktop do Windows* em Escolha quais as **definições para visualizar e configurar,** em seguida, selecione **Next**.
1. Selecione **Concluir**.
1. No seu projeto recém-criado, navegue para **configurações de tempo de execução**  >  **WindowsHelloForBusiness**  >  **SecurityKeys**  >  **UseSecurityKeyForSignIn**.
1. Defina **useSecurityKeyForSignIn** para *ativar*.
1. Selecione   >  **pacote de provisionamento de exportação**
1. Deixe as predefinições na janela **Build** sob **descrever o pacote de provisionamento** e, em seguida, selecione **Seguinte**.
1. Deixe as predefinições na janela **Build** sob **detalhes de segurança Selecione para o pacote de provisionamento** e selecione **Next**.
1. Tome nota ou altere o caminho na janela **Compilar** em **Selecionar onde guardar o pacote de aprovisionamento** e selecione **Seguinte**.
1. Selecione **Construir** na página **de pacote de provisionamento.**
1. Guarde os dois ficheiros criados *(ppkg* e *gato*) para um local onde possa aplicá-los mais tarde às máquinas.
1. Para aplicar o pacote de provisionamento que criou, consulte [Aplicar um pacote de provisionamento](/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> Os dispositivos que executam a versão 1903 do Windows 10 também devem ativar o modo pc partilhado *(EnableSharedPCMode*). Para obter mais informações sobre como ativar esta funcionalidade, consulte [Configurar um PC partilhado ou convidado com o Windows 10.](/windows/configuration/set-up-shared-or-guest-pc)

### <a name="enable-with-group-policy"></a>Ativar com Política de Grupo

Para **dispositivos híbridos Azure AD,** as organizações podem configurar a seguinte definição de Política de Grupo para permitir o sinal de chave de segurança FIDO. A definição pode ser encontrada na **configuração do computador**  >  **Modelos Administrativos**  >  **Modelos Do sistema** De início de  >    >  **sposição Ligue o sinal de chave de segurança**:

- A definição desta política para **Ativado** permite que os utilizadores entrem com as teclas de segurança.
- A definição desta política para **desativar** ou **não configurar** impede que os utilizadores entrem com teclas de segurança.

Esta definição de Política de Grupo requer uma versão atualizada do modelo de Política de `CredentialProviders.admx` Grupo. Este novo modelo está disponível com a próxima versão do Windows Server e com o Windows 10 20H1. Esta definição pode ser gerida com um dispositivo que executa uma destas versões mais recentes do Windows ou centralmente seguindo as orientações no tópico de suporte, [Como criar e gerir a Loja Central para Modelos Administrativos de Política de Grupo no Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Inscreva-se com chave de segurança FIDO2

No exemplo abaixo, um utilizador chamado Bala Sandhu já forrei a sua chave de segurança FIDO2 utilizando os passos do artigo anterior, Ativar o [sinal da chave de segurança sem palavra-passe em](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Para dispositivos híbridos AZure AD, certifique-se de que também ativou o [sinal de segurança sem palavras-passe para os recursos no local](howto-authentication-passwordless-security-key-on-premises.md). A Bala pode escolher o fornecedor de credenciais chave de segurança do ecrã de bloqueio do Windows 10 e inserir a chave de segurança para assinar no Windows.

![Início de sção da chave de segurança no ecrã de bloqueio do Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Gerir a chave de segurança biométrica, PIN ou redefinir a chave de segurança

* Versão 10 do Windows 1903 ou superior
   * Os utilizadores podem abrir **as Definições do Windows** no seu dispositivo > Chave de Segurança de **Contas**  >  
   * Os utilizadores podem alterar o seu PIN, atualizar biometria ou redefinir a sua chave de segurança

## <a name="troubleshooting-and-feedback"></a>Resolução de problemas e feedback

Se quiser partilhar comentários ou encontrar problemas sobre esta funcionalidade, partilhe através da aplicação Windows Feedback Hub utilizando os seguintes passos:

1. Lance **o Feedback Hub** e certifique-se de que está assinado.
1. Enviar feedback ao abrigo da seguinte categorização:
   - Categoria: Segurança e Privacidade
   - Subcategoria: FIDO
1. Para capturar registos, utilize a opção para **recriar o meu problema.**

## <a name="next-steps"></a>Passos seguintes

[Permitir o acesso aos recursos no local para dispositivos ad Azure Azure e híbridos Azure AD](howto-authentication-passwordless-security-key-on-premises.md)

[Saiba mais sobre o registo do dispositivo](../devices/overview.md)

[Saiba mais sobre a autenticação multi-factor Azure AD](../authentication/howto-mfa-getstarted.md)