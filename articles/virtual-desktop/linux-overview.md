---
title: Suporte virtual do Windows Desktop Linux - Azure
description: Uma breve visão geral do suporte do Linux para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3dc9d62141a63574a6796982542cb1491108eedc
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259872"
---
# <a name="linux-support"></a>Apoio Técnico para Linux

Os parceiros podem utilizar o Linux SDK para o Windows Virtual Desktop para construir um cliente virtual do Windows. Também pode usá-lo para ativar o suporte virtual do Windows desktop na sua aplicação ao cliente. Este guia rápido explicará o que é o Linux SDK e como começar a usá-lo.

## <a name="connect-with-your-linux-device"></a>Conecte-se com o seu dispositivo Linux

Os seguintes parceiros aprovaram clientes do Windows Virtual Desktop para dispositivos Linux.

|Parceiro|Documentação do parceiro|Apoio a parceiros|
|:------|:--------------------|:--------------|
|![Logotipo IGEL](./media/partners/igel.png)|[Documentação do cliente IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Suporte IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>O que é o Linux SDK?

Pode utilizar as APIs SDK para recuperar feeds de recursos, ligar-se a sessões de aplicações de ambiente de trabalho ou remotas e utilizar muitas das reorientações que os nossos clientes de primeira parte suportam.

> [!NOTE]
> O SDK está atualmente em desenvolvimento. Atualizaremos este documento com instruções para aceder ao SDK quando estiver geralmente disponível.

### <a name="supported-linux-distributions"></a>Distribuição apoiada do Linux

O SDK é compatível com a maioria dos sistemas operativos com base no Ubuntu 18.04 ou posterior. Se tiver uma distribuição Linux diferente, podemos trabalhar consigo para descobrir como melhor suportar as suas necessidades.

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

## <a name="get-started-with-the-linux-sdk"></a>Começa com o Linux SDK

Antes de poder desenvolver um cliente Linux para o Windows Virtual Desktop, tem de fazer as seguintes coisas:

1. Construa e implemente um ambiente de ambiente de trabalho virtual do Windows para testes ou utilização de produção.
2. Teste os clientes de primeira parte disponíveis para se familiarizar com a experiência do utilizador virtual do Windows Desktop.

## <a name="next-steps"></a>Passos seguintes

Consulte a nossa documentação para os seguintes clientes:

- [Cliente do Ambiente de Trabalho do Windows](connect-windows-7-10.md)
- [Cliente Web](connect-web.md)
- [Cliente Android](connect-android.md)
- [Cliente macOS](connect-macos.md)
- [Cliente iOS](connect-ios.md)
