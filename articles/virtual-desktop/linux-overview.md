---
title: Suporte de Linux do Windows Virtual Desktop - Azure
description: Um breve suporte de visão geral do Linux para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422420"
---
# <a name="linux-support"></a>Apoio Técnico para Linux

Os parceiros podem usar o Linux SDK para o Windows Virtual Desktop para construir um cliente autónomo do Windows Virtual Desktop. Também pode utilizá-lo para ativar o suporte ao Windows Virtual Desktop na sua aplicação de cliente. Este guia rápido explicará o que é o Linux SDK e como começar a usá-lo.

## <a name="connect-with-your-linux-device"></a>Conecte-se com o seu dispositivo Linux

Os seguintes parceiros aprovaram os clientes do Windows Virtual Desktop para dispositivos Linux.

|Partner|Documentação do parceiro|Apoio ao parceiro|
|:------|:--------------------|:--------------|
|![Logotipo iGEL](./media/partners/igel.png)|[Documentação do cliente IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Suporte iGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>O que é o Linux SDK?

Pode utilizar as APIs sDK para recuperar feeds de recursos, ligar-se a sessões de desktop ou aplicações remotas e utilizar muitas das redirecionações que os nossos clientes de primeira parte suportam.

> [!NOTE]
> O SDK está atualmente em desenvolvimento. Atualizaremos este documento com instruções para aceder ao SDK quando estiver geralmente disponível.

### <a name="supported-linux-distributions"></a>Distribuição de Linux suportada

O SDK é compatível com a maioria dos sistemas operativos com base em Ubuntu 18.04 ou posterior. Se tiver uma distribuição linux diferente, podemos trabalhar consigo para descobrir como melhor suportar as suas necessidades.

### <a name="feature-support"></a>Suporte de recurso

O SDK suporta múltiplas ligações a sessões de desktop e aplicações remotas. São apoiadas as seguintes redirecionamentos:

| Redirecionamento       | Suportado |
| :---------------- | :-------: |
| Teclado          | &#10004;  |
| Rato             | &#10004;  |
| Áudio em          | &#10004;  |
| Áudio para fora         | &#10004;  |
| Prancheta (texto)  | &#10004;  |
| Clipboard (imagem) | &#10004;  |
| Clipboard (ficheiro)  | &#10004;  |
| Smartcard         | &#10004;  |
| Unidade/pasta      | &#10004;  |

O SDK também suporta várias configurações de ecrã de monitor, desde que os monitores selecionados para a sua sessão sejam contíguos.

Atualizaremos este documento à medida que adicionamos suporte para novas funcionalidades e redirecionamentos. Se pretender sugerir novas funcionalidades e outras melhorias, visite a nossa [página UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Começar com o Linux SDK

Antes de poder desenvolver um cliente Linux para o Windows Virtual Desktop, tem de fazer as seguintes coisas:

1. Construir e implementar um ambiente de ambiente de trabalho virtual do Windows para testar ou utilizar a produção.
2. Teste os clientes disponíveis de primeira parte para se familiarizar com a experiência do utilizador do Windows Virtual Desktop.

## <a name="next-steps"></a>Passos seguintes

Confira a nossa documentação para os seguintes clientes:

- [Cliente do Windows Desktop](connect-windows-7-and-10.md)
- [Cliente web](connect-web.md)
- [Cliente Android](connect-android.md)
- [cliente macOS](connect-macos.md)
- [cliente iOS](connect-ios.md)
