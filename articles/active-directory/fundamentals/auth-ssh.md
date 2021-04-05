---
title: Autenticação SSH com Diretório Ativo Azure
description: Orientação arquitetónica para a consecução da integração do SSH com o Azure Ative Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b77ab0832fa19149c270d6ba5a6641069548cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172725"
---
# <a name="ssh"></a>SSH  

Secure Shell (SSH) é um protocolo de rede que fornece encriptação para operar serviços de rede de forma segura sobre uma rede não segura. O SSH também fornece um sinal de linha de comando, executa comandos remotos e transfere ficheiros de forma segura. É comumente usado em sistemas baseados na UNIX, como o Linux®. O SSH substitui o protocolo Telnet, que não fornece encriptação numa rede não garantida. 

O Azure Ative Directory (Azure AD) fornece uma extensão de Máquina Virtual (VM) para sistemas baseados em Linux® em funcionamento no Azure. 

## <a name="use-when"></a>Utilizar quando 

* Trabalhando com VMs baseados em Linux® que requerem sinal remoto em

* Executar comandos remotos em sistemas baseados ® Linux

* Transferir ficheiros de segurança numa rede não segura

![diagrama de AD AZure com protocolo SSH](./media/authentication-patterns/ssh-auth.png)

SSH com Azure AD

## <a name="components-of-system"></a>Componentes do sistema 

* **Utilizador**: Inicia o cliente SSH a estabelecer uma ligação com os VMs ® Linux e fornece credenciais para a autenticação.

* **Web browser**: O componente com o que o utilizador interage. Comunica com o Fornecedor de Identidade (Azure AD) para autenticar e autorizar o utilizador de forma segura.

* **Cliente SSH**: Conduz o processo de configuração da ligação.

* **Azure AD**: Autentica a identidade do utilizador utilizando o fluxo do dispositivo e emite um sinal simbólico para os VMs Linux.

* **Linux VM**: Aceita token e proporciona uma ligação bem sucedida.

## <a name="implement-ssh-with-azure-ad"></a>Implementar SSH com Azure AD 

* [Iniciar sessão num VM ® Linux com credenciais de Diretório Ativo Azure - Azure Virtual Machines ](../../virtual-machines/linux/login-using-aad.md) 

* [Fluxo de código de dispositivo OAuth 2.0 - plataforma de identidade da Microsoft ](../develop/v2-oauth2-device-code.md)

* [Integre com o Azure Ative Directory (akamai.com)](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

