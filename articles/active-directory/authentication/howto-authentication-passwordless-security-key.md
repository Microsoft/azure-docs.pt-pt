---
title: Entrada de chave de segurança sem palavra-passe - Azure Ative Directory
description: Ativar o sinal de chave de segurança sem palavra-passe para a Azure AD utilizando as chaves de segurança FIDO2
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
ms.openlocfilehash: 71a29a2a7e379e253b52813eb7a76f669abbf668
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653835"
---
# <a name="enable-passwordless-security-key-sign-in"></a>Ativar o sing-in da chave de segurança sem palavras-passe 

Para as empresas que hoje utilizam senhas e têm um ambiente de PC partilhado, as chaves de segurança fornecem uma forma perfeita de os trabalhadores autenticarem sem introduzirem um nome de utilizador ou palavra-passe. As chaves de segurança proporcionam uma produtividade melhorada para os trabalhadores e têm uma maior segurança.

Este documento centra-se em ativar a autenticação sem palavras-passe baseada em chaves de segurança. No final deste artigo, poderá iniciar scontabilidade de aplicações baseadas na Web com a sua conta Azure AD utilizando uma chave de segurança FIDO2.

## <a name="requirements"></a>Requisitos

- [Multi-Factor Authentication do Azure AD](howto-mfa-getstarted.md)
- Ativar o [registo combinado de informações de segurança](concept-registration-mfa-sspr-combined.md)
- Chaves de [segurança FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatíveis
- WebAuthN requer versão 10 do Windows 10 ou superior**

Para utilizar chaves de segurança para iniciar sessão em aplicações e serviços web, tem de ter um browser que suporte o protocolo WebAuthN. Estes incluem Microsoft Edge, Chrome, Firefox e Safari.

## <a name="prepare-devices"></a>Preparar dispositivos

Para o Azure AD aderiu a dispositivos, a melhor experiência está na versão 1903 do Windows 10 ou superior.

Os dispositivos híbridos Azure AD devem executar a versão 10 ou superior do Windows 10.

## <a name="enable-passwordless-authentication-method"></a>Ativar o método de autenticação sem palavras-passe

### <a name="enable-the-combined-registration-experience"></a>Permitir a experiência de registo combinado

As funcionalidades de inscrição para métodos de autenticação sem palavras-passe dependem da função de registo combinado. Siga as etapas do artigo Habilitar o [registo combinado de informações de segurança,](howto-registration-mfa-sspr-combined.md)para permitir o registo combinado.

### <a name="enable-fido2-security-key-method"></a>Ativar o método chave de segurança FIDO2

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para a **Azure Ative Directory**  >    >  **Security Authentication Methods**  >  **Authentication Method Policy**.
1. De acordo com o método **Chave de Segurança FIDO2,** escolha as seguintes opções:
   1. **Ativar** - Sim ou Não
   1. **Alvo** - Todos os utilizadores ou utilizadores selecionados
1. **Guarde** a configuração.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registo e gestão do utilizador das chaves de segurança FIDO2

1. Navegue para [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Inscreva-se se já não.
1. Clique **em Informações de Segurança.**
   1. Se o utilizador já tiver pelo menos um método de autenticação multi-factor Azure AD registado, pode registar imediatamente uma chave de segurança FIDO2.
   1. Se não tiverem registado pelo menos um método de autenticação multi-factor Azure AD, devem adicionar um.
1. Adicione uma chave de segurança FIDO2 clicando **no método Adicionar** e escolhendo a chave de **Segurança**.
1. Escolha **o dispositivo USB** ou o dispositivo **NFC**.
1. Tenha a chave pronta e escolha **a seguir.**
1. Aparecerá uma caixa e pedirá ao utilizador que crie/introduza um PIN para a sua chave de segurança e, em seguida, execute o gesto necessário para a chave, biométrica ou toque.
1. O utilizador será devolvido à experiência de registo combinado e solicitado que forneça um nome significativo para a chave para que o utilizador possa identificar qual se tiver vários. Clique em **Seguinte**.
1. Clique **em Fazer** para concluir o processo.

## <a name="sign-in-with-passwordless-credential"></a>Iniciar sessão com credencial sem palavra-passe

No exemplo abaixo, um utilizador já adpôs a sua chave de segurança FIDO2. O utilizador pode optar por iniciar seduco na web com a sua chave de segurança FIDO2 dentro de um navegador suportado na versão 1903 do Windows 1903 ou superior.

![Sinal de entrada de chave de segurança Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Resolução de problemas e feedback

Se quiser partilhar feedback ou encontrar problemas com esta funcionalidade, partilhe através da aplicação Windows Feedback Hub utilizando os seguintes passos:

1. Lance **o Feedback Hub** e certifique-se de que está assinado.
1. Enviar feedback ao abrigo da seguinte categorização:
   - Categoria: Segurança e Privacidade
   - Subcategoria: FIDO
1. Para capturar registos, utilize a opção para **recriar o meu problema.**

## <a name="known-issues"></a>Problemas conhecidos

### <a name="security-key-provisioning"></a>Provisão de chaves de segurança

O fornecimento e desavising das chaves de segurança não estão disponíveis.

### <a name="upn-changes"></a>Alterações de UPN

Estamos a trabalhar no suporte de uma funcionalidade que permite a mudança da UPN nos dispositivos híbridos Azure AD e Azure AD. Se a UPN de um utilizador mudar, já não é possível modificar as teclas de segurança FIDO2 para ter em conta a alteração. A resolução é para reiniciar o dispositivo e o utilizador tem de se recandistar.

## <a name="next-steps"></a>Passos seguintes

[Chave de segurança FIDO2, o Windows 10, in](howto-authentication-passwordless-security-key-windows.md)

[Permitir a autenticação fidulosa aos recursos no local](howto-authentication-passwordless-security-key-on-premises.md)

[Saiba mais sobre o registo do dispositivo](../devices/overview.md)

[Saiba mais sobre a autenticação multi-factor Azure AD](../authentication/howto-mfa-getstarted.md)
