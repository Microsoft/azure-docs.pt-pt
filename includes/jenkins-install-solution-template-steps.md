---
author: tomarcher
ms.service: jenkins
ms.topic: include
ms.date: 03/03/2020
ms.author: tarcher
ms.openlocfilehash: e9b8ad7a7fcc499f8760b56e6a737be8a6a9e06c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79199590"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.
* Acesso ao SSH na linha de comandos do seu computador (por exemplo, o Bash da shell ou [PuTTY](https://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Criar a VM do Jenkins a partir do modelo de solução
O Jenkins suporta um modelo no qual o servidor do Jenkins delega o trabalho a um ou mais agentes, de modo a permitir que uma instalação do Jenkins individual aloje um grande número de projetos ou proporcione diferentes ambientes necessários para compilações ou testes. Os passos nesta secção orientam-no ao longo da instalação e configuração de um servidor do Jenkins no Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Ligar-se ao Jenkins

Navegue para a sua `http://jenkins2517454.eastus.cloudapp.azure.com/`máquina virtual (por exemplo, no seu navegador web). A consola do Jenkins não está acessível através de HTTP não seguro, pelo que são fornecidas instruções na página para aceder à consola do Jenkins em segurança a partir do seu computador com um túnel SSH.

![Desbloquear o Jenkins](./media/jenkins-install-solution-template-steps/jenkins-ssh-instructions.png)

Configure o túnel com o comando `ssh` na página da linha de comandos, substituindo `username` pelo nome do utilizador administrador da máquina virtual escolhido anteriormente ao configurar a máquina virtual a partir do modelo de solução.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Depois de iniciar o túnel, navegue para `http://localhost:8080/` no computador local. 

Obtenha a palavra-passe inicial ao executar o seguinte comando na linha de comandos, enquanto está ligado através de SSH à VM do Jenkins.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Desbloqueie o painel do Jenkins pela primeira vez com a palavra-passe inicial.

![Desbloquear o Jenkins](./media/jenkins-install-solution-template-steps/jenkins-unlock.png)

Selecione **Instalar plug-ins sugeridos** na próxima página e, em seguida, crie um utilizador administrador do Jenkins utilizado para aceder ao dashboard do Jenkins.

![O Jenkins está pronto!](./media/jenkins-install-solution-template-steps/jenkins-welcome.png)

O servidor do Jenkins já está pronto para compilar o código.

## <a name="create-your-first-job"></a>Criar a sua primeira tarefa

Selecione **Criar novas tarefas** na consola do Jenkins e, em seguida, dê-lhe o nome **mySampleApp**, selecione **Projeto freestyle** e **OK**.

![Criar uma nova tarefa](./media/jenkins-install-solution-template-steps/jenkins-new-job.png) 

Selecione o separador **Gestão da Origem de Código**, ative **Git** e introduza o seguinte URL no campo **URL do Repositório**: `https://github.com/spring-guides/gs-spring-boot.git`

![Definir o repositório de Git](./media/jenkins-install-solution-template-steps/jenkins-job-git-configuration.png) 

Selecione o separador **Compilar**, em seguida selecione **Adicionar passo de compilação**, **Invocar script Gradle**. Selecione **Utilizar Gradle Wrapper**, em seguida introduza `complete` na **localização do Wrapper** e `build` para **Tarefas**.

![Utilize o Gradle wrapper para compilar](./media/jenkins-install-solution-template-steps/jenkins-job-gradle-config.png) 

Selecione **Avançado** e, em seguida, introduza `complete` no campo **Script de Compilação de raiz**. Selecione **Guardar**.

![Configure definições avançadas no passo de compilação do Gradle wrapper](./media/jenkins-install-solution-template-steps/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Compilar o código

Selecione **Compilar Agora** para compilar o código e empacotar a aplicação de exemplo. Quando tiver concluído a compilação, selecione a ligação da **Área de trabalho** para o projeto.

![Navegue para a área de trabalho para obter o ficheiro JAR da compilação](./media/jenkins-install-solution-template-steps/jenkins-access-workspace.png) 

Navegue para `complete/build/libs` e certifique-se de que `gs-spring-boot-0.1.0.jar` está disponível para verificar se a compilação foi bem-sucedida. O servidor do Jenkins já está pronto para compilar os seus próprios projetos no Azure.

## <a name="troubleshooting-the-jenkins-solution-template"></a>Resolver problemas com o modelo de solução do Jenkins

Se se deparar com erros no modelo de solução do Jenkins, comunique os problemas no [repositório Jenkins do GitHub](https://github.com/azure/jenkins/issues).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Adicionar VMs do Azure como agentes do Jenkins](/azure/jenkins/jenkins-azure-vm-agents)