---
title: Criar um laboratório para ensinar ciência de dados com Cadernos Python e Jupyter [ Microsoft Docs
description: Aprenda a criar um laboratório para ensinar ciência de dados usando cadernos Python e Jupyter.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: dfb133f9aa3dd9b76f8b4ea4c6188cfaf9a67b75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444116"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Criar um laboratório para ensinar ciência de dados com cadernos Python e Jupyter

Este artigo descreve como configurar uma máquina de modelos em Serviços de Laboratório com as ferramentas necessárias para ensinar os alunos a usar [os Cadernos Jupyter](http://jupyter-notebook.readthedocs.io).  Jupyter Notebooks é um projeto de código aberto que permite combinar facilmente texto rico e código [fonte](https://www.python.org/) python executável em uma única tela chamada um caderno.  A execução de um caderno resulta num registo linear de inputs e saídas.  Essas saídas podem incluir texto, tabelas de informação, lotes de dispersão, e muito mais.

## <a name="lab-configuration"></a>Configuração do laboratório

Para montar este laboratório, precisa de uma subscrição azure e uma conta de laboratório para começar. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que conseguir uma subscrição do Azure, pode criar uma nova conta de laboratório nos Serviços de Laboratório Azure. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [o tutorial para configurar uma conta](tutorial-setup-lab-account.md)de laboratório.  Também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Definições de conta de laboratório

Ative as definições descritas na tabela abaixo para a conta de laboratório. Para obter mais informações sobre como ativar imagens de marketplace, consulte especificar imagens do [Marketplace disponíveis para criadores de laboratório](specify-marketplace-images.md).

| Definição de conta de laboratório | Instruções |
| ------------------- | ------------ |
| Imagem de mercado | Ative a máquina virtual da ciência dos dados - imagem [do Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) para utilização na sua conta de laboratório. |

>[!TIP]
>Este artigo irá focar-se na configuração de uma máquina de modeloque utiliza o sistema operativo Windows Server.  Também é possível criar uma aula de ciência de dados com cadernos Python e Jupyter usando a Máquina Virtual de [Ciência de Dados para Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) ou Data Science Virtual Machine para imagens [linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) do Azure Marketplace.

### <a name="lab-settings"></a>Configurações de laboratório

Utilize as definições na tabela abaixo ao configurar um laboratório de sala de aula.  Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [a criação de um tutorial](tutorial-setup-classroom-lab.md)de laboratório de sala de aula.

| Configurações de laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da máquina virtual| Pequena GPU (Computa). Este tamanho é mais adequado para aplicações intensivas de computação e de rede intensiva, como Inteligência Artificial e Deep Learning. |
|Imagem de máquina virtual| Máquina Virtual da Ciência dos Dados - Windows 2016|

## <a name="template-machine"></a>Máquina de modelo

A Máquina Virtual da [Ciência dos Dados - Imagem do Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) fornece as estruturas e ferramentas de aprendizagem profunda necessárias para este tipo de classe.  A imagem inclui Cadernos Jupyter e Código de Estúdio Visual.  [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) é uma aplicação web que permite aos cientistas de dados pegar em dados brutos, executar computações, e ver os resultados todos no mesmo ambiente.  Para a nossa máquina de modelos, a aplicação web estará a funcionar localmente.  [Visual Studio Code](https://code.visualstudio.com/) é um IDE que proporciona uma rica experiência interativa ao escrever e testar um caderno.  Para mais informações, consulte [Trabalhar com Cadernos Jupyter no Código do Estúdio Visual](https://code.visualstudio.com/docs/python/jupyter-support).

A tarefa restante para a criação da classe é fornecer cadernos locais.  Para obter instruções sobre como utilizar as amostras de Aprendizagem automática Azure, consulte [como configurar um ambiente com cadernos jupyter](../../machine-learning/how-to-configure-environment.md#jupyter).  Também pode fornecer os seus próprios cadernos na máquina do modelo.  Os cadernos serão copiados para todas as máquinas de estudantes quando o modelo for publicado.

## <a name="cost-estimate"></a>Estimativa de custos

Vamos cobrir uma possível estimativa de custos para esta classe.  Usaremos uma turma de 25 alunos.  Há 20 horas de horário de aulas programado.  Além disso, cada aluno recebe uma quota de 10 horas para trabalhos de casa ou tarefas fora da hora de aula programada.  O tamanho virtual da máquina que escolhemos foi pequena GPU (computação), que é 139 unidades de laboratório.

Aqui está um exemplo de uma possível estimativa de custos para esta classe:

25 \* alunos (20 horas programadas \* + 10 \* horas de quota) 139 unidades de laboratório 0,01 USD por hora = 1042,5 USD

Mais detalhes sobre os preços, consulte o [Preço dos Serviços do Laboratório Azure.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Conclusão

Neste artigo, percorremos os degraus para criar um laboratório para uma aula de Jupyter Notebooks. Você pode usar uma configuração semelhante para outras aulas de machine learning.

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns à criação de qualquer laboratório.

- [Criar e gerir um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)
