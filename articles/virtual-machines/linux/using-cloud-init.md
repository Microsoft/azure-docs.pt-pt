---
title: Visão geral do suporte de Cloud-init para máquinas virtuais Linux no Azure | Microsoft Docs
description: Visão geral dos recursos de inicialização de nuvem no Microsoft Azure
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
ms.date: 08/20/2019
ms.author: danis
ms.openlocfilehash: 7e22aaf2ead4dd618c2907f8659455e1862110a5
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650109"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Suporte de inicialização de nuvem para máquinas virtuais no Azure
Este artigo explica o suporte que existe para [Cloud-init](https://cloudinit.readthedocs.io) para configurar uma VM (máquina virtual) ou conjuntos de dimensionamento de máquinas virtuais no tempo de provisionamento no Azure. Esses scripts de Cloud-init são executados na primeira inicialização depois que os recursos são provisionados pelo Azure.  

## <a name="cloud-init-overview"></a>Descrição geral da inicialização de cloud
[Cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Porque o cloud-init é chamado durante o processo de inicialização inicial, não são passos adicionais ou agentes necessários a aplicar a configuração.  Para obter mais informações sobre como formatar corretamente sua `#cloud-config` arquivos, consulte a [site de documentação de cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` os arquivos são arquivos de texto codificados em base64.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

Estamos a trabalhar ativamente com os nossos parceiros de distribuição do Linux apoiados para ter imagens de cloud-init ativado disponíveis no Azure marketplace. Essas imagens farão com que suas implantações e configurações de Cloud-init funcionem de forma integrada com VMs e conjuntos de dimensionamento de máquinas virtuais. A tabela seguinte descreve a disponibilidade de imagens atual do cloud-init ativado na plataforma do Azure:

| Publicador | Oferta | SKU | Versão | preparado para o cloud-init |
|:--- |:--- |:--- |:--- |:--- |
|Canónico |UbuntuServer |18.04-LTS |latest |sim | 
|Canónico |UbuntuServer |16.04-LTS |mais recente |sim | 
|Canónico |UbuntuServer |14.04.5-LTS |mais recente |sim |
|CoreOS |CoreOS |Estável |mais recente |sim |
|OpenLogic 7,6 |CentOS |7-CI |mais recente |pré-visualização |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |sim |
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 |pré-visualização |
    
Atualmente Azure Stack não oferece suporte ao provisionamento do RHEL 7. x e do CentOS 7. x usando Cloud-init.

* Para o RHEL 7,6, pacote Cloud-init, o pacote com suporte é: *18.2-1. EL7 _ 6.2* 
* Para o RHEL 7,7 (versão prévia), o pacote Cloud-init, o pacote com suporte é: *18.5-3.el7*

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Qual é a diferença entre Cloud-init e o agente do Linux (WALA)?
WALA é um agente específico da plataforma do Azure usado para provisionar e configurar VMs e manipular extensões do Azure. Estamos aprimorando a tarefa de configurar VMs para usar Cloud-init em vez do agente do Linux para permitir que os clientes existentes de Cloud-init usem seus scripts de Cloud-init atuais.  Se você tiver investimentos existentes em scripts de Cloud-init para configurar sistemas Linux, não haverá **configurações adicionais necessárias** para habilitá-los. 

Se você não incluir a opção CLI do Azure `--custom-data` no momento do provisionamento, o wala usará os parâmetros mínimos de provisionamento de VM necessários para provisionar a VM e concluir a implantação com os padrões.  Se você fizer referência à opção Cloud `--custom-data` -init, tudo o que estiver contido em seus dados personalizados (configurações individuais ou script completo) substituirá os padrões wala. 

As configurações de WALA de VMs têm restrições de tempo para trabalhar dentro do tempo máximo de provisionamento da VM.  As configurações de Cloud-init aplicadas às VMs não têm restrições de tempo e não causarão a falha da implantação ao atingir o tempo limite. 

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

Quando a VM tiver sido criada, a CLI do Azure mostrará informações específicas de sua implantação. Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder à VM.  Leva algum tempo para que a VM seja criada, os pacotes a serem instalados e o aplicativo seja iniciado. Existem tarefas em segundo plano que continuam a ser executadas após a CLI do Azure devolver o utilizador à linha de comandos. Você pode fazer SSH na VM e usar as etapas descritas na seção de solução de problemas para exibir os logs de Cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Solucionando problemas de Cloud-init
Depois que a VM for provisionada, o Cloud-init será executado por todos os módulos e o script `--custom-data` definido em para configurar a VM.  Se precisar solucionar erros ou omissões da configuração, você precisará pesquisar o nome do módulo (`disk_setup` ou `runcmd` , por exemplo) no log Cloud-init localizado em **/var/log/Cloud-init.log**.

> [!NOTE]
> Nem toda falha de módulo resulta em uma falha fatal de configuração geral de Cloud-init. Por exemplo, usando o `runcmd` módulo, se o script falhar, a Cloud-init ainda relatará o provisionamento com êxito porque o módulo runcmd foi executado.

Para obter mais detalhes sobre o log de inicialização de nuvem, consulte a [documentação de inicialização de nuvem](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos de inicialização em nuvem de alterações de configuração, consulte os seguintes documentos:
 
- [Adicionar um usuário Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização](cloudinit-update-vm.md)
- [Alterar nome de host local da VM](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicativos, atualizar arquivos de configuração e inserir chaves](tutorial-automate-vm-deployment.md)
