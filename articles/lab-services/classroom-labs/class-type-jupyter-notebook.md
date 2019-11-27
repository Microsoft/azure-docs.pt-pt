---
title: Configurar um laboratório para ensinar ciência de dados com notebooks Python e Jupyter | Microsoft Docs
description: Saiba como configurar um laboratório para ensinar ciência de dados usando notebooks Python e Jupyter.
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
ms.openlocfilehash: b69abf098ba7646ebc98d126c7c0d949205d6275
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383982"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Configurar um laboratório para ensinar ciência de dados com notebooks Python e Jupyter

Este artigo descreve como configurar um computador de modelo no Lab Services com as ferramentas necessárias para ensinar aos alunos como usar [notebooks Jupyter](http://jupyter-notebook.readthedocs.io).  O Jupyter notebooks é um projeto de código-fonte aberto que permite combinar facilmente o código-fonte de Rich Text [e executável em](https://www.python.org/) uma única tela chamada notebook.  A execução de um bloco de anotações resulta em um registro linear de entradas e saídas.  Essas saídas podem incluir texto, tabelas de informações, gráficos de dispersão e muito mais.

## <a name="lab-configuration"></a>Configuração do laboratório

Para configurar este laboratório, você precisa de uma assinatura do Azure e uma conta de laboratório para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você poderá criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre como criar uma nova conta de laboratório, consulte [tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).  Você também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações de conta do laboratório

Habilite as configurações descritas na tabela a seguir para a conta do laboratório. Para obter mais informações sobre como habilitar imagens do Marketplace, consulte [especificar imagens do Marketplace disponíveis para criadores de laboratório](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators).

| Configuração de conta do laboratório | Instruções |
| ------------------- | ------------ |
| Imagem do Marketplace | Habilite a imagem [máquina virtual de ciência de dados-Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) para uso em sua conta de laboratório. |

>[!TIP]
>Este artigo se concentrará na configuração de um computador de modelo que usa o sistema operacional Windows Server.  Também é possível configurar uma classe de ciência de dados com notebooks Python e Jupyter usando as imagens [máquina virtual de ciência de dados para Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) ou [máquina virtual de ciência de dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) do Azure Marketplace.

### <a name="lab-settings"></a>Configurações do laboratório

Use as configurações na tabela abaixo ao configurar um laboratório de sala de aula.  Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [configurar um tutorial de laboratório de sala de aula](tutorial-setup-classroom-lab.md).

| Configurações do laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da máquina virtual| GPU pequena (computação). Esse tamanho é mais adequado para aplicativos com uso intensivo de computação e rede, como inteligência artificial e aprendizado profundo. |
|Imagem da máquina virtual| SQL Server 2019 Standard no Windows Server 2019|

## <a name="template-machine"></a>Computador de modelo

A imagem [máquina virtual de ciência de dados-Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) fornece as estruturas e ferramentas de aprendizado profundo necessárias para esse tipo de classe.  A imagem inclui Jupyter notebooks e Visual Studio Code.  O [Jupyter notebooks](http://jupyter-notebook.readthedocs.io) é um aplicativo Web que permite que os cientistas de dados tomem dados brutos, executem cálculos e vejam todos os resultados no mesmo ambiente.  Para o nosso computador de modelo, o aplicativo Web será executado localmente.  [Visual Studio Code](https://code.visualstudio.com/) é um IDE que fornece uma experiência interativa sofisticada ao escrever e testar um notebook.  Para obter mais informações, consulte [trabalhando com notebooks Jupyter no Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

A tarefa restante para configurar a classe é fornecer blocos de anotações locais.  Para obter instruções sobre como usar os exemplos de Azure Machine Learning, consulte [como configurar um ambiente com notebooks Jupyter](../../machine-learning/service/how-to-configure-environment.md#jupyter).  Você também pode fornecer seus próprios blocos de anotações no computador do modelo.  Os blocos de anotações serão copiados para todos os computadores dos alunos quando o modelo for publicado.

## <a name="cost-estimate"></a>Estimativa dos custos

Vamos abordar uma possível estimativa de custo para essa classe.  Usaremos uma classe de 25 alunos.  Há 20 horas de tempo de classe agendada.  Além disso, cada aluno recebe uma cota de 10 horas de casa ou atribuições fora do tempo de classe agendado.  O tamanho da máquina virtual que escolhemos era uma GPU pequena (computação), que é 139 unidades de laboratório.

Aqui está um exemplo de uma possível estimativa de custo para esta classe:

25 alunos \* (20 horas agendadas + 10 horas de cota) \* 139 unidades de laboratório \* 0, 1 USD por hora = 1042,5 USD

Mais detalhes sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão

Neste artigo, percorremos as etapas para criar um laboratório para uma classe Jupyter notebooks. Você pode usar uma configuração semelhante para outras classes de aprendizado de máquina.

## <a name="next-steps"></a>Passos Seguintes

As próximas etapas são comuns à configuração de qualquer laboratório.

- [Criar e gerenciar um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users)
