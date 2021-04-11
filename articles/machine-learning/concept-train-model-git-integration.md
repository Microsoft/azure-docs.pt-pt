---
title: Integração de Git para Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como a Azure Machine Learning se integra com um repositório local de Git para rastrear o repositório, ramo e informação atual de compromisso como parte de uma corrida de formação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 04/08/2021
ms.openlocfilehash: 2dc50702113f591075b790878347c4ca47beec4e
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107027810"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integração de Git para Azure Machine Learning

[Git](https://git-scm.com/) é um popular sistema de controlo de versão que permite partilhar e colaborar nos seus projetos. 

A Azure Machine Learning suporta totalmente os repositórios de Git para o trabalho de rastreio - pode clonar repositórios diretamente no seu sistema de ficheiros de espaço de trabalho partilhado, usar Git na sua estação de trabalho local ou usar Git a partir de um oleoduto CI/CD.

Ao submeter um trabalho ao Azure Machine Learning, se os ficheiros de origem forem armazenados num repositório local, então a informação sobre o repo é rastreada como parte do processo de treino.

Uma vez que a Azure Machine Learning rastreia informações de um repo local, não está ligada a nenhum repositório central específico. O seu repositório pode ser clonado do GitHub, GitLab, Bitbucket, Azure DevOps ou de qualquer outro serviço compatível com git.

> [!TIP]
> Utilize o Código do Estúdio Visual para interagir com o Git através de uma interface gráfica do utilizador. Para ligar a uma instância de computação remota Azure Machine Learning utilizando o Código do Estúdio Visual, consulte [Connect to a Azure Machine Learning compute instance in Visual Studio Code (pré-visualização)](how-to-set-up-vs-code-remote.md)
>
> Para obter mais informações sobre as funcionalidades de controlo da versão Visual Studio Code, consulte [o Controlo de Versão Usando o Código VS](https://code.visualstudio.com/docs/editor/versioncontrol) e [trabalhando com o GitHub no Código VS](https://code.visualstudio.com/docs/editor/github).

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Clonar repositórios Git no sistema de ficheiros da área de trabalho
O Azure Machine Learning fornece um sistema de ficheiros partilhado para todos os utilizadores no espaço de trabalho.
Para clonar um repositório git nesta partilha de ficheiros, recomendamos que crie uma instância de computação & [abrir um terminal](how-to-access-terminal.md).
Uma vez aberto o terminal, você tem acesso a um cliente Git completo e pode clonar e trabalhar com Git através da experiência Git CLI.

Recomendamos que clone o repositório no diretório dos seus utilizadores para que outros não façam colisões diretamente no seu ramo de trabalho.

Você pode clonar qualquer repositório git que você pode autenticar (GitHub, Azure Repos, BitBucket, etc.)

Para obter mais informações sobre clonagem, consulte o guia sobre [como utilizar o Git CLI](https://guides.github.com/introduction/git-handbook/).

## <a name="authenticate-your-git-account-with-ssh"></a>Autenticar a sua Conta Git com SSH
### <a name="generate-a-new-ssh-key"></a>Gerar uma nova chave SSH
1) [Abra a janela do terminal](./how-to-access-terminal.md) no separador de caderno de aprendizagem da máquina Azure.

2) Cole o texto abaixo, substituindo no seu endereço de e-mail.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Isto cria uma nova chave ssh, usando o e-mail fornecido como uma etiqueta.

```
> Generating public/private rsa key pair.
```

3) Quando lhe for solicitado que "Introduza um ficheiro no qual guarde a tecla" prima Enter. Isto aceita a localização do ficheiro predefinido.

4) Verifique se a localização predefinida é '/home/azureuser/.ssh' e prima a entrada. Caso contrário, especifique a localização '/home/azureuser/.ssh'.

> [!TIP]
> Certifique-se de que a tecla SSH está guardada em '/home/azureuser/.ssh'. Este ficheiro é guardado na instância computacional só é acessível pelo proprietário da Instância Computacional

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) Ao solicitar, escreva uma frase de passe segura. Recomendamos que adicione uma palavra-passe à sua chave SSH para uma maior segurança

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Adicione a chave pública à Conta Git
1) Na janela do seu terminal, copie o conteúdo do seu ficheiro de chave pública. Se renomeou a chave, substitua id_rsa.pub pelo nome do ficheiro da chave pública.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Copiar e colar no terminal**
> * Janelas: `Ctrl-Insert` copiar e utilizar ou `Ctrl-Shift-v` `Shift-Insert` colar.
> * Mac OS: `Cmd-c` copiar e `Cmd-v` colar.
> * O FireFox/IE pode não suportar adequadamente as permissões de prancheta.

