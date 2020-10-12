---
title: Criar um laboratório focado em deep learning usando os Serviços Azure Lab Microsoft Docs
description: Aprenda a criar um laboratório focado na aprendizagem profunda no processamento de linguagem natural (NLP) usando os Serviços Azure Lab.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 01199e76b7b9cb1a6360b5aba010d0cfd8936c23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91251461"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Criar um laboratório focado na aprendizagem profunda no processamento de linguagem natural usando os Serviços Azure Lab
Este artigo mostra-lhe como criar um laboratório focado na aprendizagem profunda no processamento de linguagem natural (NLP) usando os Serviços Azure Lab. O processamento de linguagem natural (NLP) é uma forma de inteligência artificial (IA) que permite aos computadores com capacidades de tradução, reconhecimento de voz e outras capacidades de compreensão da linguagem.  

Os alunos que fazem uma aula de NLP obtêm uma máquina virtual Linux (VM) para aprender a aplicar algoritmos de rede neural para desenvolver modelos de aprendizagem profunda que são usados para analisar linguagem humana escrita. 

## <a name="lab-configuration"></a>Configuração de laboratório
Para montar este laboratório, precisa de uma assinatura Azure para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Uma vez que tenha uma subscrição do Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab ou usar uma conta de laboratório existente. Consulte o seguinte tutorial para a criação de uma nova conta de laboratório: [Tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).
 
Depois de criar a conta de laboratório, ative as seguintes definições na conta do laboratório: 

| Definição de conta de laboratório | Instruções |
| ----------- | ------------ |  
| Imagens do mercado | Ative a máquina virtual da Ciência de Dados para a imagem Linux (Ubuntu) para utilização na sua conta de laboratório.  Consulte o seguinte artigo para obter instruções: [Especifique as imagens do mercado disponíveis para os criadores de laboratório](specify-marketplace-images.md). | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e aplique as seguintes definições:

| Configurações de laboratório | Valor/instruções | 
| ------------ | ------------------ |
| Tamanho da máquina virtual (VM) | **Pequena GPU (Computação)**. Este tamanho é mais adequado para aplicações computacionalmente intensivas e intensivas em rede, como Inteligência Artificial e Deep Learning. |
| Imagem VM | [Máquina virtual de ciência de dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Esta imagem fornece quadros de aprendizagem profunda e ferramentas para machine learning e ciência de dados. Para ver a lista completa de ferramentas instaladas nesta imagem, consulte o seguinte artigo: [O que está incluído no DSVM?](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm) |
| Ativar a ligação remota do ambiente de trabalho | <p>A imagem da Data Science já está configurada para usar o X2Go para que professores e alunos possam ligar-se através de um ambiente de trabalho remoto GUI.  O X2Go *não* requer a **definição de ligação ao ambiente de trabalho remoto** ativada.  Esta definição só tem de ser ativada se optar por utilizar o PDR.

>**Importante**: Embora recomendemos a utilização do X2Go com a imagem de Data Science, se optar por utilizar RDP, terá de ligar-se ao LDM Linux utilizando o SSH pela primeira vez e instalar os pacotes RDP e GUI.  Em seguida, você/estudantes podem ligar-se ao Linux VM usando RDP mais tarde.  Para obter mais informações, consulte [Ativar o ambiente de trabalho remoto gráfico para os VMs Linux](how-to-enable-remote-desktop-linux.md).

A Máquina Virtual de Ciência de Dados para a imagem Linux fornece os quadros e ferramentas de aprendizagem profunda necessários para este tipo de aula. Como resultado, após a criação da máquina de modelo, você não precisa personalizá-lo ainda mais. Pode ser publicado para os alunos usarem. Selecione o botão **Publicar** na página do modelo para publicar o modelo para o laboratório.  

## <a name="cost"></a>Cost
Se quiser estimar o custo deste laboratório, pode usar o seguinte exemplo: 

Para uma turma de 25 alunos com 20 horas de horário de aula programado e 10 horas de quota para trabalhos de casa ou tarefas, o preço para o laboratório seria - 25 alunos * (20 + 10) horas * 139 Unidades de Laboratório * 0,01 USD por hora = 1042,5 USD

Mais detalhes sobre os preços, consulte o [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo acompanhou-o através dos degraus para criar um laboratório para a aula de processamento de linguagem natural. Você pode usar uma configuração semelhante para outras aulas de aprendizagem profunda.

## <a name="next-steps"></a>Passos seguintes
Os próximos passos são comuns para a criação de qualquer laboratório:

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de inscrição de e-mail para estudantes.](how-to-configure-student-usage.md#send-invitations-to-users) 

