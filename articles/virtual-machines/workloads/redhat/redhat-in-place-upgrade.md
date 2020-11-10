---
title: Upgrade no lugar de red hat enterprise linux imagens em Azure
description: Encontre passos para realizar a atualização no local a partir de imagens Red Hat Enterprise 7.x para a versão mais recente 8.x
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 48884e6faa5f26f027c772b44d5f960979a40d1d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447901"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Atualizações da Red Hat Enterprise Linux no local

Este artigo fornece instruções passo a passo sobre como realizar uma atualização no local da Red Hat Enterprise Linux 7 para Red Hat Enterprise Linux 8 usando o utilitário 'Leapp' em Azure. Durante a atualização in-place, o sistema operativo RHEL 7 existente é substituído pela versão RHEL 8.

>[!Note] 
> SQL Server em Red Hat Enterprise Linux oferece não suporta upgrade no local em Azure.

## <a name="what-to-expect-during-the-upgrade"></a>O que esperar durante a atualização
O sistema reiniciará algumas vezes durante a atualização e isso é normal. O último reboot irá atualizar o VM para o último lançamento menor do RHEL 8.

## <a name="preparations-for-the-upgrade"></a>Preparativos para a atualização
As atualizações no local são a forma oficialmente recomendada pela Red Hat e Azure para permitir que os clientes atualizem o seu sistema para a próxima versão principal. Antes de realizar o upgrade aqui são algumas coisas, você deve estar ciente e ter em consideração. 

>[!Important] 
> Por favor, tire uma foto da imagem antes de realizar a atualização.

>[!NOTE]
> Os comandos deste artigo precisam de ser executados usando a conta raiz

* Certifique-se de que está a utilizar a mais recente versão RHEL 7, que atualmente é RHEL 7.9. Se estiver a utilizar uma versão bloqueada e não conseguir atualizar para RHEL 7.9, pode utilizar os [passos aqui para mudar para um repositório não-EUS](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Faça o comando abaixo para saber como está a sua atualização e se irá completar. O comando deve gerar um ficheiro em '/var/log/leapp/leapp-report.txt' que explique o processo e o que está a ser feito e se a atualização é possível ou não
    ```bash
    leapp preupgrade --no-rhsm
    ```

## <a name="steps-for-performing-the-upgrade"></a>Passos para realizar a atualização

Execute estes passos cuidadosamente. É definitivamente recomendado experimentar o upgrade numa máquina de teste antes de casos de produção.

1. Realize uma atualização yum para obter os mais recentes pacotes de clientes.
    ```bash
    yum update
    ```

1. Instale o pacote leapp-cliente.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Utilize o ficheiro leapp-data.tar.gz com repomap.csv e pes-events.js, presente no [portal RedHat,](https://access.redhat.com/articles/3664871)e extrai-os. 
    1. Descarregue o ficheiro.
    1. Extrair o conteúdo e remover o ficheiro utilizando o seguinte comando:
    ```bash
     tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```
    


1. Adicione o ficheiro 'respostas' para 'Leapp'.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ```
    
1. Ativar PermitRootLogin em /etc/ssh/sshd_config
    1. Abra o ficheiro /etc/ssh/sshd_config
    1. Pesquisa por '#PermitRootLogin sim'
    1. Remova o '#' para descompromisso



1. Execute a atualização 'Leapp'.
    ```bash
    leapp upgrade --no-rhsm
    ```
1. Reiniciar o serviço sshd para as alterações a produzirem efeitos
    ```bash
    systemctl restart sshd
    ```
1. Comente o PermitRootLogin em /etc/ssh/sshd_config novamente
    1. Abra o ficheiro /etc/ssh/sshd_config
    1. Pesquisa por '#PermitRootLogin sim'
    1. Adicione o '#' para comentar

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as [imagens do Chapéu Vermelho em Azure.](./redhat-images.md)
* Saiba mais sobre a [Infraestrutura de Atualização do Chapéu Vermelho.](./redhat-rhui.md)
* Saiba mais sobre a [oferta DA RHEL BYOS.](./byos.md)
* As informações sobre os processos de atualização do Chapéu Vermelho podem ser encontradas na documentação do Chapéu Vermelho, [atualização de RHEL 7 a RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* As informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Red Hat Enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)