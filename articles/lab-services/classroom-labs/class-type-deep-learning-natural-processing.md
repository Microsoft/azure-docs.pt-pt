---
title: Configurar um laboratório voltado para o aprendizado profundo usando o Azure Lab Services | Microsoft Docs
description: Saiba como configurar um laboratório para ensinar o script de Shell no Linux.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 19191e84e9af140f78e529398471f0e950b36c8c
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694690"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Configure um laboratório voltado para o aprendizado profundo no processamento de linguagem natural usando Azure Lab Services
Este artigo mostra como configurar um laboratório voltado para o aprendizado profundo no NLP (processamento de idioma natural) usando Azure Lab Services. O NLP (processamento de idioma natural) é uma forma de ia (inteligência artificial) que permite aos computadores com tradução, reconhecimento de fala e outros recursos de compreensão da linguagem.  

Os alunos que tomam uma classe NLP obtêm uma VM (máquina virtual) do Linux para saber como aplicar algoritmos de rede neural para desenvolver modelos de aprendizado profundo que são usados para analisar a linguagem humana programada. 

## <a name="lab-configuration"></a>Configuração do laboratório
Para configurar este laboratório, você precisa de uma assinatura do Azure para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de ter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services ou usar uma conta de laboratório existente. Consulte o tutorial a seguir para criar uma nova conta de laboratório: [Tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).
 
Depois de criar a conta de laboratório, habilite as seguintes configurações na conta do laboratório: 

| Configuração de conta do laboratório | Instruções |
| ----------- | ------------ |  
| Imagens do Marketplace | Habilite a imagem do Máquina Virtual de Ciência de Dados para Linux (Ubuntu) para uso em sua conta de laboratório.  Veja os seguintes artigos para obter instruções: [Especifique as imagens do Marketplace disponíveis para os criadores de laboratório](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators). | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e aplicar as seguintes configurações:

| Configurações do laboratório | Valor/instruções | 
| ------------ | ------------------ |
| Tamanho da VM (máquina virtual) | GPU pequena (computação). Esse tamanho é mais adequado para aplicativos com uso intensivo de computação e rede, como inteligência artificial e aprendizado profundo. |
| Imagem da VM | [Máquina virtual de ciência de dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Esta imagem fornece estruturas e ferramentas de aprendizado profundo para aprendizado de máquina e ciência de dados. Para exibir a lista completa de ferramentas instaladas nesta imagem, consulte o seguinte artigo: [O que está incluído no DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Habilitar conexão de área de trabalho remota | Desabilitar. <p>Habilitar essa configuração permitirá que professores e alunos se conectem às suas máquinas virtuais (VM) usando o Área de Trabalho Remota (RDP).</p><p>**Importante**: O RDP já está instalado e configurado no Máquina Virtual de Ciência de Dados para a imagem do Linux. Como resultado, os professores/alunos podem se conectar a VMs via RDP sem nenhuma etapa adicional. Além disso, se você precisar se conectar à área de trabalho gráfica, essa imagem já terá o [X2Go Server](https://wiki.x2go.org/doku.php/doc:newtox2go) instalado na máquina virtual. Os alunos devem instalar o X2Go Client em seus computadores locais e devem usar o cliente para se conectar. Para obter mais informações, consulte os seguintes guias: <ul><li>[Como acessar o Máquina Virtual de Ciência de Dados para Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Conectar-se à VM de modelo para instalar pacotes RDP e GUI](how-to-enable-remote-desktop-linux.md#teachers-connecting-to-the-template-vm-using-rdp)</li></ul></p>   |

O Máquina Virtual de Ciência de Dados para imagem do Linux fornece as estruturas e ferramentas de aprendizado profundo necessárias para esse tipo de classe. Como resultado, após a criação da máquina de modelo, você não precisa personalizá-la ainda mais. Ele pode ser publicado para que os alunos usem. Selecione o botão **publicar** na página modelo para publicar o modelo no laboratório.  

## <a name="cost"></a>Custo
Se você quiser estimar o custo deste laboratório, poderá usar o exemplo a seguir: 

Para uma classe de 25 alunos com 20 horas de tempo de classe agendada e 10 horas de cota para a casa ou as atribuições, o preço do laboratório seria-25 alunos * (20 + 10) horas * 139 unidades de laboratório * 0, 1 USD por hora = 1042,5 USD

Mais detalhes sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo descreveu as etapas para criar um laboratório para a classe de processamento de idioma natural. Você pode usar uma configuração semelhante para outras classes de aprendizado profundo.

## <a name="next-steps"></a>Passos seguintes
As próximas etapas são comuns à configuração de qualquer laboratório:

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](tutorial-setup-classroom-lab.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de registro de email para alunos](tutorial-setup-classroom-lab.md#send-an-email-with-the-registration-link). 

