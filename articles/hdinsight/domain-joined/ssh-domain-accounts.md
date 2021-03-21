---
title: Gerir o acesso SSH para contas de domínio em Azure HDInsight
description: Passos para gerir o acesso SSH às contas AZURE AD em HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 3fab06f5e269f311f798c096a6465c8c6ce75fc2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946749"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Gerir o acesso SSH para contas de domínio em Azure HDInsight

Em clusters seguros, por padrão, todos os utilizadores de domínio em [Azure AD DS](../../active-directory-domain-services/overview.md) são autorizados a [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) na cabeça e nos nós de borda. Estes utilizadores não fazem parte do grupo de sudoers e não têm acesso à raiz. O utilizador SSH criado durante a criação do cluster terá acesso à raiz.

## <a name="manage-access"></a>Gerir o acesso

Para modificar o acesso SSH a utilizadores ou grupos específicos, atualize `/etc/ssh/sshd_config` cada um dos nós.

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Abra o `ssh_confi` ficheiro G.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Modifique o `sshd_config` ficheiro conforme desejado. Se restringir os utilizadores a determinados grupos, então as contas locais não podem entrar nesse nó. O seguinte é apenas um exemplo de sintaxe:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Em seguida, guarde alterações: **Ctrl + X,** **Y**, **Enter**.

1. Reiniciar sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Repita acima dos degraus para cada nó.

## <a name="ssh-authentication-log"></a>Registo de autenticação SSH

O registo de autenticação SSH está escrito em `/var/log/auth.log` . Se vir alguma falha de login através do SSH para contas locais ou de domínio, terá de analisar o registo para depurar os erros. Muitas vezes, o problema pode estar relacionado com contas específicas de utilizadores e é geralmente uma boa prática tentar outras contas de utilizador ou SSH usando o utilizador SSH padrão (conta local) e, em seguida, tentar um kinit.

## <a name="ssh-debug-log"></a>SSH depurar log

Para ativar a registo de verbose, terá de reiniciar `sshd` com a `-d` opção. Como `/usr/sbin/sshd -d` você também pode correr em uma porta personalizada `sshd` (como 2222) para que você não tenha que parar o principal daemon SSH. Também pode utilizar `-v` a opção com o cliente SSH para obter mais registos (visão do lado do cliente das falhas).

## <a name="next-steps"></a>Passos seguintes

* [Gerir clusters HDInsight com pacote de segurança empresarial](./apache-domain-joined-manage.md)
* [Ligue-se ao HDInsight (Apache Hadoop) utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
