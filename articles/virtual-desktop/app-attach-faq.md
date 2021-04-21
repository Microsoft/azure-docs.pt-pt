---
title: Aplicativo Windows Virtual Desktop MSIX anexa FAQ - Azure
description: Perguntas frequentes sobre o anexo de aplicações MSIX para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 6057f4a76f274e34b036ea352a3691b34d24b3a1
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835367"
---
# <a name="msix-app-attach-faq"></a>App MSIX anexa FAQ

Este artigo responde a perguntas frequentes sobre o anexo da app MSIX para o Windows Virtual Desktop.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Qual é a diferença entre o anexo de aplicações MSIX e MSIX?

O MSIX é um formato de embalagem para apps, enquanto o anexo de aplicações MSIX é a funcionalidade que entrega pacotes MSIX à sua implementação.

## <a name="does-msix-app-attach-use-fslogix"></a>A aplicação MSIX anexa a utilização do FSLogix?

O anexo de aplicações MSIX não utiliza FSLogix. No entanto, o anexado de aplicações MSIX e o FSLogix são projetados para trabalhar em conjunto para proporcionar uma experiência de utilizador perfeita.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>Posso utilizar a aplicação MSIX anexada fora do Windows Virtual Desktop?

As APIs que a aplicação MSIX anexa estão disponíveis para o Windows 10 Enterprise. Estes APIs podem ser utilizados fora do Windows Virtual Desktop. No entanto, não existe nenhum plano de gestão para a aplicação MSIX anexada fora do Windows Virtual Desktop.

## <a name="how-do-i-get-an-msix-package"></a>Como recebo um pacote MSIX?

O seu fornecedor de software irá dar-lhe um pacote MSIX. Também pode converter pacotes não MSIX para MSIX. Saiba mais sobre [Como mover os seus instaladores existentes para o MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Quais os sistemas operativos que suportam o anexo da aplicação MSIX?

Windows 10 Enterprise e Windows 10 Enterprise Multi-s, versão 2004 ou posterior.

## <a name="is-msix-app-attach-currently-generally-available"></a>A aplicação MSIX está atualmente disponível em geral?

O anexo de aplicações MSIX faz parte do Windows 10 Enterprise e do Windows 10 Enterprise Multi-s, versão 2004 ou posterior. Ambos os sistemas operativos estão atualmente disponíveis. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Posso utilizar o anexo de aplicações MSIX fora do Windows Virtual Desktop?

As APIs de app MSIX e MSIX fazem parte do Windows 10 Enterprise e do Windows 10 Enterprise Multi-s, versão 2004 e posterior. Atualmente, não fornecemos software de gestão para aplicações MSIX anexadas fora do Windows Virtual Desktop.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Posso executar duas versões da mesma aplicação ao mesmo tempo?

Para que duas versões das mesmas aplicações MSIX sejam executadas simultaneamente, a família pacote MSIX definida no ficheiro appxmanifest.xml deve ser diferente para cada aplicação.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Devo desativar a atualização automática quando utilizar o anexo de aplicações MSIX?

Sim. O anexo de aplicações MSIX não suporta a atualização automática para aplicações MSIX.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Como funcionam as permissões com o anexo de aplicações MSIX?

Todas as máquinas virtuais (VMs) num pool de anfitriões que utiliza o anexo de aplicações MSIX devem ter lido permissões na partilha de ficheiros onde as imagens MSIX estão armazenadas. Se também utilizar ficheiros Azure, terão de ser concedidos tanto permissões de controlo de acesso baseado em funções (RBAC) como de Sistema de Ficheiros de Novas Tecnologias (NTFS).

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Posso utilizar o anexo de aplicativo MSIX para HTTP ou HTTPs?

A utilização da aplicação MSIX anexada sobre HTTP ou HTTPs não é suportada.

## <a name="can-i-restage-the-same-msix-application"></a>Posso reesitar a mesma aplicação MSIX?

Sim. Pode reencerar aplicações que já reencenou, e isso não deve causar erros.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>A aplicação MSIX anexa certificados auto-assinados de suporte?

Sim. É necessário instalar o certificado auto-assinado em todos os VMs do anfitrião da sessão onde o anexo de aplicações MSIX é utilizado para acolher a aplicação auto-assinada.

## <a name="what-applications-can-i-repackage-to-msix"></a>Que aplicações posso reembalar para a MSIX?

Cada aplicação utiliza diferentes características do SISTEMA, linguagens de programação e quadros. Para reembalar a sua aplicação, siga as instruções de [Como mover os seus instaladores existentes para a MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix). Pode encontrar uma lista das coisas de que necessita para reembalar uma aplicação no [Prepare-se para embalar uma aplicação para desktop](/windows/msix/desktop/desktop-to-uwp-prepare). 

Certas aplicações não podem ser em camadas de aplicação, o que significa que não podem ser reembaladas num ficheiro MSIX. Aqui está uma lista das aplicações que não podem ser reembaladas:

- Controladores 
- Active-X ou Silverlight
- Clientes VPN
- Programas antivírus

## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre o anexo da app MSIX, consulte a nossa [visão geral](what-is-app-attach.md) e [glossário.](app-attach-glossary.md) Caso contrário, inicie com [o anexo de aplicações Configurar.](app-attach.md)
