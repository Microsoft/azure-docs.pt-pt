---
title: Descrição geral do suporte de cloud-init para máquinas virtuais do Linux no Azure | Documentos da Microsoft
description: Descrição geral das capacidades de cloud-init no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 6dd1dd0ce2395e2b06d80385ffd299835a280526
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002027"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Suporte de cloud-init para máquinas virtuais no Azure
Este artigo explica o suporte de que existe para [cloud-init](https://cloudinit.readthedocs.io) para configurar uma máquina virtual ou máquina virtual (VM) de conjuntos de dimensionamento (VMSS) em aprovisionamento tempo no Azure. Executam estes scripts de inicialização da cloud no primeiro arranque, assim que os recursos foram aprovisionados através do Azure.  

## <a name="cloud-init-overview"></a>Descrição geral da inicialização de cloud
[Cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Porque o cloud-init é chamado durante o processo de inicialização inicial, não são passos adicionais ou agentes necessários a aplicar a configuração.  Para obter mais informações sobre como formatar corretamente sua `#cloud-config` arquivos, consulte a [site de documentação de cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` os arquivos são arquivos de texto codificados em base64.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

 Estamos a trabalhar ativamente com os nossos parceiros de distribuição do Linux apoiados para ter imagens de cloud-init ativado disponíveis no Azure marketplace. Estas imagens fará com que as implementações de cloud-init e configurações funcionam sem problemas com VMs e conjuntos de dimensionamento de VM (VMSS). A tabela seguinte descreve a disponibilidade de imagens atual do cloud-init ativado na plataforma do Azure:

| Publicador | Oferta | SKU | Versão | preparado para o cloud-init |
|:--- |:--- |:--- |:--- |:--- |
|Canónico |UbuntuServer |18.04-LTS |mais recente |sim | 
|Canónico |UbuntuServer |17.10 |mais recente |sim | 
|Canónico |UbuntuServer |16.04-LTS |mais recente |sim | 
|Canónico |UbuntuServer |14.04.5-LTS |mais recente |sim |
|CoreOS |CoreOS |Estável |mais recente |sim |
|OpenLogic |CentOS |7-CI |mais recente |pré-visualização |
|RedHat |RHEL |7-RAW-CI |mais recente |pré-visualização |

Atualmente o Azure Stack não suporta o aprovisionamento do RHEL 7.4 e no CentOS 7.4 com o cloud-init.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>O que é a diferença entre o cloud-init e o agente do Linux (WALA)?
WALA é um agente específicos da plataforma do Azure utilizado para aprovisionar, configurar as VMs e lidar com extensões do Azure. Estamos a melhorar a tarefa de configuração de VMs para utilizar o cloud-init em vez do agente do Linux para permitir que os clientes existentes do cloud-init usar seus scripts de inicialização da cloud atual.  Se tiver investimentos existentes em scripts de inicialização da cloud para configurar os sistemas Linux, existem **sem configurações adicionais necessárias** para ativá-las. 

Se não incluir a CLI do Azure `--custom-data` comutador no provisionamento de tempo, WALA usa a parâmetros necessários para aprovisionar a VM e concluir a implementação com os padrões de aprovisionamento da VM mínima.  Se fizer referência a cloud-init `--custom-data` mude, tudo o que está contido nos seus dados personalizados (definições individuais ou script completo) substitui as predefinições WALA. 

As configurações WALA VMs estão a restrição de tempo para trabalhar dentro da VM máxima do provisionamento de tempo.  Configurações de cloud-init aplicadas às VMs não têm restrições de tempo e não irão causar uma falha ao tempo limite da implementação. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Implementar uma cloud-init ativado a Máquina Virtual
Implementar uma máquina virtual do cloud-init ativado é tão simples quanto fazer referência uma distribuição de cloud-init ativado durante a implementação.  Maintainers de distribuição de Linux têm de optar por ativar e integrar o cloud-init em suas imagens de publicados bases do Azure. Assim que tiver confirmado que a imagem que pretende implementar é o cloud-init ativado, pode utilizar a CLI do Azure para implementar a imagem. 

O primeiro passo para implementar esta imagem é criar um grupo de recursos com o [criar grupo az](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
A próxima etapa é criar um ficheiro na sua shell atual, com o nome *cloud-Init* e cole a seguinte configuração. Neste exemplo, crie o ficheiro no Cloud Shell, não no seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud-init.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolher #1 para utilizar o **nano** editor. Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Prima `ctrl-X` para o ficheiro de saída, escreva `y` para guardar o ficheiro e prima `enter` para confirmar o nome de ficheiro à saída.

A etapa final é criar uma VM com o [az vm criar](/cli/azure/vm) comando. 

O exemplo seguinte cria uma VM com o nome *centos74* e cria chaves SSH caso estas ainda não existam numa localização chave predefinida. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  Utilize o parâmetro `--custom-data` para passar o ficheiro de configuração de inicialização da cloud. Forneça o caminho completo para a configuração do *cloud-init.txt*, se tiver guardado o ficheiro fora do diretório de trabalho atual. O exemplo seguinte cria uma VM com o nome *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Quando a VM tiver sido criada, a CLI do Azure mostra informações específicas da sua implementação. Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder à VM.  Demora algum tempo para a VM ser criada, os pacotes serem instalados e a aplicação ser iniciada. Existem tarefas em segundo plano que continuam a ser executadas após a CLI do Azure devolver o utilizador à linha de comandos. Pode encaminhar o SSH na VM e utilize os passos descritos na secção de resolução de problemas para ver os registos do cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Resolução de problemas do cloud-init
Assim que a VM tiver sido aprovisionada, cloud-init será executado por meio de todos os módulos e script definido no `--custom-data` para configurar a VM.  Se precisar de resolver quaisquer erros ou omissões da configuração, tem de procurar o nome do módulo (`disk_setup` ou `runcmd` por exemplo) no registo do cloud-init - localizado na **/var/log/cloud-init.log**.

> [!NOTE]
> Nem toda falha do módulo resulta numa cloud-init fatal falha de configuração geral. Por exemplo, utilizando o `runcmd` módulo, se o script falhar, cloud-init ainda irá relatar aprovisionamento concluído com êxito uma vez que o módulo de runcmd executado.

Para obter mais detalhes de registo do cloud-init, consulte o [documentação de cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Passos Seguintes
Para obter exemplos de alterações de configuração cloud-init, veja os documentos seguintes:
 
- [Adicionar um utilizador de Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gestor de pacotes para atualizar os pacotes existentes no primeiro arranque](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião VM local](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicação, atualizar ficheiros de configuração e injetar chaves](tutorial-automate-vm-deployment.md)
