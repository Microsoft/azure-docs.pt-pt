---
title: Upgrade no lugar de red hat enterprise linux imagens em Azure
description: Saiba como fazer um upgrade no local a partir das imagens Red Hat Enterprise 7.x para a versão mais recente 8.x.
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 1be0904cc640eff5af7a77bba3abd6aa062991a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676063"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Atualizações da Red Hat Enterprise Linux no local

Este artigo fornece instruções sobre como fazer uma atualização no local da Red Hat Enterprise Linux (RHEL) 7 para Red Hat Enterprise Linux 8. As instruções utilizam a `leapp` ferramenta em Azure. Durante a atualização in-place, o sistema operativo RHEL 7 existente é substituído pela versão RHEL 8.

>[!Note] 
> As ofertas de SQL Server na Red Hat Enterprise Linux não suportam upgrades no Azure.

## <a name="what-to-expect-during-the-upgrade"></a>O que esperar durante a atualização
Durante a atualização, o sistema reinicia algumas vezes. O reinício final atualiza o VM para o último lançamento secundário do RHEL 8. 

O processo de upgrade pode demorar entre 20 minutos e 2 horas. O tempo total depende de vários fatores, como o tamanho VM e o número de pacotes instalados no sistema.

## <a name="preparations"></a>Preparativos
A Red Hat e a Azure recomendam a utilização de um upgrade no local para a transição de um sistema para a próxima versão principal. 

Antes de iniciar a atualização, tenha em mente as seguintes considerações. 

>[!Important] 
> Tire uma foto da imagem antes de iniciar a atualização.

* Certifique-se de que está a utilizar a versão mais recente do RHEL 7. Atualmente, a versão mais recente é RHEL 7.9. Se utilizar uma versão bloqueada e não conseguir atualizar para RHEL 7.9, siga [estes passos para mudar para um repositório de não-EUS (suporte de atualização alargada).](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock)

* Executar o seguinte comando para verificar a sua atualização e ver se terminará com sucesso. O comando deve gerar *ficheiro /var/log/leapp/leapp-report.txt.* Este ficheiro explica o processo, o que está a acontecer e se a atualização é possível.

    >[!NOTE]
    > Utilize a conta raiz para executar os comandos neste artigo. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* Certifique-se de que a consola em série está funcional. Utilizará esta consola para monitorização durante o processo de atualização.

* Ativar o acesso à raiz SSH em */etc/ssh/sshd_config*:
    1. Abra o ficheiro */etc/ssh/sshd_config*.
    1. Procurar `#PermitRootLogin yes`.
    1. Retire o sinal de número `#` () para descomprometer a corda.

## <a name="upgrade-steps"></a>Atualizar passos

Siga estes passos cuidadosamente. Recomendamos experimentar a atualização numa máquina de teste antes de experimentá-la em casos de produção.

1. Faça uma `yum` atualização para obter os mais recentes pacotes de clientes.
    ```bash
    yum update -y
    ```

1. Instale o `leapp-client-package` .
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. No [portal Do Chapéu Vermelho](https://access.redhat.com/articles/3664871), obtenha o ficheiro *leapp-data.tar.gz* que contém *repomap.csv* e *pes-events.jsem*. Extrair o ficheiro *.tar.gz dados de salto.*
    1. Descarregue o ficheiro *.tar.gz dados do leapp.*
    1. Extraia o conteúdo e remova o ficheiro. Utilize o seguinte comando:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Adicione um `answers` ficheiro para `leapp` .
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Começa a atualização.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Depois de o `leapp upgrade` comando terminar com sucesso, reinicie manualmente o sistema para concluir o processo. O sistema não está disponível à medida que reinicia algumas vezes. Monitorize o processo utilizando a consola em série.

1.  Verifique se a atualização terminou com sucesso.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Quando a atualização terminar, remova o acesso ao SSH raiz:
    1. Abra o ficheiro */etc/ssh/sshd_config*.
    1. Procurar `#PermitRootLogin yes`.
    1. Adicione um sinal de número ( `#` ) para comentar a cadeia.

1. Reinicie o serviço SSHD para aplicar as alterações.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>Problemas comuns

Os seguintes erros geralmente acontecem quando o `leapp preupgrade` processo falha ou o processo `leapp upgrade` falha:

* **Erro**: Não se encontram correspondências para os seguintes padrões de plugin desativado.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **Solução**: Desative o plug-in do gestor de subscrição. Desative-o editando o ficheiro */etc/yum/pluginconf.d/subscription-manager.conf* e alterando `enabled` para `enabled=0` .

    Este erro ocorre quando o plug-in do gestor de subscrição `yum` que está ativado não é utilizado para `PAYG` VMs.

* **Erro**: Possíveis problemas com o início remoto utilizando raiz.

    Pode ver este erro quando `leapp preupgrade` falhar:

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **Solução**: Permitir o acesso à raiz em */etc/sshd_config*.

    Este erro ocorre quando o acesso ao SSH de raiz não está ativado em */etc/sshd_config*. Para mais informações, consulte a secção [preparação](#preparations) neste artigo. 


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [as imagens do Red Hat em Azure.](./redhat-images.md)
* Saiba mais sobre [a infraestrutura de atualização do Red Hat.](./redhat-rhui.md)
* Saiba mais sobre a [oferta DA RHEL BYOS.](./byos.md)
* Para saber mais sobre os processos de upgrade do Chapéu Vermelho no local, consulte [upgrade de RHEL 7 a RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) na documentação do Chapéu Vermelho.
* Para saber mais sobre as políticas de suporte da Red Hat para todas as versões do RHEL, consulte o ciclo de [vida da Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) na documentação do Chapéu Vermelho.