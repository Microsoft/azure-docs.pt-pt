---
title: Criar um servidor do Jenkins no Azure
description: Instale o Jenkins numa máquina virtual do Linux do Azure a partir do modelo de solução do Jenkins e crie uma aplicação Java de exemplo.
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: c9f86ab2536d3c598bb8c7084524395b41f18db0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635463"
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Criar um servidor do Jenkins numa VM do Linux do Azure a partir do portal do Azure

Este guia de introdução mostra como instalar o [Jenkins](https://jenkins.io) numa VM do Ubuntu Linux com as ferramentas e plug-ins configurados para trabalhar com o Azure. Quando tiver terminado, terá um servidor do Jenkins em execução no Azure e a compilar uma aplicação Java de exemplo a partir do [GitHub](https://github.com).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure
* Acesso ao SSH na linha de comandos do seu computador (por exemplo, o Bash da shell ou [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Criar a VM do Jenkins a partir do modelo de solução
Jenkins suporta um modelo em que o Jenkins delegados de servidor funcionam para um ou mais agentes para permitir que uma única instalação do Jenkins para alojar um grande número de projetos ou para fornecer ambientes diferentes, necessários para baseia-se ou os testes. Os passos nesta secção ajudá-lo por meio de instalar e configurar um servidor do Jenkins no Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Ligar-se ao Jenkins

Navegue até à sua máquina virtual (por exemplo, http://jenkins2517454.eastus.cloudapp.azure.com/) no seu browser. A consola do Jenkins não está acessível através de HTTP não seguro, pelo que são fornecidas instruções na página para aceder à consola do Jenkins em segurança a partir do seu computador com um túnel SSH.

![Desbloquear o Jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Configure o túnel com o comando `ssh` na página da linha de comandos, substituindo `username` pelo nome do utilizador administrador da máquina virtual escolhido anteriormente ao configurar a máquina virtual a partir do modelo de solução.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Depois de iniciar o túnel, navegue para http://localhost:8080/ no seu computador local. 

Obtenha a palavra-passe inicial ao executar o seguinte comando na linha de comandos, enquanto está ligado através de SSH à VM do Jenkins.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Desbloqueie o painel do Jenkins pela primeira vez com a palavra-passe inicial.

![Desbloquear o Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Selecione **Instalar plug-ins sugeridos** na próxima página e, em seguida, crie um utilizador administrador do Jenkins utilizado para aceder ao dashboard do Jenkins.

![O Jenkins está pronto!](./media/install-jenkins-solution-template/jenkins-welcome.png)

O servidor do Jenkins já está pronto para compilar o código.

## <a name="create-your-first-job"></a>Criar a sua primeira tarefa

Selecione **Criar novas tarefas** na consola do Jenkins e, em seguida, dê-lhe o nome **mySampleApp**, selecione **Projeto freestyle** e **OK**.

![Criar uma nova tarefa](./media/install-jenkins-solution-template/jenkins-new-job.png) 

Selecione o separador **Gestão da Origem de Código**, ative **Git** e introduza o seguinte URL no campo **URL do Repositório**: `https://github.com/spring-guides/gs-spring-boot.git`

![Definir o repositório de Git](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

Selecione o separador **Compilar**, em seguida selecione **Adicionar passo de compilação**, **Invocar script Gradle**. Selecione **Utilizar Gradle Wrapper**, em seguida introduza `complete` na **localização do Wrapper** e `build` para **Tarefas**.

![Utilize o Gradle wrapper para compilar](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Selecione **Avançado...** e, em seguida, introduza `complete` no campo **Script de Compilação de raiz**. Selecione **Guardar**.

![Configure definições avançadas no passo de compilação do Gradle wrapper](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Compilar o código

Selecione **Compilar Agora** para compilar o código e empacotar a aplicação de exemplo. Quando tiver concluído a compilação, selecione a ligação da **Área de trabalho** para o projeto.

![Navegue para a área de trabalho para obter o ficheiro JAR da compilação](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Navegue para `complete/build/libs` e certifique-se de que `gs-spring-boot-0.1.0.jar` está disponível para verificar se a compilação foi bem-sucedida. O servidor do Jenkins já está pronto para compilar os seus próprios projetos no Azure.

## <a name="next-steps"></a>Próximos Passos

> [!div class="nextstepaction"]
> [Adicionar VMs do Azure como agentes do Jenkins](jenkins-azure-vm-agents.md)
