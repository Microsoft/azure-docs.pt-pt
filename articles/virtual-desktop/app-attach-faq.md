---
title: Aplicativo Windows Virtual Desktop MSIX anexa FAQ - Azure
description: Perguntas frequentes sobre o anexo de aplicações MSIX para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88556337"
---
# <a name="msix-app-attach-faq"></a>App MSIX anexa FAQ

Este artigo responde a perguntas frequentes sobre o anexo da app MSIX para o Windows Virtual Desktop.

## <a name="does-msix-app-attach-use-fslogix"></a>A aplicação MSIX anexa a utilização do FSLogix?

O anexo de aplicações MSIX não utiliza FSLogix. No entanto, o app attach e o FSLogix são projetados para trabalhar em conjunto para proporcionar uma experiência de utilizador perfeita.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Posso utilizar o anexo de aplicações MSIX fora do Windows Virtual Desktop?

Sim, o anexo de aplicações MSIX é uma funcionalidade que está incluída no Windows 10 Enterprise e pode ser usada fora do Windows Virtual Desktop. No entanto, não existe nenhum plano de gestão para a aplicação MSIX anexada fora do Windows Virtual Desktop.

## <a name="how-do-i-get-an-msix-package"></a>Como recebo um pacote MSIX?

O seu fornecedor de software irá dar-lhe um pacote MSIX. Também pode converter pacotes não MSIX para MSIX. Saiba mais sobre [Como mover os seus instaladores existentes para o MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Quais os sistemas operativos que suportam o anexo da aplicação MSIX?

Windows 10 Enterprise e Windows 10 Enterprise Multi-s.

## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre o anexo da app MSIX, consulte o nosso [glossário de](app-attach-glossary.md) [visão](what-is-app-attach.md) geral. Caso contrário, inicie com [o anexo de aplicações Configurar.](app-attach.md)