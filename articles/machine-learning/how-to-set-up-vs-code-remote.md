---
title: 'Depurativo: VS Código & instâncias de computação ML (pré-visualização)'
titleSuffix: Azure Machine Learning
description: Confiem o código VS Remote para depurar interativamente o seu código com a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jmartens
author: j-martens
ms.date: 08/06/2020
ms.openlocfilehash: 37d0ec0295d76f740b2e8bf70ae72f0c95e68d14
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904484"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote-preview"></a>Depurar interativamente numa Azure Machine Learning Compute Instance com VS Code Remote (pré-visualização)

Neste artigo, você vai aprender a configurar a extensão visual Studio Code Remote em uma Azure Machine Learning Compute Instance para que possa **depurar interativamente o seu código** a partir do Código VS.

* [A Azure Machine Learning Compute Instance](concept-compute-instance.md) é uma estação de trabalho totalmente gerida baseada na nuvem para cientistas de dados e fornece capacidades de gestão e prontidão empresarial para administradores de TI. 

* [Código de estúdio visual remoto](https://code.visualstudio.com/docs/remote/remote-overview) O desenvolvimento permite-lhe utilizar um contentor, uma máquina remota ou o Subsistema Windows para Linux (WSL) como um ambiente de desenvolvimento completo. 

## <a name="prerequisite"></a>Pré-requisito  

* Instância computacional ativada pelo SSH. Para obter mais informações, [consulte o guia Criar uma instância computacional.](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#create)
* Nas plataformas do Windows, tem de [instalar um cliente SSH compatível com OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) se ainda não estiver presente. 

> [!Note]
> PuTTY não é suportado no Windows, uma vez que o comando ssh deve estar no caminho. 

## <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>Obtenha a porta IP e SSH para a sua instância computacional

1. Vá ao estúdio Azure Machine Learning em https://ml.azure.com/ .

2. Selecione o seu [espaço de trabalho](concept-workspace.md).
1. Clique no **separador Instâncias computacional.**
1. Na coluna URI de **aplicação,** clique na ligação **SSH** da instância de computação que pretende utilizar como um cálculo remoto. 
1. No diálogo, tome nota do endereço IP e da porta SSH. 
1. Guarde a sua chave privada para o diretório ~/ssh/ no seu computador local; por exemplo, abrir um editor para um novo ficheiro e colar a chave em: 

   **Linux:**

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**:

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   A chave privada será um pouco assim:

   ```text
   -----BEGIN RSA PRIVATE KEY-----

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw==

   -----END RSA PRIVATE KEY-----
   ```

1. Altere as permissões no ficheiro para se certificar de que só pode ler o ficheiro.  

   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa
   ```

## <a name="add-instance-as-a-host"></a>Adicionar instância como hospedeiro

Abra o ficheiro `~/.ssh/config` (Linux) ou `C:\Users<username>.ssh\config` (Windows) num editor e adicione uma nova entrada semelhante a esta:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

Aqui alguns detalhes sobre os campos:

|Campo|Descrição|
|----|---------|
|Anfitrião|Use a abreviatura que quiser para a instância computacional |
|Nome do Anfitrião|Este é o endereço IP da instância computacional |
|Porta|Esta é a porta mostrada no diálogo SSH acima |
|Utilizador|Isto tem de ser `azureuser` |
|Ficheiro de Identidade|Deve apontar para o ficheiro onde guardou a chave privada |

Agora, você deve ser capaz de ssh para a sua instância de cálculo usando a abreviatura que você usou acima, `ssh azmlci1` .

## <a name="connect-vs-code-to-the-instance"></a>Ligue o Código VS ao caso

1. [Instale o Código do Estúdio Visual](https://code.visualstudio.com/).

1. [Instale a extensão SSH remota](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh).

1. Clique no ícone Remote-SSH à esquerda para mostrar as suas configurações SSH.

1. Clique com o botão direito na configuração do anfitrião SSH que acabou de criar.

1. Selecione **Ligar ao anfitrião na janela atual**. 

A partir de agora, está a trabalhar inteiramente na instância computacional e agora pode editar, depurar, usar git, usar extensões, etc.

## <a name="next-steps"></a>Passos seguintes

Agora que configuraste o Visual Studio Code Remote, podes usar uma instância computacional como computação remota do Código do Estúdio Visual para [depurar interativamente o teu código.](how-to-debug-visual-studio-code.md)

[Tutorial: Treine o seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância computacional com um caderno integrado.