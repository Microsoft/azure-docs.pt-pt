---
title: Lidar com upgrades de nó AKS com ações do GitHub
titleSuffix: Azure Kubernetes Service
description: Saiba como atualizar os nosdes AKS usando as ações do GitHub
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 6876cf1e5044246492e249d8a61060cbeac46f96
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217962"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>Aplicar atualizações de segurança aos nós do Serviço Azure Kubernetes (AKS) utilizando automaticamente as ações do GitHub

As atualizações de segurança são uma parte fundamental para manter a segurança do seu cluster AKS e o cumprimento das correções mais recentes para o sistema operativo subjacente. Estas atualizações incluem correções de segurança de SO ou atualizações de kernel. Algumas atualizações requerem um reboot de nó para completar o processo.

A `az aks upgrade` execução dá-lhe uma forma de inatividade zero para aplicar atualizações. O comando aplica as últimas atualizações a todos os nós do seu cluster, isolando e drenando o tráfego para os nós e reiniciando os nós, permitindo depois o tráfego para os nós atualizados. Se atualizar os seus nós utilizando um método diferente, o AKS não reiniciará automaticamente os seus nós.

> [!NOTE]
> A principal diferença entre `az aks upgrade` quando usado com a bandeira é `--node-image-only` que, quando é usado, apenas as imagens do nó serão atualizadas. Se omitidos, tanto as imagens do nó como a versão do avião de controlo Kubernetes serão atualizadas. Pode verificar [se os docs estão geridos em nós][managed-node-upgrades-article] e nos docs para obter atualizações mais [aprofundadas.][cluster-upgrades-article]

Todos os nós de Kubernetes funcionam numa máquina virtual Azure padrão (VM). Estes VMs podem ser baseados em Windows ou Linux. Os VMs baseados em Linux utilizam uma imagem Ubuntu, com o SISTEMA configurado para verificar automaticamente se há atualizações todas as noites.

Quando utiliza o comando, o `az aks upgrade` Azure CLI cria uma onda de novos nós com as mais recentes atualizações de segurança e kernel, estes nós são inicialmente isolados para impedir que quaisquer aplicações sejam programadas para eles até que a atualização esteja concluída. Após a conclusão, os cordons Azure (torna o nó indisponível para agendamento de novas cargas de trabalho) e drena (move as cargas de trabalho existentes para outros nós) os nós mais antigos e descoro os novos, transferindo efetivamente todas as aplicações programadas para os novos nós.

Este processo é melhor do que atualizar os núcleos baseados em Linux manualmente porque o Linux requer um reboot quando uma nova atualização de kernel é instalada. Se atualizar o SO manualmente, também precisa de reiniciar o VM, isolando e drenando manualmente todas as aplicações.

Este artigo mostra-lhe como pode automatizar o processo de atualização dos nós AKS. Utilizará As ações do GitHub e o Azure CLI para criar uma tarefa de atualização baseada na `cron` que funciona automaticamente.

## <a name="before-you-begin"></a>Before you begin

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão Azure CLI 2.0.59 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

Este artigo também assume que tem uma conta [GitHub][github] para criar as suas ações.

## <a name="create-a-timed-github-action"></a>Criar uma ação gitHub cronometrada

`cron` é um utilitário que lhe permite executar um conjunto de comandos, ou trabalho, em um horário automatizado. Para criar trabalho para atualizar os seus nós AKS num horário automatizado, você precisará de um repositório para hospedar as suas ações. Normalmente, as ações do GitHub estão configuradas no mesmo repositório que a sua aplicação, mas pode usar qualquer repositório. Para este artigo vamos usar o seu [repositório de perfis.][profile-repository] Se não tiver um, crie um novo repositório com o mesmo nome que o seu nome de utilizador GitHub.

1. Navegue até ao seu repositório no GitHub
1. Clique no **separador Ações** no topo da página.
1. Se já configurar um fluxo de trabalho neste repositório, será direcionado para a lista de execuções concluídas, neste caso, clique no botão **New Workflow.** Se este for o seu primeiro fluxo de trabalho no repositório, o GitHub irá apresentar-lhe alguns modelos de projeto, clique no **set up um link de fluxo de trabalho** abaixo do texto de descrição.
1. Altere o fluxo de trabalho `name` e `on` as etiquetas semelhantes às abaixo. As ações GitHub utilizam a mesma [sintaxe de cron POSIX][cron-syntax] que qualquer sistema baseado em Linux. Neste horário, estamos a dizer ao fluxo de trabalho para funcionar a cada 15 dias às 3 da manhã.

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. Crie um novo emprego usando o abaixo. Este trabalho é `upgrade-node` nomeado, funciona com um agente Ubuntu, e irá ligar-se à sua conta Azure CLI para executar os passos necessários para atualizar os nós.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>Configurar o CLI Azure no fluxo de trabalho

