---
title: 'Depuração interativa: Código VS & instâncias computacionais ml'
titleSuffix: Azure Machine Learning
description: Configurar o Código VS Remote para depurar interativamente o seu código com o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169743"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Depurar interativamente em uma instância de computação de aprendizagem automática Azure com vs código remoto

Neste artigo, aprenderá sacar o código do estúdio visual Remote numa instância de computação de aprendizagem automática Azure para que possa **desinvejar interativamente o seu código** a partir do Código VS. 

+ A [Azure Machine Learning Compute Instance](concept-compute-instance.md) é uma estação de trabalho totalmente gerida em nuvem para cientistas de dados e fornece capacidades de gestão e prontidão empresarial para administradores de TI. 


+ [Código de estúdio visual Remoto](https://code.visualstudio.com/docs/remote/remote-overview) O desenvolvimento permite-lhe utilizar um recipiente, uma máquina remota ou o Subsistema Windows para linux (WSL) como um ambiente de desenvolvimento completo. 

## <a name="prerequisite"></a>Pré-requisito  

Nas plataformas Windows, tem de [instalar um cliente SSH compatível com OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) se um ainda não estiver presente. 

> [!Note]
> O PuTTY não é suportado no Windows, uma vez que o comando ssh deve estar no caminho. 

## <a name="get-ip-and-ssh-port"></a>Obtenha porta IP e SSH 

1. Vá ao estúdio de Aprendizagem automática Azure em https://ml.azure.com/.

2. Selecione o seu [espaço de trabalho](concept-workspace.md).
1. Clique no separador **Compute Instances.**
1. Na coluna **Application URI,** clique na ligação **SSH** da instância computacional que pretende utilizar como uma computação remota. 
1. No diálogo, tome nota do endereço IP e da porta SSH. 
1. Guarde a sua chave privada para o diretório ~/.ssh/ no seu computador local; por exemplo, abra um editor para um novo ficheiro e colhe a chave em: 

   **Linux:** 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Janelas:** 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   A chave privada será um pouco assim:
   ```
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

## <a name="add-instance-as-a-host"></a>Adicione instância como hospedeiro 

Abra o `~/.ssh/config` ficheiro (Linux) ou `C:\Users<username>.ssh\config` (Windows) num editor e adicione uma nova entrada semelhante a esta:

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
|Anfitrião|Use o que quiser para a instância de cálculo |
|NomedeAnfitrião|Este é o endereço IP da instância computacional |
|Porta|Esta é a porta mostrada no diálogo SSH acima |
|Utilizador|Isto tem de ser `azureuser` |
|Ficheiro de Identidade|Deve apontar para o arquivo onde guardou a chave privada |

Agora, deve ser capaz de se esguijar para a sua `ssh azmlci1`instância de cálculo usando a abreviatura que usou acima, . 

## <a name="connect-vs-code-to-the-instance"></a>Ligue o Código VS à instância 

1. [Instale o Código](https://code.visualstudio.com/)do Estúdio Visual .

1. [Instale a extensão SSH remota](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Clique no ícone Remote-SSH à esquerda para mostrar as suas configurações SSH.

1. Clique na configuração do hospedeiro SSH que acabou de criar.

1. **Selecione Ligar ao anfitrião na janela atual**. 

A partir de agora, está a trabalhar inteiramente na instância computacional e agora pode editar, depurar, usar git, usar extensões, etc. - tal como pode com o seu Código de Estúdio Visual local. 

## <a name="next-steps"></a>Passos seguintes

Agora que configuraste o Código de Código do Estúdio Visual Remote, podes usar uma instância de cálculo como computação remota do Visual Studio Code para desinpurar interativamente o teu código. 

[Tutorial: Treinar o seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância computacional com um caderno integrado.