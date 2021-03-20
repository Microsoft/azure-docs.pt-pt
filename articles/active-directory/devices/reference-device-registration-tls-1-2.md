---
title: Aplicação TLS 1.2 - Serviço de Registo de Diretórios Ativos da Azure
description: Remova o suporte para TLS 1.0 e 1.1 para o Serviço de Registo de Dispositivos AD Azure
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb8c6c64e0a68f5176c4eb0c0177c5220394695
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89268762"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>Impor TLS 1.2 para o Serviço de Registo AZure AD

O Serviço de Registo de Dispositivos Azure Ative (Azure AD) é utilizado para ligar dispositivos à nuvem com a identidade do dispositivo. O Serviço de Registo de Dispositivos Azure AD suporta atualmente a utilização da Segurança da Camada de Transporte (TLS) 1.2 para comunicações com a Azure. Para garantir a segurança e a encriptação de melhor classe, a Microsoft recomenda a desativação dos TLS 1.0 e 1.1. Este documento fornecerá informações sobre como garantir que as máquinas utilizadas para completar o registo e comunicar com o Serviço de Registo de Dispositivos AD Azure utilizam o TLS 1.2.

O protocolo TLS versão 1.2 é um protocolo de criptografia projetado para fornecer comunicações seguras. O protocolo TLS visa principalmente fornecer privacidade e integridade de dados. O TLS passou por muitas iterações com a versão 1.2 a ser definida no [RFC 5246 (ligação externa)](https://tools.ietf.org/html/rfc5246).

A análise atual das ligações mostra pouca utilização TLS 1.1 e 1.0, mas estamos a fornecer esta informação para que possa atualizar quaisquer clientes ou servidores afetados, conforme necessário, antes do suporte para as extremidades TLS 1.1 e 1.0. Se estiver a utilizar qualquer infraestrutura no local para cenários híbridos ou Serviços da Federação de Diretórios Ativos (AD FS), certifique-se de que a infraestrutura pode suportar ligações de entrada e saída que utilizem TLS 1.2.

## <a name="update-windows-servers"></a>Atualizar servidores do Windows

Para servidores Windows que utilizem o Serviço de Registo de Dispositivos AD Azure ou que atuem como proxies, utilize os seguintes passos para garantir que o TLS 1.2 está ativado:

> [!IMPORTANT]
> Depois de ter atualizado o registo, tem de reiniciar o servidor do Windows para que as alterações entrem em vigor.

### <a name="enable-tls-12"></a>Ativar o TLS 1.2

Certifique-se de que as seguintes cordas de registo estão configuradas como mostrado:

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Cliente
  - "DisabledByDefault"=dword:00000000
  - "Ativado"=dword:0000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Servidor
  - "DisabledByDefault"=dword:00000000
  - "Ativado"=dword:0000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\. NETFramework\v4.0.30319
  - "SchUseStrongCrypto"=dword:0000001

## <a name="update-non-windows-proxies"></a>Atualizar proxies não-Windows

Todas as máquinas que atuam como proxies entre os dispositivos e o Serviço de Registo de Dispositivos AD Azure devem assegurar que o TLS 1.2 esteja ativado. Siga as orientações do seu fornecedor para garantir o suporte.

## <a name="update-ad-fs-servers"></a>Atualizar servidores AD FS

Quaisquer servidores AD FS utilizados para comunicar com o Serviço de Registo de Dispositivos AD Azure devem assegurar que o TLS 1.2 esteja ativado. Consulte [a Gestão de Protocolos SSL/TLS e Suítes Cipher para FS AD](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) para obter informações sobre como ativar/verificar esta configuração.

## <a name="client-updates"></a>Atualizações de clientes

Uma vez que todas as combinações de servidores de clientes e servidores de navegador devem utilizar o TLS 1.2 para se conectar com o Serviço de Registo de Dispositivos AD Azure, poderá ter de atualizar estes dispositivos.

Sabe-se que os seguintes clientes não conseguem suportar o TLS 1.2. Atualize os seus clientes para garantir um acesso ininterrupto.

- Versão Android 4.3 e anterior
- Versão 5.0 do Firefox e mais cedo
- Versões 8-10 do Internet Explorer no Windows 7 e anteriores
- Internet Explorer 10 no Windows Phone 8.0
- Versão safari 6.0.4 no OS X 10.8.4 e mais cedo

## <a name="next-steps"></a>Passos seguintes

[Visão geral TLS/SSL (Schannel SSP)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)