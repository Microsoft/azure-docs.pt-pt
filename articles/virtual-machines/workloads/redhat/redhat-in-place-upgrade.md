---
title: Upgrade no lugar de red hat enterprise linux imagens em Azure
description: Encontre passos para realizar a atualização no local a partir de imagens Red Hat Enterprise 7.x para a versão mais recente 8.x
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 1160bc43db0dc9ec1714b1766c8cadf09660e291
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844573"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Atualizações da Red Hat Enterprise Linux no local

Este artigo fornece instruções passo a passo sobre como realizar uma atualização no local da Red Hat Enterprise Linux 7 para Red Hat Enterprise Linux 8 usando o utilitário 'Leapp' em Azure. Durante a atualização in-place, o sistema operativo RHEL 7 existente é substituído pela versão RHEL 8.

>[!Note] 
> SQL Server em Red Hat Enterprise Linux oferece não suporta upgrade no local em Azure.

## <a name="what-to-expect-during-the-upgrade"></a>O que esperar durante a atualização
O sistema reiniciará algumas vezes durante a atualização e isso é normal. O último reboot irá atualizar o VM para o último lançamento menor do RHEL 8. 

O processo de upgrade pode demorar entre 20 minutos e algumas horas, o que depende de vários fatores, como o tamanho de VM e o número de pacotes instalados no sistema.

## <a name="preparations-for-the-upgrade"></a>Preparativos para a atualização
O upgrade no local é a forma oficialmente recomendada pela Red Hat e Azure de permitir que os clientes atualizem o sistema para a próxima versão principal. Antes de realizar o upgrade aqui são algumas coisas, você deve estar ciente e ter em consideração. 

>[!Important] 
> Por favor, tire uma foto da imagem antes de realizar a atualização.

>[!NOTE]
> Os comandos deste artigo precisam de ser executados usando a conta raiz

* Certifique-se de que está a utilizar a mais recente versão RHEL 7, que atualmente é RHEL 7.9. Se estiver a utilizar uma versão bloqueada e não conseguir atualizar para RHEL 7.9, pode utilizar os [passos aqui para mudar para um repositório não-EUS](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Faça o comando abaixo para saber como está a sua atualização e se irá completar. O comando deve gerar um ficheiro em '/var/log/leapp/leapp-report.txt' que explique o processo e o que está a ser feito e se a atualização é possível ou não
    ```bash
    leapp preupgrade --no-rhsm
    ```
* Certifique-se de que a consola em série está funcional, pois isto permite a monitorização durante o processo de atualização.

* Ativar o acesso à raiz SSH em `/etc/ssh/sshd_config`
    1. Abra o ficheiro `/etc/ssh/sshd_config`
    1. Pesquisa por '#PermitRootLogin sim'
    1. Remova o '#' para descompromisso

## <a name="steps-for-performing-the-upgrade"></a>Passos para realizar a atualização

Execute estes passos cuidadosamente. É definitivamente recomendado experimentar o upgrade numa máquina de teste antes de casos de produção.

1. Realize uma atualização yum para obter os mais recentes pacotes de clientes.
    ```bash
    yum update -y
    ```

1. Instale o pacote leapp-cliente.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Utilize dados .tar.gz ficheiros com repomap.csv e pes-events.js, presentes no [portal RedHat,](https://access.redhat.com/articles/3664871)e extraia-os. 
    1. Descarregue o ficheiro.
    1. Extrair o conteúdo e remover o ficheiro utilizando o seguinte comando:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Adicione o ficheiro 'respostas' para 'Leapp'.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Execute a atualização 'Leapp'.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Depois de o `leapp upgrade` comando ter terminado com sucesso, reinicie manualmente o sistema para concluir o processo. O sistema reiniciará algumas vezes durante as quais estará indisponível. Monitorize o processo utilizando a consola em série.

1.  Verifique se a atualização foi concluída com sucesso.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Remova o acesso à raiz após a conclusão da atualização.
    1. Abra o ficheiro `/etc/ssh/sshd_config`
    1. Pesquisa por '#PermitRootLogin sim'
    1. Adicione o '#' para comentar

1. Reiniciar o serviço sshd para as alterações a produzirem efeitos
    ```bash
    systemctl restart sshd
    ```

## <a name="common-issues"></a>Problemas Comuns
Estes são alguns dos casos comuns que o `leapp preupgrade` processo ou o processo podem `leapp upgrade` falhar.

**Erro: Não se encontram fósforos para os seguintes padrões de plugin desativado**
```plaintext
STDERR:
No matches found for the following disabled plugin patterns: subscription-manager
Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
```
**Solução**\
Desative o plugin do gestor de subscrição editando o ficheiro `/etc/yum/pluginconf.d/subscription-manager.conf` e alterando ativado para `enabled=0` .

Isto é causado pelo plugin yum gestor de subscrição ativado, que não é usado para VMs PAYG.

**Erro: Possíveis problemas com login remoto usando raiz** Pode `leapp preupgrade` falhar com o seguinte erro:
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
**Solução**\
Ativar o acesso à raiz em `/etc/sshd_config` .
Isto é causado por não permitir o acesso à raiz em `/etc/sshd_config` conforme a secção "[Preparações para a atualização](#preparations-for-the-upgrade)". 

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as [imagens do Chapéu Vermelho em Azure.](./redhat-images.md)
* Saiba mais sobre a [Infraestrutura de Atualização do Chapéu Vermelho.](./redhat-rhui.md)
* Saiba mais sobre a [oferta DA RHEL BYOS.](./byos.md)
* As informações sobre os processos de atualização do Chapéu Vermelho podem ser encontradas na documentação do Chapéu Vermelho, [atualização de RHEL 7 a RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* As informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Red Hat Enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)