2) Selecione e copie a saída da chave na área de transferência.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs)  Comece no **passo 2.**

+ [BitBucket.](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2) Comece no **passo 4.**

### <a name="clone-the-git-repository-with-ssh"></a>Clone o repositório de Git com SSH

1) Copie o URL do clone SSH Git do git repo.

2) Cole o url no `git clone` comando abaixo, para utilizar o seu URL de repo SSH Git. Isto vai parecer algo como:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

Verá uma resposta como:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

O SSH pode apresentar a impressão digital SSH do servidor e pedir-lhe que verifique. Deve verificar se a impressão digital apresentada corresponde a uma das impressões digitais na página das chaves públicas do SSH.

O SSH exibe esta impressão digital quando se conecta a um hospedeiro desconhecido para [protegê-lo de ataques man-in-the-middle](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10)). Uma vez que aceite a impressão digital do anfitrião, a SSH não o solicitará novamente, a menos que a impressão digital mude.

3) Quando lhe perguntarem se pretende continuar a ligar, escreva `yes` . Git clonará o repo e configurará o controlo remoto de origem para se conectar com o SSH para futuros comandos git.

## <a name="track-code-that-comes-from-git-repositories"></a>Código de rastreio que vem dos repositórios de Git

Quando submete uma corrida de treino a partir do Python SDK ou do Machine Learning CLI, os ficheiros necessários para treinar o modelo são enviados para o seu espaço de trabalho. Se o `git` comando estiver disponível no seu ambiente de desenvolvimento, o processo de upload utiliza-o para verificar se os ficheiros estão armazenados num repositório de git. Em caso afirmativo, então a informação do seu repositório de git também é carregada como parte da formação. Esta informação é armazenada nas seguintes propriedades para a execução de formação:

| Propriedade | Comando Git usado para obter o valor | Description |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | O URI de onde o seu repositório foi clonado. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | O URI de onde o seu repositório foi clonado. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | O ramo ativo quando a corrida foi submetida. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | O ramo ativo quando a corrida foi submetida. |
| `azureml.git.commit` | `git rev-parse HEAD` | O haxixe do código que foi submetido para a corrida. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | O haxixe do código que foi submetido para a corrida. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, se o ramo/compromisso estiver sujo; caso contrário, `false` . . |

Esta informação é enviada para corridas que utilizem um estimador, um pipeline de aprendizagem automática ou script run.

Se os seus ficheiros de treino não estiverem localizados num repositório de git no seu ambiente de desenvolvimento, ou se o `git` comando não estiver disponível, então nenhuma informação relacionada com git é rastreada.

> [!TIP]
> Para verificar se o comando git está disponível no seu ambiente de desenvolvimento, abra uma sessão de shell, pedido de comando, PowerShell ou outra interface de linha de comando e digite o seguinte comando:
>
> ```
> git --version
> ```
>
> Se instalado, e no caminho, recebe uma resposta semelhante a `git version 2.4.1` . Para obter mais informações sobre a instalação do git no seu ambiente de desenvolvimento, consulte o [site da Git.](https://git-scm.com/)

## <a name="view-the-logged-information"></a>Ver as informações registadas

A informação do git é armazenada nas propriedades para uma corrida de treino. Pode ver esta informação utilizando o portal Azure, Python SDK e CLI. 

### <a name="azure-portal"></a>Portal do Azure

1. A partir do portal do [estúdio,](https://ml.azure.com)selecione o seu espaço de trabalho.
1. Selecione __Experimentos__ e, em seguida, selecione uma das suas experiências.
1. Selecione uma das correções da coluna __RUN NUMBER.__
1. Selecione __Outputs + logs__ e, em seguida, expanda os __registos__ e entradas __azuremis.__ Selecione o link que começa com __### \_ azul__.

As informações registadas contêm texto semelhante ao seguinte JSON:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>SDK Python

Depois de submeter uma corrida de treino, um objeto [Run](/python/api/azureml-core/azureml.core.run%28class%29) é devolvido. O `properties` atributo deste objeto contém a informação do git registado. Por exemplo, o seguinte código recupera o haxixe do compromisso:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

O `az ml run` comando CLI pode ser usado para recuperar as propriedades de uma corrida. Por exemplo, o seguinte comando devolve as propriedades para a última execução da experiência `train-on-amlcompute` denominada:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Para mais informações, consulte a documentação de referência [az ml run.](/cli/azure/ext/azure-cli-ml/ml/run)

## <a name="next-steps"></a>Passos seguintes

* [Utilize alvos de computação para a formação de modelos](how-to-set-up-training-targets.md)