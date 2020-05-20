---
title: Visão geral do suporte cloud-init para VMs Linux em Azure
description: Visão geral das capacidades de init em nuvem para configurar um VM no tempo de provisionamento em Azure.
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
ms.date: 05/19/2019
ms.author: danis
ms.openlocfilehash: 989e265c83cb2a71d93453b0c3bcbc3e41079447
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701592"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>suporte cloud-init para máquinas virtuais em Azure
Este artigo explica o suporte que existe para o [cloud-init](https://cloudinit.readthedocs.io) configurar uma máquina virtual (VM) ou conjuntos de escala de máquina virtual no tempo de provisionamento em Azure. Estas configurações de cloud-init são executadas na primeira bota uma vez que os recursos foram aprovisionados pelo Azure.  

VM Provisioning é o processo em que o Azure passará para baixo os valores do seu VM Create, tais como nome de anfitrião, nome de utilizador, senha etc., e disponibilizá-los ao VM à medida que ele arranca. Um "agente de provisionamento" consumirá esses valores, configurará o VM e apresentará um relatório quando concluído. 

O Azure apoia dois agentes de fornecimento [em nuvem,](https://cloudinit.readthedocs.io)e o [Agente Azure Linux (WALA)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>nuvem-init visão geral
[cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente usada para personalizar um VM Linux como ele botas pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Como o cloud-init é chamado durante o processo inicial de arranque, não existem passos adicionais ou agentes necessários para aplicar a sua configuração.  Para obter mais informações sobre como formatar corretamente os seus `#cloud-config` ficheiros ou outras inputs, consulte o [site de documentação init na nuvem](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`os ficheiros são ficheiros de texto codificados na base64.

cloud-init também funciona em todas as distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativos para o distro que selecionar.

Estamos a trabalhar ativamente com os nossos parceiros de distro linux endossados para ter imagens ativadas em nuvem disponíveis no mercado Azure. Estas imagens farão com que as suas implementações e configurações de cloud-init funcionem perfeitamente com VMs e conjuntos de escala de máquinas virtuais. Inicialmente colaboramos com os parceiros de distro linux endossados e a montante para garantir que as funções de inite na nuvem com o SISTEMA em Azure, então os pacotes são atualizados e disponibilizados publicamente nos repositórios de pacotes de distro. 

Existem duas fases para disponibilizar o init em nuvem para o endossado Linux distro OS's em Azure, suporte de pacote, e, em seguida, suporte de imagem:
* 'cloud-init package support on Azure' documentos que os pacotes de init em nuvem são suportados ou em pré-visualização, para que você possa usar estes pacotes com o S em uma imagem personalizada.
* Documentos 'cloud-init ready' se a imagem já estiver configurada para usar cloud-init.


### <a name="canonical"></a>Canónico
| Editor / Versão| Oferta | SKU | Versão | imagem cloud-init pronto | suporte de pacote cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canónica 20.04 |UbuntuServer |18.04-LTS |mais recente |sim | sim |
|Canónica 18.04 |UbuntuServer |18.04-LTS |mais recente |sim | sim |
|Canónica 16.04|UbuntuServer |16.04-LTS |mais recente |sim | sim |
|Canónica 14.04|UbuntuServer |14.04.5-LTS |mais recente |sim | sim |

### <a name="rhel"></a>RHEL
| Editor / Versão | Oferta | SKU | Versão | imagem cloud-init pronto | suporte de pacote cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |sim | sim - suporte da versão do pacote: *18.2-1.1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | Sim (note que esta é uma imagem de pré-visualização, e uma vez que todas as imagens RHEL 7.7 suportem cloud-init, esta será removida 1 de setembro de 2020) | sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-LVM | n/a| não - atualizações de imagem para completar o final de maio| sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7.7 | n/a| não - atualizações de imagem para completar o final de maio | sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 7.7 (Gen1) |rhel-byos | rhel-lvm77 | 7.7.2020051912 | não - atualizações de imagem em voo, até final de maio  | sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 7.7 (Gen2) |rhel-byos | rhel-lvm77 | 7.7.2020051913 |não - atualizações de imagem em voo, até final de maio  | sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 8.1 (Gen1) |RHEL |8.1-ci |8.1.2020042511 | Sim (note que esta é uma imagem de pré-visualização, e uma vez que todas as imagens RHEL 8.1 suportem cloud-init, esta será removida a 1 de agosto de 2020) | Não, ETA para apoio total junho de 2020|
|RedHat 8.1 (Gen2) |RHEL |81-ci-gen2 |8.1.2020042524 | Sim (note que esta é uma imagem de pré-visualização, e uma vez que todas as imagens RHEL 8.1 suportem cloud-init, esta será removida a 1 de agosto de 2020) | Não, ETA para apoio total junho de 2020 |

As imagens RedHat:RHEL 7.8 e 8.2 (Gen1 e Gen2) são disponibilizadas usando cloud-init.

### <a name="centos"></a>CentOS

| Editor / Versão | Oferta | SKU | Versão | imagem cloud-init pronto | suporte de pacote cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |Sim (note que esta é uma imagem de pré-visualização, e uma vez que todas as imagens CentOS 7.7 suportem cloud-init, esta será removida 1 de setembro de 2020) | sim - suporte da versão do pacote: *18.5-3.el7.centos*|

* Imagens CentOS 7.7 que serão ativadas em nuvem serão atualizadas aqui em junho de 2020 
* As imagens CentOS 7.8 são aprovisionadas usando cloud-init.


### <a name="oracle"></a>Oracle

| Editor / Versão | Oferta | SKU | Versão | imagem cloud-init pronto | suporte de pacote cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oráculo 7.7 |Oráculo-Linux |77-ci |7.7.01| imagem de pré-visualização (note que esta é uma imagem de pré-visualização, e uma vez que todas as imagens Oracle 7.7 suportem cloud-init, esta será removida em meados de 2020, será dada a notificação) | não, na pré-visualização, o pacote é: *18.5-3.0.1.el7*

### <a name="suse-sles"></a>SuSE SLES
| Editor / Versão | Oferta | SKU | Versão | imagem cloud-init pronto | suporte de pacote cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|SUSE SLES 15 SP1 |susa |sles-15-sp1-básico |nuvem-init-pré-visualização| Consulte o [blog SUSE cloud-init](https://suse.com/c/clout-init-coming-to-suse-images-in-azure/) para mais detalhes | Não, na pré-estreia. |
|SUSE SLES 15 SP1 |susa |sles-15-sp1-básico |gen2-cloud-init-preview| Consulte o [blog SUSE cloud-init](https://suse.com/c/clout-init-coming-to-suse-images-in-azure/) para mais detalhes | Não, na pré-estreia. |


### <a name="debian"></a>Debian
Estamos neste momento a trabalhar para pré-visualizar o suporte, esperamos atualizações em junho de 2020.

Atualmente o Azure Stack irá apoiar o fornecimento de imagens ativadas em nuvem.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Qual é a diferença entre cloud-init e o Linux Agent (WALA)?
Wala é um agente específico da plataforma Azure usado para fornecer e configurar VMs, e lidar com [extensões Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux). 

Estamos a reforçar a tarefa de configurar vMs para usar cloud-init em vez do Agente Linux, de modo a permitir que os clientes existentes em cloud-init utilizem os seus scripts de cloud-init atuais, ou novos clientes para aproveitar a rica funcionalidade de configuração cloud-init. Se tiver investimentos existentes em scripts de cloud-init para configurar sistemas Linux, **não existem configurações adicionais necessárias** para permitir o seu processo de init em nuvem. 

Cloud-init não pode processar extensões Azure, por isso wala ainda é necessário na imagem para processar extensões, mas terá que ter o seu código de fornecimento desativado, para imagens de distros linux endossadas que estão sendo convertidas para fornecer por cloud-init, eles terão WALA instalado, e configuração corretamente.

Ao criar um VM, se não incluir o interruptor Azure CLI no tempo de `--custom-data` provisionamento, o cloud-init ou o WALA retira os parâmetros mínimos de fornecimento de VM necessários para fornecer o VM e completar a implementação com os defeitos.  Se consultar a configuração cloud-init com o `--custom-data` interruptor, o que estiver contido nos seus dados personalizados estará disponível para init em nuvem quando as botas VM.

As configurações de cloud-init aplicadas aos VMs não têm restrições de tempo e não causarão uma falha de implantação por tempo limite. Isto não é verdade para a WALA, se alterar os incumprimentos do WALA para processar dados personalizados, não pode exceder o subsídio total de tempo de fornecimento de VM de 40mins, se assim for, o VM Create falhará.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Implantação de uma máquina virtual ativada em nuvem
A implantação de uma máquina virtual ativada por cloud-init é tão simples como fazer referência a uma distribuição ativada por cloud-in durante a implantação.  Os mantendo-se de distribuição Linux têm de optar por permitir e integrar as imagens publicadas pela Cloud-init na sua base Azure. Uma vez confirmado que a imagem que pretende implementar está ativada em nuvem, pode utilizar o Azure CLI para implementar a imagem. 

O primeiro passo para a implementação desta imagem é criar um grupo de recursos com o [grupo AZ criar](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
O próximo passo é criar um ficheiro na sua concha atual, chamado *cloud-init.txt* e colar a seguinte configuração. Para este exemplo, crie o ficheiro na Cloud Shell e não na sua máquina local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud-init.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o **editor nano.** Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Prima `ctrl-X` para sair do ficheiro, escreva para guardar o ficheiro e prima para confirmar o nome do ficheiro na `y` `enter` saída.

O passo final é criar um VM com o [az vm criar](/cli/azure/vm) comando. 

O exemplo seguinte cria um VM chamado *centos74* e cria chaves SSH se ainda não existirem num local chave predefinido. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  Utilize o parâmetro `--custom-data` para passar o ficheiro de configuração de inicialização da cloud. Forneça o caminho completo para a configuração do *cloud-init.txt*, se tiver guardado o ficheiro fora do diretório de trabalho atual. O exemplo seguinte cria um VM chamado *centos74:*

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Quando o VM foi criado, o Azure CLI mostra informações específicas à sua implementação. Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder à VM.  Leva algum tempo para o VM ser criado, os pacotes para instalar e a app para começar. Existem tarefas em segundo plano que continuam em execução após a CLI do Azure o devolver à linha de comandos. Pode entrar no SSH no VM e utilizar os passos descritos na secção de resolução de problemas para visualizar os registos de inite em nuvem. 

## <a name="troubleshooting-cloud-init"></a>Resolução de problemas nuvem-init
Uma vez que o VM tenha sido provisionado, o cloud-init passará por todos os módulos e scriptdefinidos `--custom-data` para configurar o VM.  Se necessitar de resolver erros ou omissões da configuração, tem de procurar o nome do módulo `disk_setup` (ou, por exemplo) no registo de `runcmd` cloud-init - localizado em **/var/log/cloud-init.log**.

> [!NOTE]
> Nem todas as falhas do módulo resultam numa falha de configuração geral fatal. Por exemplo, utilizando o `runcmd` módulo, se o script falhar, o cloud-init continuará a reportar que o fornecimento foi bem sucedido porque o módulo runcmd executado.

Para mais detalhes sobre a exploração madeireira cloud-init, consulte a [documentação cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Passos seguintes
Para exemplos de alterações de configuração, consulte os seguintes documentos:
 
- [Adicione um utilizador linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicação, atualize ficheiros de configuração e injete chaves](tutorial-automate-vm-deployment.md)
 