Na `steps` tecla, definirá todo o trabalho que o fluxo de trabalho executará para atualizar os nós.

Faça o download e inscreva-se no Azure CLI.

1. No lado direito do ecrã GitHub Actions, encontre a barra de pesquisa do *mercado* e escreva **"Azure Login".**
1. Você receberá como resultado, uma ação chamada **Azure Login** publicada **pela Azure**:

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="Resultados da pesquisa que mostram duas linhas, a primeira ação chama-se 'Azure Login' e o segundo 'Azure Container Registry Registry Login'":::

1. Clique no **Azure Login**. No ecrã seguinte, clique no ícone de **cópia** no topo direito da amostra de código.

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="Painel de resultados de ação de Azure Login com amostra de código abaixo, quadrado vermelho em torno de um ícone de cópia realça o ponto de clique":::

1. Colar o seguinte na `steps` chave:

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. A partir do CLI Azure, executar o seguinte comando para gerar um novo nome de utilizador e senha.

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    A saída deve ser semelhante à seguinte:

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **Numa nova janela do navegador,** navegue para o seu repositório GitHub e abra o **separador Definições** do repositório. Clique em **Segredos** então, clique em **Novo Segredo de Repositório**.
1. Para *nome,* use `AZURE_CREDENTIALS` .
1.  Para *Valor*, adicione todo o conteúdo da saída do passo anterior onde criou um novo nome de utilizador e senha.

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="Formulário mostrando AZURE_CREDENTIALS como título secreto, e a saída do comando executado colado como JSON":::

1. Clique **em Adicionar Segredo.**

O CLI utilizado pela sua ação será registado na sua conta Azure e pronto para executar comandos.

Para criar os passos para executar comandos Azure CLI.

1. Navegue para a **página de pesquisa** no mercado *GitHub* no lado direito do ecrã e procure *A ação do Azure CLI*. Escolha *A ação Azure CLI por Azure*.

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="Resultado da pesquisa de 'Azure CLI Action' com o primeiro resultado a ser mostrado como feito pela Azure":::

1. Clique no botão de cópia no resultado do *mercado GitHub* e cole o conteúdo da ação no editor principal, abaixo do passo *de Login Azure,* semelhante ao seguinte:

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only --yes
    ```

    > [!TIP]
    > Pode dissociar os `-g` parâmetros e `-n` parâmetros do comando adicionando-os a segredos semelhantes aos passos anteriores. Substitua os `{resourceGroupName}` espaços reservados e `{aksClusterName}` os seus congéneres secretos, por `${{secrets.RESOURCE_GROUP_NAME}}` exemplo, e `${{secrets.AKS_CLUSTER_NAME}}`

1. Mude o nome do ficheiro para `upgrade-node-images`.
1. Clique **em Iniciar ODeseça,** adicione um título de mensagem e guarde o fluxo de trabalho.

Assim que criar o compromisso, o fluxo de trabalho será guardado e pronto para a execução.

> [!NOTE]
> Para atualizar uma única piscina de nós em vez de todas as piscinas de nós no cluster, adicione o `--name` parâmetro ao comando para `az aks nodepool upgrade` especificar o nome da piscina do nó. Por exemplo:
> ```azurecli-interactive
> az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>Executar a Ação GitHub manualmente

Pode executar o fluxo de trabalho manualmente, para além da execução programada, adicionando um novo `on` gatilho chamado `workflow_dispatch` . O ficheiro acabado deve parecer-se com o YAML abaixo:

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>Passos seguintes

- Consulte as notas de lançamento da [AKS](https://github.com/Azure/AKS/releases) para obter informações sobre as imagens mais recentes do nó.
- Saiba como atualizar a versão Kubernetes com [upgrade de um cluster AKS.][cluster-upgrades-article]
- Saiba mais sobre várias piscinas de nós e como atualizar piscinas de nó com [Criar e gerir várias piscinas de nó.][use-multiple-node-pools]
- Saiba mais sobre [piscinas de nó de sistema][system-pools]
- Para aprender a economizar custos usando instâncias spot, consulte [adicionar uma piscina de nó de ponto à AKS][spot-pools]

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
