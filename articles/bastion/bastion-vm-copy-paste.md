---
title: 'Copiar e colar de e para uma máquina virtual: Azure Bastion'
description: Neste artigo, aprenda como copiar e colar de e para um Azure VM usando Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/04/2020
ms.author: charwen
ms.openlocfilehash: ad1476847874f7cea53c6e9c0c3d9f142c949dac
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987716"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Copiar e colar a uma máquina virtual: Azure Bastion

Este artigo ajuda-o a copiar e colar texto de e para máquinas virtuais quando utilizar O Azure Bastion. Antes de trabalhar com um VM, certifique-se de que seguiu os passos para [criar um hospedeiro de bastião.](bastion-create-host-portal.md) Em seguida, ligue-se ao VM com o que pretende trabalhar utilizando o [RDP](bastion-connect-vm-rdp.md) ou [o SSH](bastion-connect-vm-ssh.md).

Para os navegadores que suportam o avançado acesso API daBoard, pode copiar e colar texto entre o seu dispositivo local e a sessão remota da mesma forma que copia e cola entre aplicações no seu dispositivo local. Para outros navegadores, pode utilizar a paleta de ferramentas de acesso à área de transferência de Bastion.

>[!NOTE]
>Apenas a cópia/pasta de texto está atualmente suportada.
>

   ![Permitir a prancheta](./media/bastion-vm-manage/allow.png)

Apenas é suportada a cópia/pasta de texto. Para cópias e pastas diretas, o seu navegador pode solicitar-lhe acesso à área de transferência quando a sessão de Bastion estiver a ser inicializada. **Permitir que** a página web aceda à área de transferência.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Copiar para uma sessão remota

Depois de ligar à máquina virtual utilizando o [portal Azure, ](https://portal.azure.com)complete os seguintes passos:

1. Copie o texto/conteúdo do dispositivo local para a área de transferência local.
1. Durante a sessão remota, lance a paleta de ferramentas de acesso à prancheta Bastion selecionando as duas setas. As setas estão localizadas no centro esquerdo da sessão.

   ![paleta de ferramentas](./media/bastion-vm-manage/left.png)

   ![A screenshot mostra uma prancheta para texto copiado em Bastion.](./media/bastion-vm-manage/clipboard.png)
1. Normalmente, o texto copiado aparece automaticamente na paleta de pasta de cópia Bastion. Se o seu texto não estiver lá, então cole o texto na área de texto na paleta.
1. Uma vez que o texto esteja na área de texto, pode colar-o à sessão remota.

   ![pasta](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Cópia de uma sessão remota

Depois de ligar à máquina virtual utilizando o [portal Azure, ](https://portal.azure.com)complete os seguintes passos:

1. Copie o texto/conteúdo da sessão remota para a área remota (utilizando o Ctrl-C).

   ![paleta de ferramentas](./media/bastion-vm-manage/remote.png)
1. Durante a sessão remota, lance a paleta de ferramentas de acesso à prancheta Bastion selecionando as duas setas. As setas estão localizadas no centro esquerdo da sessão.

   ![área de transferência](./media/bastion-vm-manage/clipboard2.png)
1. Normalmente, o texto copiado aparece automaticamente na paleta de pasta de cópia Bastion. Se o seu texto não estiver lá, então cole o texto na área de texto na paleta.
1. Uma vez que o texto esteja na área de texto, pode colar-o ao dispositivo local.

   ![pasta](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Passos seguintes

Leia as [FAQ do Bastião.](bastion-faq.md)
