---
title: 'Copiar e colar de e para uma máquina virtual: Azure Bastion'
description: Neste artigo, aprenda como copiar e colar de e para um VM Azure usando Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 0aaf816cdfe7d42fd345eb4f010cf47b1615f462
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989540"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Copiar e colar a uma máquina virtual: Bastião Azure

Este artigo ajuda-o a copiar e colar texto de e para máquinas virtuais quando utilizar o Azure Bastion. Antes de trabalhar com um VM, certifique-se de ter seguido os passos para [criar um hospedeiro bastião.](bastion-create-host-portal.md) Em seguida, ligue-se ao VM com o que pretende trabalhar utilizando [rdp](bastion-connect-vm-rdp.md) ou [SSH](bastion-connect-vm-ssh.md).

Para os navegadores que suportam o acesso avançado da Clipboard API, pode copiar e colar texto entre o seu dispositivo local e a sessão remota da mesma forma que copia e cola entre aplicações no seu dispositivo local. Para outros navegadores, pode utilizar a paleta de ferramentas de acesso à pasta Bastion.

   ![Permitir a pasta](./media/bastion-vm-manage/allow.png)

Apenas a cópia/pasta de texto é suportada. Para cópia e pasta diretas, o seu navegador pode pedir-lhe acesso à área de sobre-saque quando a sessão do Bastion estiver a ser inicializada. **Deixe** a página web aceder à área de reprodução.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Copiar para uma sessão remota

Depois de ligar à máquina virtual utilizando o [portal Azure, ](https://portal.azure.com)complete os seguintes passos:

1. Copie o texto/conteúdo do dispositivo local para a área de sobre-saque local.
1. Durante a sessão remota, lance a paleta de ferramentas de acesso à pasta Bastion selecionando as duas setas. As setas estão localizadas no centro esquerdo da sessão.

   ![paleta de ferramentas](./media/bastion-vm-manage/left.png)

   ![área de transferência](./media/bastion-vm-manage/clipboard.png)
1. Tipicamente, o texto copiado mostra automaticamente na paleta de pasta de copiar Bastião. Se o seu texto não estiver lá, colhe o texto na área de texto na paleta.
1. Uma vez que o texto esteja na área de texto, pode colá-lo à sessão remota.

   ![pasta](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Cópia de uma sessão remota

Depois de ligar à máquina virtual utilizando o [portal Azure, ](https://portal.azure.com)complete os seguintes passos:

1. Copie o texto/conteúdo da sessão remota para a área de sobre-controlo remoto (utilizando ctrl-C).

   ![paleta de ferramentas](./media/bastion-vm-manage/remote.png)
1. Durante a sessão remota, lance a paleta de ferramentas de acesso à pasta Bastion selecionando as duas setas. As setas estão localizadas no centro esquerdo da sessão.

   ![área de transferência](./media/bastion-vm-manage/clipboard2.png)
1. Tipicamente, o texto copiado mostra automaticamente na paleta de pasta de copiar Bastião. Se o seu texto não estiver lá, colhe o texto na área de texto na paleta.
1. Uma vez que o texto esteja na área de texto, pode colá-lo ao dispositivo local.

   ![pasta](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Passos seguintes

Leia o [Bastião FAQ.](bastion-faq.md)