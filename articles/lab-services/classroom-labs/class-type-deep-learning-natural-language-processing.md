---
title: Criar um laboratório focado na aprendizagem profunda utilizando os Serviços de Laboratório Azure [ Microsoft Docs
description: Aprenda a montar um laboratório para ensinar scripts de concha sintetmente no Linux.
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: f03d1cfeccf03614fe0a5828a05768a5ae3f56e2
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699661"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Criar um laboratório focado na aprendizagem profunda no processamento de linguagem natural usando serviços de laboratório azure
Este artigo mostra-lhe como criar um laboratório focado na aprendizagem profunda no processamento de linguagem natural (NLP) usando os Serviços de Laboratório Azure. O processamento de linguagem natural (NLP) é uma forma de inteligência artificial (IA) que permite computadores com capacidades de tradução, reconhecimento de fala e outras capacidades de compreensão linguística.  

Os alunos que têm uma aula de NLP recebem uma máquina virtual Linux (VM) para aprender a aplicar algoritmos de rede neural para desenvolver modelos de aprendizagem profunda que são usados para analisar a linguagem humana escrita. 

## <a name="lab-configuration"></a>Configuração do laboratório
Para montar este laboratório, precisa de uma assinatura Azure para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Uma vez que tenha uma subscrição Azure, pode criar uma nova conta de laboratório nos Serviços de Laboratório Azure ou usar uma conta de laboratório existente. Consulte o seguinte tutorial para criar uma nova conta de laboratório: [Tutorial para Configurar uma Conta lab.](tutorial-setup-lab-account.md)
 
Depois de criar a conta de laboratório, ative as seguintes definições na conta de laboratório: 

| Definição de conta de laboratório | Instruções |
| ----------- | ------------ |  
| Imagens do mercado | Ative a imagem da Máquina Virtual de Ciência de Dados para linux (Ubuntu) para utilização na sua conta de laboratório.  Consulte o seguinte artigo para obter instruções: [Especifique as imagens](specify-marketplace-images.md)do mercado disponíveis para os criadores de laboratório . | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e aplique as seguintes definições:

| Configurações de laboratório | Valor/instruções | 
| ------------ | ------------------ |
| Tamanho da máquina virtual (VM) | Pequena GPU (Computa). Este tamanho é mais adequado para aplicações intensivas de computação e de rede intensiva, como Inteligência Artificial e Deep Learning. |
| Imagem VM | [Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Esta imagem fornece quadros e ferramentas de aprendizagem profunda para a aprendizagem automática e a ciência dos dados. Para ver a lista completa de ferramentas instaladas nesta imagem, consulte o seguinte artigo: [O que está incluído no DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm) |
| Ativar a ligação remota de ambiente de trabalho | <p>A imagem da Ciência dos Dados já está configurada para usar o X2Go para que professores e alunos possam ligar-se através de um ambiente de trabalho remoto GUI.  O X2Go *não* necessita de ativar a definição de **ligação remota** de ambiente de trabalho ativada.  Esta definição só precisa de ser ativada se optar por utilizar rdp.

>**Importante**: Embora recomendemos a utilização de X2Go com a imagem data Science, se optar por utilizar RDP, terá de se ligar ao VM Linux utilizando o SSH pela primeira vez e instalar os pacotes RDP e GUI.  Em seguida, você/estudantes podem ligar-se ao Linux VM usando RDP mais tarde.  Para mais informações, consulte [Enable graphical remote desktop para VMs Linux](how-to-enable-remote-desktop-linux.md).

A Máquina Virtual de Ciência de Dados para a imagem de Linux fornece os quadros e ferramentas de aprendizagem profunda necessários para este tipo de classe. Como resultado, após a criação da máquina de modelo, não precisa personalizá-la ainda mais. Pode ser publicado para os alunos usarem. Selecione o botão **Publicar** na página do modelo para publicar o modelo no laboratório.  

## <a name="cost"></a>Custo
Se quiser estimar o custo deste laboratório, pode usar o seguinte exemplo: 

Para uma turma de 25 alunos com 20 horas de horário de aulas programada e 10 horas de quota para trabalhos de casa ou tarefas, o preço para o laboratório seria - 25 alunos * (20 + 10) horas * 139 Unidades de Laboratório * 0,01 USD por hora = 1042,5 USD

Mais detalhes sobre os preços, consulte o [Preço dos Serviços do Laboratório Azure.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Conclusão
Este artigo acompanhou-o pelas escadas para criar um laboratório para a aula de processamento de linguagem natural. Você pode usar uma configuração semelhante para outras aulas de aprendizagem profunda.

## <a name="next-steps"></a>Passos seguintes
Os próximos passos são comuns à criação de qualquer laboratório:

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de inscrição por e-mail para estudantes.](how-to-configure-student-usage.md#send-invitations-to-users) 

