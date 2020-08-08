---
title: Suporte virtual do Windows Desktop Linux - Azure
description: Uma breve visão geral do suporte do Linux para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bd3cc6c5220e2e84cbbd30b29b8034f53c813f1e
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008700"
---
# <a name="linux-support"></a>Apoio Técnico para Linux

Pode aceder aos recursos de Desktop Virtual do Windows a partir dos seus dispositivos Linux com os seguintes clientes suportados, fornecidos pelos nossos parceiros de clientes finos Linux. Estamos a trabalhar com vários parceiros para permitir clientes de Desktop Virtual do Windows suportados em mais sistemas operativos e dispositivos baseados no Linux. Se você quiser o suporte virtual do Windows desktop numa plataforma Linux que não está listada aqui, por favor, informe-nos na nossa [página UserVoice](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux).

## <a name="connect-with-your-linux-device"></a>Conecte-se com o seu dispositivo Linux

Os seguintes parceiros aprovaram clientes do Windows Virtual Desktop para dispositivos Linux.

|Parceiro|Documentação do parceiro|Apoio a parceiros|
|:------|:--------------------|:--------------|
|![Logotipo IGEL](./media/partners/igel.png)|[Documentação do cliente IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Suporte IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>O que é o Linux SDK?

Os parceiros de clientes finos do Linux podem usar as APIs do Windows Virtual Desktop Linux para recuperar feeds de recursos, ligar-se a sessões de aplicações de ambiente de trabalho ou remotas e utilizar muitas das reorientações que os nossos clientes de primeira parte suportam. O SDK é compatível com a maioria dos sistemas operativos com base no Ubuntu 18.04 ou posterior.

### <a name="feature-support"></a>Suporte de recursos

O SDK suporta múltiplas ligações a sessões de secretária e aplicações remotas. São suportadas as seguintes reorientações:

| Redirecionamento       | Suportado |
| :---------------- | :-------: |
| Teclado          | &#10004;  |
| Rato             | &#10004;  |
| Áudio em          | &#10004;  |
| Áudio para fora         | &#10004;  |
| Prancheta (texto)  | &#10004;  |
| Prancheta (imagem) | &#10004;  |
| Prancheta (arquivo)  | &#10004;  |
| Smartcard         | &#10004;  |
| Unidade/pasta      | &#10004;  |

O SDK também suporta várias configurações de visualização do monitor, desde que os monitores selecionados para a sessão sejam contíguos.

Atualizaremos este documento à medida que adicionamos suporte para novas funcionalidades e redirecionamentos. Se pretender sugerir novas funcionalidades e outras melhorias, visite a nossa [página UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="next-steps"></a>Passos seguintes

Consulte a nossa documentação para os seguintes clientes:

- [Cliente do Ambiente de Trabalho do Windows](connect-windows-7-10.md)
- [Cliente Web](connect-web.md)
- [Cliente Android](connect-android.md)
- [Cliente macOS](connect-macos.md)
- [Cliente iOS](connect-ios.md)
