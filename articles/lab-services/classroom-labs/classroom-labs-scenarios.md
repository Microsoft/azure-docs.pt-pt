---
title: Utilizar laboratórios de sala de aula para treinamentos - Azure Lab Services | Documentos da Microsoft
description: Saiba como utilizar o Azure DevTest Labs para cenários de treinamento.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 4d2ba11181977f1976b5ae933e8b93a92424fa96
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808643"
---
# <a name="use-classroom-labs-for-trainings"></a>Utilizar laboratórios de sala de aula para treinamentos
Pode configurar um laboratório para treinamentos. Laboratórios de sala de aula do Azure Lab Services permitem que crie um laboratório para fins de formação em que cada trainee utiliza ambientes idênticos e isoladas para treinamento. Pode aplicar políticas para garantir que os ambientes de treinamento estão disponíveis para cada trainee apenas quando precisam e contêm recursos suficientes - como máquinas virtuais - necessários para o treinamento. 

![Laboratório de sala de aula](../media/classroom-labs-scenarios/classroom.png)

Laboratórios de sala de aula cumpre os seguintes requisitos que são necessárias para realizar treinamento em qualquer ambiente virtual: 

- Formandos rapidamente podem aprovisionar ambientes de treinamento
- Todas as máquinas de treinamento devem ser idêntica
- Formandos não é possível ver as VMs criadas por outros formandos
- Controlar o custo, garantindo que os formandos não é possível obter as VMs de mais do que o necessário para o treinamento e também o encerramento VMs quando não estão a utilizá-los
- Partilhe facilmente o laboratório de treinamento com cada trainee
- Reutilizar o laboratório de treinamento e novamente

Neste artigo, ficará a conhecer várias funcionalidades do Azure Lab Services que podem ser utilizadas para satisfazer os requisitos de treinamento descrito anteriormente e passos detalhados que pode seguir para configurar um laboratório para treinamento.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Criar a conta de laboratório como um administrador de conta de laboratório
É a primeira etapa no uso do Azure Lab Services criar uma conta de laboratório no portal do Azure. Depois de um administrador de conta de laboratório cria a conta de laboratório, o administrador adiciona os utilizadores que pretendem criar laboratórios para o **criador do laboratório** função. Os trainers criar laboratórios com máquinas virtuais para estudantes fazer exercícios no curso que estão ensinando. Para obter detalhes, consulte [criar e gerir a conta de laboratório](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Criar e gerir laboratórios de sala de aula
Um treinador, que é um membro da função de criador do laboratório numa conta de laboratório, pode criar um ou mais laboratórios na conta de laboratório. Criar e configurar um modelo de VM com o software necessário para fazer a exercícios no seu curso. Selecione uma imagem pronta a utilizar a partir das imagens disponíveis para a criação de um laboratório de sala de aula e, em seguida, personalizá-lo ao instalar o software necessário para o laboratório. Para obter detalhes, consulte [criar e gerir laboratórios de sala de aula](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Configurar as políticas e definições de utilização
O criador do laboratório pode adicionar ou remover utilizadores de laboratório, obter ligação de registo para enviar aos utilizadores de laboratório, configurados políticas como quotas individuais de configuração por usuário, atualizar o número de VMs disponíveis no laboratório e muito mais. Para obter detalhes, consulte [configurar as políticas e definições de utilização](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Criar e gerir agendamentos
Agendas permitem-lhe configurar um laboratório de sala de aula, de modo a que as VMs no laboratório iniciarem automaticamente e encerre durante um período de tempo especificado. Pode definir um agendamento periódico ou numa agenda periódica. Para obter detalhes, consulte [criar e gerenciar cronogramas para laboratórios de sala de aula](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Configurar e publicar um modelo VM
Um modelo num laboratório é a imagem de máquina virtual de base da qual todas as máquinas virtuais dos utilizadores são criadas. Configure o modelo de VM para que este é configurado com exatamente o que deseja fornecer aos participantes de treinamento. Pode fornecer um nome e descrição do modelo que os utilizadores do laboratório vão ver. Em seguida, publicar o modelo para que as instâncias do modelo VM disponíveis aos seus utilizadores de laboratório. Ao publicar um modelo, o Azure Lab Services cria VMs no laboratório através do modelo. O número de VMs criadas neste processo é igual ao número máximo de utilizadores permitidos no laboratório, o que pode configurar na política de utilização do laboratório. Todas as máquinas virtuais têm a mesma configuração do modelo. Para obter detalhes, consulte [definir a cópia de segurança e publicar as máquinas virtuais do modelo](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Utilizar VMs num laboratório de sala de aula
Um estudante ou participante de treinamento registra para o laboratório e liga-se a VM para fazer a exercícios para o curso. Para obter detalhes, consulte [como aceder a um laboratório de sala de aula](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Passos Seguintes
Comece com a criação de uma conta de laboratório ao seguir as instruções no artigo nos laboratórios de sala de aula: [Tutorial: Configurar uma conta de laboratório com o Azure Lab Services](tutorial-setup-lab-account.md).