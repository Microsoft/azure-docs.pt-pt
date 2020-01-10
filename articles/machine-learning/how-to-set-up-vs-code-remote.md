---
title: 'Depuração interativa: instâncias de computação do VS Code & ML'
titleSuffix: Azure Machine Learning
description: Configure VS Code remoto para depurar de forma interativa seu código com o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 9798cbd2b1c9dca489e843a7102f213757e4de75
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771824"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Depurar interativamente em uma instância de computação Azure Machine Learning com VS Code remoto

Neste artigo, você aprenderá a configurar o Visual Studio Code remoto em uma instância de computação Azure Machine Learning para que você possa **depurar interativamente seu código** de vs Code. 

> [!NOTE]
> As instâncias de computação estão disponíveis somente para espaços de trabalho com uma região de **EUA Central norte** ou **sul do Reino Unido**.

+ Uma [instância de computação Azure Machine Learning](concept-compute-instance.md) é uma estação de trabalho baseada em nuvem totalmente gerenciada para cientistas de dados e fornece recursos de gerenciamento e preparação empresarial para os administradores de ti. 


+ [Visual Studio Code remoto](https://code.visualstudio.com/docs/remote/remote-overview) O desenvolvimento permite que você use um contêiner, computador remoto ou o subsistema do Windows para Linux (WSL) como um ambiente de desenvolvimento repleto de recursos. 

## <a name="prerequisite"></a>Pré-requisito  

Em plataformas Windows, você deve [instalar um cliente SSH compatível com OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) se ainda não houver um. 

> [!Note]
> Não há suporte para o percurso no Windows, pois o comando ssh deve estar no caminho. 

## <a name="get-ip-and-ssh-port"></a>Obter IP e porta SSH 

1. Acesse o Azure Machine Learning Studio em https://ml.azure.com/.

2. Selecione seu [espaço de trabalho](concept-workspace.md).
1. Clique na guia **instâncias de computação** .
1. Na coluna **URI do aplicativo** , clique no link **SSH** da instância de computação que você deseja usar como uma computação remota. 
1. Na caixa de diálogo, anote o endereço IP e a porta SSH. 
1. Salve sua chave privada no diretório ~/.ssh/no computador local; por exemplo, abra um editor para um novo arquivo e cole a chave em: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   A chave privada terá uma aparência semelhante a esta:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Altere as permissões no arquivo para garantir que apenas você possa ler o arquivo.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Adicionar instância como um host 

Abra o arquivo `~/.ssh/config` (Linux) ou `C:\Users<username>.ssh\config` (Windows) em um editor e adicione uma nova entrada semelhante a esta:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Aqui estão alguns detalhes sobre os campos: 

|Campo|Descrição|
|----|---------|
|Host|Use qualquer abreviação que desejar para a instância de computação |
|nome de anfitrião|Este é o endereço IP da instância de computação |
|Porta|Esta é a porta mostrada na caixa de diálogo SSH acima |
|Utilizador|Isso precisa ser `azureuser` |
|IdentityFile|Deve apontar para o arquivo em que você salvou a chave privada |

Agora, você deve ser capaz de usar o ssh em sua instância de computação usando a abreviação usada acima, `ssh azmlci1`. 

## <a name="connect-vs-code-to-the-instance"></a>Conectar VS Code à instância 

1. [Instale o Visual Studio Code](https://code.visualstudio.com/).

1. [Instale a extensão SSH remota](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Clique no ícone Remote-SSH à esquerda para mostrar suas configurações de SSH.

1. Clique com o botão direito do mouse na configuração do host SSH que você acabou de criar.

1. Selecione **conectar ao host na janela atual**. 

Daqui em diante, você está trabalhando totalmente na instância de computação e agora pode editar, depurar, usar o Git, usar extensões, etc.--assim como você pode com seu Visual Studio Code local. 

## <a name="next-steps"></a>Passos seguintes

Agora que você configurou o Visual Studio Code remoto, é possível usar uma instância de computação como computação remota do Visual Studio Code para depurar interativamente seu código. 

[Tutorial: treinar seu primeiro modelo de ml](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância de computação com um bloco de anotações integrado.