---
title: Criar um laboratório para ensinar big data analytics usando o Azure Lab Services Microsoft Docs
description: Aprenda a criar um laboratório para ensinar a análise de big data usando a implementação do Docker da Plataforma de Dados Hortonworks (HDP).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2020
ms.author: nicolela
ms.openlocfilehash: 3035b9debdd46c1a8ba246882312e28d1a2087e4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118435"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Criar um laboratório para análise de big data usando a implementação do Docker da Plataforma de Dados HortonWorks

Este artigo mostra-lhe como criar um laboratório para ensinar uma aula de análise de grandes dados.  Com este tipo de aula, os alunos aprendem a lidar com grandes volumes de dados e aplicam algoritmos de aprendizagem automática e estatística para obter insights de dados.  Um dos principais objetivos para os estudantes é aprender a usar ferramentas de análise de dados, como o [pacote de software de código aberto da Apache Hadoop](https://hadoop.apache.org/) que fornece ferramentas para armazenar, gerir e processar big data.

Neste laboratório, os alunos usarão uma versão comercial popular de Hadoop fornecida pela [Cloudera](https://www.cloudera.com/), chamada Plataforma de [Dados Hortonworks (HDP)](https://www.cloudera.com/products/hdp.html).  Especificamente, os alunos utilizarão [a HDP Sandbox 3.0.1,](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) que é uma versão simplificada e fácil de usar da plataforma que é livre de custos e destina-se à aprendizagem e experimentação.  Embora esta classe possa utilizar máquinas virtuais Windows ou Linux (VM) com a HDP Sandbox implementada, este artigo mostrará como utilizar o Windows.

Outro aspeto interessante deste laboratório é que vamos implantar a Caixa de Areia HDP nos VMs do laboratório usando contentores [do Docker.](https://www.docker.com/)  Cada recipiente Docker fornece o seu próprio ambiente isolado para aplicações de software para executar dentro.  Conceptualmente, os recipientes Docker são como VMs aninhados e podem ser usados para facilmente implementar e executar uma grande variedade de aplicações de software baseadas em imagens de contentores fornecidas no [Docker Hub](https://www.docker.com/products/docker-hub).  O script de implantação da Cloudera para a Sandbox HDP puxa automaticamente a [imagem HDP Sandbox 3.0.1 Docker](https://hub.docker.com/r/hortonworks/sandbox-hdp) do Docker Hub e executa dois contentores Docker:
  - sandbox-hdp
  - sandbox-proxy

## <a name="lab-configuration"></a>Configuração do laboratório

Para montar este laboratório, precisa de uma subscrição azure e uma conta de laboratório para começar. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que conseguir uma subscrição do Azure, pode criar uma nova conta de laboratório nos Serviços de Laboratório Azure. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [tutorial para configurar uma conta de laboratório.](tutorial-setup-lab-account.md)  Também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações da conta de laboratório

Ative as definições descritas na tabela abaixo para a conta de laboratório. Para obter mais informações sobre como ativar imagens de marketplace, consulte [as imagens do Mercado de Especificar disponíveis para criadores de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Definição de conta de laboratório | Instruções |
| ------------------- | ------------ |
|Imagem de mercado| Ative a imagem do Windows 10 Pro para utilização na sua conta de laboratório.|

### <a name="lab-settings"></a>Configurações de laboratório

Utilize as definições na tabela abaixo ao configurar um laboratório de sala de aula.  Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [a criação de um tutorial](tutorial-setup-classroom-lab.md)de laboratório de sala de aula.

| Configurações de laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da máquina virtual| Médio (Virtualização Aninhada). Este tamanho vm é mais adequado para bases de dados relacionais, caches na memória e análise.  Este tamanho também suporta a virtualização aninhada.|  
|Imagem de máquina virtual| Windows 10 Pro|

> [!NOTE] 
> Precisamos de usar o Medium (Nested Virtualization) uma vez que a implementação da caixa de areia HDP utilizando o Docker requer:
>   - Windows Hyper-V com virtualização aninhada
>   - Pelo menos 10 GB de RAM

## <a name="template-machine-configuration"></a>Configuração da máquina de modelo

Para configurar a máquina do modelo, iremos:
- Instalar o Docker
- Implementar caixa de areia HDP
- Utilize o PowerShell e o Windows Task Scheduler para iniciar automaticamente os recipientes Docker

### <a name="install-docker"></a>Instalar o Docker

Os passos nesta secção baseiam-se nas [instruções da Cloudera para a implantação com contentores Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Para utilizar os recipientes Docker, tem primeiro de instalar o Docker Desktop no modelo VM:

1. Siga os passos na [secção Pré-requisitos](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) para instalar [o Docker para windows](https://docs.docker.com/docker-for-windows/install/). 

    > [!IMPORTANT] 
    > Certifique-se de que os recipientes Use Windows em vez da opção de configuração **dos recipientes Linux** não são controlados.

1. Certifique-se de que os **recipientes windows e as funcionalidades Hyper-V estão** ligadas.
   ![Ativar ou desativar funcionalidades do Windows](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Siga os passos na secção [Memória para Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) para configurar a configuração da memória do Docker.

    > [!WARNING]
    > Se verificar inadvertidamente os recipientes Use Windows em vez da opção de **contentores Linux** ao instalar o Docker, não verá as definições de configuração da memória.  Para corrigir isto, pode mudar para utilizar recipientes Linux [clicando no ícone Docker na bandeja do Sistema Windows;](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) quando o menu Docker Desktop abrir, selecione **Switch para recipientes Linux**.
 
### <a name="deploy-hdp-sandbox"></a>Implementar caixa de areia HDP

Nesta secção, irá implementar a Caixa de Areia HDP e, em seguida, também aceder á sandbox HDP usando o navegador.

1. Certifique-se de que instalou [Git Bash](https://gitforwindows.org/) como listado na [secção pré-requisitos](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) do guia, uma vez que este é recomendado para completar os próximos passos.

1. Utilizando o Guia de [Implantação e Instalação da Cloudera para Docker,](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)complete os passos nas seguintes secções:
   
   -    Implementar caixa de areia HDP
   -    Verifique a caixa de areia HDP

    > [!WARNING] 
    > Ao descarregar o mais recente ficheiro .zip para HDP, certifique-se de que *não* guarda o ficheiro .zip num caminho de diretório que inclui o whitespace.

    > [!NOTE] 
    > Se receber uma exceção durante a implementação afirmando que **a Drive não foi partilhada,** precisa de partilhar a sua unidade C com o Docker para que os contentores Linux da HDP possam aceder aos ficheiros windows locais.  Para corrigir isto, [clique no ícone Docker na bandeja do Sistema Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) para abrir o menu do Docker Desktop e selecionar **Definições**.  Quando o diálogo de **Definições do Docker** abrir, selecione **Resources > Partilha de Ficheiros** e verifique a unidade **C.**  Em seguida, pode repetir os passos para implantar a Caixa de Areia HDP.

1. Assim que os recipientes Docker para a Sandbox HDP forem implantados e em funcionamento, pode aceder ao ambiente lançando o seu navegador e seguindo as instruções da Cloudera para abrir a Página de [Boas-Vindas da Sandbox](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) e lançar o Painel de Instrumentos HDP.

    > [!NOTE] 
    > Estas instruções pressupõem que você mapeou primeiro o endereço IP local do ambiente de caixa de areia para o sandbox-hdp.hortonworks.com no ficheiro anfitrião no seu modelo VM.  Se **não** fizer este mapeamento, pode aceder à página de Boas-vindas da Sandbox navegando até `http://localhost:8080` .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Inicie automaticamente os recipientes do Docker quando os alunos iniciarem login

Para proporcionar uma experiência fácil de usar para os alunos, usaremos um script PowerShell que automaticamente:
  - Inicia os recipientes HDP Sandbox Docker quando um aluno começa e se conecta ao seu vm de laboratório.
  - Lança o navegador e navega para a Página de Boas-Vindas da Sandbox.
Também usaremos o Programador de Tarefas do Windows para executar automaticamente este script quando um aluno iniciar sessão no seu VM.
Para configurar isto, siga estes passos: [Scripts Big Data Analytics](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Estimativa de custos

Se quiser estimar o custo deste laboratório, pode usar o seguinte exemplo.

Para uma turma de 25 alunos com 20 horas de horário de aulas programada e 10 horas de quota para trabalhos de casa ou tarefas, o preço para o laboratório seria:
  - 25 alunos * (20 + 10) horas * 55 Unidades de Laboratório * 0,01 USD por hora = 412,50 USD

Mais detalhes sobre os preços, consulte o [Preço dos Serviços do Laboratório Azure.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Conclusão

Este artigo acompanhou-o através dos passos necessários para criar um laboratório para uma classe de análise de big data que usando a Plataforma de Dados Hortonworks implantada com o Docker.  A configuração para este tipo de classe pode ser utilizada para classes de análise de dados semelhantes.  Esta configuração também pode ser aplicável a outros tipos de classes que utilizam o Docker para a implantação.

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns à criação de qualquer laboratório.

- [Criar e gerir um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)
