---
title: Sinal de chave de segurança sem palavras-passe (pré-visualização) - Diretório Ativo Azure
description: Ativar o acesso à chave de segurança sem palavras-passe para o Azure AD utilizando as teclas de segurança FIDO2 (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d5ff722d4a035113af8528ed8adb396b01c81eb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378222"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Ativar o sinal de entrada da chave de segurança sem palavras-passe (pré-visualização)

Para as empresas que hoje utilizam senhas de acesso e têm um ambiente de PC partilhado, as chaves de segurança fornecem uma forma perfeita de os trabalhadores autenticarem sem introduzirem um nome de utilizador ou palavra-passe. As chaves de segurança proporcionam uma maior produtividade para os trabalhadores e têm uma melhor segurança.

Este documento centra-se em permitir a autenticação sem palavras-passe baseada na chave de segurança. No final deste artigo, poderá iniciar sessão em aplicações baseadas na Web com a sua conta Azure AD utilizando uma chave de segurança FIDO2.

|     |
| --- |
| As chaves de segurança FIDO2 são uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para mais informações sobre pré-visualizações, consulte [Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) do Microsoft Azure|
|     |

## <a name="requirements"></a>Requisitos

- [Autenticação azure multi-factor](howto-mfa-getstarted.md)
- [Pré-visualização combinada de registo de informações de segurança](concept-registration-mfa-sspr-combined.md)
- Chaves de [segurança FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatíveis
- WebAuthN requer versão 1809 do Windows 1809 ou superior**

Para utilizar chaves de segurança para iniciar sessão em aplicações e serviços web, deve ter um navegador que suporte o protocolo WebAuthN. Estes incluem Microsoft Edge, Chrome, Firefox e Safari.

## <a name="prepare-devices-for-preview"></a>Prepare os dispositivos para pré-visualização

O Azure AD juntou-se a dispositivos com os seus pilotos, deve executar a versão 1809 do Windows 10 ou superior. A melhor experiência está na versão 1903 do Windows 10 ou superior.

Os dispositivos híbridos Azure AD devem executar o Windows 10 Insider Build 18945 ou mais recentes.

## <a name="enable-passwordless-authentication-method"></a>Ativar o método de autenticação sem palavras-passe

### <a name="enable-the-combined-registration-experience"></a>Ativar a experiência de registo combinado

As funcionalidades de registo dos métodos de autenticação sem palavras-passe baseiam-se na pré-visualização combinada do registo. Siga os passos do artigo Enable registo combinado de informações de [segurança (pré-visualização)](howto-registration-mfa-sspr-combined.md), para permitir a pré-visualização do registo combinado.

### <a name="enable-fido2-security-key-method"></a>Ativar o método da chave de segurança FIDO2

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Consulte o **Diretório Ativo** do Diretório ativo do Azure > **métodos** de **autenticação** >  > a política do método de **autenticação (Pré-visualização)** .
1. No método **FIDO2 Security Key,** escolha as seguintes opções:
   1. **Habilitar** - Sim ou Não
   1. **Alvo** - Todos os utilizadores ou utilizadores Selecionados
1. **Guarde** a configuração.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registo e gestão de chaves de segurança FIDO2

1. Navegue para [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Inscreva-se se ainda não.
1. Clique em **Informações**de Segurança .
   1. Se o utilizador já tiver pelo menos um método de autenticação multi-factor Azure registado, pode registar imediatamente uma chave de segurança FIDO2.
   1. Se não tiverem pelo menos um método de autenticação multi-factor Azure registado, devem adicionar um.
1. Adicione uma tecla de segurança FIDO2 clicando em **adicionar método** e escolhendo a tecla **de segurança**.
1. Escolha **o dispositivo USB** ou o dispositivo **NFC**.
1. Tenha a chave pronta e escolha **A Seguir**.
1. Aparecerá uma caixa e pedirá ao utilizador que crie/introduza um PIN para a sua chave de segurança e, em seguida, execute o gesto necessário para a chave, biométrica ou toque.
1. O utilizador será devolvido à experiência de registo combinado e solicitado a fornecer um nome significativo para a chave para que o utilizador possa identificar qual se tiver múltiplos. Clique em **Seguinte**.
1. Clique em **Feito** para completar o processo.

## <a name="sign-in-with-passwordless-credential"></a>Inscreva-se com credenciais sem palavras-passe

No exemplo abaixo, um utilizador já disponibilizou a sua chave de segurança FIDO2. O utilizador pode optar por iniciar sessão na web com a sua chave de segurança FIDO2 dentro de um navegador suportado na versão 1809 do Windows 10 ou superior.

![Sinal de chave de segurança Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Resolução de problemas e feedback

Se quiser partilhar problemas de feedback ou de encontro durante a pré-visualização desta funcionalidade, partilhe através da aplicação Windows Feedback Hub utilizando os seguintes passos:

1. Inicie o **Feedback Hub** e certifique-se de que está inscrito.
1. Envie feedback sob a seguinte categorização:
   - Categoria: Segurança e Privacidade
   - Subcategoria: FIDO
1. Para capturar registos, use a opção para **recriar** o meu Problema

## <a name="known-issues"></a>Problemas conhecidos

### <a name="security-key-provisioning"></a>Fornecimento de chaves de segurança

O fornecimento e o desprovisionamento de chaves de segurança não estão disponíveis na pré-visualização pública.

### <a name="upn-changes"></a>Alterações upn

Estamos a trabalhar no apoio a uma funcionalidade que permite a mudança da UPN no Azure AD híbrido e a Azure AD juntou-se a dispositivos. Se a UPN de um utilizador mudar, já não pode modificar as chaves de segurança FIDO2 para contabilizar a alteração. A resolução é redefinir o dispositivo e o utilizador tem de voltar a registar-se.

## <a name="next-steps"></a>Passos seguintes

[Chave de segurança FIDO2 Windows 10 iniciar sessão](howto-authentication-passwordless-security-key-windows.md)

[Permitir a autenticação FIDO2 aos recursos no local](howto-authentication-passwordless-security-key-on-premises.md)

[Saiba mais sobre o registo do dispositivo](../devices/overview.md)

[Saiba mais sobre a autenticação de multifactor azure](../authentication/howto-mfa-getstarted.md)
