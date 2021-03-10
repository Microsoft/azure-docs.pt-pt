---
title: Resolução de problemas usando cloud-init
description: Resolução de problemas que atravesse um VM Azure usando a cloud-init.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6c5922137b5d3ee14461adb88fba2e8b2cf41e16
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558972"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>Resolução de problemas de fornecimento de VM com cloud-init

Se tem vindo a criar imagens personalizadas generalizadas, utilizando o cloud-init para fazer o provisionamento, mas descobriu que o VM não criou corretamente, terá de resolver problemas com as suas imagens personalizadas.

Alguns exemplos, de questões relacionadas com o provisionamento:
- VM fica preso em 'criar' por 40 minutos, e a criação de VM é marcada como falhada
- `CustomData` não é processado
- O disco efémero não monta
- Os utilizadores não são criados, ou existem problemas de acesso ao utilizador
- A rede não está configurada corretamente
- Swap de ficheiros ou falhas de partição

Este artigo percorre-o como resolver problemas. Para obter mais detalhes aprofundados, consulte [o mergulho profundo de nuvem.](./cloud-init-deep-dive.md)

## <a name="step-1-test-the-deployment-without-customdata"></a>Passo 1: Testar a implantação sem `customData`

A cloud-init pode aceitar `customData` , que é passada para ele, quando o VM é criado. Em primeiro lugar, deve certificar-se de que isto não está a causar problemas com as implementações. Tente a provisionar o VM sem passar em qualquer configuração. Se encontrar que o VM não fornece, continue com os passos abaixo, se encontrar a configuração que está a passar não está a ser aplicada, passo [4](). 

## <a name="step-2-review-image-requirements"></a>Passo 2: Rever os requisitos de imagem
A principal causa da falha de provisão de VM é que a imagem de SO não satisfaz os pré-requisitos para correr em Azure. Certifique-se de que as suas imagens estão devidamente preparadas antes de tentar foreá-las em Azure. 


Os seguintes artigos ilustram os passos para preparar várias distribuições de linux que são suportadas em Azure:

