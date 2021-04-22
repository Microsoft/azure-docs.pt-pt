---
title: Configurar o GitHub Enterprise Server na sua nuvem privada Azure VMware Solution
description: Saiba como configurar o GitHub Enterprise Server na sua nuvem privada Azure VMware Solution.
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 0ff7ab87d7401cd3faaecf149fb1b07be3f8db42
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862944"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Configurar o GitHub Enterprise Server na sua nuvem privada Azure VMware Solution

Neste artigo, percorremos os passos para configurar o GitHub Enterprise Server, a versão "on-in" da [GitHub.com,](https://github.com/)na sua nuvem privada Azure VMware Solution. O cenário que vamos abordar é uma instância do GitHub Enterprise Server que pode servir até 3.000 desenvolvedores com até 25 postos de trabalho por minuto em GitHub Actions. Inclui a configuração de funcionalidades de *pré-visualização* (no momento da escrita), tais como GitHub Actions. Para personalizar a configuração para as suas necessidades particulares, reveja os requisitos listados na [instalação do GitHub Enterprise Server no VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations).

## <a name="before-you-begin"></a>Antes de começar

GitHub Enterprise Server requer uma chave de licença válida. Pode inscrever-se para uma [licença de julgamento.](https://enterprise.github.com/trial) Se pretender alargar as capacidades do GitHub Enterprise Server através de uma integração, poderá beneficiar de uma licença de programador gratuita de cinco lugares. Solicite esta licença através [do Programa de Parceiros do GitHub.](https://partner.github.com/)

## <a name="installing-github-enterprise-server-on-vmware"></a>Instalação do GitHub Enterprise Server em VMware

Descarregue [o lançamento atual do GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) para VMware ESXi/vSphere (OVA) e [implemente o modelo OVA](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) que descarregou.

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Opte por executar o GitHub nas instalações ou na nuvem.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Desloque o modelo OVA.":::  

Forneça um nome reconhecível para a sua nova máquina virtual, como GitHubEnterpriseServer. Não é necessário incluir os detalhes de lançamento no nome VM, uma vez que estes detalhes ficam velhos quando a instância é atualizada. Selecione todos os predefinidos por enquanto (vamos editar estes detalhes em breve) e esperar que o OVA seja importado.

Uma vez importado, [ajuste a configuração do hardware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) com base nas suas necessidades. No nosso cenário de exemplo, vamos precisar da seguinte configuração.

| Recurso | Configuração padrão | Configuração padrão + "Funcionalidades Beta" (Ações) |
| --- | --- | --- |
| vCPUs | 4 | 8 |
| Memória | 32 GB | 61 GB |
| Armazenamento anexo | 250 GB | 300 GB |
| Armazenamento de raiz | 200 GB | 200 GB |

No entanto, as suas necessidades podem variar. Consulte a orientação sobre considerações de hardware na [instalação do GitHub Enterprise Server no VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations). Consulte também [a Adição de CPU ou recursos de memória para vMware](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) para personalizar a configuração de hardware com base na sua situação.

## <a name="configuring-the-github-enterprise-server-instance"></a>Configurar a instância do Servidor de Empresas GitHub

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Instale a GitHub Enterprise.":::  

Depois de a máquina virtual (VM) recém-aertada ter ligado, [configuure-a através do seu browser](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). Será obrigado a fazer o upload do seu ficheiro de licença e definir uma senha de consola de gestão. Certifique-se de escrever esta senha em algum lugar seguro.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Aceda à concha de administração via SSH.":::    

Recomendamos pelo menos que tome os seguintes passos:

1. Faça o upload de uma chave SSH pública para a consola de gestão, para que possa [aceder à concha administrativa via SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh). 

2. [Configure o TLS no seu caso](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) para que possa utilizar um certificado assinado por uma autoridade de certificados de confiança.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Configurar o seu caso.":::

Aplique as suas definições.  Enquanto a instância recomeça, pode continuar com o próximo passo, configurando o **armazenamento de blob para as ações do GitHub**.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Crie a sua conta de administração.":::

Após o reinício do caso, pode criar uma nova conta de administração sobre o caso. Certifique-se de tomar nota da palavra-passe deste utilizador também.

### <a name="other-configuration-steps"></a>Outros passos de configuração

Para endurecer a sua instância para uso de produção, recomenda-se que sejam recomendados os seguintes passos de configuração opcionais:

1. Configure [uma elevada disponibilidade](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) para proteção contra:

    - Falhas de software (nível de SO ou aplicação)
    - Falhas de hardware (armazenamento, CPU, RAM, e assim por diante)
    - Falhas do sistema de hospedeiros de virtualização
    - Rede logicamente ou fisicamente cortada

2. [Configure](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) [os utilitários de backup,](https://github.com/github/backup-utils)fornecendo instantâneos versados para recuperação de desastres, hospedados em disponibilidades separadas da instância primária.
3. [Configurar o isolamento do subdomínio,](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)utilizando um certificado TLS válido, para mitigar a scripting de locais cruzados e outras vulnerabilidades relacionadas.

## <a name="configuring-blob-storage-for-github-actions"></a>Configurar o armazenamento de bolhas para as ações do GitHub

> [!NOTE]
> GitHub Actions está [atualmente disponível como uma versão beta limitada no GitHub Enterprise Server release 2.22](https://docs.github.com/en/enterprise/admin/github-actions).

O armazenamento de bolhas externas é necessário para permitir ações do GitHub no GitHub Enterprise Server (atualmente disponível como uma funcionalidade "beta"). Este armazenamento de bolhas externas é utilizado pelas ações para armazenar artefactos e troncos. As ações no GitHub Enterprise Server [suportam o Azure Blob Storage como fornecedor de armazenamento](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (e outros). Então vamos providenciar uma nova conta de armazenamento Azure com um tipo de conta de [armazenamento](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) de BlobStorage:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Provisão Azure Blob Storage.":::

Uma vez concluída a implantação do novo recurso BlobStorage, copie e tome nota da cadeia de ligação (disponível nas teclas Access). Vamos precisar desta corda em breve.

## <a name="setting-up-the-github-actions-runner"></a>Criação do corredor de ações gitHub

> [!NOTE]
> GitHub Actions está [atualmente disponível como uma versão beta limitada no GitHub Enterprise Server release 2.22](https://docs.github.com/en/enterprise/admin/github-actions).

Neste momento, deverá ter um caso de GitHub Enterprise Server em funcionamento, com uma conta de administrador criada. Também deve ter armazenamento de bolhas externas que as ações do GitHub usarão para persistência.

Agora vamos criar um lugar para gitHub actions para executar; novamente, usaremos a Solução VMware Azure.

Primeiro, vamos providenciar um novo VM no cluster. Basearemos o nosso VM [num recente lançamento do Ubuntu Server](http://releases.ubuntu.com/20.04.1/).

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Provisionar um novo VM.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Provisionar um novo passo VM 2.":::

Uma vez criado o VM, ligue-o e ligue-o através de SSH.

Em seguida, instale a aplicação [Actions runner,](https://github.com/actions/runner) que executa um trabalho a partir de um fluxo de trabalho GitHub Actions. Identifique e descarregue o lançamento mais atual do Linux x64 do corredor Actions, quer a partir da página de [lançamentos,](https://github.com/actions/runner/releases) quer executando o seguinte script rápido. Este script requer que o curl e [o jq](https://stedolan.github.io/jq/) estejam presentes no seu VM.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

Deve agora ter um ficheiro local no seu VM, ações-runner-linux-arm64-.tar.gz. \* Extraia esta bola de tarball localmente:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Esta extração desembala alguns ficheiros localmente, incluindo um `config.sh` `run.sh` script, ao qual voltaremos em breve.

## <a name="enabling-github-actions"></a>Habilitar ações do GitHub

> [!NOTE]
> GitHub Actions está [atualmente disponível como uma versão beta limitada no GitHub Enterprise Server release 2.22](https://docs.github.com/en/enterprise/admin/github-actions).

Quase lá! Vamos configurar e ativar as ações do GitHub na instância do GitHub Enterprise Server. Teremos de aceder à [concha administrativa do GitHub Enterprise Server sobre o SSH,](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)e depois executar os seguintes comandos:

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

Próxima corrida:

`ghe-actions-check -s blob`

Deve ver saída: "O armazenamento de bolhas é saudável".

Agora que as Ações GitHub estão configuradas, ative-as para os seus utilizadores. Inscreva-se na sua instância do GitHub Enterprise Server como administrador e selecione o ![ ícone Rocket.](media/github-enterprise-server/rocket-icon.png) no canto superior direito de qualquer página. Na barra lateral esquerda, selecione **Enterprise overview**, then **Policies**, **Actions**, e selecione a opção para **ativar Ações para todas as organizações**.

Em seguida, configuure o seu corredor a partir do separador **corredores auto-hospedados.** **Selecione Adicione novo** e, em seguida, novo **corredor** a partir da queda.

Na próxima página, será apresentado um conjunto de comandos para executar, apenas precisamos copiar o comando para **configurar** o corredor, por exemplo:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Copie o `config.sh` comando e cole-o numa sessão no seu corredor de Ações (criado anteriormente).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Corredor de ações.":::

Use o comando run.sh para *executar* o corredor:

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Corre o corredor.":::

Para disponibilizar este corredor a organizações da sua empresa, edite o acesso à sua organização:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Editar acesso ao corredor.":::

Aqui vamos disponibilizá-lo a todas as organizações, mas você pode limitar o acesso a um subconjunto de organizações, e até mesmo a repositórios específicos.

## <a name="optional-configuring-github-connect"></a>(Opcional) Configurar GitHub Connect

Embora este passo seja opcional, recomendamos que se planeia consumir ações de código aberto disponíveis no GitHub.com. Permite-lhe basear-se no trabalho de outros, referindo estas ações reutilizáveis nos seus fluxos de trabalho.

Para ativar o GitHub Connect, siga os passos para permitir o [acesso automático a ações de GitHub.com utilizando o GitHub Connect](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect).

Assim que o GitHub Connect estiver ativado, selecione o **Servidor para utilizar ações a partir de GitHub.com na** opção de fluxo de trabalho.

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Ativar a utilização de ações a partir de GitHub.com em fluxos de trabalho.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Configurar e executar o seu primeiro fluxo de trabalho

Agora que a Actions e o GitHub Connect estão preparados, vamos dar bom uso a todo este trabalho. Aqui está um fluxo de trabalho exemplo que faz referência ao excelente [octokit/pedido-acção, permitindo-nos](https://github.com/octokit/request-action)"script" GitHub através de interações usando a API GitHub, alimentada por GitHub Actions.

Neste fluxo de trabalho básico, usaremos `octokit/request-action` apenas para abrir um problema no GitHub usando a API.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Exemplo fluxo de trabalho.":::

>[!NOTE]
>GitHub.com acolhe a ação, mas quando funciona no GitHub Enterprise Server, utiliza *automaticamente* a API do GitHub Enterprise Server.

Se optar por não ativar o GitHub Connect, pode utilizar o seguinte fluxo de trabalho alternativo.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Fluxo de trabalho de exemplo alternativo.":::

Navegue para um repo no seu caso, e adicione o fluxo de trabalho acima como: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Outro exemplo de fluxo de trabalho.":::

No **separador Ações** para o seu repo, aguarde que o fluxo de trabalho seja executado.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Fluxo de trabalho de exemplo executado.":::

Também pode vê-lo sendo processado pelo corredor.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Fluxo de trabalho processado por corredor.":::

Se tudo correu com sucesso, deve ver um novo problema no seu repo, intitulado "Olá mundo".

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Exemplo no repo.":::

Parabéns! Acabaste de completar o teu fluxo de trabalho de Ações no GitHub Enterprise Server, a correr na tua nuvem privada Azure VMware Solution.

Neste artigo, criamos uma nova instância do GitHub Enterprise Server, o equivalente auto-hospedado de GitHub.com, em cima da sua nuvem privada Azure VMware Solution. Este caso inclui suporte para ações do GitHub e utiliza o Azure Blob Storage para persistência de troncos e artefactos. Mas estamos apenas a riscar a superfície do que podes fazer com as ações do GitHub. Confira a lista de Ações no [Mercado do GitHub,](https://github.com/marketplace)ou [crie a sua própria](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu a criação do GitHub Enterprise Server na sua nuvem privada Azure VMware Solution, talvez queira saber mais sobre: 

- [Como começar com as ações do GitHub](https://docs.github.com/en/actions)
- [Como aderir ao programa beta](https://resources.github.com/beta-signup/)
- [Administração do GitHub Enterprise Server](https://githubtraining.github.io/admin-training/#/00_getting_started)
