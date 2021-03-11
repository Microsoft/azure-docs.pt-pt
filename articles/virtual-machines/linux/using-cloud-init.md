---
title: Visão geral do suporte de cloud-init para Os VMs Linux em Azure
description: Visão geral das capacidades de cloud-init para configurar um VM no tempo de provisionamento em Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/14/2021
ms.author: danis
ms.openlocfilehash: ac907c2ea2ae53bd192c01232c66e0467025daae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563239"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>suporte cloud-init para máquinas virtuais em Azure
Este artigo explica o suporte que existe para [configurar](https://cloudinit.readthedocs.io) uma máquina virtual (VM) ou uma escala de máquina virtual em tempo de provisionamento em Azure. Estas configurações de cloud-init são executadas na primeira bota uma vez que os recursos foram a provisionados pela Azure.  

O Fornecimento de VM é o processo em que o Azure irá passar os seus valores de parâmetro VM Create, tais como nome de hospedeiro, nome de utilizador, palavra-passe, etc., e disponibilizá-los para o VM à medida que arranca. Um "agente de provisionamento" consumirá esses valores, configurará o VM e informará quando concluído. 

O Azure suporta dois agentes de provisionamento [em nuvem,](https://cloudinit.readthedocs.io)e o [Agente Azure Linux (WALA)](../extensions/agent-linux.md).

## <a name="cloud-init-overview"></a>visão geral de nuvem init
[cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente usada para personalizar um Linux VM como botas pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Como a cloud-init é chamada durante o processo inicial de arranque, não existem passos adicionais ou agentes necessários para aplicar a sua configuração.  Para obter mais informações sobre como formatar corretamente os seus `#cloud-config` ficheiros ou outras entradas, consulte o [site de documentação in informática em nuvem](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` os ficheiros são ficheiros de texto codificados na base64.

cloud-init também funciona através de distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. cloud-init usa automaticamente a ferramenta de gestão de pacotes nativo para o distro que seleciona.

Estamos a trabalhar ativamente com os nossos parceiros de distro Linux endossados, de forma a ter imagens ativadas em nuvem disponíveis no Azure Marketplace. Estas imagens farão com que as suas implementações e configurações de ineção em nuvem funcionem perfeitamente com VMs e conjuntos de escala de máquinas virtuais. Inicialmente colaboramos com os parceiros de distro linux endossados para garantir funções de cloud-init com o SO on Azure, então os pacotes são atualizados e disponibilizados publicamente nos repositórios de pacotes distro. 

Existem duas fases para disponibilizar cloud-init para o endossado Linux distro OS's em Azure, suporte ao pacote e, em seguida, suporte de imagem:
* 'cloud-init package support on Azure' documentos que pacotes cloud-init são suportados ou em pré-visualização, para que você possa usar estes pacotes com o SO em uma imagem personalizada.
* Documentos 'imagem cloud-init ready' se a imagem já estiver configurada para usar cloud-init.


### <a name="canonical"></a>Canónico
| Editor / Versão| Oferta | SKU | Versão | imagem nuvem-init pronto | suporte pacote de cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canônico 20.04 |UbuntuServer |18.04-LTS |mais recente |sim | sim |
|Canônico 18.04 |UbuntuServer |18.04-LTS |mais recente |sim | sim |
|Canônico 16.04|UbuntuServer |16.04-LTS |mais recente |sim | sim |
|Canônico 14.04|UbuntuServer |14.04.5-LTS |mais recente |sim | sim |

### <a name="rhel"></a>RHEL
| Editor / Versão | Oferta | SKU | Versão | imagem nuvem-init pronto | suporte pacote de cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |sim | sim - suporte da versão do pacote: *18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | sim (nota: esta é uma imagem de pré-visualização, e não **deve** ser usada mais, esta será removida a 1 de setembro de 2020) | N/D |
|RedHat 7.7 (Gen1)|RHEL |7.7 | 7.7.2020051912 | sim | sim - suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 77-gen2 | 7.7.2020051913 | sim | sim - suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen1)|RHEL |7-LVM | 7.7.2020051921 | sim | sim - suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 7lvm-gen2 | 7.7.2020051922  | sim | sim - suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen1) |rhel-byos | rhel-lvm77 | 7.7.20200416 | sim  | sim - suporte da versão do pacote: *18.5-6.el7*|
|RedHat 8.1 (Gen1) |RHEL |8.1-ci |8.1.2020042511 | sim (nota: esta é uma imagem de pré-visualização, e uma vez que todas as imagens RHEL 8.1 suportam cloud-init, esta será removida a 1 de agosto de 2020) | Não, ETA para apoio total junho de 2020|
|RedHat 8.1 (Gen2) |RHEL |81-ci-gen2 |8.1.2020042524 | sim (nota: esta é uma imagem de pré-visualização, e uma vez que todas as imagens RHEL 8.1 suportam cloud-init, esta será removida a 1 de agosto de 2020) | Não, ETA para apoio total junho de 2020 |

* Todas as imagens RedHat:RHEL 7.8 e 8.2 (Gen1 e Gen2) são avisadas usando a nuvem.

### <a name="centos"></a>CentOS

| Editor / Versão | Oferta | SKU | Versão | imagem nuvem-init pronto | suporte pacote de cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |sim (nota: esta é uma imagem de pré-visualização, e não **deve** ser usada mais, esta será removida a 1 de setembro de 2020) | N/D |
|OpenLogic 7.7 |CentOS | 7.7 |7.7.2020062400 |sim | sim - suporte da versão do pacote: `18.5-6.el7.centos.5`|
|OpenLogic 7.7 (Gen2) |CentOS | 7_7-gen2 |7.7.2020062401 |sim | sim - suporte da versão do pacote: `18.5-6.el7.centos.5`|
|OpenLogic 7.7 |CentOS-HPC | 7.7 |7.6.2020062600 |sim | sim - suporte da versão do pacote: `18.5-6.el7.centos.5`|
|OpenLogic 7.7 (Gen2) |CentOS-HPC | 7_7-gen2 |7.6.2020062601 |sim | sim - suporte da versão do pacote: `18.5-6.el7.centos.5`|
|OpenLogic 8.1 |CentOS | 8_1 |8.1.2020062400 |sim | sim - suporte da versão do pacote: `18.5-7.el8_1.1`|
|OpenLogic 8.1 (Gen2) |CentOS | 8_1-gen2 |8.1.2020062401 |sim | sim - suporte da versão do pacote: `18.5-7.el8_1.1`|
|OpenLogic 8.1 |CentOS-HPC | 8_1 |8.1.2020062400 |sim | sim - suporte da versão do pacote: `18.5-7.el8_1.1`|
|OpenLogic 8.1 (Gen2) |CentOS-HPC:8_1-gen2 | 8_1-gen2 |8.1.2020062401 |sim | sim - suporte da versão do pacote: `18.5-7.el8_1.1`|

* Todas as imagens OpenLogic:CentOS 7.8 e 8.2 (Gen1 e Gen2) são ateadas com recurso a cloud-init.

### <a name="oracle"></a>Oracle

| Editor / Versão | Oferta | SKU | Versão | imagem nuvem-init pronto | suporte pacote de cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oráculo 7.7 |Oracle-Linux |77-ci |7.7.01| imagem de pré-visualização (nota: esta é uma imagem de pré-visualização, e uma vez que todas as imagens Oracle 7.7 suportam cloud-init, esta será removida em meados de 2020, aviso será dado) | não, na pré-visualização, o pacote é: *18.5-3.0.1.el7*

### <a name="suse-sles"></a>SUSE SLES
Estas imagens SLES foram atualizadas para a oferta usando o cloud-init, as variantes de imagem Gen2 também foram atualizadas.
* suse:sles-15-sp1-{basic/byos/hpc/hpc-byos/chost-byos}:gen1:2020.06.10
* suse:sles-sap-15-sp1:gen1:2020.06.10
* suse:sles-sap-15-sp1-byos:gen1:2020.06.10
* suse:manager-proxy-4-byos:gen1:2020.06.10
* suse:manager-server-4-byos:gen1:2020.06.10
* suse:sles-{byos/sap/sap-byos}:15:2020.06.10
* suse:sles-12-sp5:gen1:2020.06.10
* suse:sles-12-sp5{-byos/basic/hpc-byos/hpc}:gen1:2020.06.10
* suse:sles-{byos/sap/sap-byos}:12-sp4:2020.06.10
* suse:sles-{byos/sap/sap-byos}:12-sp3:2020.06.10
* suse:sles-{byos/sap/sap-byos}:12-sp2:2020.06.10


### <a name="debian"></a>Debian
| Editor / Versão | Oferta | SKU | Versão | imagem nuvem-init pronto | suporte pacote de cloud-init em Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
| debian (Gen1) |debian-10 | 10-cloudinit |cloud-init-preview| sim (nota: esta é uma imagem de pré-visualização, e não **deve** ser usada mais, esta será removida a 1 de janeiro de 2021) | Não, na pré-estreia. |
| debian (Gen2) |debian-10 | 10-cloudinit-gen2 |cloud-init-preview| sim (nota: esta é uma imagem de pré-visualização, e não **deve** ser usada mais, esta será removida a 1 de janeiro de 2021) | Não, na pré-estreia. |
| debian (Gen1) |debian-10 | 10-cloudinit |10:0.20201013.422| sim | sim - suporte da versão do pacote: `20.2-2~deb10u1` |
| debian (Gen2) |debian-10 | 10-cloudinit-gen2 |0.20201013.422| sim | sim - suporte da versão do pacote: `20.2-2~deb10u1` |


Atualmente, o Azure Stack irá suportar o fornecimento de imagens ativadas por cloud-in.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Qual é a diferença entre o init de nuvem e o Agente Linux (WALA)?
WALA é um agente específico da plataforma Azure usado para fornecer e configurar VMs, e lidar com [extensões Azure](../extensions/features-linux.md). 

Estamos a melhorar a tarefa de configurar VMs para usar o cloud-init em vez do Agente Linux, de modo a permitir que os clientes existentes em nuvem utilizem os seus scripts atuais em nuvem, ou novos clientes para aproveitar a rica funcionalidade de configuração de cloud-init. Se tiver investimentos existentes em scripts cloud-init para configurar sistemas Linux, **não existem configurações adicionais necessárias** para permitir o processo de cloud-init. 

o cloud-init não pode processar extensões de Azure, pelo que a WALA ainda é necessária na imagem para processar extensões, mas terá de ter o seu código de provisionamento desativado, para imagens de distros do Linux endossadas que estão a ser convertidas para provisão por cloud-init, eles terão WALA instalado e configurado corretamente.

Ao criar um VM, se não incluir o interruptor Azure CLI `--custom-data` no tempo de fornecimento, o cloud-init ou o WALA requerem os parâmetros mínimos de provisão VM necessários para a provisionação do VM e completar a implementação com os predefinidos.  Se referenciar a configuração de inimento em nuvem com o `--custom-data` comutador, o que estiver contido nos seus dados personalizados estará disponível para o cloud-init quando as botas VM.

as configurações de cloud-init aplicadas aos VMs não têm restrições de tempo e não farão com que uma implementação falhe ao cronometrar. Isto não é verdade para a WALA, se alterar os incumprimentos da WALA para processar dados personalizados, não pode exceder o limite total de tempo de fornecimento de VM de 40mins, se assim for, o VM Create falhará.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Implantação de uma máquina virtual ativada em nuvem
Implementar uma máquina virtual ativada por nuvem é tão simples como fazer referência a uma distribuição ativada por cloud-in durante a implementação.  Os mantedores de distribuição Linux têm de escolher ativar e integrar a init na sua base Azure imagens publicadas. Uma vez confirmada a imagem que pretende implementar está ativada na cloud- init, pode utilizar o CLI Azure para implementar a imagem. 

O primeiro passo para a implementação desta imagem é criar um grupo de recursos com o [grupo az criar](/cli/azure/group) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

O próximo passo é criar um ficheiro na sua concha atual, nomeado *cloud-init.txt* e colar a seguinte configuração. Para este exemplo, crie o ficheiro na Cloud Shell e não na sua máquina local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud-init.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Escolha #1 para usar o **nano** editor. Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
> [!NOTE]
> O cloud-init tem vários tipos de [entrada,](https://cloudinit.readthedocs.io/en/latest/topics/format.html)o cloud-init utilizará a primeira linha do customData/userData para indicar como deve processar a entrada, por exemplo `#cloud-config` indica que o conteúdo deve ser processado como um config de cloud-init.


Prima `ctrl-X` para sair do ficheiro, escreva para guardar o ficheiro e prima para confirmar o nome do ficheiro na `y` `enter` saída.

O passo final é criar um VM com o [comando az vm criar.](/cli/azure/vm) 

O exemplo a seguir cria um VM chamado *centos74* e cria chaves SSH se elas já não existirem num local de chave predefinido. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  Utilize o parâmetro `--custom-data` para passar o ficheiro de configuração de inicialização da cloud. Forneça o caminho completo para a configuração do *cloud-init.txt*, se tiver guardado o ficheiro fora do diretório de trabalho atual. 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Quando o VM foi criado, o Azure CLI mostra informações específicas para a sua implantação. Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder à VM.  Demora algum tempo para que o VM seja criado, os pacotes para instalar e a aplicação para começar. Existem tarefas em segundo plano que continuam em execução após a CLI do Azure o devolver à linha de comandos. Pode entrar no SSH no VM e utilizar os passos descritos na secção de resolução de problemas para visualizar os registos de inibição de nuvens. 

Também pode implementar um VM ativado por nuvem, passando os [parâmetros no modelo ARM](../../azure-resource-manager/templates/deploy-cli.md#inline-parameters).

## <a name="troubleshooting-cloud-init"></a>Resolução de problemas na nuvem
Uma vez que o VM tenha sido a provisionado, o cloud-init irá percorrer todos os módulos e scripts definidos `--custom-data` de forma a configurar o VM.  Se precisar de resolver quaisquer erros ou omissões da configuração, tem de procurar o nome do módulo `disk_setup` `runcmd` (ou, por exemplo) no registo de inição de nuvem - localizado em **/var/log/cloud-init.log**.

> [!NOTE]
> Nem todas as falhas do módulo resultam numa falha de configuração geral fatal. Por exemplo, utilizando o `runcmd` módulo, se o script falhar, o cloud-init continuará a reportar que o provisionamento foi bem sucedido porque o módulo desatado foi executado.

Para obter mais detalhes sobre a exploração madeireira em nuvem, consulte a [documentação cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Passos seguintes

[Problemas de resolução de problemas com a nuvem init](cloud-init-troubleshooting.md).


Para exemplos de alterações de configuração em nuvem, consulte os seguintes documentos:
 
- [Adicione um utilizador Linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicações, atualize ficheiros de configuração e injete teclas](tutorial-automate-vm-deployment.md)
