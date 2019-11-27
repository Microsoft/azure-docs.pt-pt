---
title: Sinal de chave de segurança sem senha (visualização)-Azure Active Directory
description: Habilitar a entrada de chave de segurança sem senha no Azure AD usando as chaves de segurança do FIDO2 (versão prévia)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f87f1b2561b65590dfe29d7d2c8d1318e3d35e1
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381844"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Habilitar entrada de chave de segurança sem senha (visualização)

Para empresas que usam senhas hoje e têm um ambiente de PC compartilhado, as chaves de segurança fornecem uma maneira simples para os trabalhadores autenticarem sem inserir um nome de usuário ou senha. As chaves de segurança fornecem produtividade aprimorada para os trabalhadores e têm mais segurança.

Este documento se concentra na habilitação da autenticação com senha baseada em chave de segurança. No final deste artigo, você poderá entrar em aplicativos baseados na Web com sua conta do Azure AD usando uma chave de segurança FIDO2.

|     |
| --- |
| As chaves de segurança do FIDO2 são um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre visualizações, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Requisitos

- [Autenticação multifator do Azure](howto-mfa-getstarted.md)
- [Visualização do registro de informações de segurança combinadas](concept-registration-mfa-sspr-combined.md)
- [Chaves de segurança FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatíveis
- Webauthn requer o Windows 10 versão 1809 ou superior * *

Para usar as chaves de segurança para fazer logon em serviços e aplicativos Web, você deve ter um navegador que ofereça suporte ao protocolo webauthn. Entre eles estão o Microsoft Edge, o Chrome, o Firefox e o Safari.

## <a name="prepare-devices-for-preview"></a>Preparar dispositivos para visualização

Os dispositivos com os quais você executará o piloto devem estar executando o Windows 10 versão 1809 ou superior. A melhor experiência é no Windows 10 versão 1903 ou superior.

## <a name="enable-passwordless-authentication-method"></a>Habilitar método de autenticação com senha

### <a name="enable-the-combined-registration-experience"></a>Habilitar a experiência de registro combinado

Os recursos de registro para métodos de autenticação com senha contam com a visualização de registro combinada. Siga as etapas no artigo [habilitar o registro de informações de segurança combinadas (versão prévia)](howto-registration-mfa-sspr-combined.md)para habilitar a visualização de registro combinado.

### <a name="enable-fido2-security-key-method"></a>Habilitar o método de chave de segurança FIDO2

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory** > **segurança** > **métodos de autenticação** > **diretiva de método de autenticação (versão prévia)** .
1. Na chave de **segurança**do método FIDO2, escolha as seguintes opções:
   1. **Habilitar** -Sim ou não
   1. **Destino** -todos os usuários ou Selecionar usuários
1. **Salve** a configuração.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registro de usuário e gerenciamento de chaves de segurança FIDO2

1. Navegue até [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Entrar se ainda não estiver.
1. Clique em **informações de segurança**.
   1. Se o usuário já tiver pelo menos um método de autenticação multifator do Azure registrado, ele poderá registrar imediatamente uma chave de segurança FIDO2.
   1. Se eles não tiverem pelo menos um método de autenticação multifator do Azure registrado, eles deverão adicionar um.
1. Adicione uma chave de segurança do FIDO2 clicando em **Adicionar método** e escolhendo **chave de segurança**.
1. Escolha **dispositivo USB** ou **dispositivo NFC**.
1. Prepare sua chave e escolha **Avançar**.
1. Uma caixa será exibida e solicitará que o usuário crie/Insira um PIN para sua chave de segurança e, em seguida, execute o gesto necessário para a chave, biométrica ou toque.
1. O usuário será devolvido à experiência de registro combinada e será solicitado a fornecer um nome significativo para a chave para que o usuário possa identificar qual delas tem várias. Clique em **Seguinte**.
1. Clique em **concluído** para concluir o processo.

## <a name="sign-in-with-passwordless-credential"></a>Entrar com credencial sem senha

No exemplo abaixo, um usuário já provisionou sua chave de segurança FIDO2. O usuário pode optar por entrar na Web com sua chave de segurança FIDO2 dentro de um navegador com suporte no Windows 10 versão 1809 ou superior.

![Entrada de chave de segurança Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Solução de problemas e comentários

Se você quiser compartilhar comentários ou encontrar problemas ao visualizar esse recurso, compartilhe por meio do aplicativo Hub de comentários do Windows.

1. Inicie o **Hub de comentários** e verifique se você está conectado.
1. Envie comentários sob a seguinte categorização:
   1. Categoria: segurança e privacidade
   1. Subcategoria: FIDO
1. Para capturar logs, use a opção: **recriar meu problema**

## <a name="known-issues"></a>Problemas conhecidos

### <a name="security-key-provisioning"></a>Provisionamento de chave de segurança

O provisionamento e desprovisionamento de chaves de segurança do administrador não estão disponíveis na visualização pública.

### <a name="upn-changes"></a>Alterações de UPN

Se o UPN de um usuário for alterado, você não poderá mais modificar as chaves de segurança FIDO2 para considerar a alteração. A resolução é redefinir o dispositivo e o usuário precisa registrar novamente suas chaves de segurança do FIDO2.

## <a name="next-steps"></a>Passos Seguintes

[Entrada da chave de segurança do FIDO2 do Windows 10](howto-authentication-passwordless-security-key-windows.md)

[Habilitar a autenticação FIDO2 para recursos locais](howto-authentication-passwordless-security-key-on-premises.md)

[Saiba mais sobre o registro de dispositivo](../devices/overview.md)

[Saiba mais sobre a autenticação multifator do Azure](../authentication/howto-mfa-getstarted.md)
