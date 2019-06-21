---
title: 'Copie e cole de e para uma máquina virtual: Bastion do Azure | Documentos da Microsoft'
description: Neste artigo, saiba como copiar e colar de e para VM do Azure com Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 9d69d1a9fae258c9546a8c794fc0b0c68b952049
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191814"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion-preview"></a>Copie e cole a uma máquina virtual: Bastion do Azure (pré-visualização)

Este artigo ajuda-o a copiar e colar o texto de e para máquinas virtuais ao utilizar o Azure Bastion. Antes de trabalhar com uma VM, certifique-se de que seguiu os passos para [criar um anfitrião de bastião](bastion-create-host-portal.md). Em seguida, ligar à VM que pretende trabalhar com através de um [RDP](bastion-connect-vm-rdp.md) ou [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

Para navegadores que suportam o acesso de API de área de transferência avançadas, pode copiar e colar o texto entre o seu dispositivo local e na sessão remota da mesma forma, copiar e colar entre aplicações no dispositivo local. Para outros navegadores, pode usar a paleta de ferramenta de acesso de área de transferência de Bastion.

  ![Permitir que a área de transferência](./media/bastion-vm-manage/allow.png)

É suportada apenas, como texto copiar/colar. Para copiar direto e colar, o browser poderá pedir-lhe para acesso de área de transferência quando a sessão de Bastion está sendo inicializada. **Permitir** a página de web para acessar a área de transferência.

## <a name="to"></a>Copiar para uma sessão remota

Depois de ligar para a máquina virtual utilizando o [portal do Azure](https://aka.ms/BastionHost) para a pré-visualização de Bastion:

1. Copie o texto/conteúdo do dispositivo local para local da área de transferência.
1. Durante a sessão remota, inicie a paleta de ferramenta de acesso de área de transferência de Bastion selecionando duas setas. As setas estão localizadas no centro da esquerda da sessão.

    ![paleta de ferramenta](./media/bastion-vm-manage/left.png)

    ![área de transferência](./media/bastion-vm-manage/clipboard.png)

1. Normalmente, o texto copiado automaticamente apresenta a paleta de colagem de cópia de Bastion. Se o seu texto não está lá, em seguida, cole o texto na área de texto na paleta.
1. Assim que o texto estiver na área de texto, pode colá-lo para a sessão remota.

    ![Colar](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Copiar de uma sessão remota

Depois de ligar para a máquina virtual utilizando o [portal do Azure](https://aka.ms/BastionHost) para a pré-visualização de Bastion:

1. Copie o texto/conteúdo da sessão remota na área de transferência remota (utilizando Ctrl-C).

    ![paleta de ferramenta](./media/bastion-vm-manage/remote.png)

1. Durante a sessão remota, inicie a paleta de ferramenta de acesso de área de transferência de Bastion selecionando duas setas. As setas estão localizadas no centro da esquerda da sessão.

    ![área de transferência](./media/bastion-vm-manage/clipboard2.png)

1. Normalmente, o texto copiado automaticamente apresenta a paleta de colagem de cópia de Bastion. Se o seu texto não está lá, em seguida, cole o texto na área de texto na paleta.
1. Assim que o texto estiver na área de texto, pode colar no dispositivo local.

    ![Colar](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Passos Seguintes

Leitura a [Bastion FAQ](bastion-faq.md).