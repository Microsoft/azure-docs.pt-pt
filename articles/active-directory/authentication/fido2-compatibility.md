---
title: Suporte ao navegador da autenticação sem palavras-passe FIDO2 | Diretório Ativo Azure
description: Os navegadores e as combinações de sistemas operativos suportam a autenticação sem palavras-passe FIDO2 para apps que utilizam o Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f90edd5729ff5229be09bc3798082c33bdeead2
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632106"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Suporte ao navegador da autenticação sem palavras-passe FIDO2

O Azure Ative Directory permite que [as chaves de segurança FIDO2](./concept-authentication-passwordless.md#fido2-security-keys) sejam usadas como um dispositivo sem palavras-passe. A disponibilidade de autenticação fidod2 para contas da Microsoft foi [anunciada em 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Tal como discutido no anúncio, devem ser implementadas determinadas funcionalidades opcionais e extensões à especificação CTAP do FIDO2 para suportar a autenticação segura com as contas microsoft e Azure Ative Directory. O diagrama que se segue mostra quais os navegadores e combinações do sistema operativo que suportam a autenticação sem palavras-passe utilizando chaves de autenticação FIDO2 com diretório Azure Ative.

## <a name="supported-browsers"></a>Browsers suportados

Esta tabela mostra suporte para autenticar o Azure Ative Directory (Azure AD) e as Contas Microsoft (MSA). As contas da Microsoft são criadas pelos consumidores para serviços como Xbox, Skype ou Outlook.com. Os tipos de dispositivos suportados incluem **USB,** comunicação de perto do campo **(NFC)** e baixa energia bluetooth **(BLE).**

| SO | Chrome | Chrome  | Chrome | Microsoft Edge | Microsoft Edge | Microsoft Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | BLE | USB | NFC | BLE | USB | NFC | BLE |
| **Windows**  | ![O Chrome suporta USB no Windows para contas AAD.][y] | ![O Chrome suporta o NFC nas contas AAD do Windows.][y] | ![O Chrome suporta o BLE no Windows para contas AAD.][y] | ![Edge suporta USB no Windows para contas AAD.][y] | ![Edge suporta NFC no Windows para contas AAD.][y] | ![Edge suporta BLE no Windows para contas AAD.][y] | ![O Firefox suporta USB no Windows para contas AAD.][y] | ![O Firefox suporta o NFC no Windows para contas AAD.][y] | ![O Firefox suporta o BLE no Windows para contas AAD.][y] |
| **macOS**  | ![O Chrome suporta USB em contas AAD.][y] | ![O Chrome não suporta NFC em contas aad.][n] | ![O Chrome não suporta BLE no macOS para contas AAD.][n] | ![Edge suporta USB em contas AAD.][y] | ![Edge não suporta NFC em contas aad.][n] | ![Edge não suporta BLE em contas AAD.][n] | ![O Firefox não suporta USB em macOS para contas AAD.][n] | ![O Firefox não suporta o NFC no macOS para contas AAD.][n] | ![O Firefox não suporta ble em contas AAD.][n] |
| **Linux**  | ![O Chrome suporta USB no Linux para contas AAD.][y] | ![O Chrome não suporta NFC em Linux para contas AAD.][n] | ![O Chrome não suporta ble em Linux para contas AAD.][n] | ![Edge não suporta USB em Linux para contas AAD.][n] | ![Edge não suporta NFC em Linux para contas AAD.][n] | ![Edge não suporta BLE em Linux para contas AAD.][n] | ![O Firefox não suporta USB no Linux para contas AAD.][n] | ![O Firefox não suporta o NFC no Linux para contas AAD.][n] | ![O Firefox não suporta ble em Linux para contas AAD.][n] |



## <a name="unsupported-browsers"></a>Navegadores não suportados

As seguintes combinações de sistema operativo e navegador não são suportadas, mas o futuro suporte e testes estão a ser investigados. Se quiser ver outro sistema operativo e suporte ao navegador, por favor deixe feedback usando a ferramenta de feedback do produto na parte inferior da página.

| Sistema operativo | Browser |
| ---- | ---- |
| iOS | Safari |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="minimum-browser-version"></a>Versão mínima do browser

Seguem-se os requisitos mínimos de versão do navegador. 

| Browser | Versão mínima |
| ---- | ---- |
| Chrome | 76 |
| Microsoft Edge | Versão 1903 1 do Windows<sup>10</sup> |
| Firefox | 66 |

<sup>1</sup> Todas as versões do novo Suporte Microsoft Edge baseado em Chromium. O suporte ao legado da Microsoft Edge foi adicionado em 1903.

## <a name="next-steps"></a>Passos seguintes
[Ativar o sing-in da chave de segurança sem palavras-passe (pré-visualização)](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
