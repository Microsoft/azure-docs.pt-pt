---
title: Visão geral do suporte de Cloud-init para VMs Linux no Azure
description: Visão geral de recursos de inicialização de nuvem para configurar uma VM no tempo de provisionamento no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 01/23/2019
ms.author: danis
ms.openlocfilehash: e3a09a0d8412af711bfb6c539dc9d2829b1f0898
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964588"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Suporte de inicialização de nuvem para máquinas virtuais no Azure
Este artigo explica o suporte que existe para [Cloud-init](https://cloudinit.readthedocs.io) para configurar uma VM (máquina virtual) ou conjuntos de dimensionamento de máquinas virtuais no tempo de provisionamento no Azure. Estas configurações de cloud-init são executadas na primeira bota uma vez que os recursos foram aprovisionados pelo Azure.  

VM Provisioning é o processo em que o Azure passará para baixo os valores do seu VM Create, tais como nome de anfitrião, nome de utilizador, senha etc., e disponibilizá-los ao VM à medida que ele arranca. Um "agente de provisionamento" consumirá esses valores, configurará o VM e apresentará um relatório quando concluído. 

O Azure apoia dois agentes de fornecimento [em nuvem,](https://cloudinit.readthedocs.io)e o [Agente Azure Linux (WALA)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>nuvem-init visão geral
[cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente usada para personalizar um VM Linux como ele botas pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Porque o cloud-init é chamado durante o processo de inicialização inicial, não são passos adicionais ou agentes necessários a aplicar a configuração.  Para obter mais informações sobre como formatar corretamente os seus ficheiros `#cloud-config` ou outras inputs, consulte o [site de documentação init init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` os arquivos são arquivos de texto codificados em base64.

cloud-init também funciona em todas as distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativos para o distro que selecionar.

Estamos a trabalhar ativamente com os nossos parceiros de distribuição do Linux apoiados para ter imagens de cloud-init ativado disponíveis no Azure marketplace. Essas imagens farão com que suas implantações e configurações de Cloud-init funcionem de forma integrada com VMs e conjuntos de dimensionamento de máquinas virtuais. Inicialmente colaboramos com os parceiros de distro linux endossados e a montante para garantir que as funções de inite na nuvem com o SISTEMA em Azure, então os pacotes são atualizados e tornados públicos disponíveis nos repositórios de pacotes de distro. 

Existem duas fases para disponibilizar o init em nuvem para o endossado Linux distro OS's em Azure, suporte de pacote, e, em seguida, suporte de imagem:
* 'cloud-init package support on Azure' documentos que os pacotes de init em nuvem são suportados ou em pré-visualização, para que você possa usar estes pacotes com o S em uma imagem personalizada.
* Documentos 'cloud-init ready' se a imagem já estiver configurada para usar cloud-init.


### <a name="canonical"></a>Canonical
| Editor / Versão| Oferta | SKU | Versão | imagem cloud-init pronto | suporte de pacote cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canónica 18.04 |UbuntuServer |18.04-LTS |mais recente |sim | sim |
|Canónica 16.04|UbuntuServer |16.04-LTS |mais recente |sim | sim |
|Canónica 14.04|UbuntuServer |14.04.5-LTS |mais recente |sim | sim |

### <a name="rhel"></a>RHEL
| Editor / Versão | Oferta | SKU | Versão | imagem cloud-init pronto | suporte de pacote cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |sim | sim - suporte da versão do pacote: *18.2-1.1.el7_6.2*|
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 | Sim (note que esta é uma imagem de pré-visualização, e uma vez que todas as imagens RHEL 7.7 suportem cloud-init, esta será removida em meados de 2020, será dada a notificação) | sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 7,7 |RHEL |7-RAW | n/d| não - atualizações de imagem para iniciar fev 2020| sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 7,7 |RHEL |7-LVM | n/d| não - atualizações de imagem para iniciar fev 2020| sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 7,7 |RHEL |7.7 | n/d| não - atualizações de imagem para iniciar fev 2020 | sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 7,7 |RHEL-BYOS | RHEL-lvm77 | n/d|não - atualizações de imagem para iniciar fev 2020  | sim - suporte da versão do pacote: *18.5-3.el7*|

### <a name="centos"></a>CentOS

| Editor / Versão | Oferta | SKU | Versão | imagem cloud-init pronto | suporte de pacote cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |Sim (note que esta é uma imagem de pré-visualização, e uma vez que todas as imagens CentOS 7.7 suportem cloud-init, esta será removida em meados de 2020, será dada a notificação) | sim - suporte da versão do pacote: *18.5-3.el7.centos*|

* Imagens CentOS 7.7 que serão ativadas em nuvem serão atualizadas aqui em fevereiro de 2020 

### <a name="oracle"></a>Oracle

| Editor / Versão | Oferta | SKU | Versão | imagem cloud-init pronto | suporte de pacote cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7,7 |Oracle-Linux |77-CI |7.7.01| imagem de pré-visualização (note que esta é uma imagem de pré-visualização, e uma vez que todas as imagens Oracle 7.7 suportem cloud-init, esta será removida em meados de 2020, será dada a notificação) | não, na pré-visualização, o pacote é: *18.5-3.0.1.el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
Estamos neste momento a trabalhar para pré-visualizar o suporte, esperamos atualizações em fevereiro e março de 2020.

Atualmente o Azure Stack irá apoiar o fornecimento de imagens ativadas em nuvem.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Qual é a diferença entre Cloud-init e o agente do Linux (WALA)?
Wala é um agente específico da plataforma Azure usado para fornecer e configurar VMs, e lidar com [extensões Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux). 

Estamos a reforçar a tarefa de configurar vMs para usar cloud-init em vez do Agente Linux, de modo a permitir que os clientes existentes em cloud-init utilizem os seus scripts de cloud-init atuais, ou novos clientes para aproveitar a rica funcionalidade de configuração cloud-init. Se tiver investimentos existentes em scripts de cloud-init para configurar sistemas Linux, **não existem configurações adicionais necessárias** para permitir o seu processo de init em nuvem. 

Cloud-init não pode processar extensões Azure, por isso wala ainda é necessário na imagem para processar extensões, mas terá que ter o seu código de fornecimento desativado, para imagens de distros linux endossadas que estão sendo convertidas para fornecer por cloud-init, eles terão WALA instalado, e configurar corretamente.

Ao criar um VM, se não incluir o interruptor de `--custom-data` Azure CLI no tempo de provisionamento, o cloud-init ou o WALA retira os parâmetros mínimos de fornecimento de VM necessários para fornecer o VM e completar a implementação com os incumprimentos.  Se consultar a configuração cloud-init com o `--custom-data` interruptor, o que estiver contido nos seus dados personalizados estará disponível para inite na nuvem quando as botas VM.

As configurações de cloud-init aplicadas aos VMs não têm restrições de tempo e não causarão uma falha de implantação por tempo limite. Isto não é verdade para a WALA, se alterar os incumprimentos do WALA para processar dados personalizados, não pode exceder o subsídio total de tempo de fornecimento de VM de 40mins, se assim for, o VM Create falhará.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Implantando uma máquina virtual habilitada para Cloud-init
Implantar uma máquina virtual habilitada para Cloud-init é tão simples quanto fazer referência a uma distribuição habilitada para Cloud-init durante a implantação.  Os mantenedores de distribuição do Linux precisam optar por habilitar e integrar o Cloud-init em suas imagens publicadas do Azure base. Depois de confirmar que a imagem que você deseja implantar é habilitada para Cloud-init, você pode usar o CLI do Azure para implantar a imagem. 

A primeira etapa na implantação dessa imagem é criar um grupo de recursos com o comando [AZ Group Create](/cli/azure/group) . Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
A próxima etapa é criar um arquivo no shell atual, chamado *Cloud-init. txt* , e colar a configuração a seguir. Para este exemplo, crie o arquivo no Cloud Shell não em seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud-init.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o editor do **nano** . Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Pressione `ctrl-X` para sair do arquivo, digite `y` para salvar o arquivo e pressione `enter` para confirmar o nome do arquivo na saída.

A etapa final é criar uma VM com o comando [AZ VM Create](/cli/azure/vm) . 

O exemplo a seguir cria uma VM denominada *centos74* e cria chaves SSH, se elas ainda não existirem em um local de chave padrão. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  Utilize o parâmetro `--custom-data` para passar o ficheiro de configuração de inicialização da cloud. Forneça o caminho completo para a configuração do *cloud-init.txt*, se tiver guardado o ficheiro fora do diretório de trabalho atual. O exemplo a seguir cria uma VM chamada *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Quando a VM tiver sido criada, a CLI do Azure mostrará informações específicas de sua implantação. Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder à VM.  Leva algum tempo para que a VM seja criada, os pacotes a serem instalados e o aplicativo seja iniciado. Existem tarefas em segundo plano que continuam em execução após a CLI do Azure o devolver à linha de comandos. Você pode fazer SSH na VM e usar as etapas descritas na seção de solução de problemas para exibir os logs de Cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Solucionando problemas de Cloud-init
Depois que a VM for provisionada, o Cloud-init será executado por todos os módulos e o script definido em `--custom-data` para configurar a VM.  Se necessitar de resolver erros ou omissões da configuração, tem de procurar o nome do módulo (`disk_setup` ou `runcmd` por exemplo) no registo de cloud-init - localizado em **/var/log/cloud-init.log**.

> [!NOTE]
> Nem toda falha de módulo resulta em uma falha fatal de configuração geral de Cloud-init. Por exemplo, usando o módulo `runcmd`, se o script falhar, o Cloud-init ainda relatará o provisionamento com êxito porque o módulo runcmd foi executado.

Para obter mais detalhes sobre o log de inicialização de nuvem, consulte a [documentação de inicialização de nuvem](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos de inicialização em nuvem de alterações de configuração, consulte os seguintes documentos:
 
- [Adicionar um usuário Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização](cloudinit-update-vm.md)
- [Alterar nome de host local da VM](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicativos, atualizar arquivos de configuração e inserir chaves](tutorial-automate-vm-deployment.md)
 
