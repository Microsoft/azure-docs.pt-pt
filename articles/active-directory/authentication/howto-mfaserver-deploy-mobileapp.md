---
title: Serviço Web web da aplicação móvel Azure MFA Server - Diretório Ativo Azure
description: Configure o servidor MFA para enviar notificações de push para os utilizadores com a Aplicação Microsoft Authenticator.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f62aacf8264eb583c523b6ee785f1908f0241644
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848158"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Ativar a autenticação de aplicação móvel com o Servidor Multi-Factor Authentication do Azure

A aplicação Microsoft Authenticator oferece uma opção de verificação fora de banda adicional. Em vez de fazer uma chamada telefónica automática ou enviar um SMS ao utilizador durante o início de sessão, o Multi-Factor Authentication do Azure envia uma notificação push para a aplicação Microsoft Authenticator no smartphone ou tablet do utilizador. O utilizador toca simplesmente em **Verificar** (ou introduz um PIN e toca em "Autenticar") na aplicação para concluir o início de sessão.

É preferível utilizar uma aplicação móvel para a verificação de dois passos quando a receção de telefone é pouco fiável. Se utilizar a aplicação como um gerador de token OATH, não é precisa qualquer ligação de rede ou à Internet.

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores dos seus utilizadores devem utilizar a autenticação multi-factor Azure baseada na nuvem. Os clientes existentes que ativaram o MFA Server antes do dia 1 de julho poderão descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

> [!IMPORTANT]
> Se tiver instalado o Servidor Multi-Factor Authentication do Azure v8.x ou superior, a maior parte dos passos abaixo não são precisos. A autenticação da aplicação móvel pode ser configurada seguindo os passos em [Configurar a aplicação móvel](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Requisitos

Para utilizar a aplicação do Microsoft Authenticator, tem de executar o Servidor Multi-Factor Authentication do Azure v8.x ou superior

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar as definições da aplicação móvel no Servidor Multi-Factor Authentication do Azure

1. Na consola do Servidor Multi-Factor Authentication, clique no ícone do Portal de Utilizador. Se os utilizadores tiverem permissão para controlar os respetivos métodos de autenticação, selecione **Aplicação Móvel** no separador Definições, em **Permitir que os utilizadores selecionem o método**. Sem esta funcionalidade ativada, os utilizadores finais têm de contactar o Suporte Técnico para concluir a ativação da Aplicação Móvel.
2. Selecione a caixa **Permitir que os utilizadores ativem a aplicação móvel**.
3. Selecione a caixa **Permitir a inscrição de utilizadores**.
4. Clique no ícone da **Aplicação Móvel.**
5. Preencha o campo **Nome da conta** com o nome da empresa ou organização a apresentar na aplicação móvel desta conta.
   ![Definições da Aplicação Móvel de configuração do Servidor MFA](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Passos seguintes

- [Cenários avançados com autenticação multi-factor Azure e VPNs de terceiros.](howto-mfaserver-nps-vpn.md)
