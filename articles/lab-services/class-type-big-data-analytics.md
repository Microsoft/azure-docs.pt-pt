---
title: Crie um laboratório para ensinar análise de big data usando os serviços do Azure Lab | Microsoft Docs
description: Aprenda a criar um laboratório para ensinar a análise de big data usando a implementação de Docker da Plataforma de Dados Hortonworks (HDP).
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5eb9cd00350c41645d4427e30a6f25a6c163358c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659901"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Crie um laboratório para análise de big data usando a implementação do Docker da Plataforma de Dados HortonWorks

Este artigo mostra-lhe como criar um laboratório para ensinar uma grande aula de análise de dados.  Com este tipo de aula, os alunos aprendem a lidar com grandes volumes de dados e aplicam algoritmos de aprendizagem automática e estatística para obter insights de dados.  Um objetivo fundamental para os alunos é aprender a usar ferramentas de análise de dados, como [o pacote de software de código aberto da Apache Hadoop](https://hadoop.apache.org/) que fornece ferramentas para armazenar, gerir e processar grandes dados.

Neste laboratório, os estudantes utilizarão uma versão comercial popular de Hadoop fornecida pela [Cloudera,](https://www.cloudera.com/)chamada [Plataforma de Dados Hortonworks (HDP).](https://www.cloudera.com/products/hdp.html)  Especificamente, os alunos utilizarão [a HDP Sandbox 3.0.1,](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) que é uma versão simplificada e fácil de usar da plataforma que é livre de custos e destinada à aprendizagem e experimentação.  Embora esta classe possa utilizar máquinas virtuais Windows ou Linux (VM) com a Sandbox HDP implantada, este artigo mostrará como usar o Windows.

Outro aspeto interessante deste laboratório é que vamos implantar a Caixa de Areia HDP nos VMs do laboratório usando contentores [Docker.](https://www.docker.com/)  Cada recipiente Docker fornece o seu próprio ambiente isolado para aplicações de software para executar dentro.  Conceptualmente, os contentores Docker são como VMs aninhados e podem ser usados para implantar e executar facilmente uma grande variedade de aplicações de software com base em imagens de contentores fornecidas no [Docker Hub](https://www.docker.com/products/docker-hub).  O script de implementação da Cloudera para a HDP Sandbox puxa automaticamente a imagem de [estiva 3.0.1 do Docker](https://hub.docker.com/r/hortonworks/sandbox-hdp) Hub e executa dois contentores Docker:
  - sandbox-hdp
  - sandbox-proxy

## <a name="lab-configuration"></a>Configuração de laboratório

Para montar este laboratório, precisa de uma assinatura Azure e uma conta de laboratório para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que tiver uma assinatura Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [Tutorial para Configurar uma Conta de Laboratório.](tutorial-setup-lab-account.md)  Também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Definições de conta de laboratório

Ativar as definições descritas na tabela abaixo para a conta de laboratório. Para obter mais informações sobre como ativar imagens de marketplace, consulte [as imagens do Mercado Dese especificar disponíveis para os criadores de laboratório.](./specify-marketplace-images.md)

| Definição de conta de laboratório | Instruções |
| ------------------- | ------------ |
|Imagem do Marketplace| Ative a imagem do Windows 10 Pro para utilização na sua conta de laboratório.|

### <a name="lab-settings"></a>Configurações de laboratório

Utilize as definições na tabela abaixo ao configurar um laboratório de sala de aula.  Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [a criação de um tutorial de laboratório em sala de aula.](tutorial-setup-classroom-lab.md)

| Configurações de laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da máquina virtual| Médio (Virtualização Aninhada). Este tamanho VM é mais adequado para bases de dados relacionais, caching na memória e análise.  Este tamanho também suporta a virtualização aninhada.|  
|Imagem de máquina virtual| Windows 10 Pro|

> [!NOTE] 
> Precisamos de usar o Medium (Nested Virtualization) uma vez que a implementação da Caixa de Areia HDP utilizando o Docker requer:
>   - Hiper-V do Windows com virtualização aninhada
>   - Pelo menos 10 GB de RAM

## <a name="template-machine-configuration"></a>Configuração da máquina do modelo

Para configurar a máquina de modelo, iremos:
- Instalar o Docker
- Implementar caixa de areia HDP
- Utilize o Programador de Tarefas PowerShell e Windows para iniciar automaticamente os contentores Docker

### <a name="install-docker"></a>Instalar o Docker

Os passos desta secção baseiam-se nas [instruções da Cloudera para implantar com os contentores docker.](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html) 

Para utilizar os recipientes Docker, tem primeiro de instalar o Docker Desktop no modelo VM:

1. Siga os passos na [secção Pré-Requisitos](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) para instalar o [Docker para o Windows](https://docs.docker.com/docker-for-windows/install/). 

    > [!IMPORTANT] 
    > Certifique-se de que os recipientes Use Windows em vez da opção de configuração **de contentores Linux** não são controlados.

1. Certifique-se de que **os recipientes do Windows e as funcionalidades de Hiper-V** estão ligados.
   ![Ativar ou desativar funcionalidades do Windows](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Siga os passos na secção [Memória do Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) para configurar a configuração de memória do Docker.

    > [!WARNING]
    > Se verificar inadvertidamente os recipientes Use Windows em vez da opção **de recipientes Linux** ao instalar o Docker, não verá as definições de configuração da memória.  Para corrigir isto, pode mudar para a utilização de recipientes Linux [clicando no ícone Docker no tabuleiro do Sistema Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog); quando o menu Docker Desktop abrir, selecione **Switch para os recipientes Linux**.
 
### <a name="deploy-hdp-sandbox"></a>Implementar caixa de areia HDP

Nesta secção, irá implementar a Sandbox HDP e, em seguida, também aceder à Sandbox HDP utilizando o navegador.

1. Certifique-se de que instalou [git bash](https://gitforwindows.org/) conforme listado na [secção Pré-requisitos](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) do guia, uma vez que este é recomendado para completar os próximos passos.

1. Utilizando [o Guia de Implantação e Instalação da Cloudera para Docker,](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)complete os passos nas seguintes secções:
   
   -    Implementar caixa de areia HDP
   -    Verifique a caixa de areia DO HDP

    > [!WARNING] 
    > Quando descarregar o ficheiro de .zip mais recente para o HDP, certifique-se de que *não* guarda o ficheiro .zip num caminho de diretório que inclua o whitespace.

    > [!NOTE] 
    > Se receber uma exceção durante a implementação afirmando que **a Drive não foi partilhada,** tem de partilhar a sua unidade C com o Docker para que os contentores Linux da HDP possam aceder aos ficheiros locais do Windows.  Para corrigir isto, [clique no ícone Docker no tabuleiro do Sistema Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) para abrir o menu Docker Desktop e selecionar **Definições**.  Quando o diálogo **de Definições do Docker** abrir, selecione Recursos > Partilha de **Ficheiros** e verifique a unidade **C.**  Em seguida, pode repetir os passos para implantar a Caixa de Areia HDP.

1. Uma vez implantados e em funcionamento os contentores Docker para a Sandbox HDP, pode aceder ao ambiente lançando o seu navegador e seguindo as instruções da Cloudera para abrir a [Página de Boas-Vindas da Sandbox](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) e lançar o Painel de Instrumentos HDP.

    > [!NOTE] 
    > Estas instruções pressupõem que mapeou pela primeira vez o endereço IP local do ambiente da caixa de areia para o sandbox-hdp.hortonworks.com no ficheiro anfitrião no seu VM modelo.  Se **não** fizer este mapeamento, pode aceder à página De boas-vindas da Sandbox navegando para `http://localhost:8080` .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Inicie automaticamente os recipientes Docker quando os alunos iniciarem o login

Para proporcionar uma experiência fácil de usar para os alunos, usaremos um script PowerShell que automaticamente:
  - Inicia os contentores HDP Sandbox Docker quando um aluno começa e liga-se ao seu VM de laboratório.
  - Lança o navegador e navega para a Página de Boas-Vindas da Sandbox.
Também usaremos o Windows Task Scheduler para executar automaticamente este script quando um aluno entrar no seu VM.
Para configurar isto, siga estes passos: [Scripting Big Data Analytics](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Estimativa de custos

Se quiser estimar o custo deste laboratório, pode usar o seguinte exemplo.

Para uma turma de 25 alunos com 20 horas de horário de aula programado e 10 horas de quota para trabalhos de casa ou tarefas, o preço para o laboratório seria:
  - 25 alunos * (20 + 10) horas * 55 Unidades de Laboratório * 0,01 USD por hora = 412,50 USD

Mais detalhes sobre os preços, consulte o [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão

Este artigo acompanhou-o através dos passos necessários para criar um laboratório para uma grande classe de análise de dados que usa a Plataforma de Dados Hortonworks implementada com o Docker.  A configuração para este tipo de classe pode ser usada para classes de análise de dados semelhantes.  Esta configuração também pode ser aplicável a outros tipos de classes que usam Docker para implantação.

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns para montar qualquer laboratório.

- [Criar e gerir um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)