- [Distribuições baseadas em CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES e openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [Outros: Distribuições Não Endossadas](create-upload-generic.md)

Para as [imagens suportadas em nuvem Azure,](./using-cloud-init.md)as distribuições do Linux já dispõem de todos os pacotes e configurações necessários para que a imagem seja corretamente disponibilizada em Azure. Se descobrir que o seu VM não está a conseguir criar a partir da sua própria imagem curada, experimente uma imagem suportada do Azure Marketplace que já está configurada para a cloud-init, com a sua `customData` opcional. Se o `customData` funciona corretamente com uma imagem do Azure Marketplace, então provavelmente há um problema com a sua imagem curada.

## <a name="step-3-collect--review-vm-logs"></a>Passo 3: Recolher & rever registos VM

Quando o VM não for o fornecimento, o Azure mostrará o estado de "criação", durante 20 minutos, e depois reiniciará o VM, e esperará mais 20 minutos antes de finalmente marcar a implementação do VM como falhado, antes de finalmente marcá-lo com um `OSProvisioningTimedOut` erro.

Enquanto o VM está em funcionamento, você precisará dos registos do VM para entender por que o provisionamento falhou.  Para entender por que o fornecimento de VM falhou, não pare o VM. Mantenha o VM a funcionar. Terá de manter o VM falhado num estado de funcionamento para recolher registos. Para recolher os registos, utilize um dos seguintes métodos:

- [Consola de Série](../troubleshooting/serial-console-grub-single-user-mode.md)

- [Ativar o Boot Diagnostics](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#enable-boot-diagnostics) antes de criar o VM e depois [vê-los](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#view-boot-diagnostics) durante a bota.

- [Executar A AZ VM Reparação](../troubleshooting/repair-linux-vm-using-azure-virtual-machine-repair-commands.md) para anexar e montar o disco OS, que lhe permitirá recolher estes registos:
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
Para iniciar a resolução inicial de problemas, comece com os registos de insípição de nuvens e compreenda onde ocorreu a falha, em seguida, use os outros troncos para mergulhar profundamente, e fornecer insights adicionais. 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* Registos de série/arranque

Em todos os registos, comece a procurar "Falhado", "AVISO", "WARN", "err", "erro", "ERROR". Recomenda-se a definição de configuração para ignorar pesquisas sensíveis a casos. 

> [!TIP]
> Se estiver a resolver problemas com uma imagem personalizada, deve considerar adicionar um utilizador durante a imagem. Se o provisionamento não definir o utilizador administrativo, ainda pode iniciar sessão no SISTEMA.

## <a name="analyzing-the-logs"></a>Analisar os registos

Aqui estão mais detalhes sobre o que procurar em cada registo de cloud-init.

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

Por predefinição, todos os eventos de init em nuvem com prioridade de depuração ou superior, são escritos para `/var/log/cloud-init.log` . Isto fornece registos verbosos de todos os eventos que ocorreram durante a inicialização de cloud-init. 

Por exemplo:

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


Depois de ter encontrado um erro ou aviso, leia para trás no registo de init nuvem para perceber o que estava a tentar fazer antes de atingir o erro ou o aviso. Em muitos casos, o cloud-init terá executado comandos de SO ou realizado operações de provisionamento antes do erro, o que pode fornecer informações sobre o porquê de erros aparecerem nos registos. O exemplo que se segue mostra que a insição de nuvem tentou montar um dispositivo mesmo antes de atingir um erro.

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

Se tiver acesso à [Consola em Série,](../troubleshooting/serial-console-grub-single-user-mode.md)pode tentar refazer o comando que a cloud-in estava a tentar executar.

O registo `/var/log/cloud-init.log` também pode ser reconfigurado dentro de /etc/cloud/cloud.cfg.d/05_logging.cfg. Para obter mais detalhes sobre o registo de informação em nuvem, consulte a [documentação de insição de nuvem](https://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

Pode obter informações do `stdout` e `stderr` durante as [fases de cloud-init](cloud-init-deep-dive.md). Isto normalmente envolve a informação de encaminhamento de tabelas, informações de networking, informações de verificação chave do anfitrião ssh, `stdout` e para cada fase de `stderr` cloud-init, juntamente com a etiqueta de tempo para cada fase. Se desejar, `stderr` e o registo pode ser `stdout` reconfigurado a partir de `/etc/cloud/cloud.cfg.d/05_logging.cfg` .

### <a name="serialboot-logs"></a>Registos de série/arranque 

A cloud-init tem múltiplas dependências, estas são documentadas em pré-requisitos necessários para imagens em Azure, tais como networking, armazenamento, capacidade de montar um ISO, e montar e formatar o disco temporário. Qualquer um destes pode lançar erros e fazer com que a nuvem falhe. Por exemplo, se o VM não conseguir obter um contrato de arrendamento DHCP, o cloud-init falhará.

Se ainda não consegue isolar o porquê de a nuvem não ter conseguido a provisão, então precisa de compreender que fases de nuvem e quando os módulos funcionam. Consulte [mergulhar mais profundamente na nuvem para obter](cloud-init-deep-dive.md) mais detalhes.


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>Passo 4: Investigar por que a configuração não está a ser aplicada
Nem todas as falhas no nebulosidade resulta numa falha fatal no fornecimento. Por exemplo, se estiver a utilizar o `runcmd` módulo num config de nuvem, um código de saída não zero do comando que está a executar fará com que o fornecimento de VM falhe. Isto porque corre atrás da funcionalidade de provisão central que acontece nas primeiras 3 fases da cloud-init. Para resolver os problemas por que a configuração não se aplica, reveja os registos no Passo 3 e os módulos de init em nuvem manualmente. Por exemplo:

- `runcmd` - os scripts funcionam sem erros? Executar a configuração manualmente a partir do terminal para garantir que funcionam como esperado.
- Instalação de pacotes - o VM tem acesso a repositórios de pacotes?
- Deve também verificar a configuração de `customData` dados que foi fornecida ao VM, isto está localizado em `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` .


## <a name="next-steps"></a>Passos seguintes

Se ainda não consegue isolar o porquê de a cloud-init não ter executado a configuração, precisa de olhar mais de perto para o que acontece em cada fase de cloud-init e quando os módulos são executados. Consulte [mergulhar mais profundamente na configuração do cloud-init](./cloud-init-deep-dive.md) para obter mais informações.